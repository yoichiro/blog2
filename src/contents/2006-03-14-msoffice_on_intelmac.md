---
layout: post
status: publish
published: true
title: MS-Office on IntelMac
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 182
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=182
date: '2006-03-14 22:49:34 +0900'
date_gmt: '2006-03-14 13:49:34 +0900'
categories:
- MacBook Pro
---

購入したMacBook Proだが，やはりOffice文書を扱うことができないと不便でならない。そこで，Office文書の読み書きを行うべく，いろいろと調査を始めた。
そもそも，Apple Storeで本体購入と一緒にMS-Office for Macも買っちゃおうと考えていたが，金額も結構高く，フリーのものも使えるかもしれないと店員さんも話していたため，購入は見送っていた。
そのフリーのアプリケーションこそが，Open Officeだ。しかし，そもそもOpenOfficeをMacで動作させるには，X Window Systemをインストールしないといけないらしい。さらに，日本語の入力で難ありって感じっぽいので，ちょっとOpenOfficeはパス。
調べていくと，どうやらOpenOfficeのMac専用版であるNeoOfficeは，マルチプラットフォームを売りにしているJavaが何故か足かせになっているらしく，残念ながらIntelMacでは動作しないらしい。

[OpenOffice.orgのMacネイティブ版「NeoOffice 1.2」- Intel Mac非対応](http://pcweb.mycom.co.jp/news/2006/02/03/346.html)
夏くらいまで待たないといけないみたいだ。
という訳で，iWorkだとExcel相当のものがなかったりして，MS-Officeじゃないとだめそうだ。QEMU上のWindowsでOfficeするのもなんか本末転倒だし。結局MS-Office2004 for Macを購入。Rosetta上で動いているみたいだが，速度的には何の問題もなく使えている。使用感はWindowsのそれとほとんど一緒なので，違和感は感じなかった。
これで仕事はとりあえずIntelMacでもOK。Macユーザとしての第一歩を踏み出した。
