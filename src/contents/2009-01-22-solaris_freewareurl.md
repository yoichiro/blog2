---
layout: post
status: publish
published: true
title: Solaris FreewareのURLが変更になってる件
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 615
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=615
date: '2009-01-22 11:05:39 +0900'
date_gmt: '2009-01-22 02:05:39 +0900'
categories:
- My PC environment
---

サーバのOSに使ってるSolaris 10は、フリーソフトのインストールに関して
[Open Source Software for Solaris](http://www.blastwave.org/packages.php)を利用している。pkg-getコマンドを使って、パッケージをサクッとインストールできる手軽さが魅力なのだが、今日やってみると・・・

>Resolving ibiblio.org... 152.46.7.80
Connecting to ibiblio.org|152.46.7.80|:80... connected.
HTTP request sent, awaiting response... 404 Not Found
10:58:52 ERROR 404: Not Found.

ERROR: could not get catalog file

とエラーになってしまった。どうやら、パッケージの配布のURLが変更になったらしい。
/opt/csw/etc/pkg-get.confファイルのurlパラメータを以下のように変更すると、うまくいった（
[情報ソース](http://wahyupratama.blogspot.com/2009/01/pkg-get-on-solaris-there-is-new-link.html)）。
#url=http://ibiblio.org/pub/packages/solaris/csw/unstable
url=http://ibiblio.org/pub/packages/solaris/
opencsw/unstable
なんでこんな変更するのよ。。。
