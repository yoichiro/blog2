---
layout: post
status: publish
published: true
title: Eclipse on Solaris10(x86) on VMWare夢かなわず
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 117
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=117
date: '2005-10-09 18:43:39 +0900'
date_gmt: '2005-10-09 09:43:39 +0900'
categories:
- Eclipse
---

IP Messenger for Eclipseの開発には，複数台のマシンが必要となる。とはいえ，1台でも何とか動作確認とかしたかったので，久々にVMWareなんぞを使ってみようかなと思い，挑戦を開始した。

VMWareは早くもバージョン5まで到達していた模様(前に使っていたときは3だった)。早速
[VMWareのWebサイト](http://www.vmware.com/jp/products/desktop/ws_features.html)からブツを購入。雑誌の付録についてきたIntel版Solaris10のインストールを開始した。

昔の印象と違って，インストールも思ったよりも早く終了。そりゃ3,4年前に比べれば，搭載しているメモリも多いし，CPUパワーも強い今どきのPCなので，当然の結果である。

![](http://www.eisbahn.jp/yoichiro/images/1521802.gif)

ネットワークの設定もXの設定もサクッと終わり，Solaris10はVMWareで稼動を始めた。全部入りのインストールなので，JDK5.0もインストール済みの状態だ。

さぁ，Eclipseをダウンロードしてインストールしよう！と思い，Eclipseのダウンロードサイトに行ってみると・・・。

ない。x86用Solaris向けのバイナリがない。。。

そう，SPARC版しか提供されていなかったのだ。Fedora Coreは正式対応してないみたいだし，かといって他のディストリビューションを使った経験がない。

儚くも散ってしまった今回の野望。一応
[x86版Solarisで動作させる](http://jroller.com/page/letiemble/eclipsesolaris)ことは頑張ればできるみたいだけど。どうしようかなぁ。。。
