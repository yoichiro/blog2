---
layout: post
status: publish
published: true
title: Update-Siteから見えるJava5.0の利用状況
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 108
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=108
date: '2005-08-27 22:47:07 +0900'
date_gmt: '2005-08-27 13:47:07 +0900'
categories:
- Java
---

Eclipseの
[翻訳プラグイン](http://yoichiro.cocolog-nifty.com/eclipse/2005/07/ver_111_09c1.html)を自前のサーバで公開している。もちろんUpdate-Siteを立てて，自動インストールができるようにしている。Update-Siteは普通のWebサーバに所定のファイルを置いておくだけの話だが，そこにアクセスしてくるのは，Webブラウザではなく，Eclipse(というかJavaVM)なところが，ちょっと面白い。

Webサーバは，Webalizerによってアクセスログを視覚的に見れるようにしている。Update-Site以外は，今まで何もおいていなかったので，アクセスログはプラグインのインストールのログがほとんどだ。これは
[以前のエントリ](http://blog.so-net.ne.jp/yoichiro/2005-08-02)でも書いたとおり。

さて，今日時点でのアクセスしてきたときのUser-Agentのランキングがこれ。

![](http://www.eisbahn.jp/yoichiro/images/1281037.gif)

Java/1.5.0_04が最も多いのがびっくりである。ソフトウェア開発の現場では，Java 5.0はほとんど使われていない。J2EE対応アプリケーションサーバが，まだJava 5.0に対応していないからだ。まだまだJava 1.4の独壇場だと思っていたのに，上記の結果は完全に予想外である。

EoDを加速するJava 5.0，その広がりは着実なようだ。
