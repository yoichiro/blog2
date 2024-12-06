---
layout: post
status: publish
published: true
title: Children = Kids？
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 260
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=260
date: '2006-11-10 13:55:54 +0900'
date_gmt: '2006-11-10 04:55:54 +0900'
categories:
- Java
---

コンポジットなオブジェクトの構造を辿るときに必ずと言っていいほど見られるメソッド，getChildren()。これを変数で受けるとき，あなたなら変数名をどう命名するだろうか？

>Hoge hoge = ...;
Collection children = hoge.getChildren();

まぁこれが多数派だろう。僕も今までこうしてた。しかし，JavaServer FacesのSun RIは違った。

>Hoge hoge = ...;
Collection 
kids = hoge.getChildren();

なんてセンスのいい名前だろう。チルドレンではなく，キッズ。インスタンス達を小馬鹿にしている印象もあるが，子供は大好きだ。
今後はkidsでいこうと思う。
