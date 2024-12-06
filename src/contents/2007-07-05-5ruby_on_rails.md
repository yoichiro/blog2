---
layout: post
status: publish
published: true
title: "約5時間かかったRuby on Railsの実行環境構築"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 395
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=395
date: '2007-07-05 21:19:22 +0900'
date_gmt: '2007-07-05 12:19:22 +0900'
categories:
- Ruby on Rails
---

ここ数日はJavaからほとんど離れて，Ruby on Railsのお勉強に多くの時間を割いている。確かにRoRがこんだけ騒がれる理由がわかる。railsの内部処理に踏み込むと宇宙な世界だが，railsの上でアプリケーションを書いて単体テストをしている分には，この上ない気持ちにさせてくれる。
しかし，RoRアプリの運用環境構築に5時間も苦戦するとは思わなかった。
RoRの運用環境は，現在では以下のパターンがあるらしい。細かく分ければ，もっと多くの組み合わせがあるだろう。

* apache + mod_ruby

* apache + mod_fastcgi

* apache + mod_proxy + lighttpd

* apache + mod_proxy + mongrel

* lighttpd + FastCGI

* lighttpd + mongrel

* mongrel
まず，mod_rubyを使ったパターンは，「できれば避けた方がいい」的な記述が見受けられたため，即効却下。やはりインストールするソフトウェアやライブラリは最低限な方が好ましいと思うので，2番目のapache + mod_fastcgiのパターンにチャレンジすることにした。
参考にしたページは，以下のページ。
「
[Apache2.2＋fastcgiで Ruby on Rails]()」- pools.jp
途中までは順調だったのだが，mod_fastcgi.soのビルドがどうやっても通らない。もちろん，多くの場所で指摘されているapache2.2系のためのパッチはあてている。しかし，「期待してるのは"{"じゃねーんだよ」とコンパイラに怒られてしまう。Fedora Core 6の64bit版な環境がいけないのか，はたまた何らかのコマンドやライブラリに不備があるのか。いろいろググってみても，コンパイルが通らないという現象に遭遇している人は見つからず，「パッチをあてればうまくいった」という記述ばかり。原因を突き止めてC言語のソースなどを編集する気にもなれず，apache + mod_fastcgiのパターンは断念せざるを得なかった。
次に考えたのが，mongrelの使用。mongrelはrubyを使って書かれているらしく，きっとRoRアプリとの親和性もいい感じに想像できる。しかも，apache + mongrelの組み合わせが最近では最も一般的とのこと。これはいい！と調査を始めた。
参考にしたページは，以下のページ。
「
[MongrelとApache2.2でRails。](http://saikyoline.jp/weblog/2006/08/mongrelapache22.html)」- SaikyoLine.jp
mod_proxyの設定についてちょっと記述量があるが，比較的シンプルなパターンっぽい。しかし，mongrelの起動と停止について，Fedora Core 6で一般的な手法ではなく，あくまで手作業に見えた。

>$ mongrel_rails start -d -p 3000

自分で起動処理なんて行いたくはない。しかし，init.d内に自分で起動用スクリプトを書くだけの知識もなければ元気もない。よって，残念ながらこれも却下。
残ったのが，lighttpdの使用。apacheとlighttpdの組み合わせである。参考にしたページは，以下のページ。
「
[Apacheをプロキシ（Proxy）として用いてRailsをLighttpdで動かす](http://blog.matake.jp/archives/apacherailslighttpd.html)」- 京都の大学院を卒業し、恵比寿で働くWebエンジニアのブログ
上記のエントリでは，lighttpdをソースからインストールしているが，lighttpdはyumを使ってインストールが可能なので，yumを使った。他の手順は全く問題なく進み，あっさりとRoRアプリが動作した。lighttpdはyumインストール時にinit.d内に起動用スクリプトを作ってくれるため，chkconfigで自動起動を登録することができる。よって，マシン再起動時にも余計な手順は必要なし。
ただ，lighttpdユーザのホームディレクトリが/srv/www/lighttpdなのは非常にいただけない。しかも，DocumentRootまでこのディレクトリとなっている。いくらなんでも自己主張し過ぎな感があるのだが，余計なトラブルを起こしたくないため，現状ではそのまま従うことにした。
mod_fastcgiのビルドがなぜダメだったのか，後日再チャレンジしてみたい。わかってしまえばどのパターンもシンプルな構成なのだが，やはりapache + mod_rubyでさっくりと動いて欲しいな，という感想を持った。
ところで，glassfish + JRubyな環境では，RoRの運用形態や配置方法などはどうなるのだろうか。NetBeansが裏でやっていることなはずなので，今後ハックしてみようと考えている。が，もちろんご存知の方がいらっしゃれば，是非コメントなり説明ページのURLなりを寄せて欲しい。
