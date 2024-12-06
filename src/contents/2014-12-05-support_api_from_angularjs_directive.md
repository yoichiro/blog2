---
layout: post
status: publish
published: true
title: AngularJSのDirectiveにAPIを追加するための方法
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 3149
wordpress_url: https://www.eisbahn.jp/yoichiro/?p=3149
date: '2014-12-05 14:17:35 +0900'
date_gmt: '2014-12-05 05:17:35 +0900'
categories:
- AngularJS
---

AngularJSには、自分独自のHTML要素を作り出すためのDirectiveという仕組みがあります。これを使うと、一般的に「div地獄」となるような動的Webアプリケーションの作り方ではなく、論理的で直感的な、やりたいこと実現したいことを端的に表すタグを記述するだけで、Webアプリケーションを構成していくことが可能になります。現在Web Componentsの仕様策定や実装、さらにそれを推し進めるためのPolymerの開発が進んできていますが、それを先取りしたようなものと考えれば良いでしょう。

Directiveでは、AngularJSが本来持つデータバインディングや関数バインディングの仕組みをフルに使って、簡単に動的な情報を扱う独自要素を実装することができるようになっています。そうやって部品化戦略を進めていくことができるのですが、一つだけ個人的に「あれ、ない」って思ったことがあります。それは、

「DirectiveにAPIを定義するための方法がない」

ということです。データバインディングによって、双方向の情報のやり取りは自動化されます。関数バインディングによって、例えばDirective内で何かイベントが発生したときに、バインディングされた関数を呼び出すこともできます。しかし、Directiveが持つ関数を、Directiveの利用側から呼び出すための機構が見当たらないのです。つまり、Directiveには「API」と呼ぶことができるであろう「利用側から呼び出されることを前提とした関数の露出」について、何のサポートもなさそうに思えます。

