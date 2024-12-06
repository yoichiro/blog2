---
layout: post
status: publish
published: true
title: railsとrakeは似ている
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 495
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=495
date: '2007-12-27 14:19:24 +0900'
date_gmt: '2007-12-27 05:19:24 +0900'
categories:
- Ruby on Rails
---

Railsアプリのディレクトリの中で、テストケースを実行するために、
rake test
と打たなければならないところを、
rails test
と打ってしまうと、もれなくtestアプリケーションがテスト対象アプリの中にできあがる。
Railsアプリを「正しく」作っている（＝ちゃんとテストコードを書いて品質を担保している）人々はきっと経験していることではないか、と想像してみるが、いかがだろうか。。。
