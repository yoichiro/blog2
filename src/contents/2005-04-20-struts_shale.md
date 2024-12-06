---
layout: post
status: publish
published: true
title: Struts Shaleって？
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 48
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=48
date: '2005-04-20 10:11:49 +0900'
date_gmt: '2005-04-20 01:11:49 +0900'
categories:
- Shale
---

Strutsのバージョン２は当然Struts2かと思いきや，「そりゃ２じゃないだろ」と猛反発を食らったために「
[Struts Shale](http://wiki.apache.org/struts/StrutsShale)」となったらしい。このStruts Shale，JSFとの結びつきの強いものになるそうだ。* [JSF(JavaServer Faces)【.NET死亡？！！！】](http://pc8.2ch.net/test/read.cgi/tech/1059208396/286) by 2ch
まぁ作者が一緒ということで，JSFとStruts Shaleでうまく分業体制をとっていくという方向性らしい。Struts Shaleの具体的なサポートとして，

* [ベストフレームワークを使わない理由](http://izu.shinzui.org/comments/start/2005-03-24/1) by izu＠San Francisco
を見ると，AJAXやJSFや戻るボタン対応など，興味を引く単語がならんでいる。
さて，ざっとStruts Shaleでググってみると，どうやら「
[Commons-Chain](http://jakarta.apache.org/commons/chain/)」が重要のようだ。Chain of Responsibilityパターンを使って複雑な処理フローを組み立てることをサポートするライブラリみたい。
Struts Shaleを理解するには，まずはCommons-Chainを理解するところからはじめる必要がありそうだ。
