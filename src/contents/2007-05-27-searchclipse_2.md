---
layout: post
status: publish
published: true
title: Lingrclipseをちょこっとバージョンアップ
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 373
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=373
date: '2007-05-27 17:51:51 +0900'
date_gmt: '2007-05-27 08:51:51 +0900'
categories:
- Eclipse
---

いままでLingrclipseを使っていただいてきた皆さんはご存じかと思うが，今までLingrclipseでlingrにログインすることができなくなっていた。
原因は，APIのRoomにtimezone属性が追加されたこと。今のLingrclipseは，APIとJavaBeansクラスの形が完全に一致しないと例外を吐いてしまう。とりあえず，timezoneに対応したlingrclipse-core（1.3.1）を公開したので，lingrclipseをお使いの方はぜひバージョンアップしていただきたい。
そのうち根本解決したいと思っているが，なかなか実行に移せないのは何故だろう。。。orz
