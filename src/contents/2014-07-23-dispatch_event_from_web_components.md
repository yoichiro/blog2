---
layout: post
status: publish
published: true
title: Web Componentsでイベントを伝達する部品の作り方
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2798
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2798
date: '2014-07-23 00:16:05 +0900'
date_gmt: '2014-07-22 15:16:05 +0900'
categories:
- Web Components
---

Web Componentsで作られた部品は、もちろんそれを置いただけで効果があるものも多いでしょうが、その部品と他の部品の間、あるいはその部品を置いたWebページとの間でインタラクションをしたくなると思います。簡単な例であれば、画像付きボタン部品をWeb Componentsにて独自に実装した場合、そのボタンが押されたことをその利用側に伝達できなければなりません。伝達するための方法は、もちろんイベントです。

部品内でイベントを発生させ、それを部品の利用側に伝えるためのコーディング方法について、実際にサンプルを作ってみました。ここで紹介してみたいと思います。

# カスタムイベントとその捕捉の基本

JavaScriptでは既存の多くのイベント種別が定義されています。その代表例は、clickやloadでしょう。このようなイベントを開発者が独自に定義し、実際にそのイベントを配送することができます。

まず、独自のイベント、つまりカスタムイベントを生成するには、CustomEventオブジェクトを作れば良いです。

```
var event = new CustomEvent("clicked", {
  detail: {
    // Something...
  }
});
```

第1引数にはイベントの種別名を、第2引数にはイベントオブジェクトに入れておきたいものをdetailプロパティ内にセットしておきます。

CustomEventオブジェクトを生成しただけでは何も起きません。これをdispatchEvent()関数に渡すことで、実際にイベントが配送されます。dispatchEvent()関数を呼び出すレシーバは、もちろんこのイベントを捕捉する対象となる要素です。

```
var targetElement = ...;
targetElement.dispatchEvent(event);
```

これで配送側は完了です。このイベントを捕捉したい側では、addEventListener()関数を使ってイベントハンドラを登録します。これはお馴染みですね。

```
var targetElement = ...;
targetElement.addEventListener("clicked", function(evt) {
  var detail = evt.detail;
  // Do something...
});
```

# Web Componentsへのイベント配送の適用

では、実際にイベントを配送する機能を持つWeb Componentsの部品を作ってみましょう。まずは以下のコードをご覧ください。このコードを、x-component.htmlというファイル名で保存したと仮定します。

```
<template>
<div style="cursor: pointer;">
  </div>
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
            var div = root.querySelector("div");
            var message = this.getAttribute("message");
            div.innerText = message;
            div.addEventListener("click", function(evt) {
              var event = new CustomEvent("clicked", {
                detail: {
                  message: message
                }
              });
              this.dispatchEvent(event);
            });
          }
        }
      })
    });
  })();
</script>
```

この部品のscript要素内で行っていることは、大きく3つに分かれています。

* 新規タグ名の登録、Shadow DOMの生成、テンプレートの読み込み、Shadow DOMへのテンプレートの内容の適用。/li>* この部品のタグに記述されたmessage属性値のShadow DOMへの反映。

* Shadow DOM内のdiv要素のclickイベントハンドラの登録と、カスタムイベントの生成、それの配送。

一つのWebページ内に複数のx-component部品が配置された時の動作確認を考えて、message属性値が発生するイベントの内容に含まれるようにしてみました。先ほど説明したCustomEventオブジェクトやdispatchEvent()関数が使われていることがわかると思います。

一つ注意点として、Polyfillに頼ることになるWebブラウザの場合のことを考慮しておいた方が良いでしょう。つまり、div要素を取得する際は、作成したShadow DOMからの相対位置を指定して取得するようにしておきます。そうでないと、Webページ側の要素や他の部品の要素を取得してしまう可能性が出てきてしまうからです。

「クリックイベントなんだから、"clicked"ではなく"click"という名前でいいのでは？」と思った方も多いと思います。僕も最初は"click"で試したのですが、見事にChromeがクラッシュします（正確にはそのWebページを担当するプロセスがクラッシュする）。既存のイベント種別名とは異なる名前を指定した方が良さそうです。

