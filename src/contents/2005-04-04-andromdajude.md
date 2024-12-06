---
layout: post
status: publish
published: true
title: AndroMDA＆JUDEはダメっぽい・・・
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 42
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=42
date: '2005-04-04 22:37:22 +0900'
date_gmt: '2005-04-04 13:37:22 +0900'
categories:
- Java
---

次のおもちゃとして注目しているMDAを実践するための
[AndroMDA](http://www.andromda.org/)。これと何かしらのUMLモデリングツールを組み合わせてMDAを実践しようと思っている。個人的に
[JUDE/Professional](http://www.esm.jp/jude-web/product/professional.html)を買っているので，これを使おうと思って試してみた。

結果から言うと，残念ながらNGだ。

AndroMDAのサンプルで付いてくるejb-templateを使って，EJBの雛形をGet。それに入っているxmiファイルをJUDEで読み込ませていざモデリング開始！といそしんでみたものの，JUDEがNullPointerExceptionを吐いてxmiファイルの読み込みに失敗してしまった。

どうやらxmiファイルだからといって何でもかんでも互換があるわけではないらしい。うーん，これは困った。

AndroMDAはUMLモデリングツールとして，
[poseidon for UML](http://www.gentleware.com/)を標準としている模様だ。これのCommunity Editionは無償（印刷できないなどの制限あり）らしいので，時間を作って試してみよう。もしツールの完成度が良ければ，JUDEから乗り換えるのもありかも。。。
