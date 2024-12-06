---
layout: post
status: publish
published: true
title: Shadow DOMがもたらしてくれる再利用可能性
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2744
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2744
date: '2014-07-20 15:25:49 +0900'
date_gmt: '2014-07-20 06:25:49 +0900'
categories:
- Web Components
---

Web Componentsは、Custom elements、HTML Templates、Shadow DOM、そしてHTML Importsと言う4つの基礎技術から成り立っています。これらを組み合わせると、独自のタグセットを再利用可能な形で部品化できる、という触れ込みになってます。

その再利用可能性はどこから来るのでしょうか？その可能性を大きく引き上げてくれているのが、Shadow DOMによる「スコープの隔離」です。

Web Componentsで再利用される部品のほとんどは、見た目を伴うもの、つまりUI部品になるでしょう。UIにとって大事なものは、見た目です。つまり、デザインが部品を利用する側から干渉されてしまっては、再利用可能と言うことができません。CSSでのスタイル指定について、部品の中と外でそれぞれ独立している必要があります。

これを可能にしてくれるものが、Shadow DOMです。HTMLElement#createShadowRoot()関数により作られたShadow DOMの中は、その要素外で指定されているCSSは基本的に適用されず、Shadow DOM内で完結しています。つまり、Shadow DOMは外の世界から隔離されているわけです。

具体的に検証してみましょう。まず、独自の部品を作ってみます。このファイルは、x-component.htmlという名前で保存しておきます。

```
<template>
<style>
    .warning {
      color: red;
      font-weight: bold;
      border-bottom: 1px dotted blue;
    }
  </style>
<p class="warning">This is the shadow DOM
</template>
<script>
  (function() {
    var thisDoc = document._currentScript.ownerDocument;
    var xElement = document.registerElement("x-component", {
      prototype: Object.create(HTMLElement.prototype, {
        createdCallback: {
          value: function() {
            var root = this.createShadowRoot();
            var template = thisDoc.querySelector("template");
            var content = thisDoc.importNode(template.content, true);
            root.appendChild(content);
          }
        }
      })
    });
  })();
</script>
```

template要素の中にstyle要素を置いて、そこでwarningというスタイルを定義しています。つまり、この部品で作られるShadow DOMには、そのstyle要素と、warningスタイルが適用されたpタグによるメッセージ文字列がセットされる、ということを期待しています。

この部品を使ったコード例として、以下を作ってみます。IEでも動作するように、
[platform.js](https://github.com/Polymer/platform)を読み込んでいます。

```
<!DOCTYPE html>
<html>
<head>
  <script src="bower_components/platform/platform.js"></script>
<link rel="import" href="x-component.html">
<style>
    .warning {
      color: gold;
      font-style: itaric;
      border: 1px solid red;
    }
  </style>
</head>
<body>
<p class="warning">This is the normal DOM
  <x-component></x-component>
</body>
</html>
```

Webページ上で指定されている要素が部品に干渉しているかどうか確認できるように、同名のスタイル定義をしてみました。結果は以下となりました。

![スクリーンショット 2014-07-20 15.23.04](http://www.eisbahn.jp/yoichiro/images/2014/07/88875e1c71b9445d06be404309a3698f.png)

Webページ上のメッセージは金色に、部品内のメッセージは赤色に表示されていることがわかります。Shadow DOMは、このように隔離されているわけです。つまり、このx-component部品は、どこのWebページに置いても、メッセージは赤色で表示されるということになります。もちろん色だけでなく、部品内で指定された全てのスタイル定義がそのまま保持されていることがわかると思います。

現在は、こういう部品はiframeによって実現するのが主流ですが、iframeの場合はその隔離度が本当に強烈なので、単にWebページに配置するだけであり、「Webページ内でその部品を使う」という感覚ではない（iframe内で押されたボタンに応じて、親のWebページ側で何か行う、という実装方法を想像していただければわかると思います）ですね。

Shadow DOMはとても魅力的な機能です。Web Componentsを支えている最も重要なものだと言っても良いかも知れませんね。
