---
layout: post
status: publish
published: true
title: Chrome拡張機能をWeb Intents対応してみた
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1477
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1477
date: '2012-05-30 10:22:10 +0900'
date_gmt: '2012-05-30 01:22:10 +0900'
categories:
- Chrome extension
---

goo.glサービスを手軽に使うための「goo.gl URL Shortener extension」ですが、Chrome 19からWeb Intentsが普通に使えるようになったので、さっそく組み込んでみました。

![](http://www.eisbahn.jp/yoichiro/images/2012/05/logo.png)

[goo.gl URL Shortener extension](https://chrome.google.com/webstore/detail/pjnggipjiafeklgjdclhhkeefdebipmm?hl=ja) - Chrome Web Store

試す場合は、goo.gl URL Shortener extensionをインストール後に、以下のページに行って、[Shorten link]ボタンをクリックします。

[Shorten Intent Example](http://examples.webintents.org/intents/shorten/shorten.html)

すると、対象インテントを受け取ることが可能なChromeアプリ/拡張機能の一覧が表示されるので、そこで「goo.gl URL Shortener extension」を選択します。

![](http://www.eisbahn.jp/yoichiro/images/2012/05/c1.png)

すると、goo.gl URL Shortener extensionにインテントが送信され、短縮されたURLが表示されます。

![](http://www.eisbahn.jp/yoichiro/images/2012/05/c2.png)

[Return]ボタンを押すと、ポップアップが閉じて、短縮URLが結果として元のページに送信されます。テキストボックスにそれがセットされたことがわかるでしょう。

Chrome拡張機能をWebIntents対応するのは、実はとても簡単でした。まず、manifest.jsonにサポートするインテントの定義を書きます。以下のように。

```
"intents": {
  "http://webintents.org/shorten": [ {
      "type": ["text/uri-list"],
      "href": "./webintent.html",
      "title": "goo.gl URL Shortener extension",
      "disposition": "inline"
  } ]
}
```

これでインテントを受け取る準備は完了です。上記の場合、インテントを受け取る相手がgoo.gl URL Shortener extensionに決まった時に、webintent.htmlがポップアップに表示されます。このHTML内で実行されるJavaScript内でインテントを得るためには、以下のコードを実行します。

```
var intent = window.intent || window.webkitIntent;
var longUrl = intent.data;
```

[Web Intentsの仕様](http://dvcs.w3.org/hg/web-intents/raw-file/tip/spec/Overview.html)では window.intent がインテントにアクセスするための手順となるのですが、Chrome 19ではその代わりに window.webkitIntent を使ってアクセスする必要があります。送信されたインテントは、そのdataプロパティに保持されています。例えば、

```
var url = "http://www.eisbahn.jp/yoichiro/";
var intent = new Intent(
    "http://webintents.org/shorten",
    "text/uri-list",
    url);
window.navigator.startActivity(intent, intentResponse);
```

でインテントが投げられたとすると、intent.dataプロパティ値は"http://www.eisbahn.jp/yoichiro/"文字列となります。

あとは短縮されたURLを投げ返すことになりますが、そのためにはpostResult()関数を使います。

```
var shortUrl = "..."; // 短縮URL
var intent = window.intent || window.webkitIntent;
intent.postResult(shortUrl);
window.close();
```

注意点としては、inlineで受けた場合にはポップアップウィンドウが開かれてその中で処理されることになるのですが、そのポップアップウィンドウを閉じるためには、以下の条件が必要でした。

* ユーザのアクション（イベントの発火）に応じてwindow.close()しないといけない（これは通常の制限と一緒）。

* window.close()する前には、postResult()もしくはpostFailure()のどちらかが呼び出されていないといけない。

また、現状のChrome 19では、少なくともchrome.i18n.getMessage()しても空文字しか返ってこなくて、inlineで受けた際のポップアップ内の表示を国際化することができませんでした。そのうちできるようにはなると思いますが。。。

返送された情報を得るためには、startActivity()関数の呼び出し時に指定した第2引数のコールバック関数に渡されてきます。上記の例だと、以下のようになるでしょう。

```
var intentResponse = function(shortUrl) {
  // 短縮URLの表示など
};
```

かなり簡単にインテントの送受信ができます。Web Intentsはこれからかなり適用事例が増えていくと思うので、ぜひ今のうちに身につけておくといいでしょう。Let's try!
