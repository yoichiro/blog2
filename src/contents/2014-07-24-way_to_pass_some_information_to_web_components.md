---
layout: post
status: publish
published: true
title: Web Components部品へ何か情報を渡すための方法
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2822
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2822
date: '2014-07-24 00:00:54 +0900'
date_gmt: '2014-07-23 15:00:54 +0900'
categories:
- Web Components
---

Web Componentsを使った部品を考えたときに、単にタグをWebページに配置する利用方法も多いと思いますが、同時に何かパラメータやコンテンツを指定することで部品の挙動が変化する、といったことができると嬉しい部品もいっぱいあると思います。Shadow DOMが適用されることになるため、Webページと部品は基本的に「隔離」されます。そのため、何か情報を渡すための仕組みが必要になってきます。

ここでは、以下の2つの方法について紹介してみたいと思います。

* 属性を使って情報を渡す。

* 本文を使って情報を渡す。

# 属性を使って情報を渡す

最初に紹介するのは、部品が提供するタグの属性値として情報を渡す方法です。まずは、部品を使ったWebページ側のコードを見てみましょう。

```
<!DOCTYPE html>
<html>
<head>
  <script src="bower_components/platform/platform.js"></script>
<link rel="import" href="x-component.html">
</head>
<body>
  <x-component message="Passed via attribute"></x-component>
</body>
</html>
```

x-componentというタグに、messageという名前の属性を指定しています。この属性値として指定された文字列を表示する、という部品を考えてみます。実現するための方法として、x-component要素の属性値を部品内で取得し、それをShadow DOM内に反映してあげれば良さそうです。

では、属性値を扱う部品のコードを見てみましょう。

```
<template>
<div>Message: <span></span></div>
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
            var span = root.querySelector("span");
            var message = this.getAttribute("message");
            span.textContent = message;
          }
        }
      })
    });
  })();
</script>
```

テンプレート内に、メッセージを表示するためのspan要素を準備しておきます。そのspan要素は、生成したShadow DOMから相対的に取得しておきます。

この部品のJavaScriptコードが実行される時のthisは、x-component要素になります。つまり、this.getAttribute()関数を使うことで、属性値にアクセスすることが可能なわけです。上記のコードでは、messageという名前の属性の値を取得して、span要素のテキストノードとしてセットしています。

Chromeにて実行した結果は、以下のようになります。ちゃんと属性で渡した文字列がセットされていることがわかるでしょう。

