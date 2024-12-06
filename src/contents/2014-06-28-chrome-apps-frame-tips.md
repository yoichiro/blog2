---
layout: post
status: publish
published: true
title: Chrome appsのFrameを自分で作る際のTips
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2680
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2680
date: '2014-06-28 13:15:52 +0900'
date_gmt: '2014-06-28 04:15:52 +0900'
categories:
- Chrome Apps
---

Chrome appsでアプリケーションを作る際に、OSが提供してくれるウィンドウのフレームを使うかどうか、選択することができます。例えばmacであれば、以下のようなフレームが提供されるわけですが、

![frame_by_chrome](http://www.eisbahn.jp/yoichiro/images/2014/06/frame_by_chrome.png)

これを使わないという選択肢もとれるわけです。例えば、以下のように自分で自由に作ることができます。

![frame_by_myself](http://www.eisbahn.jp/yoichiro/images/2014/06/frame_by_myself.png)

OS標準のフレームに似せずに、まったく自由にウィンドウを作ることができるわけです。閉じるボタンやフルスクリーンボタンなども、置くかどうかも自由です。

OS標準のフレームを使うかどうかは、ウィンドウを作り出す際のオプションで切り替えることが可能です。OS標準のフレームを使いたいときは、特に何も指定しないか、以下のように指定します。

```
chrome.app.window.create("window.html", {
    bounds: {
        width: 800,
        height: 600
    },
    minWidth: 800,
    minHeight: 600,
    resizable: true,
    frame: "chrome"
});
```

OS標準のフレームなんていらねーよ、と指定したい場合は、frameプロパティに"none"を指定します。

```
chrome.app.window.create("window.html", {
    bounds: {
        width: 800,
        height: 600
    },
    minWidth: 800,
    minHeight: 600,
    resizable: true,
    frame: "none"
});
```

これで、OS標準のフレームがない、単なる矩形のウィンドウが表示されます。

OS標準のフレームを使わなかった場合、ウィンドウを閉じたり最大化したりフルスクリーンにしたり、そもそも移動させたりする機能は、当然自分で実装していかなければなりません。Chrome appsでは、それぞれ実現するためのAPIが提供されています。

```
// 現在のウィンドウを取得
var window = chrome.app.window.current();
// 最大化
window.maximize();
// 最小化
window.minimize();
// フルスクリーン化
window.fullscreen();
// 上記から元の大きさに戻す
window.restore();
```

どれもわかりやすい直球の関数名です。これらの関数を、自前で提供するUIの中から呼び出してあげれば良いということになります。どんなUIを提供するのか、そもそも何かの副作用として上記を呼び出すか、開発者の自由です。

また、ウィンドウを作る際にresizeプロパティをtrueにしておけば、ウィンドウの端から大きさの変更をすることが可能になります。falseを指定したときはもちろんその逆で大きさ変更ができなくなります。

ここまではシンプルなのですが、ではウィンドウを移動できるようにするためにはどうしたら良いでしょうか？最初僕は悩みました。Chrome apps APIを眺めていても、それができるようなAPIを見つけることができませんでした。そのものズバリではなく、何か使えそうなものはないかと考えていくと、moveTo()という関数が目に付きました。これは、指定された座標にウィンドウを移動させるための関数です。

「ほほぉ、ウィンドウ内のある領域内でマウスをドラッグ操作したときに、その移動量を検出して、その分moveTo()で位置変更してあげれば良さそうだな。」

と考えたわけです。目の前にAngularJSで書いているアプリケーションがあったので、以下のように書いて試してみました。

```
<div ... ng-controller="hogeController" ng-mousedown="onMouseDown()" ng-mouseup="onMouseUp()" ng-mousemove="onMouseMove($event)" ng-mouseleave="onMouseLeave()">
  ...
</div>
```

```
myapp.controller("hogeController", ["$scope", function($scope) {
  var mouseDown = false;
  var previousBounds = null;
  $scope.onMouseMoved = function($event) {
    if (mouseDown) {
      var clientX = $event.clientX;
      var clientY = $event.clientY;
      if (previousBounds) {
        var deltaX = clientX - previousBounds.clientX;
        var deltaY = clientY - previousBounds.clientY;
        var window = chrome.app.window.current();
        var bounds = window.outerBounds;
        bounds.left += deltaX;
        bounds.top += deltaY;
        window.outerBounds = bounds;
      }
      previousBounds = {
        clientX: clientX,
        clientY: clientY
      };
    }
  };
  $scope.onMouseDown = function() {
    mouseDown = true;
  };
  $scope.onMouseUp = function() {
    mouseDown = false;
    previousBounds = null;
  };
  $scope.onMouseLeave = function() {
    mouseDown = false;
    previousBounds = null;
  };
}]);
```

この手の座標計算＋UI反映系は、全く良い思い出がありません。大抵の場合、イベントが十分に発火せず、実際のマウスカーソルの移動量に追随できない、という結果になります。上記のコードも同様です。ちょっと早くマウスカーソルを移動させると、ウィンドウはチカチカするだけで移動しません。ゆっくり動かしても、どうも移動量の半分しかウィンドウが移動してくれません。

では上記のコードをより洗練させていかなければならないのか？そんなことは実はなく、CSSで1つ指定するだけで十分なのです。

```
<div style="-webkit-app-region: drag;">
  ...
</div>
```

"-webkit-app-region: drag;"と書いてあげるだけで、その要素の領域をドラッグしていくと、ウィンドウもそれに追随して移動してくれます。例えばbody要素に指定すれば、ウィンドウのどこをドラッグしてもウィンドウが移動するようにできることでしょう。

もし上記のdiv要素の子要素の領域でウィンドウ移動して欲しくないとき、例えば「閉じる」ボタンを置いていて、その中では移動させたくないときは、以下のようにすると良いでしょう。

```
<div style="-webkit-app-region: drag;">
  ...
  <button style="-webkit-app-region: no-drag;">Close</button>
  ...
</div>
```

便利なCSSがあるものですね！
