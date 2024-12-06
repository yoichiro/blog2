---
layout: post
status: publish
published: true
title: mnesiaからオブジェクトをソートして得るには
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1320
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1320
date: '2012-02-19 13:32:12 +0900'
date_gmt: '2012-02-19 04:32:12 +0900'
categories:
- Erlang
---

qlc:sort()関数を使えば実現できます。例えば以下のようなRecordがあったとして、

>-record(sample, {id, name, sort_order}).


sort_orderでソートした結果を得るには、以下のような感じになります。

>mnesia:transaction(fun() ->
　　QH1 = qlc:q([X || X 
　　　　Sample1#sample.sort_order < Sample2#sample.sort_order
　　end),
　　qlc:e(QH2)
end).


qlc:sort()でラップしてあげる感じですね。
