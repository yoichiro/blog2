---
layout: post
status: publish
published: true
title: Eclipse on IntelMacにWTPインストール
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 187
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=187
date: '2006-03-18 20:18:24 +0900'
date_gmt: '2006-03-18 11:18:24 +0900'
categories:
- MacBook Pro
---

「
[IntelMacでEclipse](http://www.eisbahn.jp/yoichiro/2006/03/intelmaceclipse_1.html)」でインストールしたEclipseだが，Webアプリケーション開発がほとんどを占める最近では，WTP（Web Tools Platform）が欠かせない。ただし，All-in-oneなWTPは，WindowsとLinuxしか提供されていないため，MacOS XではEclipseを入れた後で別途WTPを入れなければならない。
幸い，WTPはupdate-siteから手軽にインストールすることができた。

[Eclipse Web Tools Platform](http://www.eclipsewiki.net/eclipse/?Eclipse%20Web%20Tools%20Platform) - eclipse wiki
「http://download.eclipse.org/webtools/updates/」のupdate-siteを使えばよい。ここにはWTPに必要なプラグインも一緒においてあるため，このサイトだけでWTPのインストールを完了できる。
OSの違いをほとんど意識せずにすむのも，やはりJavaの威力である。