![スクリーンショット 2014-07-23 21.04.20](http://www.eisbahn.jp/yoichiro/images/2014/07/0e5b3b39073d013f7e6afa8200b50121.png)

IE11でもちゃんと動作しています。問題なさそうですね。

![スクリーンショット 2014-07-23 21.05.48](http://www.eisbahn.jp/yoichiro/images/2014/07/9056f6589ec92a513341a9bad0c3431e.png)

# 本文を使って情報を渡す

次に、属性値としてではなく、x-componentタグの子要素として指定された内容を部品内で扱うための方法を紹介しましょう。まずは、Webページ側のコードをご覧ください。

```
<!DOCTYPE html>
<html>
<head>
  <script src="bower_components/platform/platform.js"></script>
<link rel="import" href="x-component.html">
</head>
<body>
  <x-component>Passed via attribute</x-component>
</body>
</html>
```

先ほどのmessage属性はなく、その代わりにx-componentタグの本文として文字列を記述しています。先ほどと同じように、「"Message: " + 指定された文字列」としたいので、部品内で何らかの処理を行う必要があります。

「x-component要素のオブジェクトを取得することができるんだから、さっきと同じように子要素からテキストノードを取得するコードを書けばいいのでは？」と思ったと思います。確かにそれでも実現できるでしょう。しかし、実はWeb Componentsを使うと、もっと簡単に実現することができるのです。

それは、「content要素を使う」という方法です。さっそくcontent要素を使ったコード例を見てみましょう。

```
<template>
<div>Message: <content></content></div>
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

先ほどは属性値を取得するためのJavaScriptコードを書く必要がありました。しかし、上記のコードには、thisから子要素にアクセスしているようなコードが一切ありません。その代わりに、先ほどはテンプレートの中でspan要素を使いましたが、ここではcontent要素を記述しています。

たったこれだけですが、実際に動かしてみましょう。Chromeで確認した結果は、以下になります。

![スクリーンショット 2014-07-23 23.32.13](http://www.eisbahn.jp/yoichiro/images/2014/07/ea668319127ab26b36544182f75b8dd3.png)

ちゃんと表示されています。実際の要素の構成がどうなっているか、確認してみましょう。

![スクリーンショット 2014-07-23 23.33.22](http://www.eisbahn.jp/yoichiro/images/2014/07/a02bbf00de0a51e214f806a0825db70e.png)

Shadow DOMの中にcontent要素が存在していることがわかりますが、そのcontent要素は特に子要素を何も持っていません。しかし、Webブラウザ上では、ちゃんと表示されています。x-component要素の子要素であるテキストノードが、contentn要素の内容として適用され、Webブラウザ上で表示されている、という動作がここからわかります。

Polyfillが使われるIE11でも動作を見ておきましょう。

![スクリーンショット 2014-07-23 23.36.04](http://www.eisbahn.jp/yoichiro/images/2014/07/acdf3df883a54859157df3440f6276b9.png)

こちらも動作上は問題なさそうです。適用されている要素を細かく見てみましょう。

![スクリーンショット 2014-07-23 23.37.10](http://www.eisbahn.jp/yoichiro/images/2014/07/55e47fb9b5dc9d2156a17aa99525566b.png)

Chromeとは違い、Shadow DOMは存在していません。その代わり、x-component要素の子要素であるdiv要素に、直接テキストノードが追加されています。

# content要素に適用される内容の動的な変更とその効果

最後に、もう一歩踏み込んだ実験をしてみたいと思います。Chromeでの動作の際、Shadow DOM内にあるcontent要素には、何の子要素も追加されておらず、しかし部品側のx-component要素にあるテキストノードが表示上適用されている、ということがわかったと思います。では、一旦Webブラウザでレンダリングされた後に、動的にx-component要素のテキストノードの内容を変更したら、一体どういう動作になるでしょうか？

x-componentの部品は先ほどと全く同じものを使い、Webページ側に動的変更するためのコードを追記してみます。

```
<!DOCTYPE html>
<html>
<head>
  <script src="bower_components/platform/platform.js"></script>
<link rel="import" href="x-component7.html">
  <script>
    window.addEventListener("load", function(e) {
      document.querySelector("#changeText").addEventListener("click", function(e) {
        document.querySelector("x-component").textContent = "Changed!";
      });
    });
  </script>
</head>
<body>
  <x-component>Passed via body</x-component>
  <button id="changeText">Change text</button>
</body>
</html>
```

新規にボタンを配置し、そのボタンがクリックされた後にx-component要素のテキストノードの内容を変更してみます。

最初に、ネイティブでWeb ComponentsをサポートしているChromeから実験してみましょう。

![スクリーンショット 2014-07-23 23.51.29](http://www.eisbahn.jp/yoichiro/images/2014/07/72e2572a5081871de1149b0f5fab6014.png)

ボタンを押してみます。すると・・・

![スクリーンショット 2014-07-23 23.52.06](http://www.eisbahn.jp/yoichiro/images/2014/07/2f56b19034b9340c392ce845dd91474b.png)

見事にcontent要素に対して新しいテキストノードの内容が反映されました！

![スクリーンショット 2014-07-23 23.53.02](http://www.eisbahn.jp/yoichiro/images/2014/07/c333d4b45798f242da272726657c90e3.png)

実際の要素を確認してみると、content要素は相変わらずですが、x-component要素のテキストノードの内容は変化していて、変化後の内容がWebブラウザにちゃんと反映されていることがわかります。つまり、x-component要素の内容とcontent要素の表示内容が自動的に連動することがわかりました。

この動作、結構派手ですよね？ChromeやOperaではこの連動がされていますが、Polyfillでそこまでサポートしているのでしょうか？IE11で検証してみましょう。

![スクリーンショット 2014-07-23 23.55.40](http://www.eisbahn.jp/yoichiro/images/2014/07/afd81c14acf472b77735ea89a1afdd42.png)

ボタンを押すと・・・

![スクリーンショット 2014-07-23 23.56.11](http://www.eisbahn.jp/yoichiro/images/2014/07/6e6945fcb3f2d635d020e0ea8b5a0adf.png)

なんと！予想に反して、Chromeと同じ挙動をしました。自動的に連動することまでPolyfillは実装されているようです。

実際の要素を確認してみましょう。

![スクリーンショット 2014-07-23 23.57.29](http://www.eisbahn.jp/yoichiro/images/2014/07/c136920188e32bb19f84257ceb9070c8.png)

先ほどと同じように、変更されたテキストノードの内容が、x-component要素内のdiv要素に直接反映されていました。Polyfillが暗黙的にテキストノードの移動(?)をやってくれているようです。

content要素の挙動については、Polyfillの頑張りのおかげで、全てのWebブラウザにて動作を期待しても良さそうです。content要素では、select属性の指定による部分的な反映もできたりするのですが、これはまた別の機会に検証してみたいと思います。
