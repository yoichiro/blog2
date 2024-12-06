---
layout: post
status: publish
published: true
title: AngularJSにてMaterial Designサポートの開発が進められているようです
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2713
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2713
date: '2014-07-03 10:03:08 +0900'
date_gmt: '2014-07-03 01:03:08 +0900'
categories:
- AngularJS
---

先日行われたGoogle I/Oで華々しいデビューを飾ったMaterial Design。Appleのフラットデザインをさらに推し進めて、視覚効果がリッチになったり、情報を読んでいく際のナビゲーションが規定されていたり、さらに当たり前のようにレスポンシブな仕上がりになっていましたね。個人的な印象は「とにかく派手！見た目重視フィードバック重視！」でした。操作した際の楽しさが考えられてるって感じましたが、皆さんはどう思いましたか？

基本的なUI部品の視覚効果を見て、「あ、さっそく使ってみたい！」って思いますよね。特に、ずっとBootstrapを使ってきた開発者は、Material Designに乗り換えたい！Polymerやってみたい！って思ったことでしょう。僕もそのうちの一人です。さらにPolymerによってWeb Componentsの波はすぐそこまで来ているので、早く自分のものにしておきたいと思ってます。

そのためには、適度にそういったものを試せる「題材となるアプリ」が手元にあるとやりやすいですね。私もBootstrapを使って作ったChromeアプリを持っています。Bootstrapが提供している基本的なComponentしか基本的に使っていないので、置き換えは簡単なはず。そう、簡単なはずです。。。

まだ実際に試していないのですが、一つだけ気になることがあります。それは「AngularJSとの相性はどうなんだろう」という点です。AngularJSが提供する最も重要かつ有用な機能は、データバインディングです。ModelとViewの橋渡しを暗黙的にしてくれるAngularJSですが、Web Componentsにおいてもデータバインディングはあります。

```
<polymer-element name="name-tag">
  <template>
    This is <b>{{owner}}</b>'s name-tag element.
  </template>
  <script>
    Polymer('name-tag', {
      // initialize the element's model
      ready: function() {
        this.owner = 'Rafael';
      }
    });
  </script>
</polymer-element>
```

BootstrapとAngularJSをガッツリ使って組み上げてしまったアプリケーションをPolymerベースのMaterial Design適用アプリケーションに移行するためには、Bootstrapだけでなく、AngularJSも窓から放り投げる必要があるのでしょうか？

・・・とここまで書いてきて、僕はかなりいろんなことを混同しているな、と自覚しました。ちゃんと整理すると、

* Material Designは、あくまでデザインである。

* Material Designは、Web Componentsとは直接関係ない。

* Material Designは、Polymerとも直接関係ない。

* AngularJSと比較すべきは、Web ComponentsやPolymer。Material Designではない。

* Material Designと比較すべきは、Bootstrapである。

故に、AngularJSとMaterial Designは共存可能、なはずですね。つまり、AngularJSとBootstrapを使って作られたアプリケーションは、AngularJSはそのままにして、Bootstrapを捨ててMaterial Designに移行すれば良い、ということです。

そして、今日偶然に知りましたが、やはりMaterial Designを使いたいからと言ってAngularJSを捨てなくても良さそうです。AngularJSを使ったアプリケーション向けのMaterial Designサポートの開発が現在進められているようです。

[Material Design for AngularJS Apps]

[https://github.com/angular/material/](https://github.com/angular/material/)

デモサイトもあります。

[https://material.angularjs.org/#/](https://material.angularjs.org/#/)

[![angularjs-material-design-demo](http://www.eisbahn.jp/yoichiro/images/2014/07/angularjs-material-design-demo.png)](https://material.angularjs.org/#/)

このデモサイトの内容を見てみると、あくまでMaterial DesignはCSS(少しJSもあるけど）として適用していき、データバインディングはAngularJSの機能を使っていく、ということになっていました。デモサイトにおいては、head属性の中でMaterial Design向けの記述して以下のような感じになってました。

```
<head>
...
<link rel="stylesheet" href="../../../lib/material-design.css">
...
<script src="../../../lib/angular-animate-sequence.js"></script>
<script src="../../../lib/angular-animate-stylers.js"></script>
<script src="../../../lib/material-design.js"></script>
...
</head>
```

最もシンプルなButtonは、class属性でMaterial Design向けの指定をしているだけですが、何やらmaterial-contentという要素で全体が囲われています。

```
<body class="demo-cloaked">
<div ng-app="app" ng-controller="AppCtrl">
  <material-content>
<section class="margin">
      <button class="material-button">Button</button>
      <br>
      <button class="material-button material-button-colored">Button</button>
      <br>
      <button disabled class="material-button material-button-colored">Colored</button>
      <br>
<div class="label">flat</div>
    </section>
    ...
  </material-content>
  ...
```

![angularjs-material-design-demo3](http://www.eisbahn.jp/yoichiro/images/2014/07/angularjs-material-design-demo3.png)

スライダーに関しては、material-sliderという要素があって、それを指定するだけでMaterial Designのスライダー部品になるっぽいです。データバインディングはng-modelを使った馴染みのものですね。

```
<body>
<div ng-app="app" ng-controller="AppCtrl">
  <material-slider>
    <input type="range" ng-model="data.slider1">
  </material-slider>
  <material-slider>
    <input type="range" ng-model="data.slider2" min="33" max="67">
  </material-slider>
  <material-slider>
    <input type="range" ng-model="data.slider3" min="-10" max="10" step="2">
  </material-slider>
Slider 1: {{ data.slider1 }}
Slider 2: {{ data.slider2 }}
Slider 3: {{ data.slider3 }}
</div>
</body>
```

![angularjs-material-design-demo2](http://www.eisbahn.jp/yoichiro/images/2014/07/angularjs-material-design-demo2.png)

上記のデモを見ていく限り、Material Design向けのJSやCSSを読み込んでおき、Material Design向けに提供されている各種Directiveを使って部品を配置していき、データバインディングはng-***ディレクティブで実現、という感じになりそうです。シンプルですね！

近いうちに正式リリースのアナウンスがあると思いますので、期待して待ちましょう。
