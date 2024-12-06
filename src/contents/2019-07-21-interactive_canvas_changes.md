---
layout: post
title: Interactive Canvasにいくつか変更点があります
categories:
- actions on google
---

今年2019年の Google I/O にて発表された Google アシスタント関連の発表の中で、スマートディスプレイへの対応はかなり大きく取り上げられていました。そして、スマートディスプレイの特徴でもある「画面」を活用したアクションを開発するために、Interactive Canvas という仕組みが導入されています。これにより、アクションがスマートディスプレイ向けにウェブアプリを提供することができます。

Interactive Canvas の詳しい内容は、以下のリファレンスドキュメントを参考にしてください。

[Interactive Canvas - Actions on Google Reference Document](https://developers.google.com/actions/interactivecanvas/)

現在は Developer Preview という段階であり、Interactive Canvas を使ったアクションを一般公開することはまだできません。しかし、 Interactive Canvas を使ったアクションを開発すること自体は可能です。すでに多くの開発者の方々が、Interactive Canvas を利用したアクションの開発にチャレンジしていると思います。

Interactive Canvas の各種 API について、そのネーミングに違和感を感じた開発者の方々はもしかしたら多かったかもしれません（僕もその一人です）。昨日、名前の一貫性の確保のために、いくつか変更が施されました。ここでは、その変更内容について紹介いたします。

# ImmersiveResponseからHtmlResponseに変更

フルフィルメントから Interactive Canvas 向けのレスポンスを行うために、従来は ImmersiveResponse クラスを利用していました。今後は、[HtmlResponse](https://actions-on-google.github.io/actions-on-google-nodejs/2.9.1-preview.1/classes/_service_actionssdk_conversation_response_html_.htmlresponse.html) クラスを利用することになります。使用方法は、ImmersiveResponse クラスの時と同様に、HtmlResponse オブジェクトを生成して conv.ask() メソッドに渡します。

```js
const { dialogflow, HtmlResponse, ... } = require('actions-on-google');

const app = dialogflow();

app.intent('Default Welcome Intent', conv => {
  conv.ask('...');
  conv.ask(new HtmlResponse({
    url: '...'
  }));
});
```

# stateからdataに変更

HtmlResponse クラスに変更されると同時に、フルフィルメントとウェブアプリ間での情報のやり取りを行うために、従来では state というプロパティ名が使用されていました。この名前が、state から [data](https://actions-on-google.github.io/actions-on-google-nodejs/2.9.1-preview.1/classes/_service_actionssdk_conversation_response_html_.htmlresponse.html#data) と変更されました。

```js
conv.ask(new HtmlResponse({
  data: {
    ...
  }
}));
```

# JavaScript SDKファイルのURL変更

Interactive Canvas 対応にて必要となるウェブアプリ側で利用される JavaScript SDK の URL が、変更になりました。現在は、以下のURLとなります。

* https://www.gstatic.com/assistant/interactivecanvas/api/interactive_canvas.min.js

HTML ファイル内で以下のように読み込みます。

```html
<!DOCTYPE html>
<html>
  <head>
    ...
    <script type="text/javascript" src="https://www.gstatic.com/assistant/interactivecanvas/api/interactive_canvas.min.js"></script>
    ...
  </head>
  <body>
    ...
  </body>
</html>
```

# assistantCanvasからinteractiveCanvasに変更

JavaScript SDK ファイルの URL 変更に伴い、コードの内容にも修正があります。従来は assistantCanvas という名前のオブジェクトが提供されていましたが、現在は [interactiveCanvas](https://developers.google.com/actions/interactivecanvas/reference/interactivecanvas) という名前のオブジェクトに変更されています。

```js
interactiveCanvas.ready({
  onUpdate(data) {
    ...
  }
});

interactiveCanvas.sendTextQuery(...);
```

フルフィルメント側にて state から data に変更されたことに合わせて、interactiveCanvas オブジェクトの ready() メソッドに渡すコールバック関数の引数名についても、state から data に変更すると良いでしょう。

# CSSファイルの廃止

Interactive Canvas のウェブアプリでは、アクション名を表示するために、画面のクライアント領域の上部にヘッダ区画が確保されます。このヘッダ領域の高さは、デバイスによって異なります。つまり、アクションが利用可能な領域は、クライアント領域全体の高さからヘッダ区画の高さを引いた高さとなります。そして、その領域の大きさは、動的に変化するということです。

従来は、このヘッダ区画の高さを body 要素の padding-top スタイル属性に設定するための CSS ファイルが提供されていました。しかし、この CSS ファイルは、廃止となります。

# ヘッダ区画の高さを取得するためのAPI追加

CSS ファイルの廃止に伴い、ヘッダ区画の高さを動的に取得するための JavaScript API が新規に提供されました。開発者は、interactiveCanvas オブジェクトの [getHeaderHeightPx()](https://developers.google.com/actions/interactivecanvas/reference/interactivecanvas#getheaderheightpx) メソッドを呼び出すことで、ヘッダ区画の高さを取得することが可能です。

```js
const headerHeight = await interactiveCanvas.getHeaderHeightPx();
```

注意点として、その getHeaderHeightPx() メソッドの戻り値の型は、Promise&lt;number&gt; だということです。await や then() などを使ってください。 

# 上記の変更点を適用するための方法

以上の変更点を既存のアクションに反映させるために、Actions on Google Client Library のバージョンアップが必要となります。

* NodeJS - `npm install actions-on-google@preview` を実行する。

上記コマンドの実行により、HtmlResponse などの API が利用可能となります。

残念ながら、本エントリ執筆時点では、Java 向けの Actions on Google Client Library は Interactive Canvas に対応していません。
