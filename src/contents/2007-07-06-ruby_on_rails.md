---
layout: post
status: publish
published: true
title: Ruby on Railsのテスト環境が気持ちいい
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 396
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=396
date: '2007-07-06 10:49:16 +0900'
date_gmt: '2007-07-06 01:49:16 +0900'
categories:
- Ruby on Rails
---

Javaな世界に10年以上どっぷりと浸かってきたが，Ruby on Rails(RoR)をやっていると「良く考えられているなぁ」と感心してしまう箇所が随所に見られる。とかくScaffoldやActiveRecordに関するコーディングに目が行ってしまいがちだが，僕個人的にはテストに関する環境が最も「おぉ」と感じている。

言うなれば，「JUnit + DbUnit + Cactus」な環境が標準で整備されている，ということだ。

JUnitについては，もちろんTest::Unitがそれに相当する。Test::Unit::TestCaseクラスを継承し，"test_"で始まる名前のメソッドを定義していくというのは，xUnitの流儀とほぼ一致している。JUnitで単体テストを行ったことがある開発者は，違和感なくTest::Unitでテストケースクラスを書いていけることだろう。

Web+DBアプリケーションをJavaで開発した場合，その単体テストは環境面の考慮が正しく行われていないと大変なことになる。何ら準備がなければ，単体テストを途中で諦めなければならないだろう。環境面の考慮とは，多くの場合，データベースやServletコンテナという環境に依存した処理をどうするか，という点である。

Javaでデータベースを扱う処理を単体テストするとき，僕はDbUnitをよく使用する。DbUnitに頼っている機能として，以下のような機能があげられる。

* 初期データをデータベースに投入する。

* テスト対象の処理を実行した後のデータベース内の状態を検証する。

前者の初期データ投入は，地味ではあるが非常に重要な機能である。いちいち各テストメソッドで初期データ投入のためのコードを書くことなく，DbUnitを使えばCSVファイルやXMLファイル，Excelファイルに記述された内容をデータベースに登録してくれる。もちろん投入の直前にデータベース内をきれいに全行削除してから投入を行ってくれる。しかし，本番用のデータベースとテスト用のデータベースを分けて考えなければならず，DbUnitは接続の切り替えまでやってはくれないので，両データベースの整備（主にスキーマ定義の同期取り）と接続情報の切り替えについては，自分で面倒を見る必要がある。DbUnitは自由な反面，さくさくテストができるまでには前準備が毎回必要となる。

RoRでは，このDbUnitが標準で搭載されているという感じになっている。特に初期データ投入機能については，fixturesという機構が提供されている。railsコマンドでプロジェクトファイル一式を作成した時には，すでにfixtures機能は使用可能な状態になっている。fixturesでは，

* test/fixturesディレクトリ内に，YAMLまたはCSVファイルで初期データの内容を定義しておけば，それを各テストメソッドの実行直前にデータベースに投入してくれる（"fixture :users"と書くだけ！）。

* YAMLまたはCSVファイルで記述された内容は，テストメソッド内で簡単に参照でき，assertに利用可能（"users(:user1)"と書くだけ！）。

が実施可能となる。素晴らしい。

例えば，users表があったとすると，YAMLでは以下のように初期データを記述することができる。

>user1:
  id: 1
  name: Yoichiro Tanaka
  password: pass
user2:
  ・・・


そして，Userモデルクラスに対するテストコードは，以下のようになるだろう（間違っていたらコメントPlease!）。

require File.dirname(__FILE__) + '/../test_helper'
class UserTest < Test::Unit::TestCase
fixtures :users
def test_user_1
assert_equal(1, users(:user1).id)
assert_equal('Yoichiro Tanaka', users(:user1).name)
・・・
end
end

さらにRoRでは，データベースの表定義の世代管理（migration）が可能となっている。このmigrationは，表定義だけでなく，表に格納された行の内容についても対応することが可能である。例えば，ある時点で「Users」という表に，"leave"という退会フラグの列を追加したいとする。その場合，

