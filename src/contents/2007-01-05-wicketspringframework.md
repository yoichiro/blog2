---
layout: post
status: publish
published: true
title: WicketとSpringFrameworkの連携
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 307
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=307
date: '2007-01-05 09:44:17 +0900'
date_gmt: '2007-01-05 00:44:17 +0900'
categories:
- Wicket
---

WicketはPOHPのソリューションとして非常に素晴らしいが，やはりUIに対するライブラリであり，ビジネスロジックに関してはDIコンテナを採用してAOPなどの恩恵を受けることが今日の開発スタイルにマッチすると考えられる。

Wicketでは，DIコンテナであるSpringFrameworkと連携するための2つの方法を提供している。

* ApplicationオブジェクトにDIコンテナ管理下のオブジェクトをセッターインジェクションして，各WebPageオブジェクトからgetして利用する。

* 各WebPageオブジェクトにDIコンテナ管理下のオブジェクトをフィールドインジェクションして利用する。

1つ目の方法は，ApplicationクラスにDIコンテナ管理下のオブジェクトへの参照が集中してしまうため，一般的なDIの利用手法とは違った感じになる。使いたいときにそこ（＝インスタンスフィールド）にある，という形態ではなく，いちいちApplicationオブジェクトから取得処理を行わなければならないため，感覚的にはDIコンテナを自分でルックアップするのと変わらない。

2つ目の方法は，インジェクション対象のインスタンスフィールドに@SpringBeanアノテーションを記述することで連携を実現する手法となる。実際には，オブジェクトがインジェクションされてしまうわけではなく，実行時にはプロキシオブジェクトが間に入り，利用の度にDIコンテナから対象オブジェクトをルックアップする動作となる。そのために，WebPageインスタンスのライフサイクルに関係なくSpringFrameworkとの連携が可能となっている。

ここでは，2つ目の方法を採用し，そのために必要な事柄を説明することにしよう。

@SpringBeanアノテーションを使うためには，Wicketのコアライブラリでは足りず，SpringFramework連携用の以下のライブラリが必要となる。

* wicket-spring

* wicket-spring-annot

* spring

maven2を利用している場合は，wicket-spring-annotのみを依存ライブラリとして指定しておけば，自動的にwicket-springやSpringFramework自体のライブラリも依存ライブラリとして含まれるようになる。具体的には，以下のように記述すれば良い。

wicket

wicket-spring-annot

1.2.3

最新バージョンは1.2.4なのだが，pom.xmlファイルに記述ミスがあるらしく，maven2の実行時にエラーが生じてしまうため，1.2.3を指定するのが現状では良いだろう。

次に，Applicationクラスのサブクラスにて，@SpringBeanアノテーションを処理してくれるSpringComponentInjectorオブジェクトを登録する処理を記述する。登録には，Application#addComponentInstanciationListener()メソッドを使用する。登録する場所は，init()メソッドをオーバーライドして，その中で行うのが良いだろう。

public class HogeApplication extends WebApplication {
@Override
protected void init() {
super.init();
addComponentInstanciationListener(new SpringComponentInjector(this));
}
}

Wicket側に対する準備はこれで完了。次にSpringFramework側の準備に取り掛かる。先ほど登録したSpringComponentInjectorクラス内では，サーブレットコンテキストからSpringFrameworkが提供するWebApplicationContextオブジェクトを取得する処理が行われる。つまり，SpringFrameworkが提供するContextLoaderListenerクラスを使用して，WebApplicationContextオブジェクトをサーブレットコンテキストに登録しておけば良い。ContextLoaderListenerオブジェクトは，web.xmlファイルに記述しておくことにより実行される。



org.springframework.web.context.ContextLoaderListener



ContextLoaderListenerクラスでは，/WEB-INF/applicationContext.xmlファイルが初期値として採用される。applicationContext.xmlファイルは，Wicketに依存した記述というものはない。インジェクションしたいオブジェクトをbean要素を使って普通に記述すれば良い。



以上で準備完了である。上記のコードで定義したDIコンテナ管理下のhogeオブジェクトを，あるWebPageクラス内で使用する場合のコード例を，以下に示す。

import wicket.spring.injection.annot.SpringBean;
public class HogePage extends WebPage {
@SpringBean(name="hoge")
private Hoge hoge;
public HogePage() {
Form form = ...;
form.add(new Button("btnHoge") {
protected void onSubmit() {
btnHogePressed();
}
} );
}
private void btnHogePressed() {
hoge.foo();
setResponsePage(BarPage.class);
}
}

@SpringBeanアノテーションは，name属性により，ルックアップするDIコンテナのbean名を指定することができる。hogeインスタンスフィールドに@SpringBeanアノテーションを付与することにより，btnHogePressed()メソッド内でhoge.foo()メソッド呼び出しを行った際には，DIコンテナからhogeオブジェクトが取得され，foo()メソッドの呼び出しがデレゲートされる。

あとは，applicationContext.xml内でAOPをかけるなりインジェクションをネストするなり，DIの恩恵を思う存分受ければ良い。単体テスト用にモックオブジェクトを利用することを考える場合は，hogeインスタンスフィールドのスコープをprotectedやpackage privateにすることを考えれば良いだろう。もちろんリフレクションを使用するのもありだと思う。

WicketにおけるSpring Frameworkとの連携は，コンポーネントのライフサイクルが開発者次第となってしまうことと，コンポーネントがHttpSessionに格納されてしまうことにより，実現方法にちょっとした議論が起きた。しかし，JDK1.5の登場により，スマートな決着がついたといえるだろう。

ちなみに，もちろん今回紹介した方式はJDK1.5以上が必要である。Java2SE1.4でWicketを使用する場合は，Applicationクラスに対してインジェクションする方式のみが選択肢となる。
