---
layout: post
status: publish
published: true
title: WebKitBlobBuilderがChromeから削除されたっぽい
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2113
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2113
date: '2013-01-18 20:12:46 +0900'
date_gmt: '2013-01-18 11:12:46 +0900'
categories:
- Chrome extension
---

Chrome拡張機能として提供しているImage Collector extensionの中で、今までWebKitBlobBuilderを使っていました。使ってた箇所は、画像を一気にダウンロードするコマンドが列挙されたスクリプトファイルを作る箇所。以下のようにしてました。

```
var script = "...";
var blobBuilder = new WebKitBlobBuilder();
blobBuilder.append(script);
var link = document.createElement("a");
link.href = window.webkitURL.createObjectURL(blobBuilder.getBlob());
```

そして今日、上記のコードが使えないことに気がつきました。追ってみると、なんと「WebKitBlobBuilderがない！」ではありませんか。

これ、昨年の夏前にはすでにアナウンスされていたことだったりします。

[DON'T BUILD BLOBS, CONSTRUCT THEM](http://updates.html5rocks.com/2012/06/Don-t-Build-Blobs-Construct-Them) - HTML5Rocks

Chrome24からは、Builderに頼ることなく、Blobを直接newすれば良くなります。以下のように。

```
var script = "...";
var blob = new Blob(
    [ script ],
    { type: "text/plain" }
);
var link = document.createElement("a");
link.href = window.webkitURL.createObjectURL(blob);
```

もし未だにWebKitBlobBuilderを使ってる箇所があれば、きっと動かなくなってます。すぐに修正しましょう。
