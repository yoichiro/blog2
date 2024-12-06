---
layout: post
status: publish
published: true
title: FreeNAS8のHDD交換
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2322
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2322
date: '2013-05-06 23:39:19 +0900'
date_gmt: '2013-05-06 14:39:19 +0900'
categories:
- My PC environment
---

FreeNAS8で運用してきたNASのHDDが1つ遂にお亡くなりになってしまいました。SMARTの値もどんどん酷くなっていたんだけど、ある日気がつくとHDDが認識されてなくて、プールのステータスもDEGRADEDに。

RAID-Z2で作ってあったために、もう1台壊れても大丈夫。焦ることはありません。ダメになったHDDを特定し、予め買ってあった新しいHDDに交換します。

その後電源を入れてプールの状況を見ると、以下のような感じに。

```
# zpool status

NAME                      STATE     READ WRITE CKSUM
tobu                        DEGRADED     0     0     0
  raidz2                    DEGRADED     0     0     0
    ada0p2                ONLINE       0     0     0
    ada1p2                ONLINE       0     0
    ada2p2                ONLINE       0     0     0
    15868252100758834784  UNAVAIL      0     0     0  was /dev/ada3p2
```

あれ、変な数字の羅列が出てます。ネットでいろいろ探したときは、数字の羅列ではなくデバイス名が表示されてたのに。

まあいいや。さっそくreplaceして、resilverを始めるべく、以下のコマンドを実行。なぜか失敗。

```
# zpool replace tobu /dev/ada3p2 /dev/ada3p2

... No such device. ...
```

あれ、そんなデバイスないって言われる。なんでだ？もしかして、上に出てた数値を入れるのか？

```
# zpool replace tobu 15868252100758834784 /dev/ada3p2

... No such device. ...
```

変わらず。

そもそも、/dev/ada3p2はあるのか？と思い、確認してみると・・・

/dev/ada3 しかない！

ちゃんと把握しよう。zpool replaceのオプションを確認してみます。

```
# zpool replace tobu
missing 
specification
usage:
        replace [-f]

[new-device]
```

なるほど、古いデバイス名、新しいデバイス名、の順に入れればいいのか。やってみよう。

```
# zpool replace tobu /dev/ada3p2 /dev/ada3
```

しばらくして、エラーが出ることなく正常終了しました。これが正解だったのかな。

すでに上記を実行してから2時間以上経過。順調にresilver作業が進んでいるようです。

```
NAME                      STATE     READ WRITE CKSUM
tobu                        DEGRADED     0     0     0
  raidz2                    DEGRADED     0     0     0
    ada0p2                ONLINE       0     0     0
    ada1p2                ONLINE       0     0     0  1.06M resilvered
    ada2p2                ONLINE       0     0     0  8.94M resilvered
    replacing             DEGRADED     0     0     0
        15868252100758834784  UNAVAIL      0     0     0  was /dev/ada3p2
        ada3                ONLINE       0     0     0  72.0G resilvered
```

しかし、時間かかるな。あと6時間とか出てる。。。
