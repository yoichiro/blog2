---
layout: post
status: publish
published: true
title: OpenPNE3がOpenSocialに対応しました
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 619
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=619
date: '2009-01-28 10:35:53 +0900'
date_gmt: '2009-01-28 01:35:53 +0900'
categories:
- OpenSocial
---

前々から対応することを表明していたOpenPNEが、Version 3にてOpenSocialに対応しました。
[OpenPNE3.0をリリースしました！ ]

[http://www.openpne.jp/archives/1117/](http://www.openpne.jp/archives/1117/)
OpenPNEのプラットフォーム化戦略の一つとして、このOpenSocial対応があります。これにより、多くの場所で、OpenSocialプラットフォームが登場することになります。素敵なことですね！
・・・がしかし、OpenPNE3のダウンロードページに書いてもらいましたが、現状の配布パッケージのまま、OpenPNE3のOpenSocial Plug-inを有効にすべきではありません。個人情報がダダ漏れになります。OpenSocialコンテナの実装としてApache Shindigが搭載されているのですが、このShindig、初期設定は完全なInsecureです。つまり、

* セキュリティトークンが暗号化されず有効期限もない。

* 匿名でのアクセスが許可されている。
という状況になります。詳しくは
[ここ](http://groups.google.co.jp/group/opensocial-japan/browse_thread/thread/5db84f55b970630a)をご覧ください。
OpenPNEの開発チームにこの問題は伝えてあるので、いずれセキュアにするための方法がドキュメント化され、配布パッケージの初期設定に関するポリシーの変更が加えられることと期待しています。それまでは、OpenSocialアプリケーションの開発＆動作確認、という目的以外でOpenSocial Plug-inを有効にするのは（Shindigの設定に自信がある人を除いて）残念ながら避けるべきです。
もしOpenPNEをお使いの方で、OpenPNE3にバージョンアップをお考えの方は、お気を付けください。
