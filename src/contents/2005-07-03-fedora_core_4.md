---
layout: post
status: publish
published: true
title: Fedora Core 4 導入!
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 84
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=84
date: '2005-07-03 00:48:03 +0900'
date_gmt: '2005-07-02 15:48:03 +0900'
categories:
- My PC environment
---

VAIOで運用してきたFedora Core 3だが、インストール時のパッケージの選択に失敗したらしく、POPサーバのdevocotがどうしてもインストールできなかった。うーん、どうしたものかと悩んだ末、せっかくなのでFedora Core 4にアップグレードすることにした。

Fedora Core 4のCDイメージをGetし、さくっとまずはインストール開始。インストーラの画面や操作は、Fedora Core 3と全く一緒だった。面白いのは、Java関連のパッケージ(EclipseとかTomcatとか)がやけに充実していること。まぁ、一個も選択しなかったけど。

インストールはさくっと終了し、あとは各種ソフトウェアの設定を開始。以下、自分勝手な覚え書き。

『bind』 バックアップしておいた各種ファイル(named.confなど)を復活させて終了。問題なし。基本的には、「
[外部／内部向けDNSサーバー構築(BIND)](http://fedorasrv.com/bind-wan.shtml)」を参考にした。

『postfix&dovecot』 「
[メールサーバー構築(Postfix+Dovecot)](http://fedorasrv.com/postfix.shtml)」を見ながら設定。なんとか問題無く動き出した。

『VNC』 これもバックアップしておいたxinetd用の設定ファイルを復元して、gdmの設定も変更（「
[Linux graphical login using VNC](http://nkozawa.hp.infoseek.co.jp/wikiout/xloginbyvnc.html)」、「
[VNSでリモートデスクトップ](http://www.liris.org/technology/linux/vnc)」を参照した）。

と，ここでトラブル発生。いくらvncviewerで接続しようとしても，はじかれてしまう。設定ファイルなどを見ても，特に問題はない。試しに，ユーザごとにXvncを立ち上げて接続してみると，ちゃんと動く。30分ほどいろいろ見て回った結果，大変なことに気がついた。

xinetdがない！！おまけに，inetdすら動いてない。。。

速やかに「yum install xinetd」でxinetdをインストールしたら，さくっとvnc接続できた。恐るべしFedora Core 4（パッケージの選択でxinetdなんてなかったのに･･･）。

『CVS』 「cvs -d /var/cvsrep init」でリポジトリを作成し、バックアップしておいた内容をそこに展開、xinetd用のcvs pserver起動用ファイルを設置して、準備完了。と思いきや、Eclipseからupdateしてみると、権限がないというエラーになってしまった。これは、cvsusersグループを作成して、/var/cvsrepディレクトリ内をcvsusersグループに対して書き込み許可に設定。これでOKになった。

とりあえず最低限必要な内容は完了。あとはゆっくり育てていくことにする。それにしても、まさかxinetdがインストールされない状態になるとは思ってもみなかった。今後問題が出ないか心配だ。。。
