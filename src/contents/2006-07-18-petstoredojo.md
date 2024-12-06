---
layout: post
status: publish
published: true
title: PetStoreはDojoでいっぱい
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 231
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=231
date: '2006-07-18 09:04:51 +0900'
date_gmt: '2006-07-18 00:04:51 +0900'
categories:
- dojo
---

「
[Java PetStore 2.0ea1のインストール](http://www.eisbahn.jp/yoichiro/2006/07/java_petstore_20ea1.html)」によって動作を開始したJava PetStore。画像のエフェクトの種類が多く，触っていてなかなか面白い。どこでAjaxが使われているのか，さくっと触っただけではよくわからないが，Webブラウザ上でスクリプトが盛り沢山なのだけは明確にわかる。あまりスクリプトを書いたことのない僕としては，なかなか手ごわそうだ。
さて，さっそくコードを追っていきたいのだが，まず考えることといえば，どのファイルから見ていけばいいのか，ということだ。Webアプリケーションなんだから最初はindex.htmlだろうな，と勝手な推測は見事に的中。petstore/Web Pages/index.htmlファイルが，PetStore Demoを説明したページのファイルだった。
説明ページには，確か「Enter the Store」というリンクがあった。探してみると，以下のようになっていた。

>##[Enter the Store](/petstore/faces/index.jsp)

なるほど，index.jspがPetStoreの本当の入り口のようだ。おもむろにpetstore/Web Pages/index.jspを見てみると，タグまでは普通のHTMLだが，さっそく


dojoというオブジェクトのrequireメソッドを呼んでいる。dojoって聞いたことがある。なんだっけ？

[Dojo](http://dojotoolkit.org/)は，Ajax時代のインタラクティブなWebアプリケーションを実現するためのJavaScriptライブラリである。豊富なWidgetの提供が特徴であり，Sunなどの企業がスポンサーとなっているため，今後の発展も期待できる注目すべきライブラリだ。と，完全に知ったかぶりをしてみたが，なるほど，PetStoreはDojoを使っているのか。
JavaScriptでは，大きなライブラリであれば，jsファイルにメソッド群がまとめられて，


banner.jspファイル内では，バナーにPetStoreに関するRSSを表示するためのrss.jsファイルも読み込んでいる。banner.jspファイルでは，RSSの表示に関する処理をdojoオブジェクト読み込み時に実行することを割り当てるスクリプトが書かれている。RSSの表示に関する部分はまだよく見ていないので後回し。
index.jspファイルに戻ると，dojoというキーワードがあちこちにあることがわかる。










catalog.jspファイルの中では，dojoというキーワードは一つしか出てこない。しかし，読み込んでいる３つのjsファイルの中では，dojoというキーワードをいくつも発見することができる。しかし，widgetという単語ではなく，ioやbind，publishなどといった単語が見受けられるため，イベント関連やサーバとの通信でDojoが使われているようだ。
他のjspファイルではdojoという単語は見受けられないが，jsファイルを見るとdojoという単語を多くの箇所で見つけることができる。
PetStoreの動作にDojoが根幹をなしていることは確かなようだ。
