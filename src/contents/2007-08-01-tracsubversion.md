---
layout: post
status: publish
published: true
title: tracがsubversionの履歴を拾ってくれなくなったとき
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 426
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=426
date: '2007-08-01 11:37:35 +0900'
date_gmt: '2007-08-01 02:37:35 +0900'
categories:
- My PC environment
---

気がつくと，ある日からsubversionへのコミット履歴などをtracが拾ってくれなくなっている現象が発生するときがある。今まさに，その状況に直面した。「subversionのリポジトリが壊れたのかっ！？」とtracの表示を信じてしまったのだが，別のsubversionクライアント(subclipse)で覗いてみると，壊れていないっぽい。
ググって調べてみると，tracがsubversionの履歴を拾ってくれなくなったら，以下のコマンドを試すといいらしい。

>trac-admin [svnリポジトリのパス] resync

見事にこれで復活可能。subversionの履歴がtracのTimelineに表示されるようになった。
・・・冷や冷やものだ。そろそろバックアップを真剣に考えなければ。
