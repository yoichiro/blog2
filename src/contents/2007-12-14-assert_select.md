---
layout: post
status: publish
published: true
title: assert_selectで属性値を扱う方法
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 485
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=485
date: '2007-12-14 23:03:16 +0900'
date_gmt: '2007-12-14 14:03:16 +0900'
categories:
- Ruby on Rails
---

rhtmlのレンダリング結果を確認するために、assert_selectを使用する。この際、要素が持つテキストを確認するのではなく、ある属性の値を検証する必要も出てくるだろう。

ある要素のある属性値を確認するためには、以下のようにすれば良い。

assert_select element, '[name=?]', 'value'

上記の例では、ある要素elementに対して、それが持つ"name"属性の値が"value"であることを確認するコードである。つまり、
[CSSセレクタ](http://msugai.fc2web.com/web/CSS/selector.html)の属性セレクタを使って属性を特定し、「?」の部分、つまり属性値に対してassert_selectが第３引数で指定した値と一致するか比較を行ってくれるのである。

真面目にfunctionalテストを行う場合には必須となるテクニックなので、ぜひ身に付けておきたい。
