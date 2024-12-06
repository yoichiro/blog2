---
layout: post
status: publish
published: true
title: FreeNAS8でreplaceするもDEGRADEDなままだった話
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2330
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2330
date: '2013-05-07 11:57:19 +0900'
date_gmt: '2013-05-07 02:57:19 +0900'
categories:
- My PC environment
---

[FreeNAS8のHDD交換](http://www.eisbahn.jp/yoichiro/2013/05/hdd_change_freenas8.html)エントリで紹介した通り、FreeNAS8で故障したHDDの交換をしてました。replaceを開始して、残り6,7時間と表示され、気を長くして待っていると見せかけて、寝てました。

朝になっても終わってなかったのですが、replaceは何とか終わった模様。状況を確認してみます。

```
# zpool status
  pool: tobu
 state: DEGRADED
 scrub: resilver completed after 12h58m with 0 errors on Tue May  7 10:00:37 2013
config:

NAME                        STATE     READ WRITE CKSUM
        tobu                        DEGRADED     0     0     0
          raidz2                    DEGRADED     0     0     0
            ada0p2                  ONLINE       0     0     0
            ada1p2                  ONLINE       0     0     0  1.06M resilvered
            ada2p2                  ONLINE       0     0     0  30.2G resilvered
            replacing               DEGRADED     0     0     0
              15868252100758834784  UNAVAIL      0     0     0  was /dev/ada3p2
              ada3                  ONLINE       0     0     0  260G resilvered
```

うわ、13時間もかかったのか。。。エラーはなかった、と表示されているけど、DEGRADEDなままじゃん。

ada3デバイスは、ONLINEになってる。問題は"15868252100758834784"。こいつが残っちゃってるせいで、DEGRADEDなままなのかな。

プールから謎の"15868252100758834784"を切り離すために、detachを試みます。

```
zpool detach tobu /dev/ada3p2
```

無事完了した模様。確認してみます。

```
# zpool status
  pool: tobu
 state: ONLINE
 scrub: resilver completed after 12h58m with 0 errors on Tue May  7 10:00:37 2013
config:

NAME        STATE     READ WRITE CKSUM
        tobu        ONLINE       0     0     0
          raidz2    ONLINE       0     0     0
            ada0p2  ONLINE       0     0     0
            ada1p2  ONLINE       0     0     0  1.06M resilvered
            ada2p2  ONLINE       0     0     0  30.2G resilvered
            ada3    ONLINE       0     0     0  260G resilvered
```

わーい、ONLINEにやっとなりました。めでたしめでたし。

ちなみに、プールの名前が"tobu"なのは、うちの近くにある東武動物公園内の「
[東武スーパープール](http://www.tobuzoo.com/facilities/pool/)」から拝借しました。
