---
layout: post
status: publish
published: true
title: Web ComponentsのWebブラウザ別動作を調べてみた
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2749
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2749
date: '2014-07-21 22:43:18 +0900'
date_gmt: '2014-07-21 13:43:18 +0900'
categories:
- Web Components
---

Web Componentsでは、標準技術として幅広いWebブラウザで利用可能とすべく、Polyfillという名の「未対応Webブラウザ向け実装」が開発され、すでに利用可能です。このPolyfillを使うことで、Chromeだけでなく、Firefox、Opera、そしてIEでもWeb Componentsを動作させることができます。

では、実際どこまでPolyfillでWeb Componentsが動作するのでしょうか？Polyfillとして適用するのは、もちろん
[platform.js](https://github.com/Polymer/platform)です。これを使って、複数のWebブラウザで動作検証をちょっとしてみようと思います。試してみたのは、以下の3つです。

* 基本的なWeb Componentsの動作確認

* HTMLElement#createShadowRoot()の動作確認

* Shadow DOMのCSSスコープ

それぞれ見ていきましょう。

# 基本的なWeb Componentsの動作確認

まずは、Web Componentsを構成するCustom elements、HTML Templates、Shadow DOM、そしてHTML Importsが全て機能するかどうかを確認しましょう。2つのファイルを準備します。まずは、Custom elements、HTML Templates、Shadow DOMを使って、独自のx-componentタグを作成します。これは、x-component.htmlファイルとして保存しておきます。

```
<template>
This is the shadow DOM
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

次に、HTML Importsを使って上記のファイルを読み込み、x-componentタグを実際に利用するコードを準備します。

```
<!DOCTYPE html>
<html>
<head>
  <script src="bower_components/platform/platform.js"></script>
<link rel="import" href="x-component.html">
</head>
<body>
  <x-component></x-component>
</body>
</html>
```

このファイルを各Webブラウザに読み込んで、正しく動作するかどうかを確認してみます。

Chrome (Win: 36.0.1985.125)

![スクリーンショット 2014-07-21 15.36.59](http://www.eisbahn.jp/yoichiro/images/2014/07/6c969dc8bfd862a48bf67fe67db81b6b.png)

ちゃんと動作しているようですね。

![スクリーンショット 2014-07-21 15.37.42](http://www.eisbahn.jp/yoichiro/images/2014/07/de1e87c5a72d8325710ff4fc99ee0fc7.png)

動作後の要素を確認すると、ちゃんとShadow DOMができているのがわかります。

Opera (WIn: 22.0.1471.70)

![スクリーンショット 2014-07-21 15.42.27](http://www.eisbahn.jp/yoichiro/images/2014/07/8bcdd2e63768191f885b4e80e27b5329.png)

ちゃんと動作しています。

![スクリーンショット 2014-07-21 15.43.06](http://www.eisbahn.jp/yoichiro/images/2014/07/a5fdcc1b2f9f4c2c63027a100fe7b523.png)

OperaもShadow DOMを確認することができました。Polyfillに頼らず動作しているようですね。

Firefox (Win: 30.0)

![スクリーンショット 2014-07-21 15.46.41](http://www.eisbahn.jp/yoichiro/images/2014/07/1f86040719c5185b9c35c9f589eec220.png)

こちらもちゃんと表示はされました。

![スクリーンショット 2014-07-21 15.47.42](http://www.eisbahn.jp/yoichiro/images/2014/07/b9c4c516f7c9937b0c3a7178ac4c4020.png)

しかし、要素を確認してみると、templateの内容は反映されているものの、Shadow DOMは見当たりません。

IE (Win: 11.0.9600.17207)

![スクリーンショット 2014-07-21 15.56.01](http://www.eisbahn.jp/yoichiro/images/2014/07/1294e3c3f9e644b9428dcfdf1bb2dc76.png)

最後のIEも、ちゃんと表示はされました。

![スクリーンショット 2014-07-21 15.56.57](http://www.eisbahn.jp/yoichiro/images/2014/07/45c31ba0bf39e5e5c63dc025139caba8.png)

要素を確認してみると、Firefoxと同じく、Shadow DOMが見当たりません。どうやら、FirefoxとIEは、PolyfillによってWeb Componentsが動作しているようです。

# HTMLElement#createShadowRoot()の動作確認

上記の確認から、基本的なWeb Componentsの動作には問題がなさそうですが、Shadow DOMに関しては何か不穏さを感じます。では、Shadow DOMについてもう少し細かく動作を見ていきましょう。

ある要素に対してShadow DOMを作成した場合、その要素が元々持っていた子要素よりも、作成されたShadow DOMの方が優先されます。つまり、Webブラウザ上に表示されるのは、Shadow DOMの方です。既に子要素を持つ場合において、Shadow DOMを作った際の動作について見てみることにしましょう。

Shadow DOMの動作確認のみであれば、HTMLElement#createShadowRoot()関数の呼び出しだけやってあげれば大丈夫です。以下のようなコードで実験してみます。

```
<!DOCTYPE html>
<html>
<head>
  <script src="bower_components/platform/platform.js"></script>
  <script>
    window.addEventListener("load", function(evt) {
      var target = document.getElementById("target");
      var shadowRoot = target.createShadowRoot();
      shadowRoot.innerHTML = "
Shadow DOM";
    });
  </script>
</head>
<body>
<div id="target">
Hello!
  </div>
</body>
</html>
```

"target"というIDを持つdiv要素を準備し、予め"Hello!"というテキストを持つp要素を持たせておきます。そして、loadイベント発生後に、そのdiv要素にShadow DOMを作成し、別のp要素をinnerHTMLで差し込みます。

Chrome (Win: 36.0.1985.125)

![スクリーンショット 2014-07-21 17.47.46](http://www.eisbahn.jp/yoichiro/images/2014/07/dbda8c6d91a1592f220290e330d8c964.png)

ちゃんとShadow DOM側にinnerHTMLした内容が表示されています。

![スクリーンショット 2014-07-21 19.34.09](http://www.eisbahn.jp/yoichiro/images/2014/07/7e49ff9dd2825c788d67961323bc1a2b.png)

要素を確認すると、Shadow DOMが存在し、しかも"Hello!"テキストを持つ子要素も見えています。Shadow DOM側の表示が優先されていることがわかります。

Opera (WIn: 22.0.1471.70)

![スクリーンショット 2014-07-21 19.42.35](http://www.eisbahn.jp/yoichiro/images/2014/07/0392a84e1f795a1c0f0c0e041fa1443c.png)

こちらも、ちゃんとShadow DOM側にinnerHTMLした内容が表示されています。

![スクリーンショット 2014-07-21 19.43.14](http://www.eisbahn.jp/yoichiro/images/2014/07/e6e64e53a088a5a9e6ab37cc79a24175.png)

Chromeと同様に、OperaもShadow DOMがちゃんとあります。"Hello!"テキストよりもShadow DOMが優先されていることも全く同じです。問題なさそうです。

Firefox (Win: 30.0)

![スクリーンショット 2014-07-21 19.45.51](http://www.eisbahn.jp/yoichiro/images/2014/07/0cd63e7d012a747c69a7be7e0e7eca68.png)

Firefoxも、表示は問題なさそうです。

![スクリーンショット 2014-07-21 19.48.11](http://www.eisbahn.jp/yoichiro/images/2014/07/04c132e7e7ef99149ddfc525553d3f15.png)

要素を確認すると、Shadow DOMは存在していません。その代わり、Shadow DOMのinnerHTMLにセットした内容が、Shadow DOMを持つはずのdiv要素の子要素として追加されています。そして、元々持っていた"Hello!"テキストのp要素がなくなってます。どうやら、platform.jsのShadow DOMの実装は、元の子要素を削除して、代わりにShadow DOMとして作成した要素が子要素として追加される、という挙動になっているようです。

IE (Win: 11.0.9600.17207)

![スクリーンショット 2014-07-21 20.15.41](http://www.eisbahn.jp/yoichiro/images/2014/07/3e81e8aecebb99bab3af14d6f8ee1447.png)

IEも一見問題なさそうですが・・・、

![スクリーンショット 2014-07-21 20.16.32](http://www.eisbahn.jp/yoichiro/images/2014/07/612b98160574ec2022bf4e1d264dcb84.png)

Firefoxと同じように、Shadow DOMが作られる代わりに、子要素の入れ替えが起こっています。これらからわかることは、Shadow DOMのPolyfillでの動作は、実際のShadow DOMと同じ挙動ではなく、単なる子要素の置き換えで実現されている、ということですね。

# Shadow DOMのCSSスコープ

Shadow DOMが提供する最も重要な機能は、スコープの分離です。再利用可能な部品の条件として、その部品がどこで使われようとも原則として同一の見た目を保てなければならない、ということがあります。そのため、部品を利用するWebページとは隔離された環境で、部品の見た目が決定されなければなりません。つまり、WebページのCSSが、部品内に影響しないようにする、つまりCSSのスコープが隔離されている必要があります。

CSSの指定が干渉していないかどうか、確認をしてみましょう。せっかくなので、Web Componentsに基づく部品としてサンプルを作成してみます。まず、部品側のコードです。下記のコードをstyled-component.htmlというファイル名で保存します。

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
    var xElement = document.registerElement("styled-component", {
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

templateの中に、style要素とそれが適用されたp要素を入れておき、Shadow DOMを作ってtemplateの中身をセットするという処理を書いておきます。警告メッセージっぽく、文字色を赤、太字、そして下線を青色のドットと指定しておきます。そして、この部品を使うWebページを準備します。

```
<!DOCTYPE html>
<html>
<head>
  <script src="bower_components/platform/platform.js"></script>
<link rel="import" href="styled-component.html">
<style>
    .warning {
      color: black;
      font-style: itaric;
      background-color: yellow;
    }
  </style>
</head>
<body>
<p class="warning">This is the normal DOM
<styled-component></styled-component>
</body>
</html>
```

Webページ側のスタイル定義が部品側に影響しているかどうかを確認できるように、内容が異なる同名のスタイルを定義して、それが適用されたp要素を配置しておきます。ちゃんとスコープが分離されていれば、それぞれ異なるスタイルが適用されるはずです。

Chrome (Win: 36.0.1985.125)

![スクリーンショット 2014-07-21 22.13.02](http://www.eisbahn.jp/yoichiro/images/2014/07/fe50e4353febcaaf47c24da6b4705b3c.png)

ちゃんとスコープが分離されています。Shadow DOMのスタイル定義が適用されていますね。

![スクリーンショット 2014-07-21 22.14.47](http://www.eisbahn.jp/yoichiro/images/2014/07/4ea006db3584e0eb535fdc4d362963d7.png)

要素を確認しても、部品側のstyle要素はShadow DOMの中に配置されていることがわかります。

Opera (WIn: 22.0.1471.70)

![スクリーンショット 2014-07-21 22.16.15](http://www.eisbahn.jp/yoichiro/images/2014/07/a70f37db1c1162bc14f67b1a02d24c88.png)

Chromeと同じように、Operaもちゃんと異なるスタイル定義がそれぞれ適用されたのがわかります。

![スクリーンショット 2014-07-21 22.17.19](http://www.eisbahn.jp/yoichiro/images/2014/07/98bee7133cdbc2b5d87466bf6f5fd54e.png)

最終的な要素の構成についても、Chromeと同じですね。問題なさそうです。

Firefox (Win: 30.0)

![スクリーンショット 2014-07-21 22.19.16](http://www.eisbahn.jp/yoichiro/images/2014/07/3a00e701a8665857328ebde3e3c7cb70.png)

Firefoxについては、ChromeやOperaとは違い、同じ表示になってしまいました。なにやらスタイル定義がごちゃ混ぜになってしまっています。Shadow DOMのスコープは分離されていないようです。どうしてこうなってしまったのか、要素の構成を見てみればわかりそうです。

![スクリーンショット 2014-07-21 22.21.29](http://www.eisbahn.jp/yoichiro/images/2014/07/d810375a0167c57dcbb633c42be8bd95.png)

一つ前の動作検証で、Polyfillが適用されたWebブラウザにおいて、Shadow DOMの動作は「子要素の置き換え」だったことがわかりました。つまり、実際のShadow DOMとは違い、特に隔離された環境ができるということではないので、部品が配置されたWebページ内の様々な影響がそのまま反映されてしまいます。上記の要素構成を見ればわかる通り、head要素内で定義したwarningスタイル定義は、部品内にあるスタイル定義で上書きされています。そのため、どちらもごちゃ混ぜになった見た目になってしまったというわけです。

IE (Win: 11.0.9600.17207)

![スクリーンショット 2014-07-21 22.27.03](http://www.eisbahn.jp/yoichiro/images/2014/07/142cafd57ac8d56b812ca1f16de858fb.png)

IEについても、Polyfillでの挙動となり、Firefoxと同じような結果になっています。

![スクリーンショット 2014-07-21 22.28.28](http://www.eisbahn.jp/yoichiro/images/2014/07/75695a33e48b85d024ccb1ccaa2f9d55.png)

要素を確認してみても、Firefoxと状況は同じようです。つまり、PolyfillによるShadow DOMの実装は、スコープの隔離までは実現されていない、ということのようです。

****


実際、WebブラウザによるShadow DOMの実装と違い、PolyfillによってShadow DOMのスコープ分離を実現するのは、かなり至難の業かもしれません。スタイル定義のクラス名などが完全に重複しないように「前処理」しなければならないだろうし、その副作用として部品内でクラス名などを直接書くことが今度は困難になりかねません。そこまでしてPolyfillの実装を頑張るくらいなら、ちゃんと各WebブラウザがShadow DOMを実装してくれることを期待したいわけです。

一つ前のエントリ「
[Shadow DOMがもたらしてくれる再利用可能性](http://www.eisbahn.jp/yoichiro/2014/07/reusable_brought_by_shadow_dom.html)」では、Shadow DOMのメリットを説明してみましたが、実際にはまだ全てのWebブラウザでその恩恵を受けられるところまでは来ていない模様です。

AppleはSafari向けのWebkitからShadow DOM関連のコードセットを削除すると表明したこともありますが、Web Componentsの未来は上記で見てきたとおりShadow DOM頼みといっても過言ではなく、一日も早く各Webブラウザでの環境整備が進んで欲しいと希望します。

というわけで、ちょっとした動作確認でした。ちゃんちゃん。
