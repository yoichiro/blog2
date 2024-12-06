---
layout: post
status: publish
published: true
title: Commons-Langはオツだねぇ
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 17
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=17
date: '2005-03-18 01:33:06 +0900'
date_gmt: '2005-03-17 16:33:06 +0900'
categories:
- Java
---

ちょっとしたことでも「便利だなぁ」と思うことは多々ある。決まりきったコーディングはホントに面倒だ。このようなときに，
[JakartaのCommonsシリーズ](http://jakarta.apache.org/commons/index.html)はとっても役に立つ。

自作したクラスのインスタンスをコレクション，例えばSetコレクションに格納したいとき，内容が同じものが既に登録されているかどうかを判断するために，equalsメソッドをオーバーライドする。この中で，２つのインスタンスが持つ値を比較する処理は，決まりきったものであり，実にめんどくさい。比較する値がプリミティブでない場合は，null値を意識して条件判断を行わなければならないからだ。

　　public boolean equals(Object obj) {

　　　　if (!(obj isntanceof Hoge)) {

　　　　　　return false;

　　　　} else {

　　　　　　Hoge target = (Hoge)obj;

　　　　　　String targetName = target.getName();

　　　　　　if (name == targetName) {

　　　　　　　　return true;

　　　　　　} else if (name == null || targetName == null) {

　　　　　　　　return false;

　　　　　　} else {

　　　　　　　　return name.equals(targetName);

　　　　　　}

　　　　}

　　}

上記はequalsメソッドの中で，nameプロパティの比較によりオブジェクトの等価を判断しているコードである。こんな処理が，プロパティが複数になったり，クラス数が多かったりしたら・・・面倒なことこの上ない。もちろん，staticなユーティリティメソッドを作成して解決すればよいが，
[Commons-Lang](http://jakarta.apache.org/commons/lang/)がまさに上記コードをやってくれる。

　　public boolean equals(Object obj) {

　　　　if (!(obj isntanceof Hoge)) {

　　　　　　return false;

　　　　} else {

　　　　　　Hoge target = (Hoge)obj;

　　　　　　return new 
**EqualsBuilder**
().
append(name, target.getName()).
isEquals();

　　　　}

　　}

if文がなくなった分，すっきりしたコードになる。EqualsBuilderを使うことで，nullを意識しなくても２つの値が一致するかどうかを判定することができる。さらに，appendメソッドを連続して呼び出すことで，複数の値の比較にも簡単に対応できる。

プログラミングは単純であればあるほど良い。ちょっとしたことでも楽をして品質の向上に努めたいものである。
