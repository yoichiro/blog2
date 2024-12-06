---
layout: post
status: publish
published: true
title: make gconfigできないじゃん！
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 59
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=59
date: '2005-05-04 00:32:33 +0900'
date_gmt: '2005-05-03 15:32:33 +0900'
categories:
- My PC environment
---

VAIO Z1/PにインストールしたFedora Core 3。無線LANも開通したので，モバイル的に使用してみようと思い，電源ケーブルをはずし，PCMCIAカードもはずし，有線LANケーブルもはずした状態で使用してみた。

バッテリーが３０分ももたないじゃん。。。

どうやら，CPUも液晶もHDDも，フル稼働状態のようだ。特にCentrino MOBILE TECHNOLOGYなんだから，CPUの周波数制御を有効にしておきたい。そのためには，カーネルの再構築が必要である。

以下のサイトの内容どおり，カーネルソースの入手とソースファイルの作成作業を進めた。

* [Fedora Core 3でカーネスソースをインストールするには](http://www.atmarkit.co.jp/flinux/rensai/linuxtips/680kernelsfc3.html) by @IT

* [カーネルソースの再構築](http://takke-ayrton93.blog.ocn.ne.jp/data/fedora_core_3/) by 遊びたいな～

さて，さっそくカーネルの設定だ。Fedora Coreでは，GNOME版のconfigツールがあるらしい。早速「make gconfig」をしたら・・・

起動しないじゃん。

原因はgtk関連の開発環境をインストールしなかったためっぽい。確かに，インストール時にGNOME開発ツールを入れなかった。それと同様の原因で，「make xconfig」もダメ。残るはCUIベースの「make menuconfig」だが，せっかくGUIがあるのに，使えないのは何か悔しい。

じゃあ開発ツール入れればいいんでしょ？ということで，プログラムの追加・削除ツールでインストールしようとしても，「xorg-x11-libsがない」という意味不明なエラーメッセージが出てインストールできない。。。rpm -qで調べればちゃんとインストールされているのに，だ。意味わかんない。

・・・というわけで，Fedora Core 3をインストールしなおすことにした。同じ作業を２度行うことほど，面倒なことはない。うーん，Linuxのインストールは難しい。。。
