---
layout: post
status: publish
published: true
title: jqplot Tips
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2905
wordpress_url: https://www.eisbahn.jp/yoichiro/?p=2905
date: '2014-10-03 15:08:49 +0900'
date_gmt: '2014-10-03 06:08:49 +0900'
categories:
- Web Technologies
---

最近
[Chrome MySQL Admin](https://chrome.google.com/webstore/detail/chrome-mysql-admin/ndgnpnpakfcdjmpgmcaknimfgcldechn)でグラフ表示をしたく、
[jqplot](http://www.jqplot.com/)を使ってみました。そこで見つけたTipsをまとめておきます。

# IDを指定せずにjqplotを初期化する方法

jqplotを使う際には、以下のように記述するのが普通です。

```
<div id="jqplot-sample" ...></div>
```

```
var jqplot = $.jqplot(
    "jqplot-sample",
    [ data ],
    { ... }
);
// 例えば再描画
jqplot.replot();
```

$.jqplot() 関数の第1引数は、jqplotを置きたい要素のIDを指定します。そう、IDです。ID文字列です。ここをセレクタにしたり、事前に取得した要素オブジェクトを指定することはできません。

これは、jqplotを動的に生成したい場合に非常に不都合になります。ID指定ではなく、動的に追加した要素に対して追加したいわけです。jqplotのコードを見て調べたところ、以下のようにすることで実現できました。

```
<!-- 動的に追加した要素と仮定 -->
<div class="jqplot-sample" ...></div>
```

```
var jqplot = $(".jqplot-sample").jqplot(
    [ data ],
    { ... }
).data("jqplot");
// 例えば再描画
jqplot.replot();
```

セレクタで取得した結果に対してjqplot()関数を呼び出すと、その結果としてjqplotオブジェクトが返ってくれば嬉しいのですが、残念ながら要素オブジェクトが得られるだけです。これでしばらく途方にくれていたのですが、実は"jqplot"という名前でdataとしてjqplotオブジェクトがセットされた形で返却されていました。これを取り出せば、$.jqplot() 関数を使った時と同じ結果を得ることができます。

# destroyせずに再描画

stackoverflowなんかで探してみると、グラフを再描画したい場合は以下のようにするって回答が多かったです。

```
var jqplot = ...;
jqplot.destroy();
jqplot = $.jqplot(...);
[/javascript}

つまり、1回捨ててもう一回作りなおすっていう方法。これでもいいんだけど、なんか無駄な感じがします。再描画のためにreplot()という関数があるんだけど、これを単に引数なしで呼んだだけでは、実は何も起きません。

いろいろ調べた結果、以下のようにしてあげれば、ちゃんとグラフが再描画されました。

```
var jqplot = ...;
var opts = { ... }; // グラフのオプション
opts.data = [ [...] ]; // 新しいデータ
opts.clear = true;
jqplot.replot(opts);
```

ポイントは、グラフオプションのオブジェクトの中にdata属性やclear属性を入れてreplot()関数を呼ぶ、ということ。replot()関数の中では、dataやclearなどいくつかの決められた属性を取得して、それらが入ってたオブジェクトから削除した後に、他の属性値をグラフオプションとして適用している、という処理が行われていました。なんでそんな感じになっているのかは謎ですが。。。

# AngularJSの中でjqplotを使う時の方法

やはりAngularJSであれば、カスタムディレクティブを作ってその中でjqplot関連の処理を隠蔽したいですよね？Chrome MySQL Adminで作ったカスタムディレクティブを下に晒しておきます。

* [status_graph.html](https://github.com/yoichiro/chrome_mysql_admin/blob/master/app/templates/status_graph.html)

* [status_graph.js](https://github.com/yoichiro/chrome_mysql_admin/blob/master/app/scripts/window/directives/status_graph.js)

何かの参考になれば幸いです。
