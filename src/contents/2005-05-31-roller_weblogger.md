---
layout: post
status: publish
published: true
title: Roller Weblogger始動！
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 73
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=73
date: '2005-05-31 01:06:05 +0900'
date_gmt: '2005-05-30 16:06:05 +0900'
categories:
- Java
---

先週から挑戦していた
[Roller Weblogger](http://www.rollerweblogger.org/wiki/)だが，やっと動き出した。「
[インストールガイド](http://www.rollerweblogger.org/wiki/Wiki.jsp?page=UserGuide_JP)」を読みながら，手順に従って作業を進めたつもりだったが，以下のようにNullPointerExceptionが発生してしまい，絶妙なところで足止めを食らってしまった。

![](http://www.eisbahn.jp/yoichiro/images/399680.gif)

気づいてしまえば何てことはない，「jdbc;postgresql://~」というように，DB接続で本来「：」なところを「；」にしてしまっていただけだった。fedora core 3にはVNCで繋いで，しかも縦横比を3:4にしていたため，コロンなのかセミコロンなのか判別つかなかったので発見が遅れてしまった。

 これにより，Rollerは無事動作を開始した。

![](http://www.eisbahn.jp/yoichiro/images/407697.gif)

さて，入れたはいいけど，何のブログにしようかな・・・。
