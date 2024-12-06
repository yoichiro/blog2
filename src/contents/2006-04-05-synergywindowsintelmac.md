---
layout: post
status: publish
published: true
title: SynergyでWindowsからIntelMacを操作！
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 198
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=198
date: '2006-04-05 23:27:34 +0900'
date_gmt: '2006-04-05 14:27:34 +0900'
categories:
- MacBook Pro
---

仕事上，２台のPCを一つの机の上で操るのが，僕の普通である。デスクトップとノートの組み合わせになるので，「
[どこドア２](http://quickware.a-quest.com/dkdr2/about.htm)」というソフトを使って，デスクトップのキーボードとマウスを使って，となりのノートPC（Windows）を今までは操作していた。
しかし，先月から僕のパートナーは，MacBook Proに変わった。そうなると，「どこドア２」は当然使えない。しかし，デスクトップからノートを操作する快適さは忘れられない。さて，困った。こうなったら自分で作るしかないか。Macのプログラミングを覚えなくっちゃ。
と思っていたら，「
[Synergy](http://synergy2.sourceforge.net/)」というソフトウェアがあることを知った。Synergyは，Win-Mac-Unixという代表的なOSを問わずキーボードおよびマウスを共有することができる素晴らしいソフトウェアだ。早速ここからダウンロードして，試してみた。
がーん，ダメじゃん。接続は確立されるものの，すぐにMac側でBus errorが発生して接続が切られてしまう。やはり，IntelMacではダメなのか。。。
と思っていたら，IntelMacでSynergyが動作するように修正した方を発見！
「
[IntelMacでSynergy2を動かす](http://blog.masuidrive.jp/articles/2006/03/24/synergy2-on-intel-mac)」 - masuidrive on rails
ずっとWinで育ってきた僕にとって，Macは完全に初体験。バイナリを提供してもらって，快適にキーボード＆マウス共有ができるようになった。キー配列（特に記号系）が合わないけど，マウスが共有できるだけで感動である。
上記ページの方に感謝x2である。
