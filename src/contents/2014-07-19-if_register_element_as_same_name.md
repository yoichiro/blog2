---
layout: post
status: publish
published: true
title: "もし同一の名前でdocument.registerElement()しようとしたら？"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2737
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2737
date: '2014-07-19 21:37:42 +0900'
date_gmt: '2014-07-19 12:37:42 +0900'
categories:
- Web Components
---

Custom elements, HTML Templates, Shadow DOM, そしてHTML Importsの組み合わせで成り立っているWeb Componentsは、開発者が独自に「部品」を作ることを可能にします。その部品とは、独自に定義された「HTMLタグ」に他なりません。決められたタグセットを使ってWebアプリケーションを作っていく時代は、そろそろ終わりに近づいています。自分で部品を作り、さらに他の開発者によって公開された再利用可能な部品を取り込んでアプリケーションを構成していく、これが当たり前の世界になりそうです。

例えば、以下のようにして部品を作成することができます。

```
<template id="template1">
This is the shadow DOM
</template>
<script>
  (function(thisDoc) {
    var xElement = document.registerElement("x-component", {
      prototype: Object.create(HTMLElement.prototype, {
        createdCallback: {
          value: function() {
            var root = this.createShadowRoot();
            var template = thisDoc.querySelector("#template1");
            var content = thisDoc.importNode(template.content, true);
            root.appendChild(content);
          }
        }
      })
    });
  })(document.currentScript.ownerDocument);
</script>
```

上記をx-component.htmlというファイル名で保存しておき、部品を使う側で以下のようにして利用します。

```
<!DOCTYPE html>
<html>
  <head>
<link rel="import" href="x-component.html">
  </head>
  <body>
    <x-component></x-component>
  </body>
</html>
```

これを実行すると、以下のようになります。

![スクリーンショット 2014-07-19 21.34.40](http://www.eisbahn.jp/yoichiro/images/2014/07/da6c74b304fbd26a0445c0f3ff0a8f09.png)

![スクリーンショット 2014-07-19 20.05.57](http://www.eisbahn.jp/yoichiro/images/2014/07/908fe9aecf13e8242cb33a6f72d93701.png)

x-component要素にShadow DOMが作られ、その中にtemplate要素の内容がコピーされていることがわかるでしょう。

さて、ここでちょっとした疑問が沸きます。「もし同じ名前のタグ名がregisterElement()されたらどうなる？」という疑問です。先勝ち？後勝ち？どちらも評価されずにエラー？どうなるのでしょうか？やってみましょう。

上記のx-component.htmlファイルをコピーしてx-component2.htmlファイルを作ります。x-component2.htmlファイルは、templateのid属性を変更して置きますが、registerElement()する名前は"x-component"のままにしておきます。更に、Shadow DOMに入るメッセージを"This"から"That"に変えて、どちらが適用されたかわかるようにしておきます。

```
<template id="template2">
That was the shadow DOM
</template>
<script>
  (function(thisDoc) {
    var xElement = document.registerElement("x-component", {
      prototype: Object.create(HTMLElement.prototype, {
        createdCallback: {
          value: function() {
            var root = this.createShadowRoot();
            var template = thisDoc.querySelector("#template2");
            var content = thisDoc.importNode(template.content, true);
            root.appendChild(content);
          }
        }
      })
    });
  })(document.currentScript.ownerDocument);
</script>
```

そして、x-component.htmlとx-component2.htmlの両方をインポートします。

```
<!DOCTYPE html>
<html>
  <head>
<link rel="import" href="x-component.html">
<link rel="import" href="x-component2.html">
  </head>
  <body>
    <x-component></x-component>
  </body>
</html>
```

ChromeはVersion 36.0.1985.125, mac版です。表示をしてみたところ、DevToolsのConsoleに以下のようなエラーが表示されました。

![スクリーンショット 2014-07-19 20.13.42](http://www.eisbahn.jp/yoichiro/images/2014/07/4fbcd267a471027a0baa401068cf9049.png)

x-component2.html、つまり後からregisterElement()しようとした箇所でエラーが発生しました。やはり同名の要素を登録しようとすることはエラー扱いになっていました。Webブラウザには、"This is the shadow DOM"と表示されていましたので、最初に登録された方は有効のようです。つまり、先勝ちです。

他人が開発したいろいろな部品を使うようになると、このような「名前の衝突」が度々起きるかもしれませんね。

以上、ちょっとした実験でした。
