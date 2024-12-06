---
layout: post
status: publish
published: true
title: jCanvasによる楽々グラフィック描画
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 3105
wordpress_url: https://www.eisbahn.jp/yoichiro/?p=3105
date: '2014-12-02 10:14:34 +0900'
date_gmt: '2014-12-02 01:14:34 +0900'
categories:
- Web Technologies
---

最近はずっとChrome MySQL Adminの機能追加にいそしんでいる毎日です。データベースと言ったら、何を連想しますか？SQL？Oracle？いろいろと出てくると思いますが、きっと10位以内に入ってるキーワードとして、必ず「ER図」はあると思います。ER図は書くのが面倒なので、もし手元にある実際のデータベースからER図を自動的に書いてくれたら、こんなに楽なことはないですよね？はい、作ってみました。

![cma9_small](https://www.eisbahn.jp/yoichiro/images/2014/12/cma9_small.png)

この機能を作るために、いくつか図を描画するためのライブラリを検討しました。しかし、しっくり来るものは残念ながらありませんでした。一つ使えそうだったのが
[JointJS](http://www.jointjs.com/)だったのですが、依存ライブラリとして「jQuery, Backbone, Lodash, Geometry, Vectorizer」というそうそうたるメンバーが必要で、やむなく断念しました。そう、ないものは自分で作るしかありません。ER図のような作図は、オブジェクトを描画すること自体は難しくありません。とにかく面倒なのが、「配置」と「結線」です。もうこれらに尽きます。ただただ面倒な処理なのですが、覚悟を決めて、HTML5 Canvasに自分で書いていくことにしました。

# とはいえjCanvasで楽をする

着手したときはHTML5 Canvasを触ったこともなかったので、まずはCanvasで何ができるのかを調べるところから始めました。Canvasがどういったものなのかが見えてくるに従って、

「こりゃやばい。低レベルすぎる。」

という気持ちが強くなっていきます。まるで、大昔にあったLogo（タートルグラフィック）です。それはそれで楽しいのかもしれませんが、もう少し楽にCanvasを扱えないものかと、再度インターネットの荒波に乗ってみました。そこで見つけたのが、
[jCanvas](http://calebevans.me/projects/jcanvas/)です。これは、jQueryのプラグインとして作成されていて、Canvasへの描画処理を少し軽めのAPIで実現できるというものです。HTML5 Canvasの勉強は早々に離脱してしまったのですが、jCanvasを使うと、Grouping、Layer、Event handlingなどの機能が使えるとのことだったので、飛びつきました。

jCanvasのWeb siteに掲載されている短いCode snippetを見ると、その威力が伝わってきます。

```
// Draw a circle
$("canvas").drawArc({
  draggable: true,
  fillStyle: "green",
  x: 100, y: 100,
  radius: 50
});
```

"draggable: true"と書くだけで、
[ドラッグして移動可能な図形が書ける](http://calebevans.me/projects/jcanvas/sandbox/?code=Ly8gRHJhdyBhIGNpcmNsZQokKCJjYW52YXMiKS5kcmF3QXJjKHsKICBkcmFnZ2FibGU6IHRydWUsCiAgZmlsbFN0eWxlOiAiZ3JlZW4iLAogIHg6IDEwMCwgeTogMTAwLAogIHJhZGl1czogNTAKfSk7Cg%3D%3D)んですよ！すごくないですか？

# jCanvas事始め

jCanvasを利用するために必要となる準備は、本当に簡単です。

```
<script type="text/javascript" src="jquery.min.js"></script>
<script type="text/javascript" src="jcanvas.min.js"></script>
```

これだけです。楽勝ですね！

HTML5 Canvasなので、もちろんHTML内にcanvas要素を書いておきます。例えば以下のような感じです。

```
<body>
...
<canvas width="300" height="300"></canvas>
...
</body>
```

例えば、jCanvasを使って上記のcanvas要素に四角形を描画するには、以下のようにします。

```
$("canvas").drawRect({
  strokeStyle: "black",
  strokeWidth: 1,
  x: 100,
  y: 100,
  width: 100,
  height: 100
});
```

[JSFiddle](http://jsfiddle.net/yoichiro/91u3byqp/1/)

枠の色を黒、枠の幅を1、(100, 100)の位置に大きさ(100, 100)で正方形が描かれます、って言いたいところですが、残念ながら位置が(50, 50)で表示されます。これは、X座標とY座標の指定が「図形の中央」になっていることが原因です。これが初期設定になっていて、僕はこれの何が嬉しいのか良くわかりません。なので、初期値を「左上指定」に速攻変えました。

```
$.jCanvas.defaults.fromCenter = false;
```

[JSFiddle](http://jsfiddle.net/yoichiro/2es7bsbq/4/)

こうやって、jQueryのセレクタで持ってきたcanvas要素に対して、drawHogeHoge()関数を呼び出していくことで、グラフィックを描けるわけです。

# ER図を書くときに使用した描画関数

基本的に、ER図を描くために必要となる図形は少ないです。四角形、直線、テキスト、これだけで描けます。それぞれの描画関数を紹介しておきましょう。

四角形

```
$("canvas").drawRect({
  strokeStyle: "black",
  strokeWidth: 1,
  x: 100,
  y: 100,
  width: 100,
  height: 100
});
```

先ほども出てきましたね。

直線

```
$("canvas").drawLine({
    strokeStyle: "black",
    strokeWidth: 1,
    x1: 50,
    y1: 50,
    x2: 100,
    y2: 100
});
```

[JSFiddle](http://jsfiddle.net/yoichiro/xowrLjtj/3/)

2つの点を与えれば、線が描けます。

テキスト

```
$("canvas").drawText({
    fillStyle: "black",
    strokeStyle: "black",
    strokeWidth: "1",
    x: 50,
    y: 50,
    fontSize: 14,
    fontFamily: "sans-serif",
    text: "テキスト"
});
```

[JSFiddle](http://jsfiddle.net/yoichiro/j71j0nf6/3/)

描画位置とフォント関連の設定を記述することで、テキストを描画できます。

組み合わせた結果のコード

上記を組み合わせて、一つEntityを描画するコードは、以下のようになるでしょう。

```
$.jCanvas.defaults.fromCenter = false;
function drawText(text, x, y) {
    $("canvas").drawText({
        fillStyle: "black",
        strokeStyle: "black",
        strokeWidth: "0.5",
        x: x,
        y: y,
        fontSize: 14,
        fontFamily: "sans-serif",
        text: text
    });
}
drawText("categories", 50, 50);
drawText("category_id", 50, 80);
drawText("name", 50, 100);
$("canvas").drawRect({
  strokeStyle: "black",
  strokeWidth: 1,
  x: 45,
  y: 45,
  width: 100,
  height: 75
});
$("canvas").drawLine({
    strokeStyle: "black",
    strokeWidth: 1,
    x1: 45,
    y1: 70,
    x2: 145,
    y2: 70
});
```

[JSFiddle](http://jsfiddle.net/yoichiro/3ucg3L76/3/)

![スクリーンショット 2014-12-03 6.48.12](https://www.eisbahn.jp/yoichiro/images/2014/12/63ae56432e1faefd2a5b225096660ee5.png)

# 図形のDrag and dropによる移動

冒頭で紹介してしまいましたが、描画した各図形は、jCanvasを使うと簡単にDrag and dropで移動可能な図形に変身させることができます。これは驚くほど簡単で、図形描画関数を呼び出す際に、単に"draggable: true"を追記するだけです。

```
$("canvas").drawRect({
    strokeStyle: "black",
    strokeWidth: 1,
    x: 45,
    y: 45,
    width: 100,
    height: 75,
    draggable: true
});
```

[JSFiddle](http://jsfiddle.net/yoichiro/a3vvhgqo/1/)

ドラッグして移動可能な図形には、テキストも含めて、全てこのdraggable指定をしておきます。例えば、先ほどのEntityを描くためのコードについてdraggableを指定すると、以下のJSFiddleになります。

[JSFiddle](http://jsfiddle.net/yoichiro/mw4aaLha/1/)

全ての図形がマウスで移動できるようになりました。そして、見事にEntitiy図がバラバラになってしまうこともわかります。

![スクリーンショット 2014-12-03 6.47.00](https://www.eisbahn.jp/yoichiro/images/2014/12/8221d39cbb67afdbf9ff47e54916f6ad.png)

# 複数の図形のグループ化

せっかく描画したEntityをバラバラにならずに移動させるために、一緒に移動して欲しい図形を1つのグループとして設定します。jCanvasでは、論理的に「君たちを一つのグループとして扱うよ」と単に指定するグループと、「さらに君たちは一緒に動くんだよ」というDrag and dropのためのグループ化の2段階で設定可能になっています。具体的には、groups、dragGroupsという2つの属性を指定します。

```
$("canvas").drawRect({
    strokeStyle: "black",
    strokeWidth: 1,
    x: 45,
    y: 45,
    width: 100,
    height: 75,
    draggable: true,
    groups: ["categories"],
    dragGroups: ["categories"]
});
```

配列で指定可能です。つまり、一つの図形を複数のグループに所属させることができます。ER図の場合は、個々のEntityごとに一つのグループとして設定していくことになります。

[JSFiddle](http://jsfiddle.net/yoichiro/n2fpw0bm/2/)

# 動的に枠の大きさを決定するには？

今までの例では、図形の配置や大きさを固定値で指定していました。データベースには多くのTableが存在するはずですし、個々のTableには様々なColumnがあります。その名前も予測不可能です。つまり、Entityの枠の大きさは、その中にあるテキストの数や文字列長に応じて変化させなければなりません。

まず、描かれる図形を「レイヤ化」します。レイヤ化によって、図形それぞれに名前を付けて、あとでその図形の情報を取得することができるようになります。ほとんどの図形をレイヤ化する必要があるので、初期値として指定してしまいましょう。

```
$.jCanvas.defaults.layer = true;
```

次に、今まで描いた図形それぞれに対して、name属性を追加して名前を振っていきます。例えばテキストに対しては、"
-
-text"という命名規則で名前を付けていく場合は、以下のようになるでしょう。

```
function drawText(text, x, y, name) {
    $("canvas").drawText({
        fillStyle: "black",
        strokeStyle: "black",
        strokeWidth: "0.5",
        x: x,
        y: y,
        fontSize: 14,
        fontFamily: "sans-serif",
        text: text,
        name: name + "-text"
    });
}
var textNames = ["categories", "categories-category_id", "categories-name"];
drawText("categories", 50, 50, textNames[0]);
drawText("category_id", 50, 80, textNames[1]);
drawText("name", 50, 100, textNames[2]);
```

こうしておくことで、描画結果の各Layerを取得して、それぞれの描画幅の最大値を求めれば、Entityの枠の幅を決定することができるようになります。同じく、列の数（category_id, name）に個々の高さをかけ算することで、Entityの高さも決定可能になるでしょう。

```
var borderWidth = 0;
for (var i = 0; i < textNames.length; i++) {
    borderWidth = Math.max(
        borderWidth,
        $("canvas").getLayer(textNames[i] + "-text").width);
}
$("canvas").drawRect({
    strokeStyle: "black",
    strokeWidth: 1,
    x: 45,
    y: 45,
    width: borderWidth + 10,
    height: 75,
    draggable: true,
    groups: ["categories"],
    dragGroups: ["categories"]
});
```

[JSFiddle](http://jsfiddle.net/yoichiro/uuj6ntf7/1/)

getLayer()関数がポイントです。名前を渡すことで、Layerの情報が得られます。

# Entity間を繋ぐ線の描画はどうする？

個々のEntityは描画できるようになりましたが、それだけではER図は不十分です。Entity間の関連を結線で表現できて初めて「entity RELATION diagram」になりますね。このEntityとEntityを繋ぐ線の描画が、最も面倒です。最終的には泥臭い計算と再描画のためのコードを書く必要が出てきます。Chrome MySQL Adminの実例だと、以下のコードになります。

* [2つのEntity間で線が最も短くなる2点の計算](https://github.com/yoichiro/chrome_mysql_admin/blob/master/app/scripts/window/directives/er_diagram.js#L516)

* [線の描画](https://github.com/yoichiro/chrome_mysql_admin/blob/master/app/scripts/window/directives/er_diagram.js#L483)

* [あるEntityがDragされている時の線の再描画](https://github.com/yoichiro/chrome_mysql_admin/blob/master/app/scripts/window/directives/er_diagram.js#L557)

今振り返ると、そんなにコードは多くなかったですね。試行錯誤をしていた記憶があるので、難解な、すっごい量のコードになっていると思い込んでいました。。。

さて、このエントリでは話を単純にするために、お互いに移動する図形を線で結ぶのではなく、片方は位置が固定された状態で線の引き方を見ていこうと思います。以下のように図形を追加します。接続相手の円形と結線はDrag and dropで移動できてはいけないので、draggable: falseを指定しています。

```
$("canvas").drawEllipse({
    fillStyle: 'black',
    x: 250,
    y: 100,
    width: 20,
    height: 20,
    draggable: false
});
$("canvas").drawLine({
    strokeStyle: "black",
    strokeWidth: 1,
    x1: 45 + borderWidth + 10,
    y1: 80,
    x2: 260,
    y2: 110,
    draggable: false
});
```

[JSFiddle](http://jsfiddle.net/yoichiro/d55dzxfp/1/)

なんとなくそれっぽい図になってきました。しかし、もちろん何も処理を書いていないので、Entityを移動させると、悲しい結果になります。

![Screen Shot 2014-12-03 at 10.25.10](https://www.eisbahn.jp/yoichiro/images/2014/12/Screen-Shot-2014-12-03-at-10.25.10.png)

# Drag and dropイベントのハンドリング

Entityを移動させた際に結線もそれにつれて再描画するために、図形が移動された際に発生するイベントを受け取って処理をするハンドラ関数を登録することが必要です。イベントハンドリングは、「Dragされた」「Dropされた」「Dragがキャンセルされた」の3種類をハンドリングすることができます。Dragのキャンセルとは、DragしてたけどCanvasの外に出ちゃった、とかそういう時に発生します。それぞれ、ハンドラ関数を図形描画時に指定します。例えば、Entityの枠に対してイベントハンドラを登録するには、以下のようになります。

```
$("canvas").drawRect({
    strokeStyle: "black",
    strokeWidth: 1,
    x: 45,
    y: 45,
    width: borderWidth + 10,
    height: 75,
    draggable: true,
    groups: ["categories"],
    dragGroups: ["categories"],
    drag: onDrag, // Dragされた
    dragstop: onDragStop, // Dropされた
    dragcancel: onDragCancel // Dragがキャンセルされた
});
function onDrag(layer) {
  // Do something...
}
function onDragStop(layer) {
  // Do something...
}
function onDragCancel(layer) {
  // Do something...
}
```

Entityは複数の図形から成り立っているので、それぞれの図形の描画時に同じようにイベントハンドラ登録していきます。

# Drag時の結線の再描画

上記のonDrag()イベントハンドラ関数で行うべきこと、それは「結線を再描画すること」です。そのために、いくつか修正が必要です。

まず、移動されたEntityの現在位置を入手できることが必要です。そのために、Entityの枠のLayerに名前を付けて、後で取得できるようにしておきます。

```
$("canvas").drawRect({
    strokeStyle: "black",
    strokeWidth: 1,
    x: 45,
    y: 45,
    width: borderWidth + 10,
    height: 75,
    draggable: true,
    groups: ["categories"],
    dragGroups: ["categories"],
    drag: onDrag,
    dragstop: onDragStop,
    dragcancel: onDragCancel,
    name: "categories-border"
});
```

これで"categories-border"という名前でgetLayer()できるようになりました。次に、結線の描画について、固定座標ではなく、名前を付けたEntityの枠の位置を動的に取得して計算するように変更します。また、再描画できるように、結線の描画処理を関数にしておきます。

```
function drawLink() {
    var border = $("canvas").getLayer("categories-border");
    $("canvas").drawLine({
        strokeStyle: "black",
        strokeWidth: 1,
        x1: border.x + border.width,
        y1: border.y + 50,
        x2: 260,
        y2: 110,
        draggable: false,
        name: "link"
    });
}
drawLink();
```

結線にも名前を付けていることがポイントです。最後に、Dragされた際のイベントハンドラ内で、結線を消して、直後にdrawLink()関数を呼び出す処理を書いておきます。

```
function onDrag(layer) {
    $("canvas").removeLayer("link");
    drawLink();
}
```

[JSFiddle](http://jsfiddle.net/yoichiro/zhgfrx4j/1/)

上記のJSFiddleを開いて、実際にEntityをDragしてみてください。結線も一緒に再描画されているのがわかると思います。

今回のサンプルでは、結線の片方は固定ですし、Entityも一つです。しかし、複数のEntityとそれらを繋ぐ結線の描画とDrag and drop処理については、上記のテクニックをあとはうまく組み合わせて「泥臭い処理」を書いていけば実現できます。ここから必要になる能力、それは「途中でくじけない根性」です。

# まとめ

jCanvasを使うと、LayerやDrag and drop処理など、図形を動的に扱うための処理を簡単に実現することができることがわかったと思います。あとは、これらの発展系です。HTML5 Canvasを素で使うと、ここまで簡単にはいかないと思いますし、高級なコンポーネントを使っても今度は自由が効きません。GUI関連のプログラミングに少し慣れていれば、jCanvasレベルのライブラリを使って、結線を伴うDiagramの作図を比較的簡単に作っていけると思います。

興味のある方は、ぜひお試しください。
