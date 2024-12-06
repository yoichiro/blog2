---
layout: post
status: publish
published: true
title: "ライフサイクルイベント提供開始！"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 691
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=691
date: '2009-12-03 21:52:32 +0900'
date_gmt: '2009-12-03 12:52:32 +0900'
categories:
- OpenSocial
---

mixi Platformにて、待望の「ライフサイクルイベント」の提供が開始されています。

[ライフサイクルイベントについて - mixi Developer Center]

[http://developer.mixi.co.jp/appli/pc/lets_enjoy_making_mixiapp/lifecycle_event](http://developer.mixi.co.jp/appli/pc/lets_enjoy_making_mixiapp/lifecycle_event)

これは、例えばユーザがmixiアプリをインストールしたとき、もしくはmixiアプリをアンインストールしたときに、外部サーバにそのことを通知してくれる仕組みです。元々はHi5で独自実装された機能が、OpenSocial 0.8.1の時に標準仕様として取り込まれたものとなります。

[v0.8からライフサイクルイベントを受け取れるようになります - OpenSocial-Japan]

[http://groups.google.co.jp/group/opensocial-japan/browse_thread/thread/baa5590cd35b58da/ba87c004d87508b6?lnk=gst&q=Lifecycle#ba87c004d87508b6](http://groups.google.co.jp/group/opensocial-japan/browse_thread/thread/baa5590cd35b58da/ba87c004d87508b6?lnk=gst&q=Lifecycle#ba87c004d87508b6)

mixi Platformでは、event.addappおよびevent.rempveappに対応しています。これによって、開発したmixiアプリのインストールとアンインストールの状況を開発者が把握することができるようになります。つまり、

* ユーザの利用状況の把握

* 使わなくなったユーザに関する情報のゴミ掃除
などが実現可能となります。

これらに加えて、event.addappの際には、「誰から招待を受けてmixiアプリの利用を始めたか」という情報も受け取れるようになります。今まで多くのmixiアプリでは、招待時に選択されたユーザ一覧と招待したユーザを覚えておいて、インセンティブを与えるようなことをしていたかと思います。

しかし、実際には「誰からの招待を受けてインストールしたか」を開発者は把握することができず、例えば同時に2ユーザから同じアプリの招待を受けていた際には、どちらからの招待を受けたのか判断することができませんでした。

event.addappの際に、mixi_invite_fromパラメータが付与されていた際には、そのユーザからの招待を受けてインストールを行ったことがわかります。これにより、インセンティブを与える対象ユーザを厳密に特定することができるようになった、ということができるのです。

mixi側の負荷や外部サーバへの多量リクエスト送信をさけるために、リアルタイムに通知を行うのではなく、一定時間ごとにリクエストをまとめて通知を行うようになっています。よって、mixiアプリをインストールした瞬間にインセンティブを与えることが可能、というわけではありませんので、注意が必要です。

ライフサイクルイベントは、PC向けアプリ、モバイル向けアプリ共に通知されます。この機能をうまく使った、さらに素敵なアプリケーションが登場するのを楽しみにしています！
