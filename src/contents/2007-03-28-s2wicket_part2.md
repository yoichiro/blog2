---
layout: post
status: publish
published: true
title: "次期S2Wicketではこうなります Part2"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 348
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=348
date: '2007-03-28 09:37:28 +0900'
date_gmt: '2007-03-28 00:37:28 +0900'
categories:
- Wicket
---

[前のエントリ](http://www.eisbahn.jp/yoichiro/2007/03/s2wicket.html)で，
[S2Wicket](http://s2wicket.sandbox.seasar.org/)の今後の方向性として，以下のコードを示した。

public class HelloWorldPage extends WebPage {
@WicketModel
private LabelModel helloModel;
@WicketComponent
private Label hello;
@WicketComponent
private Link link;
@SeasarComponent
private HelloService helloService;
public void onClickLink() {
helloModel.setHello(helloService.say());
}
}

前回までの構想では，LinkコンポーネントのイベントハンドラとなるonClickLink()メソッドを実装する必要があった。上記の場合，イベントハンドラ内では，helloServiceオブジェクトのsay()メソッドを呼び出し，その結果をhelloModelオブジェクトのhelloプロパティにセットする，というだけの処理を行っている。何かを行う，という点ではメソッドを作成することは自然と言えば自然だが，イベント処理すらも宣言的に記述したくならないだろうか？

S2Wicketでは，Wicketコンポーネントのイベント発生時に行いたい処理を，さらに簡潔に記述できるようにする。

public class HelloWorldPage extends WebPage {
@WicketModel
public LabelModel helloModel;
@WicketComponent
private Label hello;
@WicketComponent(actions={
@WicketAction(method="onClick",
exp="helloModel.hello=helloService.say()")})
private Link link;
@SeasarComponent
public HelloService helloService;
}

見ての通り，フィールド定義のみになる。その分，使用するアノテーションが派手になっている。

@WicketComponentアノテーションの中に，@WicketActionアノテーションを記述できるようにした。@WicketActionアノテーションは，method属性にイベントハンドラのメソッド名を，exp属性に実行したい処理をOGNL式で記述する。@WicketActionアノテーションを使用すれば，実行時にmethod属性で指定したメソッドが呼び出されたとき，S2Wicketはexp属性に書かれたOGNL式を評価する。

多くの場合，プレゼンテーション層で行う処理といえば，ビジネスロジック層の呼び出しとその結果の表示用モデルへのマッピングだろう。S2Wicketは，そのような単純なことを，単純な記述で可能にする。

さらに「Javaらしさが失われた」S2Wicket，あなたはどう感じるだろうか？ (^^;
