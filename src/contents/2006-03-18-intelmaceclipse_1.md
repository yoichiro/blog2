---
layout: post
status: publish
published: true
title: IntelMacでEclipse
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 184
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=184
date: '2006-03-18 08:39:45 +0900'
date_gmt: '2006-03-17 23:39:45 +0900'
categories:
- MacBook Pro
---

Javaの開発に欠かせないEclipse。もちろん昔からMac版は提供されてきたが，ネイティブバイナリのSWTがPowerPC向けのものなために，そのままではIntelMacでは動作しない。
そこで，SWTをユニバーサリーバイナリとしてリコンパイルしたものが，EclipseのBugデータベースで提供されている。

[Bugzilla Bug 98889　Add support for Mac OSX (X86/Intel)](https://bugs.eclipse.org/bugs/show_bug.cgi?id=98889)
ここの，「3.1.2: SWT 3139 with universal binaries for Eclipse 3.1.2」というリンクからダウンロードできるjarファイルを，EclipseのpluginsディレクトリにコピーすればOK。めでたくEclipseがIntelMacで起動する。