![Screen Shot 2014-12-05 at 14.15.31](https://www.eisbahn.jp/yoichiro/images/2014/12/Screen-Shot-2014-12-05-at-14.15.31.png)

Stackoverflowでも、同じような疑問を持った人が数人いて、「どうやってAPIをExposeすればいいの？」と質問を投げているのを見つけました。そのほとんどには「$emit()でイベントでも使えば？」という回答が付いていました。確かにそれも実現方法の一つだと思いますが、もう少し手軽な手段はないかな、と考えてみました。そこで編み出したアイディアが、「コールバック関数にAPIを渡す」です。

# 実際どんなものを作ったか？

とにかく最初に以下のJSFiddleを見てみてください。

[JSFiddle](http://jsfiddle.net/yoichiro/m0L5xLsw/8/)

これは、mydirectiveという名前の独自要素を作った例です。mydirectiveは、以下のような仕様になっています。

```
<mydirective
    text="初期表示文字列"
    on-load="このDirectiveが読み込まれた後に呼び出されるコールバック関数">
</mydirective>
```

このmydirectiveにて、表示文字列を変更するためのsetText()関数をAPIとして提供したい、と考えたとします。「データバインディングすればいいじゃん」という発想は、一旦捨ててください。とにかく、上記のDirectiveに対して何か操作するための関数を定義して、利用する側から叩きたい、ということを実現するための方法を考えます。

試行錯誤の結果、僕が辿り着いたDirectiveのコードは、以下になります。

```
myapp.directive("mydirective", [function() {
    return {
        restrict: "E",
        template: "<span></span>",
        scope: {
            onLoad: "&",
            text: "@"
        },
        compile: function(element, attrs) {
            return function(scope, element, attrs, ctrl) {
                element.text(scope.text);
                if (scope.onLoad) {
                    scope.onLoad({
                        api: (function(scope, element) {
                            return {
                                setText: function(text) {
                                    element.text(text);
                                }
                            };
                        })(scope, element)
                    });
                }
            };
        }
    };
}]);
```

初期文字列をもらうためのtext属性は、普通に値バインディングを使って受け取り、element変数を使ってspan要素にTextNodeをセットしています。これは普通にやることだと思います。API提供についてのポイントは、on-load属性によって関数バインディングされたことをうまく使うことです。つまり、このDirectiveがLinkされたときに、APIで提供したい関数群を持つオブジェクトを作成し、それをon-load属性で利用側から提供された関数を呼び出す際に引数として渡すのです。上記では、setText()関数が定義されたapiという属性値を持つオブジェクトを作って、それをon-load属性で指定されたコールバック関数に渡しています。

このmydirectiveを使う側は、以下のようになります。

```
<div ng-app="myapp">
<div ng-controller="mycontroller1">
        <mydirective text="foo" on-load="onLoad(api)"></mydirective>
        <button ng-click="onClick()">Set text</button>
    </div>
...
```

まずHTML側としては、mydirectiveを利用するコントローラを準備して、コントローラのスコープを作ります。つまり、on-load属性で渡すコールバックハンドラ関数は、mycontroller1コントローラに定義したonLoad()関数がバインドされます。その際、引数としてapiを指定しているので、mydirectiveで先ほど作ったオブジェクトのapi属性値が渡されることになります。

実際にmydirectiveのAPIを呼び出すきっかけとして、ボタンを一つ置いています。クリックイベントハンドラとして、mycontroller1コントローラにonClick()関数が呼び出されるようにします。

```
myapp.controller("mycontroller1", ["$scope", function($scope) {
    $scope.onLoad = function(api) {
        $scope.api = api;
    };
    $scope.onClick = function() {
        $scope.api.setText("Yoichiro");
    };
}]);
```

次にmydirectiveの利用側であるコントローラですが、onLoad()関数に引数で渡されるAPIオブジェクトを、自身のスコープに保持しておきます。そして、ボタンが押された際に呼び出されるonClick()関数の中で、保持しておきたAPIを叩いています。

実行すると、まず以下のように表示されて、

![Screen Shot 2014-12-05 at 13.54.30](https://www.eisbahn.jp/yoichiro/images/2014/12/Screen-Shot-2014-12-05-at-13.54.30.png)

Set textボタンを押すと、以下のように変更されるのがわかると思います。

![Screen Shot 2014-12-05 at 13.55.15](https://www.eisbahn.jp/yoichiro/images/2014/12/Screen-Shot-2014-12-05-at-13.55.15.png)

あとは、公開したいAPIをDirective側で追加していけば、リッチなDirectiveに仕上がっていくと思います。

# これ、本当に必要？

ここまで読んだ方は、全員以下の気持ちになったはずです。

「だから、データバインディングで事足りるじゃん」

はい、上記のUse-caseであれば、データバインディングを使うべきです。こんな回りくどいことする必要ないです。ごもっともです。100%同意です。

では、ここまでして関数をDirective利用側に露出させたいUse-caseがあるのか？ってことになるのですが、例えば「Canvasに図形を描画し、その結果をBLOB形式で取得したい」という時が個人的な経験ではUse-caseとしてありました。図形が描画される度にBLOBを作っていくわけにも行きませんし、実際ユーザがその図形を画像保存でも求めない限り、BLOBを作る作業自体が「無駄」です。CanvasへのアクセスはDirective内で完結されているべきですので、BLOBを欲しい時に初めて利用側がDirectiveに指令を出したいわけです。

例として、上記のUse-caseを簡単に作成してみました。

[JSFiddle](http://jsfiddle.net/yoichiro/qtcmwj0d/5/)

以下のようにmydirectiveを利用すると、

```
<mydirective text="foo" on-load="onLoad(api)"></mydirective>
```

以下のようにCanvasに描画され、

![Screen Shot 2014-12-05 at 14.03.26](https://www.eisbahn.jp/yoichiro/images/2014/12/Screen-Shot-2014-12-05-at-14.03.26.png)

Get BLOBボタンを押した時に、DirectiveのAPIを呼び出して、DirectiveがCanvasからBLOBを作って、それが利用側に返却されてきます。

![Screen Shot 2014-12-05 at 14.04.31](https://www.eisbahn.jp/yoichiro/images/2014/12/Screen-Shot-2014-12-05-at-14.04.31.png)

APIの実装は、以下のようにしています。

```
compile: function(element, attrs) {
    return function(scope, element, attrs, ctrl) {
        $(element.find("canvas")).drawText({
            ...
        }).drawRect({
            ...
        });
        if (scope.onLoad) {
            scope.onLoad({
                api: (function(scope, element) {
                    return {
                        getBlob: function() {
                            var canvas = $(element.find("canvas"));
                            return canvas.getCanvasImage("png");
                        }
                    };
                })(scope, element)
            });
        }
    };
}
```

なかなかこのような作り方をする機会はないかもしれませんが、たまーにあるかもしれないので、紹介してみました。
