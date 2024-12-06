---
layout: post
status: publish
published: true
title: ErlangでUUIDの生成をするには
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1318
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1318
date: '2012-02-19 12:11:15 +0900'
date_gmt: '2012-02-19 03:11:15 +0900'
categories:
- Erlang
---

erlang-uuidモジュールを使えば良さそう。

[travis / erlang-uuid](https://github.com/travis/erlang-uuid) - Github

厳密に言えばmac addressとか使ってなくて完全乱数なんだけど、まぁきっと大丈夫。

追記：2012-02-20

voluntasな人に
[このように](https://twitter.com/#!/voluntas/statuses/171071687448735745)ご指摘いただきました。代わりのものを探してみると、こんなのもあるっぽい。

[mrijkeboer / euuid](https://github.com/mrijkeboer/euuid) - Github

あと、voluntasな人からこれを勧められました。

[boundary / flake](https://github.com/boundary/flake) - Github

教えてくれてありがとー。
