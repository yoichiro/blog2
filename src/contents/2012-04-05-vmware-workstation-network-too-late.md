---
layout: post
status: publish
published: true
title: VMware Workstationでネットワークが遅い場合の改善策
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1399
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1399
date: '2012-04-05 20:46:31 +0900'
date_gmt: '2012-04-05 11:46:31 +0900'
categories:
- My PC environment
---

僕は会社のPCでは、Windows 7にVMware Workstationを入れて、ゲストOSとしてUbuntu 11.10を入れて「プログラミングはUbuntu、それ以外はWindows」って感じで仕事してます。開発をする時はどっぷりUbuntuの世界の中で、それ以外の業務はWindowsで、って感じで完全に独立して作業してることがほとんどでした。

しかし、あるツールを作っている時に、Ubuntuでサーバを上げて、Windows上のWebブラウザからそのサーバにアクセスしてテストする、ってことをやり始めた時に、ホストとゲスト間のネットワークが劇遅であることに気がつきました。SSHでUbuntu内から他のサーバにつなぐ際にも遅かったんですけど、SSH絡みの手順が何か遅い or 相手のサーバが混んでるんだきっと、と自分に言い聞かせてごまかしてきました。でも、ホストOSのWebブラウザからAJAXリクエストがゲストOSに飛ぶ度に数十秒待たされては、さすがに我慢できません。

そこで調べてみると、TCP Segmentation Offload(TSO)をOFFにすればいい、ということが書かれているブログがいくつか見つかりました。例えば以下とか。

[仮想マシンからホストマシンへのネットワークが異常に遅い](http://ameblo.jp/anisjp/entry-10968001790.html)

なるほど簡単そうだ、と思って自分のPCのNICに対する詳細設定を確認しに行くと・・・

「ない！あるはずのものがない！！」

TSO関連の設定項目が一つも見あたりません。これは困った。

そこで同僚に教えてもらいました。なんとWindowsのレジストリをいじってOFFにできると！

[Windows Vista/7のオフロードを無効化する](http://www.ginnokagi.com/2011/12/windows_vista7.html)

早速試してみると・・・おぉぉ！無事ホストOSとゲストOS間の通信速度が改善。全くストレスのない状態になりました。もちろんゲストOSから外のサーバにSSHする際もバッチリ。すばらしい。

もしVMwareを使っててネットワークの遅さを我慢している人は、上記を試してみてください。

あわせて読みたい： 
[Facebook上でのやり取り](http://www.facebook.com/yoichiro6642/posts/10150719890724539?notif_t=feed_comment)
