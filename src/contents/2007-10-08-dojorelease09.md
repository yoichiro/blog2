---
layout: post
status: publish
published: true
title: dojo-release-0.9を見てみた
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 453
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=453
date: '2007-10-08 19:23:05 +0900'
date_gmt: '2007-10-08 10:23:05 +0900'
categories:
- dojo
---

今年も気がつくともう10月。振り返ってみると，dojoを使う機会がすごく多くなった。とはいえ，使用しているのはdojo-0.4.2とか0.4.3。そろそろdojo-1.0が登場する予定なこともあって，0.9を見ておかないといけないぞ，と。dojo使いまくりの
[こみゅすけ](http://commusuke.eisbahn.jp/)の起動速度が遅くなってきているのも気になるので，dojo-release-0.9を試してみることにした。
0.4.3から0.9の差分を中心として，ぱっと見で気になった点を以下にまとめてみる。

* dojo一個だったのが，dojo, dojox, dijit, utilと４分割されている。

* テーマが導入された結果，Widget毎だったCSSファイルが0.9では統一されている。

* dojo.widget.defineWidget()でWIdgetクラスを定義していたが，0.9からはdojo.declare()を使う。

* WIdgetの親クラスとしてHtmlWidgetを指定することが多かったが，0.9からは以下のクラスを組み合わせて親クラス指定をする。

* dijit._Widget

* dijit._Templated

* dijit._Container

* dijit._Contained

* dijit.layout._LayoutWidget

* dijit.form._FormWidget

* dijitには「Window Systemを目指しているわけではない」という理由で，FloatingPaneが含まれていない（dojoxに含まれている）。

* Google Map，Yahoo Mapは，dhartingのPortingのあとに，dojox.geoとして作成をするつもりらしく，0.9では含まれていない。

* Widgetを使用する際に，createWIdget()を使うのではなく，0.9からはWidgetクラスをnewするだけで良い。
特にカスタムWidgetを作る際の手順が，0.9でかなり変わっている。特に「親クラスとして何を指定するか」や「CSSをどこで定義すればいいか」，「テーマを使えるようにしておくにはどうすればいいか」などが，ぱっと見では見えてこない。
勉強して今後レポートしていくつもりである。
