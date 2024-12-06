---
layout: post
status: publish
published: true
title: mixiにてClassmates APIが登場しました
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 679
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=679
date: '2009-11-29 00:46:17 +0900'
date_gmt: '2009-11-28 15:46:17 +0900'
categories:
- OpenSocial
---

先週、mixi Platformに新しいAPIが追加されました。Classmates API、同級生というソーシャルグラフをmixiアプリで扱うことを可能にするAPIです。

[Classmates API、メッセージ送信APIの提供開始について]

[http://developer.mixi.co.jp/news/2009112701](http://developer.mixi.co.jp/news/2009112701)

Classmates APIはmixi独自のAPIとなりますので、mixiアプリでのみ使用可能です。PC向けAPI、モバイル向けAPIの両方が同時にリリースされています。モバイル向けのAPIはパートナー向けの限定情報となりますが、できることはPC向けのAPIと同じです。

今までのmixiアプリで利用可能なソーシャルグラフは、マイミクのみでした。しかし、先週リリースされたmixi同級生というサービスにより、ユーザは母校を登録し、同じ母校のユーザを見つけることができるようになりました。つまり、同級生というソーシャルグラフが新たに加わったことになります。Classmates APIは、この同級生ソーシャルグラフを扱うためのAPIです。

ただし、マイミクを扱うことを可能にするPerson & Friends APIと比べて、Classmates APIは以下の点でちょっと特殊です。

* 学校情報を取得することはできない

* 同級生のユーザ一覧を直接取得することはできない

例えば、ユーザが登録している学校の名前などを取得することはできず、その代わりに学校を特定するための文字列（学校トークン）の一覧がAPIを使って取得されます。mixiアプリの開発者は、ユーザが登録した学校の学校トークンを外部サーバのDBに格納します。ユーザが同じ学校を登録していれば、同じ学校トークンがAPIで得られますので、外部サーバのDBに蓄積していくことで、同級生ソーシャルグラフがDB内で育っていきます。同級生ユーザ一覧をAPIで得ることができないので、外部サーバのDBに蓄積していくことが必要となります。

あとは、そのソーシャルグラフを活用して、同級生ならではの機能を、開発するmixiアプリにてユーザに提供すれば良い、ということです。

すでにClassmates APIを使ったアプリがいくつかあります。mixi同級生サービスによる学校登録が進めば進むほど、Classmates APIの利用価値も今後あがっていきますので、素敵なmixiアプリが次々と登場するのを期待しています。

mixiアプリの開発に、ぜひチャレンジしてみてください！
