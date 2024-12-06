---
layout: post
status: publish
published: true
title: "こみゅすけのRESTful APIを公開します！"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 501
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=501
date: '2008-01-15 23:20:14 +0900'
date_gmt: '2008-01-15 14:20:14 +0900'
categories:
- Commusuke
---

Java Appletのように重い
[こみゅすけ](http://commusuke.eisbahn.jp/)のUIだが、こみゅすけの次の一手として、思い切ってこみゅすけの情報を取得あるいは操作するためのRESTful APIの公開を開始することにした。すでに実装は完了し、下記のドキュメントも執筆完了。

[RESTful APIリファレンス](http://www.eisbahn.jp/trac/commusuke/wiki/rest) - Trac
リソースの削除については、間違ったAPIの利用によって情報が消去されてしまう懸念があるため、HTTP DELETEメソッドは受け付けないようにしてある。その他のGET、POST、PUTメソッドについては、ちゃんと実装を行っているので、試してみて欲しい。利用条件なども上記のページに記載してあるので、一読願いたい。
こみゅすけの開発当初は、Dojo Toolkitをフルに使ったUIをみんなに使って欲しい、という考えだったのだが、今ではこみゅすけの管理下にあるデータベースこそ、価値のあるものであり、みんなにうまく活用して欲しい、と思っている。なかなか完全なRESTful APIというのも、まだ世の中に少ないと思うので、RESTful APIを使ったサービスのマッシュアップを、こみゅすけを題材にして、ぜひ皆さんにいろんな言語で行ってみて頂きたい。その結果、こみゅすけがいろんな場所でいろんな使われ方を見れるようになることが、僕が今考えている最も嬉しいこみゅすけの将来である。
データベースのバックアップは毎時取っているので、「壊しちゃったらどうしよう」など考えず、ぜひチャレンジして見て欲しい。もちろん節度ある行動を求めたいが、ま、とにかく気軽にご利用ください、ということである。
みんなでこみゅすけの将来を一緒に作っていきましょう。作って欲しいです。作ってください。作ってー！