# 上記部品の利用とカスタムイベントの捕捉

最後に、上記の部品を利用する側のWebページのコードを紹介しましょう。以下のようになります。

```
<!DOCTYPE html>
<html>
<head>
  <script src="bower_components/platform/platform.js"></script>
<link rel="import" href="x-component.html">
  <script>
    window.addEventListener("load", function(evt) {
      var components = document.querySelectorAll("x-component");
      for (var i = 0; i < components.length; i++) {
        components[i].addEventListener("clicked", function(evt) {
          alert(evt.detail.message);
        });
      }
    });
  </script>
</head>
<body>
  <x-component message="This is the shadow DOM 1"></x-component>
  <x-component message="This is the shadow DOM 2"></x-component>
</body>
</html>
```

Polyfillであるplatform.jsを読み込み後に、HTML Importsを使って、x-component部品を読み込みます。上記の例では、x-componentタグを2つ配置し、それぞれ内容が異なるmessage属性を指定してみました。

そして、loadイベント発生後に、それぞれのx-component部品に対して、addEventListsner()関数を使って"clicked"イベントに対するイベントハンドラを登録しています。イベントハンドラに渡されるカスタムイベントオブジェクトの中には、message属性で指定した文字列が格納されているはずです。evt.detail.messageプロパティ値を取得して、alert()関数で表示しています。

まずはChromeで実行してみます。結果は以下のようになりました。

![スクリーンショット 2014-07-23 0.05.02](http://www.eisbahn.jp/yoichiro/images/2014/07/1b25601495b163b0dcfe4d40539aa003.png)

message属性値がそれぞれShadow DOMにセットされていることがわかりますね。

![スクリーンショット 2014-07-23 0.06.13](http://www.eisbahn.jp/yoichiro/images/2014/07/b45ff1917eb53839ecb617a6c96796c0.png)

"This is the shadow DOM 1"の方をクリックすれば、そのメッセージがalert()関数で表示されますし、

![スクリーンショット 2014-07-23 0.07.05](http://www.eisbahn.jp/yoichiro/images/2014/07/7cb08f1b96d1f7d626bad8f59bc905fa.png)

"〜 DOM 2"の方をクリックすれば、ちゃんと"〜 DOM 2"の方がalert()関数で表示されました。問題なさそうです。

念のため、IE11でも試しておきましょう。

![スクリーンショット 2014-07-23 0.08.43](http://www.eisbahn.jp/yoichiro/images/2014/07/c178fd34741437643053b3869f17fe1a.png)

なんちゃってですが、Shadow DOMとして指定した内容が表示されています。

![スクリーンショット 2014-07-23 0.09.35](http://www.eisbahn.jp/yoichiro/images/2014/07/5f4859ff99998319cababcdfae9cdacd.png)

カスタムイベントも無事配送され、メッセージの内容がalert()関数で表示されています。

![スクリーンショット 2014-07-23 0.10.35](http://www.eisbahn.jp/yoichiro/images/2014/07/2475e1547b448d6bb019ad0df3a919a4.png)

ちゃんと部品ごとの区別も問題なさそうです。

以上のように、Web ComponentsとCustomEventオブジェクト、そしてdispatchEvent()関数を組み合わせることで、インタラクティブな部品を開発することができるようになることがわかりました。

最後に、カスタムイベントの名前を"click"にしたときのChromeのスクリーンショットを載せて、このエントリを締めましょう。

![スクリーンショット 2014-07-23 0.13.42](http://www.eisbahn.jp/yoichiro/images/2014/07/e2d0e8bd0997661e48994bfade29de02.png)

crbug.comにレポートしておくか。。。

追記 2014/07/26 - 続きのエントリ「
[Web Componentsを使った部品のイベント伝達の続き](http://www.eisbahn.jp/yoichiro/2014/07/dispatch_event_from_web_components_2.html)」を書きましたので、そちらも合わせてお読みください。
