---
layout: post
title: actions-on-google-nodejs version 2へのマイグレーションポイント
categories:
- actions on google
---

Googleアシスタント向けアプリ開発で欠かせないものの一つに、SDKがあります。プラットフォームであるActions on Googleや
DialogflowからのWebhookのリクエストとレスポンスはJSON形式なのですが、その項目数も多く、手で扱うにはなかなかしんどい
ものがあります。actions-on-google-nodejsは、Webhookを受け取って必要な処理を手軽に書くことができる便利機能を数多く
提供してくれるJavaScript SDKです。ほとんどの開発者は、このSDKを使っていることと思います。

実はこのSDKは、初期バージョン（v1）からVersion 2（v2）にアップグレードした際に、その使い方が大きく変わりました。v1 を
使っていたアプリは、v2 に入れ替える際に、かなり大きくコードを変更する必要があります。

僕も公開されているアプリが3つほどあって、やっと v2 へのマイグレーションを終えました。そこでいくつかポイントとして
抑えておくべきことがあったので、ここにまとめておきます。

# Action name RoutingからIntent name Routingに変更する

v1 では、Dialogflowのインテント定義に含まれる "Action name" によって、呼び出されるフルフィルメントの処理が決定される、
というポリシーでした。つまり、以下のようなコードです。

```js
const App = require("actions-on-google").DialogflowApp;
const app = new App({request: req, response: res});

const handleWelcome = app => {
  app.ask("Welcome to my app! ...");
};

const actionMap = new Map();
actionMap.set("action_welcome", handleWelcome);
app.handleRequest(actionMap);
```

しかし、v2 では "Action name" による処理のルーティングは廃止され、なんと "Intent name" によるルーティングとなりました。
具体的には、以下のような感じになります。

```js
const { dialogflow } = require("actions-on-google");
const app = dialogflow({ debug: true });

app.intent("Default Welcome Intent", conv => {
  conv.ask("Welcome to my app! ...");
});
```

[個人的にはこれは受け入れがたい](https://github.com/actions-on-google/actions-on-google-nodejs/issues/108#issuecomment-387321310)のですが、v2 の現状の仕様なので仕方ありません。ハンドラ関数を `app.intent("...", conv => { ... })` に着々と変換していきます。

いままで「複数のインテントから一つの Action name にして、フルフィルメントの処理を一つにしていた」場合は、 `app.intent()`
関数の第一引数は、Intent name の配列を渡すことができるので、それで処理の共通化を実現できます。

# 返事を返す際の関数呼び出しを変更する

v1 では、返事を返すための関数は、ask と tell でした。特に tell は、わかりにくい、という意見が多かったようです。

```js
// 返事を返す
app.ask("...");

/// 返事をして会話を終了する
app.tell("...");
```

v2 では、ask は変わりませんが、会話の終了は close に変更になりました。また、これらの関数は、ハンドラ関数の引数として
渡されてくる conv に対して呼び出します。

```js
app.intent("...", conv => {
  // 返事を返す
  conv.ask("...");

  // 返事をして会話を終了する
  conv.close("...");
});
```

ask 関数については、もう一つ以下の変更が生じます。

# Static Reprompt から Dynamic Reprompt に変更する

Reprompt とは、ユーザが一定時間何も言わなかった際に、アプリから再度ユーザに問いかけるための機能です。v1 では、
Static Reprompt がありました。これの利用方法は、とても簡単でした。 `app.ask()` 関数の第2引数に、問いかけのための
メッセージ文字列の配列を渡すだけでした。

```js
const repromptMessages = [
    "Hello?",
    "Are you here?",
    ...
];

app.ask("Please say something.", repromptMessages);
```

この Static Reprompt 機能が、v2 では残念ながらなくなってしまいました。[ドキュメント](https://developers.google.com/actions/assistant/reprompts) からも、その記載がなくなっています。その代わり、[Dynamic Reprompt](https://developers.google.com/actions/assistant/reprompts#dynamic_reprompts) として対応する
ことになります。Dialogflowの場合、具体的には Reprompt のためのインテントを新規に作っていくことになります。

![]({{ "/images/2018/05/aog_sdk_v2_01.webp" | prepend: site.baseurl }})

そして、 `app.intent()` 関数を使って、ハンドラ関数の登録を増やしていきます。

# 非同期処理の場合は Promise を返す

v1 の場合、呼び出されたハンドラ関数内で非同期処理が行われたとしても、その中のどこかで `app.ask()` などが呼び出されさえ
すれば、それで動作しました。具体的には、以下のように書いても大丈夫でした。

```js
const request = require("request");

const welcome = app => {
  request(..., (error, response, body) => {
    ...
    app.ask("...");
  });
};
```

v2 になると、上記のようなコードは、以下のエラーが発生するようになります。

```
Error: No response has been set. Is this being used in an async call that was not returned as a promise to the intent handler? at DialogflowConversation.response
```

ハンドラ関数の結果として返信するなどすることができず、代わりに非同期通信などを行った後に返信するなどをしたい場合は、
ハンドラ関数の戻り値として Promise を返す必要があります。例えば、以下のような感じです。

```js
const request = require("request");

app.input("welcome", conv => {
  return new Promise((resolve, reject) => {
    ...
    request(..., (error, response, body) => {
      ...
      if (error) {
        ...
        reject(...);
      } else {
        ...
        resolve(...);
      }
  }).then(result => {
    ...
    conv.ask(...);
  }).catch(error => {
    ...
    conv.close(...);
  });
});
```

僕が作ったアプリの中では、外部通信（APIの呼び出し）や、Datastore へのアクセスなどがあったので、そういった箇所は全て
Promise を使った処理に書き換えました。

# コンテキストの設定と削除

今までフルフィルメントのハンドラ関数の中でコンテキストを操作するには、 `app.setContext()` 関数を利用するしかありませんでした。
具体的には、v1 でのコンテキスト操作は以下のようになります。

```js
// コンテキストの設定（第2引数は無指定あるいは 1 以上の数値）
app.setContext("CONTEXT_FOOBAR", 1);

// コンテキストの削除（第2引数に 0 を渡すと削除になる）
app.setContext("CONTEXT_FOOBAR", 0);
```

v2 からは、コンテキストの操作が明示的に規定されました。

[class ContextValues<TContexts>](https://actions-on-google.github.io/actions-on-google-nodejs/classes/dialogflow.contextvalues.html)

つまり、v2 でのコンテキスト操作は、以下になります。

```js
// コンテキストの設定
conv.contexts.set("CONTEXT_FOOBAR", 1);

// コンテキストの削除
conv.contexts.delete("CONTEXT_FOOBAR");
```

ここは v1 から v2 でわかりやすくなった箇所かなと思います。

# まとめ

v1 から v2 に変わって、僕が持っているアプリは基本的な機能しか使っていなかった割には、比較的大きな構造変更が発生
しました。v1 のヘルパー関数を使っていたアプリの場合は、もっと大きくコードが変わることも考えられます（関数呼び出し
ではなく、 conv.ask() 関数に必要なオブジェクトを渡す形式に変える）。

もしまだ v1 を使っている方々は、上記を参考にして、早めに v2 に移行すると良いかと思います。
