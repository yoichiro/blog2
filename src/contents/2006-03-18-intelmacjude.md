---
layout: post
status: publish
published: true
title: IntelMacでJudeを動かす
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 185
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=185
date: '2006-03-18 10:02:19 +0900'
date_gmt: '2006-03-18 01:02:19 +0900'
categories:
- MacBook Pro
---

Eclipseと並んで，僕のJava開発で欠かせないモデリングツールであるJude。
[Judeのホームページ](http://jude.change-vision.com/jude-web/support/faq.html#q5)では，保証外とはいえ，MacOS Xでの動作について記述がされている。もともとJavaで記述されているので，基本的には問題ないはずだ。
まずは実際にやってみた人がいるかどうか調査。以下のページに，JudeのインストールとJude.appファイルの作り方まで記述があった。

[Judeインストール](http://d.hatena.ne.jp/fender/20041106) - DebianなPowerbookでDarwinと遊ぶ
ポイントは，起動のためのシェルスクリプトのJUDE_HOME変数の変更だろう。そして
[Platypus](http://mac.softpedia.com/get/System-Utilities/Platypus.shtml)ツールによって，ターミナルを扱うことなく，Judeを起動できるようになる。いい感じだ。
