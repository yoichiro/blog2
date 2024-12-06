---
layout: post
status: publish
published: true
title: Looking GlassってWindowsでも動くのね！
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 43
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=43
date: '2005-04-05 10:55:36 +0900'
date_gmt: '2005-04-05 01:55:36 +0900'
categories:
- Java
---

次のWindows(Longhorn)では，奥行きのあるデスクトップ（3D Desktop）が売りになるらしい。確かに，せっかくウィンドウのZオーダーがあるんだから，遠近法とか使って，遠くにウィンドウとか置けるようになれば嬉しいかもしれない（そんなフリーソフトもいくつかある）。もちろん2D向けデバイスのマウスだと，遠くにあるウィンドウを引っ張ってくる操作を直感的に行えないが，そんなことは度外視しても，3Dデスクトップは考えただけで楽しくなる。

さて，今ではLonghornを待つことなしに，3Dデスクトップを体感することができる。それがLooking GlassというJava 3Dデスクトップだ。

* [Project Looking Glass](http://www.sun.com/software/looking_glass/)

このプロジェクトの発起人が日本人だということがなかなかすごい。

 このLooking Glass，確かついこの前までは，X11を使って超ネイティブコード書きまくりでパフォーマンスを確保していたはず。つまり，Looking Glassをさくっと試すためには，LinuxでX Window Systemが動作する環境を用意する必要があった。しかも，グラフィックカードをかなり選んだような気がする。Looking Glassを体感するために，新しくマシンを組み立てようかどうか考えていた。

しかし！Looking GlassがWindowsでも動作することを今日知った。手順のページも複数個見つかった。

* [Looking Glass DesktopをWindowsで動かす](http://andore.com/inami/mtarchives/001501.html) by アメリカでがんばりましょう* [LG3D under Windows XP with SP1 HOW TO](http://www.javadesktop.org/forums/thread.jspa?threadID=8741&tstart=0) by javadesktop.org* [lg3d-install](http://oss.timedia.co.jp/index.cgi/kahua-web/show/column/hibi/lg3d-install) by Open Source WEB* [WindowsでLG3Dを動かしてしまおう](http://www5.airnet.ne.jp/sakuraba/java/lg3d/windows.html)

Looking GlassがX11に依存するのは，どうやらフルスクリーンモードでの動作だけのようだ。つまり，デスクトップモードならJavaの部分だけで済むので，X11がなくてもいろんなOS上で動かせる，ということらしい。

 時間を見つけて試してみよう。Windowsで。
