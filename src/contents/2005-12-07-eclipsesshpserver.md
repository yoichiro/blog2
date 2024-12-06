---
layout: post
status: publish
published: true
title: EclipseでのSSH越しpserver接続
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 140
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=140
date: '2005-12-07 14:21:43 +0900'
date_gmt: '2005-12-07 05:21:43 +0900'
categories:
- Eclipse
---

Eclispeを入れ直す度に調べ直しているのもいい加減いやになったので，このエントリに記しておく。

Eclipseにおいて，SSH越しにCVSのpserverを叩くには，「org.eclipse.team.cvs.ssh2」プラグインのplugin.xmlファイルに，以下の内容を追記する。

>　
　　
　　　


　　
　


その後，-noregistrycacheオプション付きでEclipseを起動すると，PServerSSH2を選択できるようになる。
