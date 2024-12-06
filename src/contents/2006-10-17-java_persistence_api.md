---
layout: post
status: publish
published: true
title: Java Persistence API時代到来！
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 245
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=245
date: '2006-10-17 10:49:12 +0900'
date_gmt: '2006-10-17 01:49:12 +0900'
categories:
- Java
---

Javaの世界の中で老舗かつ最も使われているであろうO/Rマッピングライブラリの
[Hibernate](http://www.hibernate.org/)が，3.2にバージョンアップした。

[Red HatのJBoss，Java Persistenceに対応したORMソフトの新版「Hibernate 3.2」](http://itpro.nikkeibp.co.jp/article/USNEWS/20061017/250798/) - ITpro
このリリースにより，HibernateをJava Persistence APIに従って使用することが可能となる。これは，今までHibernateやiBATIS，Toplinkなどのどれを採用するか悩み，どのライブラリのAPIを勉強すべきなのかといった非常に困難な判断から，開発者を解放してくれることを意味するだろう。あくまで個々のO/Rマッピングライブラリのプロダクトは，Java Persistence APIのプロバイダという位置付けという時代が到来した。もちろん個々のプロバイダを完全に意識しなくて良くなるとは言えないが，実装者や基盤を担うアーキテクトに対しては，負担を軽減してくれるに違いない。
現在は，無数にあるOSSを組み合わせて，一つのアーキテクチャを構築している状態である。OSSは個別に独自のAPIを持つために，実装者は毎度毎度プロジェクトが変わるたびにAPIを勉強しなければならない状態。今回のHibernateのJPAプロバイダ対応に対して，今後はよりJavaEE5の標準APIの重要性が高まってくるだろうと感じた。OSSの組み合わせを「開発のし易さ」で判断するのではなく（JavaEE APIによって開発のし易さがある程度均一化されるだろうから），「性能面」などの実行時の能力面で判断する時代が近づいてきているのかも知れない。
