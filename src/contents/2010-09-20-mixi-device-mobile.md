---
layout: post
status: publish
published: true
title: "せっかくならmixi-device-mobileも指定しましょう"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 796
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=796
date: '2010-09-20 23:03:16 +0900'
date_gmt: '2010-09-20 14:03:16 +0900'
categories:
- Social web
---

久々にブログ書きます。

9/10に行われたmixi meetup 2010では、
[mixiチェック](http://developer.mixi.co.jp/connect/mixi_plugin/mixi_check/spec_mixi_check)が中心的な話題でした。既に多くのWebサイトがmixiチェックによってソーシャル化を遂げています。

mixiチェックは、世界に先駆けてマルチデバイス対応を遂げています。日本では、多くの情報はPCでも携帯電話端末でも閲覧することができます。しかし、残念ながらそのようなサイトの多くが、"mixi-device-mobile"を使用していないようです。

例えば、PC向けのサイトがモバイル向けにもページを提供している場合、以下のように記述することで、mixiユーザはモバイルにおいてもチェックフィードをクリックすることができるようになります。

```

```
これにより、モバイルユーザのトラフィックも得ることができるようになるでしょう。お試しください！
