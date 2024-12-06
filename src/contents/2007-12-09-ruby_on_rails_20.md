---
layout: post
status: publish
published: true
title: Ruby on Rails 2.0がリリースされました！
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 480
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=480
date: '2007-12-09 16:05:27 +0900'
date_gmt: '2007-12-09 07:05:27 +0900'
categories:
- Ruby on Rails
---

見落としていたのだが、すでにRuby on Rails 2.0が、12月6日にリリースされていた。現在は、すでに2.0.1になっている。

[root/tags/rel_2-0-1](http://dev.rubyonrails.org/browser/tags/rel_2-0-1) - Rails Trac
今日時点で、例えば「jruby -S gem install rails --include-dependencies」すれば、2.0.1一式を得ることができる。ちなみに、僕のmacではヒープサイズが足りなくなったため、「-J-Xmx512m」オプションを追加することによりインストールをすることができた。
かねてからの噂の通りに、ActionWebServiceが含まれなくなり、代わりにActiveResourceが含まれるようになった。これについては、
[12月18日の丸レク](http://www.c-sq.com/modules/article/article131.html)で話す予定。
