---
layout: post
status: publish
published: true
title: "気が付かないAuto-boxing"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 103
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=103
date: '2005-08-10 09:08:05 +0900'
date_gmt: '2005-08-10 00:08:05 +0900'
categories:
- Java
---

プライベートなプログラミングでは，すっかりJDK5.0+Eclipse3.1な環境に移行してしまった。EoDが特徴(?)なJDK5.0では，さまざまな言語あるいはコンパイラの拡張が行われたが，その全貌はまだ把握できていない。

最近ちょっとかじったアノテーションを使うときは，もちろん明確に「@hogehoge」というように書くので，注釈つけちゃいましたっ！，と言わんばかりの自覚がある。Genericsについても，文法が拡張されているから，使用したことに対する自覚は明確だろう。

さっきまで宇都宮線のグリーン車内で，あるシステム(not 本職)のコーディングをしていた。StrutsのActionFormオブジェクトからO/Rマッピングツールで自動生成したモデルへの値の受け渡しのコードをせっせと書いていた。「model.set~(form.get~());」という非常に退屈なコードだ。モデルのプロパティの中には，データベースの列定義でnull許可のものもあるので，int型ではなくInteger型なものもあったりする。つまり，「new Integer(int)」を使って，フォームのint値をIntegerオブジェクトにしてsetterに渡さないといけない。

しかし，サクサク値の受け渡しコードを書いていたときに，ふと気が付いた。

「あ，intからIntegerに変換しなくちゃ。あれ？変換してないのに，コンパイルエラーになってない・・・」

おーこれがAuto-boxingなのか！と気が付いた。すっかりJDK5.0を使ってるんだということを忘れていた。確かに，new Integer()する必要はないのは楽かも。しかも，アノテーションとかGenericsとかと違って，完全に無意識。気が付いたときは，かなり自分の中でSurpriseだった。

でも，Un-boxingについては，勝手に行われちゃいけないような気がする。オブジェクトの状態でnullだったときって，Un-boxingに失敗するはず。どうなっちゃうんだろ。。。
