---
layout: post
status: publish
published: true
title: Lomboz&Tomcat5.0の組み合わせ
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 46
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=46
date: '2005-04-10 21:12:09 +0900'
date_gmt: '2005-04-10 12:12:09 +0900'
categories:
- Java
---

EclipseでJ2EE開発を支援するLombozプラグイン。Tomcat5.0.28との組み合わせの場合，LombozがTomcatをうまく起動してくれない。この問題の解決法が以下のサイトに載っていた。

* [How to configure Lomboz 3.0 & Tomcat 5.0](http://forge.objectweb.org/forum/forum.php?thread_id=1028&forum_id=360)

Tomcat 5.0.X用のサーバ定義ファイルに書いてある，「${serverRootDirectory}/bin」を削除すればOK。無事Tomcatを起動できるようになる。
