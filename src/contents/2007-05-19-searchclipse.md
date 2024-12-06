---
layout: post
status: publish
published: true
title: Searchclipse公開開始！
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 368
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=368
date: '2007-05-19 09:11:46 +0900'
date_gmt: '2007-05-19 00:11:46 +0900'
categories:
- Eclipse
---

Rimoclipse，Lingrclipse，Twitterclipseと，どちらかというと生産性を低下させてしまうEclipseプラグインを開発してきたが，「〜clipse」シリーズ第４段として，Searchclipseの公開を開始した。
Searchclipseは，FirefoxやIE7に搭載されている検索バーのようなものだ。

![searchclipse1.jpg](http://www.eisbahn.jp/yoichiro/images/searchclipse1.jpg)
検索サイトとして，インストール直後はGoogleやWikipedia，Exciteの翻訳サイトを登録してある。

![searchclipse2.jpg](http://www.eisbahn.jp/yoichiro/images/searchclipse2.jpg)
検索サイトは，設定画面上で追加することができる。

![searchclipse4.jpg](http://www.eisbahn.jp/yoichiro/images/searchclipse4.jpg)
この設定画面では，サイト名とURLテンプレートを組で追加することができる。URLテンプレートは，検索サイトのURL中に検索キーワードが置換される"${keyword}"を含む文字列である。${keyword}の部分には，入力されたキーワードがUTF-8のBASE64でエンコードされた文字列で置換される。
検索サイトを選んで，キーワードを入力して[ENTER]キーを押すことで，Webブラウザが開いて検索サイトにアクセスされる。

![searchclipse3.jpg](http://www.eisbahn.jp/yoichiro/images/searchclipse3.jpg)
機能としてはこれだけだ。Eclipseでプログラミングをしているときに，調べものをしたくなったときや，Issueトラッキングのサイトを見たくなったときなどは，Webブラウザを使用することだろう。つまり，EclipseとWebブラウザを行ったり来たりすることになる。特徴的なのは，Webブラウザは「ちょっとだけ使用して，すぐにEclipseに戻る」ということだ。調べがついたらWebブラウザを閉じてしまうかもしれない。となると，やはりWebブラウザでの調べものについては，Eclipse内で完結していて欲しい。そういった気持ちから作ったプラグインが，Searchclipseである。
他のプラグインと同様に，アップデートサイト（http://www.eisbahn.jp/update-site/）からインストールできるので，ぜひお試しいただきたい。そして，要望や不具合報告をコメントして欲しい。
