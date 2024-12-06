---
layout: post
status: publish
published: true
title: Web Componentsを使った部品のイベント伝達の続き
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2851
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2851
date: '2014-07-26 22:53:54 +0900'
date_gmt: '2014-07-26 13:53:54 +0900'
categories:
- Web Components
---

数日前に「
[Web Componentsでイベントを伝達する部品の作り方](http://www.eisbahn.jp/yoichiro/2014/07/dispatch_event_from_web_components.html)」っていうエントリを投稿しましたが、さらに検証をしてみたのと、ちょっと勘違いしてた点があったので、再度イベント伝達について取り上げようと思います。

# Shadow DOM内で発生したイベントの伝達

そもそもここから勘違いしてたのですが、最初僕は「Shadow DOM内で発生したイベントは、ホスト側には伝達されない」と思ってました。Shadow DOM内で起きたことは部品内で捕捉して、その後ホスト側にイベント伝達しなおすコードを書かなくちゃいけない、って考えてました。そのため、Polymerが提供してる部品は、イベントの伝達をどうしてるんだろう、きっとコードの中でfire()してるに違いない、って思ってたんです。

ところが、確認してみると、イベントを何か扱ってるコードは、全く見当たりません。例えば、core-icon-button部品であれば、Clickイベントを扱っててもおかしくないんですが、以下のコードを見ると何もしてません。

```
...
<polymer-element name="core-icon-button" attributes="src icon active">
  <template>
<link rel="stylesheet" href="core-icon-button.css">
    <core-icon src="{{src}}" icon="{{icon}}"></core-icon><content></content>
  </template>
  <script>
    Polymer('core-icon-button', {
      src: '',
      active: false,
      icon: '',
      activeChanged: function() {
        this.classList.toggle('selected', this.active);
      }
    });
  </script>
</polymer-element>
```

From: 
[https://github.com/Polymer/core-icon-button/blob/master/core-icon-button.html](https://github.com/Polymer/core-icon-button/blob/master/core-icon-button.html)

ここで思いました。「もしかして、普通にClickイベントホスト側にも飛んでるのでは？」と。早速検証してみました。

部品側のコードです。

```
<template>
  <button>Click me!</button>
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

そして、上記の部品を使う側のコードです。x-component要素に対して、addEventListener()関数でクリックイベントに対するハンドラを登録しています。

```
<!DOCTYPE html>
<html>
<head>
  <script src="bower_components/platform/platform.js"></script>
<link rel="import" href="x-component.html">
  <script>
    window.addEventListener("load", function(evt) {
      var xc = document.querySelector("x-component");
      xc.addEventListener("click", function(event) {
        alert("Clicked!");
      });
    });
  </script>
</head>
<body>
  <x-component></x-component>
</body>
</html>
```

Webブラウザで表示後、"Click me!"というボタンを押すと、どうなるでしょうか？

![スクリーンショット 2014-07-25 23.41.00](http://www.eisbahn.jp/yoichiro/images/2014/07/caf5f1968811d6ea3b6c47d4a4ad0401.png)

イベントハンドラが呼び出されて、alert()関数によるダイアログが表示されました。どうやら、Shadow DOM内で発生したクリックイベントに関しては、ホスト側にも伝達された模様です。

数日前に書いたエントリ「
[Web Componentsでイベントを伝達する部品の作り方](http://www.eisbahn.jp/yoichiro/2014/07/dispatch_event_from_web_components.html)」では、部品内にあるdiv要素のクリックイベントを部品内で捕捉し、CustomEventオブジェクトとdispatchEvent()関数を使って、部品が自分自身でイベントを発行されました。しかし、上記の例を見てわかる通り、クリックイベントにおいては、自分で処理しなくても勝手にホスト側に伝達されるようですね。そして、イベントの発行元についても、自動的にホスト要素（registerElement()関数で登録したカスタムタグ）からのイベントと設定されていました。

![スクリーンショット 2014-07-25 23.46.44](http://www.eisbahn.jp/yoichiro/images/2014/07/7ef18959f8ffcbd6064e0b1d8ebe0bfb.png)

このイベント発行元の再設定処理は「リターゲッティング」と呼ばれています。部品の利用側からは、発生したイベントのソースはあくまで部品の要素からであり、部品内の内部構造はイベントの情報として渡さないようになっているということです。

このリターゲッティング機能は、Polyfillでも同様に機能するのでしょうか？上記のコードをIE11で動作させてみて、発生したイベントの内容を覗いてみます。ちなみに、Chromeでは、発生したイベントのsrcElementプロパティを見てみると、tagNameは"X-COMPONENT"でした。

![スクリーンショット 2014-07-26 20.06.54](http://www.eisbahn.jp/yoichiro/images/2014/07/f486f32fa5db9ea6153a358a21a306ab.png)

IE11はというと・・・、

![スクリーンショット 2014-07-26 20.13.31](http://www.eisbahn.jp/yoichiro/images/2014/07/3761eb6d067309839153dadb871f8ea0.png)

tagNameは"BUTTON"になってました。Polyfillでは残念ながらリターゲッティングにはなってないようですね。

# ホスト側に伝達されるイベント、されないイベント

先ほどはクリックイベントを検証してみましたが、何でもかんでもホスト側に伝達されるわけではなさそうです。例えば、部品内にチェックボックスを配置して、その変更イベントがホスト側に伝達されるかどうか検証してみましょう。

まずは部品側のコードです。テンプレート内のinput要素に対して、changeイベントに対応するイベントハンドラを登録しました。

```
<template>
  <input type="checkbox" />
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
            var checkbox = root.querySelector("input[type=checkbox]");
            checkbox.addEventListener("change", function(event) {
              console.log("Component: Changed!");
            });
          }
        }
      })
    });
  })();