class AddColumnLeave < ActiveRecord::Migration
def self.up
add_column :users, :leave, :boolean, :null => false
User.find_all.each do |user|
user.update_attribute('leave', false)
end
end
def self.down
remove_column :users, :leave
end
end

というファイルを「003_add_column_leave.rb」という名前で作成しておけば，RoRのrakeコマンド（antのようなもの）で，

>rake db:migrate VERSION=3


と実行することで，users表にleave列が追加され，すでに存在していた行全てのleave列にfalse値を入れることが可能になる。同時に，self.downメソッドにleave列の削除処理を記述しておけば，

>rake db:migrate VERSION=2


とバージョンダウンさせた際に，自動的にleave列を削除できるようになる。これも素晴らしい機能であり，実案件に即適用したいものである。

開発用のデータベースと単体テスト用のデータベースの表定義の同期については，今までではエクスポート＆インポートで移行したり，DDLファイルを配って各自最新にしておく，などの工夫をしなければならなかった。しかし，RoRであれば，

>rake db:test:clone_structure


と打つだけで，自動的に同期を取ってくれる。

マイグレーションのためのコードを書く際に，create table文などのSQLについて，以下のようにRubyの構文で記述できるのはホントに驚きである。もちろん先の列追加の例に関しても，SQLのalter table文を使うところを，add_columnメソッド（マクロ?）の呼び出しで行っているのがわかるだろう。

class CreateUsers < ActiveRecord::Migration
def self.up
create_table :users do |t|
t.column :name, :string
t.column :password, :string
end
end
def self.down
drop_table :users
end
end

設定をしているのか，定義を書いているのか，処理を記述しているのか，わからなくなる感覚を味わうことができる。

Webのリクエストやレスポンスを扱うControllerのテストも，かなりいけている。例えばWicketだとその辺のテストに対するサポートが結構充実していて，イベントを発生させる（＝リクエストをシミュレートする）ことや，それに対して発生したページ遷移などのアサーションをコーディングすることができる。下記のコードでは，submit()メソッドの呼び出しで，フォームのサブミットをシミュレートできる。

class HogeTest extends WicketWebTestCase {
・・・
public void testSimple() throws Exception {
beginAt("/hoge");
assertTitleEquals("Create User");
setFormElement("name", "yoichiro");
submit();
assertTitleElement("Confirm");
・・・
}
・・・
}

ではRoRではどうかというと，同じようなテストをControllerに対して行うことが可能だ。しかも，特別に何もライブラリのインストールなどを行う必要がない。

・・・
class HogeControllerTest < Test::Unit::TestCase
fixtures :users
・・・
def test_update_act
post :update, :id => 1, :user => {:name => 'yoichiro'}
user1 = User.find(1)
assert_equal 'yoichiro', user1.name
assert_redirected_to 'users/list'
end
end

フォームに対してPOSTでサブミットすることをシミュレートし，結果として行われたビジネスロジック（上記ではUserモデルの更新を想定）の結果を検証し，listビューにリダイレクトされたことを最後に確認しているコードである。非常に直感的だ。

Javaにおいて，上記のようなテスト環境を作リ出すことは，非常に限られた人間でしか行えないだろう（ま，そのおかげで良い飯が食えている人がいるのだが）。しかし，RoRでは，全く苦労することなく，上記のようなテスト環境を手に入れることができる。そして，その環境がRoRな世界では当たり前であり，全員が同じ知識ベースの元にテストを行うことができる。これって実は素晴らしいことであり，もっとRoRの紹介で取り上げられてもいいことなのではないか，と感じた。ちょっと前までは「RoRなんてマスタメンテ生成ツールでしょ？」なんて言っていた自分が恥ずかしい。十分規模の大きな開発でも威力を発揮する環境ではないかと。むしろ，関わる人数が多くなれななるほど，その効力が良い方に大きく作用することになるのではないかと。若干認めたくない気持ちが残っていることは事実なのだが，これは認めざるを得ない。

JavaEEやStruts+SpringFramework+Hibernateなどに代表されるフルスタックを構成するOSSの組み合わせだけでは，RoRが提供してくれる環境には程遠い。

そんなわけで，しばらくはRoRな生活を楽しもうと目論んでいる今日この頃である。
