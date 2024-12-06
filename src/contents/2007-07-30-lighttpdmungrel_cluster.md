---
layout: post
status: publish
published: true
title: "結局lighttpdからmungrel_clusterに移行"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 421
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=421
date: '2007-07-30 21:57:16 +0900'
date_gmt: '2007-07-30 12:57:16 +0900'
categories:
- Ruby on Rails
---

「
[lighttpdで稼働しているrailsアプリでリダイレクトが効かなくなった](http://www.eisbahn.jp/yoichiro/2007/07/lighttpdrails.html)」件だが，結局原因は判明せず。どうもyumの自動更新によってlighttpdがバージョンアップされたらしく，そのタイミングで動作がおかしくなった模様。
apacheとlighttpdの組み合わせは，mongrelの自動機能の設定を行うのが面倒そう，という安易な理由で選んだのだが，やはり世の中はapacheとmongrelの組み合わせが多数派なようである。いつまでも放置していると，ユーザ(とっても目の肥えた数人)に対して非常に恥ずかしく，最悪の場合「Ruby on Railsなんてそんなもんだよね」とか言われてしまうかもしれない。よって，mongrelに運用環境を移行することを決意。
さっそく手順を探していると，とってもよさそうなブログのエントリを見つけた。
「
[mongrel_cluster](http://rubyist.g.hatena.ne.jp/muscovyduck/20070402/p1)」- バリケンのRuby日記

>「mongrel_cluster」は名前のとおり、本当は複数のMongrelを起動して負荷分散とセットで利用することを想定しているみたいだけど、サービスとして登録する機能が便利だから、ひとつしかMongrelを起動しないようなケースでも利用するといいと思うよ。

なんて風に素敵なことが書いてある。ここを参考にして，mongrel_clusterへの移行を行ってみた。
基本的には，ハマることなく順調に移行完了。リダイレクトの不具合も現象は出ないようになった。完璧。
いくつか手順の中で上記エントリと異なる部分があったので，以下にまとめておく。
---
[mongrelを起動するユーザ]
「mongrel_rails cluster::configure」によって生成されるmongrel_cluster.ymlファイルの中に，mongrelを起動するユーザを設定する項目がある。初期状態では"mongrel"ユーザが記述されているが，そんなユーザは作成していないので，"root"に変更した。
直接ファイルを書き換えてしまったが，「--user root」とオプション指定してmongrel_cluster.ymlファイルに反映させるのがセオリーかも。
[relative_rootの指定]
lighttpdでの運用時には，config/routes.rbファイルに「relative_root = '/hoge'」という記述を行うことで，アプリケーションのコンテキストルートを指定するようにしていた。これは，apache側のmod_proxyに関する設定として，

>ProxyPass /hoge/ http://127.0.0.1:3000/hoge/
ProxyReverse /hoge http://127.0.0.1:3000/hoge

という設定を施していたからである。apache側のこの設定を生かしたままmongrel_clusterを正しく動作させるためには，mongrel_cluster.ymlファイルにprefixの指定が記述されなければならない。具体的には，「mongrel_rails cluster::configure」の実行時に「--prefix=/hoge」というオプション指定をしてあげれば良い。
lighttpd時代には，同一ポートで複数のRailsアプリケーションを稼働させるために，上記のようにコンテキストルートをわざわざ記述する方式をとっていた。しかし，mongrel_clusterの場合は，複数Railsアプリを運用したければ，それぞれポート番号を変えて立てれば良く，例えば，

>ProxyPass /foo/ http://127.0.0.1:3000/
ProxyPass /bar/ http://127.0.0.1:3001/

としてあげれば良いため，relative_rootやprefixの指定は無用の産物なのかも。
[Fedora Core 6なので]
上記エントリではDebianが想定されていたが，僕が行ったのはFedora Core 6上だったため，mongrel_clusterの起動スクリプトの登録は，

>chkconfig --add mongrel_cluster_hoge
chkconfig mongrel_cluster_hoge on

で行った。
---
「
[約5時間かかったRuby on Railsの実行環境構築](http://www.eisbahn.jp/yoichiro/2007/07/5ruby_on_rails.html)」で取り上げたように，fastcgiのコンパイルに失敗したり，lighttpdを別途インストールして長ったらしい設定ファイルと格闘したり，という経験をしたわけだが，結論としては「mongrelが楽でいいよ」という結果となった。世間の評判をそのまま受け入れなかった僕の敗北である。悲しい。
これからRuby on Railsを始める方々には，僕のように苦戦しないよう，最初からmongrel_clusterをお勧めしたい。
