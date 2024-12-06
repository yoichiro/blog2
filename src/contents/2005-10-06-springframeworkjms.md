---
layout: post
status: publish
published: true
title: SpringFrameworkあーんどJMS
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 116
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=116
date: '2005-10-06 17:25:37 +0900'
date_gmt: '2005-10-06 08:25:37 +0900'
categories:
- Java
---

次案件では，後ろに構えるシステムとのやり取りにJMS(Java Messaging Service)を使用する予定だ。もちろんそこにはトランザクションも挟める必要がある。つまり，あるデータベースへのアクセスと，MOM(Message Oriented Middleware)との通信を，一つのトランザクションとして束ねなくちゃいけなくて，抽象化したり，AOPでトランザクションをかけたり，といったことをしたくなってくる。

そこでやっぱり有効なのはDIコンテナだ。DAOだろうがMOMだろうが，とにかくインタフェースを規定して実装をInject，トランザクションをAOPでかませる，という流行の手法である。

SpringFrameworkは，バージョン1.1からJMSをサポートしている。実際にはどんな感じになるのかを調べていたら，とっても素敵なエントリを発見した。

[MBeans!](http://d.hatena.ne.jp/MBeans/)

* [[Spring] JMS その１](http://d.hatena.ne.jp/MBeans/20050706)

* [[Spring] JMS その２](http://d.hatena.ne.jp/MBeans/20050708)

* [[Spring] JMS その３](http://d.hatena.ne.jp/MBeans/20050713)

* [[Spring] JMS その４](http://d.hatena.ne.jp/MBeans/20050714)

「な」がすべて「ニャ」に変わっているので，読むのになかなか慣れが必要。しかし，一旦慣れてしまえば，全く苦にならない。

JMSが絡むシステムの場合，もちろん将来的な成長を見据えて，何らかのESBを適用しておく(ESBにJMSを隠蔽させる？)のも手なのかな。つまり，他システムとの連携が発生するシステム構築においては，SOAとして認知できるまでの，業務的な，そしてソフトウェア的な手法を「とりあえず」適用しておくのが主流になるのだろうか。。。

でも，その対象は，ある業務処理を扱うために叩く必要があるバックシステムが複数台存在しないと意味がないのかな。やっぱり単にバックオフィス1台だけを相手にするフロントのシステム構築でESB適用は，，，やり過ぎだよニャ。
