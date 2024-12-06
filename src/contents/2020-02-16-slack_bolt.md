---
layout: post
title: Slack BoltとFirebase Functionsは相性が悪い
categories:
- Slack
---

4, 5年前から Slack BOT のコードを書いている気がしますが、長らく Hubot の Ruby 実装である Ruboty を使ってきました。ただ、Ruboty だと他の BOT の開発についてもサポートしている都合上、Slack が提供しているリッチな機能のほとんどを利用できません。BOT との会話のみであればそれで事足りるのですが、やはりユーザ体験を高めるためには、modals などを利用したくなってきます。

そこで、最近は Slack Bolt を使い始めています。これは、TypeScript で書かれた Slack App を作るためのフレームワークです。

[https://slack.dev/bolt/ja-jp/tutorial/getting-started](https://slack.dev/bolt/ja-jp/tutorial/getting-started)

今回は、この Slack Bolt を使い始めたときに、ハマりにハマった話をしたいと思います。

# Firebase Functionsで書き始めた

僕は Firebase や Google Cloud Platform を使う機会が多く、Slack Bolt を使って Slack App を書き始めようと思ったときに、真っ先に思ったことは「Firebase Functions で作ろう！」ということでした。

例えば、Slack BOT に対して何かメンションされたときに応答するコードを、以下のように書くことができます。

```js
import * as functions from 'firebase-functions';
import {
  App, ExpressReceiver
} from '@slack/bolt';

const expressReceiver = new ExpressReceiver({
  signingSecret: '...',
  endpoints: '...'
});

const app = new App({
  receiver: expressReceiver,
  token: '...'
});

app.error(console.log);

app.event('app_mention', async ({ say }) => {
  say('ボーっと生きてんじゃねーよ！');
});

exports.slack = functions.https.onRequest(expressReceiver.app);
```

上記のコードは、ちゃんと問題なく動作します。これを見て、よし、Slack Bolt と Firebase Functions の組み合わせでいけるぜ！と思ってしまいました。

# モーダルビューを使い始めておかしくなった

Slack Bolt を使い始めた理由は、モーダルビューを使ってみたかったのですが、それをやり始めてから、なぜか動作がおかしくなってきました。例えば、以下のようなコードを書いたときです。

```js
const datastore = new Datastore();

app.action('button_pressed', async ({ ack }) => {
  ack();

  const query = datastore.createQuery('...').filter('foo', '=', 'bar');
  const [entities] = await datastore.runQuery(query);

  await app.client.views.open({
    token: '...',
    trigger_id: '...',
    view: {
      type: 'modal',
      blocks: createBlocks(entities),
      ...
    }
  });
});
```

ボタンが押された際に、Cloud Datastore から何か読み込みを行って、その結果からモーダルビューを作って表示する、というコードになります。このコード、実際に動作させてみると、うまく動くこともあれば、エラーを吐いてしまってモーダルビューが表示されないこともある、という非常に不安定な動作となります。

具体的には、Datastore へのアクセス時に、以下のエラーが発生します。

`Error: Could not load the default credentials. Browse to https://cloud.google.com/docs/authentication/getting-started for more information...`

これは、Datastore にアクセスするために必要となるクレデンシャル情報がない、っていうことを意味しています。

でも、実は、以下のコードだと、このエラーは出ません。

```js
const datastore = new Datastore();

app.event('app_mention', async ({ say }) => {
  const query = datastore.createQuery('...').filter('foo', '=', 'bar');
  const [entities] = await datastore.runQuery(query);

  say('ボーっと生きてんじゃねーよ！');
});
```

これらの差は、一体何なんでしょうか？

# Firebase Functionsの特性が原因だった

コードを見比べていて、明確に違う点は、 `ack()` を呼び出しているかどうかが挙げられます。最初は Datastore 関連の何かミスがあるのかと思って調査をしていたのですが、全く原因が思いつきませんでした。そこで原点に戻ってコードを見てみたところ、 `ack()` の呼び出しの有無が何かあるのかな、と思いついたわけです。

では、 `ack()` は一体何をしているのか？Slack Bolt のコードを見てみると、以下でした。

```js
const event: ReceiverEvent = {
  body: req.body as { [key: string]: any },
  ack: (response: any): void => {
    // TODO: if app tries acknowledging more than once, emit a warning
    if (timer !== undefined) {
      clearTimeout(timer);
      timer = undefined;
    
      if (!response) res.send('');
      if (typeof response === 'string') {
        res.send(response);
      } else {
        res.json(response);
      }
    }
  },
  respond: undefined,
};
```

上記のコード例に関して言えば、 `res.send('')` をしているだけ、つまり、Firebase Functions の呼び出し元に HTTP Status 200 を返しているだけです。ここが、今回の問題を引き起こすポイントでした。

Firebase Functions の HTTP トリガーでの特性として、実は以下のような説明があります。

> HTTP 関数は、常に send()、redirect()、または end() を使用して終了してください。そうしないと、関数は引き続き実行され、システムによって強制終了される可能性があります。

[HTTP リクエスト経由で関数を呼び出す](https://firebase.google.com/docs/functions/http-events?hl=ja)

これは、関数を `send()` を使わないと、途中で関数の実行が叩き切られますよ、というように読めますが、実はそれだけではありません。今回の問題では、

「 `send()` を実行した後に何か処理を行っていると、その途中で実行が叩き切られますよ。それを避けるために、 `send()` は関数の最後の処理としてください。」

というように読まなければなりませんでした。つまり、

1. `ack()` の実行（= `res.send()` の実行）。
1. Datastore へのアクセス。
1. Slack API へのアクセス。

という処理では、1つ目の処理で `send()` が呼び出されているため、その後の処理の実行については一切の保証がない、ということだったのです。

このような制約のため、エラーが発生した場面では、おそらく `ack()` 実行後には、関数の実行環境が壊されてしまっていて、その結果 Datastore へのアクセスに必要なクレデンシャル情報を得ることができずに、結果 Datastore アクセスに失敗した、と考えれば辻褄が合います。

# 処理順を変えてみるも・・・

さて、原因はわかりました。では、どう修正すればよいか？

単純に考えて、「 `res.send(...)` を最後にすればいいんでしょ？」と思いつきます。つまり、こういうことです。

```js
const datastore = new Datastore();

app.action('button_pressed', async ({ ack }) => {
  const query = datastore.createQuery('...').filter('foo', '=', 'bar');
  const [entities] = await datastore.runQuery(query);

  await app.client.views.open({
    token: '...',
    trigger_id: '...',
    view: {
      type: 'modal',
      blocks: createBlocks(entities),
      ...
    }
  });

  ack();
});
```

`ask()` を最後にしました。これで、先程の制約である「 `res.send(...)` は関数の処理の最後にする」っていうことには準拠できたわけです。

でも、なにか違和感があります。処理の順番として、

* イベントの配送に応答する → DB アクセスする → Slack API 叩いてモーダルビューを出す

っていう処理順が本来のはずですが、

* DB アクセスする → Slack API 叩いてモーダルビューを出す → イベントの配送に応答する

という順番になってしまっていて、これでも良いのかもしれませんが、気持ち悪さが残ります。

そして、問題はこれだけにとどまりません。 `ack()` の呼び出しにも、実は制約があったのです。

# ack() 呼び出しは「急がないといけない」

先程みた `ack()` の実際のコードには、 `timer` に関する処理が書かれていたのに気がついたでしょうか？実は、Slack Bolt の説明にも、以下のような記載があります。

> この ack() による応答は 3 秒以内にしなければならないので、新しいメッセージを送信したり、データベースから情報を取得したりする直前に ack() を呼び出すことをお勧めします。

[イベントの確認](https://slack.dev/bolt/ja-jp/concepts#acknowledge)

いろんな処理の前に `ack()` を呼び出せ、って書いてあるのです。つまり、上記の例の場合、Datastore へのアクセスに時間がかかったりすると、 `ack()` 呼び出しの前に 3 秒ルールをカウントしてる `timer` によって、エラー扱いされてしまうのです。

もちろん、3 秒というのは比較的長いので「普通はそんなに時間かからないだろ、かかってちゃいけないだろ」とは思います。しかし、他のサービスの API を使っていたりしてネットワーク I/O がかかる場合は、なかなかそれを厳密に守るのも厳しいです。

# 双方の制約によって相性が悪くなっている

まとめると、Firebase Functions と Slack Bolt それぞれに、以下の制約がありました。

* Firebase Functions - `res.send(...)` の後の処理は保証されない。
* `ack()` は 3 秒以内に呼び出さないといけない。

前者を満たすためには `ack()` を最後に呼びたい、でも後者を満たすためには `ack()` 前の処理を 3 秒以内に収めなければならない。

故に、Slack Bolt を Firebase Functions で使うのは相性が悪い、となります。

# では、どうしたら良いか？

この相性問題を避けるためにはどうしたら良いか？答えは結構な数があると思いますが、どれも基本的には「別の機構を使う」となるかと思います。特に、Firebase Functions の「レスポンスを送った後の処理が保証されない」という制約を避けられる別のものを使う、っていうことになるかと思います。

僕の場合は、できるだけコードを変更せずに解決したかったのと、Datastore は使い続けたかったので、AppEngine for NodeJS に移行しました。こちらであれば、Firebase Functions のような制約はありませんので、素直にコードを書いていくことが可能です。もちろんリクエストベースではなくインスタンスベースの課金になるなどのデメリットがありますが、Slack BOT の利用数もそう多くないため、今のところは課金額については大きな問題は出ません。

# まとめ

以上のような感じで、最初あまり双方の制約を意識しないというか、知らずにガンガン作っていったがために、かなりハマりました。もし Slack Bolt を使おうと考えている方、特にサーバレスな環境でやろうと思っている方々には、上記のような相性問題があるってことを知っておいていただくと、時間を無駄にすることなく開発ができるかと思います。
