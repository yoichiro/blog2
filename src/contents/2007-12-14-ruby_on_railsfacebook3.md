---
layout: post
status: publish
published: true
title: Ruby on Railsでもfacebookアプリは作れます(3)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 484
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=484
date: '2007-12-14 10:37:46 +0900'
date_gmt: '2007-12-14 01:37:46 +0900'
categories:
- facebook
---

かなりもったいぶっている「Ruby on Railsでもfacebookアプリは作れます」シリーズだが、やっと今回からrfaceookプラグインを使って実際にfacebookアプリを作る話をしてみようと思う。

rfacebookプラグインを使ったRuby on Railsでのfacebookアプリの作り方は、
[前のエントリ](http://www.eisbahn.jp/yoichiro/2007/12/ruby_on_railsfacebook.html)でも紹介した通り、

* [RFacebook Quick Start Guide for Rails](http://rfacebook.rubyforge.org/quickstart.html)

* [Using Ruby on Rails with Facebook Platform](http://wiki.developers.facebook.com/index.php/Ruby_on_Rails) - Facebook Developers Wiki

にて紹介されている。基本的には、上記に書かれている手順を踏めば良い。とはいえ、僕は最近JRubyがお気に入りなので、JRubyのインストールから順に説明していきたいと思う。もちろん、MySQLのインストールは完了していることが前提。

まず、以下の場所から、JRury1.0.2のアーカイブをダウンロードし、任意の場所に展開する。

・
[jruby-src-1.0.2.tar.gz](http://dist.codehaus.org/jruby/jruby-src-1.0.2.tar.gz)

展開後のディレクトリに移動してantを実行(antがない人は
[apache-ant](http://ant.apache.org/)から入手)すれば、jrubyコマンドを得ることができる。あとは、

* 環境変数JRUBY_HOMEに展開したディレクトリを指定する。

* 環境変数PATHに「$JRUBY_HOME/bin」を追加する。

の2点を施せば、JRubyの準備が完了する。あ、言い忘れたが、
[JDKの入手とインストール](http://java.sun.com/javase/downloads/?intcmp=1281)が大前提。

次に、JRubyに付属しているgemを使って、Railsをインストールする。この際、先週リリースされたRails2.0はrfacebookプラグインが正しく動作しないために、Rails1.2.6をインストールすることにする。その後、他に必要となるものも、順次インストールする。

>jruby -S gem install -v=1.2.6 rails --include-dependencies
jruby -S gem install jruby-openssl
jruby -S gem install activerecord-jdbc


特に最近はRails2.0のリリースがあったために、サーバにアクセスが集中していることが多い。その場合404などが返されるので、何度か繰り返してみて欲しい。

さて、いよいよrfacebookのインストールだ。やはりgemを使ってインストールすることができる。

>jruby -S gem install rfacebook


途中で「hpricot」のインストールを行うか聞かれるので、「Y」をタイプしてインストールを指示し、さらにプラットフォームを聞かれるので、「1」のjrubyを入力する。引き続き、「mocha」のインストールを行うかも聞いてくるので、やはり「Y」をタイプしてインストールを指示する。その後、rfacebookのインストールが行われる。上記であれば、rdocも一緒にインストールされるはずだ。

これでgem系の準備は完了。次は、実際にfacebookアプリケーションの開発に移る。何はともあれ、railsコマンドでプロジェクトを作成しよう。

>jruby -S rails fb-test


これだけだと、普通のrailsアプリである。このプロジェクトに、rfacebookプラグイン(正確にはrfacebook_on_railsプラグイン)を導入する。

>cd fb-test
jruby ./script/plugin install svn://rubyforge.org/var/svn/rfacebook/plugins/rfacebook_on_rails


上記のコマンドを打てば、Subversionサーバから勝手に一式ダウンロードしてくれるのだが、例えばNetBeansを使って開発している場合は、svn://が使えない。その場合は、
[ここ](http://rfacebook.rubyforge.org/rfacebook_on_rails.zip)からダウンロードできるzipファイルを、vendor/plugins/rfacebook_on_railsディレクトリ以下に展開すれば同じことになる。

もうひとつ準備が必要になる。rfacebookプラグインをインストールすると、facebook:setupというrakeタスクが追加されるので、これを実行しておく。実行が完了すると、config/acebook.ymlというファイルが生成される。このfacebook.ymlファイルに、facebookアプリケーション固有の設定を記述することになる。

>jruby -S rake facebook:setup


さて、
[前のエントリ](http://www.eisbahn.jp/yoichiro/2007/12/ruby_on_railsfacebook.html)で、僕は以下のようなことを言っている。

>しかし、非常に残念なのだが、上記URLではRFacebookはちゃんと動いてくれない。RFacebookのForumで「facebook APIの脆(もろ)さは、PHP、ASP、そしてJavaコミュニティに感じる脆さなんだよ」といった過激な発言がでるくらいの原因で不具合が出てしまうのだが、これの解決策はあるのでご安心を。


rfacebookの現在のバージョン(0.9.7)では、facebookとの認証処理後にfacebookから送られているリクエストの2発目以降についてちゃんとパラメータを解釈してくれない、という不具合が潜んでいる。これはrfacebook開発側から言うと「facebookが送ってくるパラメータが不安定」という言い方がされているのだが、何はともあれ動かない。

この問題を解決するための方法が、forumに投稿されている。

[more facebook sessions problem](http://jypsie.com/RFacebook/forums/RFacebook-Help/topics/more-facebook-sessions-problem-) - Ruby on Rails Facebook

このページの2007年10月26日付けのcocotron氏による投稿(今日時点でページの一番下)に、パッチのコードが掲載されている。これを、fb-test/app/controller/application.rbファイルに記述する。

# Filters added to this controller apply to all controllers in the application.
# Likewise, all the methods added will be available for all controllers.
class ApplicationController < ActionController::Base
# Pick a unique cookie name to distinguish our session data from others'
session :session_key => '_bermuda_session_id'
end
# ここから
module RFacebook
module Rails
module ControllerExtensions
・・・
end
# ここまで

これにより、通常のfacebookからのGET, POSTリクエスト(Ajax含む)の処理が、rfacebookプラグインで正しく行われるようになる。

最後に、facebook.ymlファイルへの設定方法を紹介しておく。facebook.ymlには、rfacebookプラグインが動作をするために最低限以下の設定を記述しておく必要がある。development, test, そしてproductそれぞれ設定項目は一緒である。

* key: facebookから割り当てられたAPI Keyを記述する。

* secret: facebookから割り当てられたSecretを記述する。

* canvas_path: アプリ登録時に入力したCanvas Page URLの入力した部分(「http://apps.facebook.com/★/」の★)を、前後に「/」を付けて記述する。

* callback_path: アプリ登録時に入力したCallback Urlのコンテキスト部分(例:「http://www.eisbahn.jp:3000/★/」の★)を、前後に「/」を付けて記述する。

API KeyとSecretに関しては、facebookのDeveloperアプリケーションの右上にある「My Applications」区画の「See My Apps」リンクをクリックすると出てくるアプリケーション一覧ページで確認することができる。もしまだアプリケーションを登録していない方は、
[前のエントリ](http://www.eisbahn.jp/yoichiro/2007/12/ruby_on_railsfacebook2.html)を参考にして欲しい。

以上で準備は完了。次は、ControllerとViewを作成して、facebookアプリが稼働することを確認してみたいと思う。

--- 2007/12/14 追記

rfacebookのパッチコードをActionControllerクラスの中に記述すると再定義されないため、ActionControllerクラスの外にパッチコードとなるRFacebookモジュール定義を記述するように、蒸気コードを変更しました。
