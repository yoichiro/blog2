---
layout: post
status: publish
published: true
title: Fedora Core 3 on VAIO Z1/Pで無線LAN稼動開始！
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 58
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=58
date: '2005-05-03 00:41:14 +0900'
date_gmt: '2005-05-02 15:41:14 +0900'
categories:
- My PC environment
---

VAIO Z1/Pで稼動を開始しているFedore Core 3だが，インストールしてから一度も使っていないハードウェアがあった。それがWireless LANだ。ざっとググってみても，何もせずに使えるようにはならなそうなので，ずっとOFFにしておいたのだ。

せっかくのWireless LANだし，ぜひ使えるようにしてみたい。早速挑戦を開始したが，意外とあっさり使えるようになってしまった。

まず最初にやったことは，Wireless LANのスイッチをONにしてVAIOを起動してみた。いろんなサイトでは「カーネルの再構築が必要」みたいなことも書いてあったので，何もしなければWireless LANの存在すらわからないだろうな，と思っていたが，その考えは直後に崩れ去る。しっかりとカーネルはWireless LANの存在を認識していたのだ。しかし，「ipw2100 device eth1 does not seem to be present. delaying initialization.」とエラーメッセージが表示され，動作はしなかった。

さすがに世の中甘くない。ipw2100というキーワードを使ってちょっとググってみると，いいサイトが見つかった。

* [Fedore core 3でIntel PRO/Wireless 2915ABGを使う](http://www.toshikazu.org/archives/000089.html) by 仕事と家事の狭間に

この場合は2200bgの場合だけど，基本的には2100に置き換えれば手順は同じはずだ。

早速開始。まずはカーネルチェック。

　　$ uname -r

　　2.6.11-1.14_FC3

よしよし。次にWireless Extensions and Toolsが入っているかどうか確認。

　　$ /sbin/iwconfig --version

　　iwconfig Wireless-Tools version 27

　　　　　　　Compatible with Wireless Extension v11 to v17.

　　kernel　 Currently compiled with Wireless Extension v17.

　　eth1　　　Recommend Wireless Extension v16 or later.

　　　　　　　Currently compiled with Wireless Extension v17.

入っているらしい。内容はよくわからない。

次に，ipw2100のドライバと，カーネルモジュール，そしてファームウェアを以下の場所からGetする。

* [http://atrpms.net/name/ipw2100/](http://atrpms.net/name/ipw2100/)* [http://atrpms.net/dist/common/ipw2100-firmware/](http://atrpms.net/dist/common/ipw2100-firmware/)

Getしたのは以下のファイル。

* ipw2100-1.1.0-31.rhfc3.at.i386.rpm* ipw2100-firmware-1.3-5.at.noarch.rpm* ipw2100-kmdl-2.6.11-1.14_FC3-1.1.0-31.rhfc3.at.i686.rpm

これを「sudo rpm -Uvh ファイル名」で一つずつインストールしていく。

基本的にはこれだけ。とりあえずWireless LANのスイッチをONにして再起動してみると，しっかりとハードウェアは認識され，「ケーブル接続？」というエラーメッセージが表示された。うまく動き出したようだ。

この後に「システム設定」-「ネットワーク」の設定ダイアログを開くと，しっかりとワイヤレス接続のタブが増えている。このタブ内にSSIDとかWEPのキーを入力すれば，Wireless LANが通信を行いだした。やったぁ！

GNOMEのパネルでネットワーク監視では，しっかりと電波状態も表示されるようだ。

 
![](http://www.eisbahn.jp/yoichiro/images/302604.gif)

基本はサーバとして使うんだけど，あるものは使える状態にしておきたい。Fedore Core 3，ホントいい感じである。

Solarisじゃこうは行かなかっただろうなぁ。。。
