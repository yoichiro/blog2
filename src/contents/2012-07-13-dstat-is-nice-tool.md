---
layout: post
status: publish
published: true
title: dstat、いい感じ
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1635
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1635
date: '2012-07-13 12:57:25 +0900'
date_gmt: '2012-07-13 03:57:25 +0900'
categories:
- Other
---

Image Collector extensionのサーバでどれくらいのネットワーク転送量になってるのかな、と調べたくてモニタリングのためのコマンドを探していたところ、dstatがいい感じっぽい。

Fedora系であれば、以下のコマンドでインストール可能。

>$ sudo yum install dstat


5秒ごとに見たい場合は、以下のコマンドを実行。

>$ dstat 5


こんな感じでいろいろと見ることができます。

![](http://www.eisbahn.jp/yoichiro/images/2012/07/Screenshot_from_2012-07-13-123159.png)

オプションがいろいろあって、man dstat見るとちょっと夢が膨らみます。
