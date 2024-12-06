---
layout: post
status: publish
published: true
title: "あるタグ全体を検証するassert_dom_equalメソッド"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 487
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=487
date: '2007-12-17 22:56:20 +0900'
date_gmt: '2007-12-17 13:56:20 +0900'
categories:
- Ruby on Rails
---

RailsにおけるControllerの挙動をテストするために、functionalsテストがある。functionalsテストの対象は各種Controllerクラスのメソッドであり、Webブラウザのリクエストをシミュレートして、それに対するControllerおよび使用されたViewの結果の内容を検証するのが目的。つまり、rhtmlやrxmlなどのViewによってレンダリングされた内容についても、動的に変化する部分に関してはfunctionalsテストの範囲となる。

functionalsテストにおいて、レンダリング結果に対すテストで最も使用される検証用メソッドは、assert_selectだろう。これにより、例えば、

assert_select 'div.block > span' do |elements|
elements.each do |element|
assert_select element, :text => 'hoge'
end
end

というようにして、タグセットの内容を検証できる。上記では、class属性にblockという属性値を持つdiv要素の子要素であるspan要素全てについて、それがhogeという文字列をbodyに持つかどうかを検証するコードになる。

ほとんどのケースでassert_selectを使えると思うのだが、時として属性値についても検証が必要になることがある。
[前のエントリ](http://www.eisbahn.jp/yoichiro/2007/12/assert_select.html)で属性値に関するテストをための方法を紹介したが、もっと直感的に要素全体の検証をするための手順として、assert_dom_equalが提供されている。

assert_dom_equalを使えば、以下のようにして要素全体を検証することが可能になる。

assert_select 'div.block > span' do |element|
assert_dom_equal element.to_s, '
hoge'
end

これにより、elementの文字列表現、つまり「
hoge」が、期待する「
hoge」と一致するかどうかを検証することができる。ポイントは、属性の順番が入れ替わっていても検証可能なこと。これはかなり重要であり、特にレンダリングを比較的動的に行っているパターンの場合は、assert_dom_equalが非常に有効に機能するはずである。

「え、CSSセレクタ使って、assert_selectで検証すればいいじゃん」という話もあるのだが、CSSセレクタ、特に属性セレクタによるassert_selectがちゃんと機能しない現象が今日見受けられた。原因を特定するに至ってはいないが、現状として確実にassertしたい場合は、このassert_dom_equalを使うと良いだろう。
