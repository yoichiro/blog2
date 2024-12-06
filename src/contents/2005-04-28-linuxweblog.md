---
layout: post
status: publish
published: true
title: LinuxでWeblogサーバ構築しちゃおうかな
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 52
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=52
date: '2005-04-28 01:44:25 +0900'
date_gmt: '2005-04-27 16:44:25 +0900'
categories:
- My PC environment
---

愛機 VAIO Z1/P への Fedora Core 3 インストールも完了し，最低限整えようと思っていた機能（Samba，VNC，CVS，SSH）も動き出した。Fedora Core 3 の起動画面やGNOMEデスクトップは超かっこよく，非常に満足である。

さて，せっかくLinuxを入れたので，Windowsではできない or 大変なことをやってみたい。とりあえず思いつくのは，

* 自前Weblogサーバ構築

である。WeblogのサーバはどれもUNIX環境をターゲットにしていて，Windowsで動作させようと思ってもいろんなUNIX系ソフトウェアをかき集めなければならず，やる気がおきなかった。ココログで作ってある「
[Eclipseプラグイン開発](http://yoichiro.cocolog-nifty.com/eclipse/)」ブログも，できれば自前で運用したいと思っていたし，移行するにはいい機会だ。

ココログからの移行なら，もちろん「
[Movable Type](http://www.movabletype.jp/)」が適しているだろう。情報量も多いはず。ただし，有料なのが気になるとこだ（1万円以内だろうけど）。

あと候補としては，Javaで書かれた「
[Roller Weblogger](http://www.rollerweblogger.org/page/project)」というものも非常に興味がある（職場の隣人さんからの紹介）。StrutsやHibernateといった「超身近な」技術で構築されていることや，無償で構築できるのも魅力だ。これで，Movable Typeのエクスポートデータが取り込めるのなら文句はない（たぶんダメだろうけど）。日本語関連がちょっと問題ありそうなのが気になるところだ（どうもRollerの利用者は記事の題名を英語にしたりしているっぽい）。

どうしようかなぁ。。。
