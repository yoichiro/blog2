---
layout: post
status: publish
published: true
title: GlassFishがちょっぴり身近になるらしい
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 259
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=259
date: '2006-11-10 13:04:39 +0900'
date_gmt: '2006-11-10 04:04:39 +0900'
categories:
- Java
---

Redhat Linuxの牙城を崩すつもりでいる（だろうと思われる）Canonicalが，Ubuntu（うぶんちゅ？）ディストリビューションにGlassFishをバンドルすることを決定した。
「
[Ubuntu maker to distribute Sun's Java](http://news.com.com/Ubuntu+maker+to+distribute+Suns+Java/2110-7344_3-6133411.html?tag=html.alert)」 - CNET News.com
近い将来，UbuntuをインストールすればGlassFishももれなくついてくる，ということになる。もちろんGlassFishの起動や終了についても，Ubuntuに統合された機構を使って行うことになるだろう。
これ，Redhat LinuxとJBossの関係と全く一緒。Redhat Linuxは，エンタープライズアプリケーションの構築＆運用基盤として，JBossやHibernateなどを取り込んでいる。実際にそのインフラが普及しているかどうかは微妙なところだけど，.Net Frameworkの実装をOSに統合して透過的に提供するというMicrosoftの方向性が，Javaの世界においてLinuxの主要な商用ディストリビュータに波及したと言えるだろう。
UbuntuにGlassFishがバンドルされるようになると，GlassFishのバージョンアップはSynapticやapt-getでできるようになるのだろうか？それだと結構嬉しいかも。
しかし，GlassFishは言うてもCDDLだし，ちゃんとしたサービス運用時に採用されるのはSun Application Serverの方だろう。そう考えると，何か中途半端な感じに思えるのは，自分が何か勘違いをしているからだろうか。。。