</script>
```

次にホスト側のコードです。x-component要素に対して、changeイベントのイベントハンドラを登録しています。

```
<!DOCTYPE html>
<html>
<head>
  <script src="bower_components/platform/platform.js"></script>
<link rel="import" href="x-component.html">
  <script>
    window.addEventListener("load", function(evt) {
      var xc = document.querySelector("x-component");
      xc.addEventListener("change", function(event) {
        console.log("Host: Changed!");
      });
    });
  </script>
</head>
<body>
  <x-component></x-component>
</body>
</html>
```

動作させてみましょう。まずはChromeからです。

![スクリーンショット 2014-07-26 9.27.48](http://www.eisbahn.jp/yoichiro/images/2014/07/a1fc635658f8dcb0906dcd1254adacb7.png)

チェックを入れてみました。コンソールを見てみましょう。

![スクリーンショット 2014-07-26 9.28.24](http://www.eisbahn.jp/yoichiro/images/2014/07/3a2f8a8ec603f3714cb6d267dcd520a1.png)

部品の中ではchangeイベントが捕捉されたことが確認できましたが、x-component要素に仕掛けたchangeイベントハンドラは呼び出されませんでした。つまり、changeイベントに関しては、部品の外側には伝達されないようです。

では、Polyfillが適用されるIE11でも動作確認をしてみましょう。

![スクリーンショット 2014-07-26 18.46.25](http://www.eisbahn.jp/yoichiro/images/2014/07/c959949020bcb145a732dd7b8442bdb6.png)

やはり、部品内のチェックボックスで発生したchangeイベントは、ホスト側には伝達されませんでした。IE11の場合、つまりPolyfillが適用されている場合は、Shadow DOMではなく、部品内に定義されたtemplateタグの内容がホスト要素の子要素として追加されるだけです。よって、普通のWebページと同じように、チェックボックスで発生したchangeイベントは親の要素に伝達されません。結果として、Chromeと同じ挙動となっています。

確認のためにShadow DOMの仕様を見てみたところ、ホスト側に伝達されないイベントの種類が明確に定義されていました。

[Shadow DOM (W3C Working Draft 17 June 2014)
5.1 Events that are Always Stopped](http://www.w3.org/TR/shadow-dom/#events-that-are-always-stopped)

>* abort

* error

* select

* change

* load

* reset

* resize

* scroll

* selectstart


先ほど試したchangeイベントもリストに入っています。これらはホスト側にイベント伝達されませんが、これら以外のイベントはホスト側にリターゲッティングされた状態（Polyfill適用時を除く）で伝達されるということです。

もし上記のリストにある伝達されないイベントに関してホスト側に伝達したい場合は、「
[Web Componentsでイベントを伝達する部品の作り方](http://www.eisbahn.jp/yoichiro/2014/07/dispatch_event_from_web_components.html)」で取り上げた方法で、部品内で独自にイベントオブジェクトを生成してdispatchEvent()関数を利用して伝達すれば良いです。その場合、独自のイベント種別にしても良いし、上記の基本的なイベント種別を適用しても良いでしょう。部品の設計次第、と言えます。

注意点としては、複数の要素から構成されるちょっと複雑なUIを持つ部品においては、個々の要素で発生するClickイベントはホスト側にそのまま伝達されてしまう、という点があります。開発した部品において、特定の要素へのクリック操作時にのみホスト側にクリックイベントを伝達したい、という場合には、

* 対象の要素以外の全ての要素について、部品内でclickイベントを捕捉し、ホスト側に伝達しないようにしてしまう。

* clickイベントではなく、別の独自イベント種別を定義して、ホスト側ではその独自イベントに対するイベントハンドリングをしてもらう。

といった工夫が必要になると思います。
