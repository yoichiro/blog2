---
layout: post
title: Media responsesを使った長いオーディオコンテンツの提供
categories:
- actions on google
---

6月14日に、 [Leon Nicholls](https://plus.google.com/u/0/+LeonNicholls) は、 `ko-KR` を除く全ての Actions on Google の
ロケール向けに、Media responses がサポートされたことを G+ 上でアナウンスしました。

Media Response Update: [https://plus.google.com/u/0/+LeonNicholls/posts/cbATWnXx5cv](https://plus.google.com/u/0/+LeonNicholls/posts/cbATWnXx5cv)

![]({{ "/images/2018/06/media_responses_en_1.webp" | prepend: site.baseurl }})

SSML では、オーディオの長さに制限がありました。その長さは、 120 秒以内です。しかし、"Media responses" を使うことで、
その制限を超えることができます。アナウンスの通り、Media responses 機能は、ほとんどの言語にて使用することができます。
素晴らしいことです！

本当に機能するかどうかを確認するために、実際に使ってみました。結果として、正常に機能しました。

僕は、以下のテストコードを使いました:

```js
app.intent("input.media", conv => {
    if (!conv.surface.capabilities.has("actions.capability.MEDIA_RESPONSE_AUDIO")) {
        conv.ask("メディアレスポンスオーディオはサポートされていません。");
        return;
    }
    conv.ask(new SimpleResponse({
        speech: "これはメディアレスポンスでの返事です。",
        text: "これはメディアレスポンスでの返事です。"
    }));
    conv.ask(new MediaObject({
        name: 'Jazz in Paris',
        url: 'http://storage.googleapis.com/automotive-media/Jazz_In_Paris.mp3',
        description: 'A funky Jazz tune',
        icon: new Image({
            url: 'http://storage.googleapis.com/automotive-media/album_art.jpg',
            alt: 'Media icon',
        }),
    }));
    conv.ask(new Suggestions("次の機能のテスト"));
});
```

新しい `actions.capability.MEDIA_RESPONSE_AUDIO` サーフェスが導入されています。もし Media responses を送信する場合は、
ユーザのデバイスがそのサーフェスをサポートしているかどうかをチェックする必要があります。もしサポートされていれば、
Media responses を送信することが可能です。

Media responses を送信するために、 `MediaObject` クラスを使用します。このクラスの使い方はシンプルです。基本的に、
オーディオファイルの URL を渡すだけです。しかし、追加として、もしそのときに会話を続けたい場合は、以下も行わなければ
なりません:

* `SimpleResponse` を送信する。
* サジェスチョンチップを提供するために、 `Suggestions` も送信する。

上記の Media responses の送信後、Google アシスタントからの以下の応答を目にすることでしょう:

![]({{ "/images/2018/06/media_responses_ja_2.webp" | prepend: site.baseurl }})

Actions simulator にて、 Media responses をテストすることができます。特に、オーディオプレイヤーの UI を含む応答を
見ることができるでしょう。そのときに、指定されたオーディオが自動的に再生開始されます。

Media responses を送信した時に会話を終えたい場合は、以下のコードのように、サジェスチョンチップは必要ありません:

```js
app.intent("input.media", conv => {
    if (!conv.surface.capabilities.has("actions.capability.MEDIA_RESPONSE_AUDIO")) {
        conv.ask("メディアレスポンスオーディオはサポートされていません。");
        return;
    }
    conv.close(new SimpleResponse({
        speech: "これはメディアレスポンスでの返事です。",
        text: "これはメディアレスポンスでの返事です。"
    }));
    conv.close(new MediaObject({
        name: 'Jazz in Paris',
        url: 'http://storage.googleapis.com/automotive-media/Jazz_In_Paris.mp3',
        description: 'A funky Jazz tune',
        icon: new Image({
            url: 'http://storage.googleapis.com/automotive-media/album_art.jpg',
            alt: 'Media icon',
        }),
    }));
});
```

再生の完了時に、コールバックを受けることができます。もし Dialogflow を使う場合は、再生が完了したことを知るために、
`actions_intent_MEDIA_STATUS` イベントを受け取ることが可能です。

![]({{ "/images/2018/06/media_responses_ja_3.webp" | prepend: site.baseurl }})

より詳しい情報は、Actions on Google 開発者向けの [公式ドキュメント](https://developers.google.com/actions/assistant/responses#media_responses) を参照ください。
