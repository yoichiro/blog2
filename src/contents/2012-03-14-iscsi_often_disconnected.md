---
layout: post
status: publish
published: true
title: iSCSIでたまに接続が切れてしまうのなんで？
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1380
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1380
date: '2012-03-14 22:20:10 +0900'
date_gmt: '2012-03-14 13:20:10 +0900'
categories:
- My PC environment
---

タイトル通りなんですけど、FreeNASでiSCSIのターゲットを作って、Fedora 16をイニシエータにしてマウントしてるんですが、2日に1回くらいの頻度で接続が切れてる(?)ような現象が出て困ってます。

logwatch君が報告してくるメールには、以下のように書かれていました。

>WARNING:  Kernel Errors Present
    connection9:0: detected conn error (1011) ...:  17 Time(s)
    connection9:0: detected conn error (1021) ...:  2 Time(s)
   EXT4-fs (sdb1): error count: 3 ...:  1 Time(s)
   EXT4-fs (sdb1): initial error at 1331143779: e ...:  1 Time(s)
   EXT4-fs (sdb1): last error at 1331429225: e ...:  1 Time(s)


/var/log/messages には、以下のように書かれてました。ネットワークが切れた？なんでだろう？これを見ると、FreeNAS側が原因な気がするけど。。。

>kernel: [2645664.994394]  connection9:0: ping timeout of 5 secs expired, recv timeout 5, last rx 6941032080, last ping 6941037088, now 6941042096


うーん。。。
