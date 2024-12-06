---
layout: post
status: publish
published: true
title: "メールが受信できなくなった＆復旧"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2566
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2566
date: '2013-11-02 10:05:40 +0900'
date_gmt: '2013-11-02 01:05:40 +0900'
categories:
- My PC environment
---

昨日、家に帰ってきてネットでぶらぶらしてたら、突然自宅サーバのSMTPサービスからエラーメールが次々と送信されてきた。そのメールの中身を注意深く見てみると、

"Insufficient system storage"

と書かれてる。あれ？vmのファイルイメージ壊れた？と一瞬思ったけど、Postfixを動かしてるvmのOSやPostfix自体は動作してるわけで、HDD障害ってわけでもなさそう。でも、storageって言ってるので、何かファイル関連の話かなぁ、と調査を開始。

わかってみれば、どうってことはない。Disk full。dfで見ると、HDDの使用率が100%に達してた。なるほど、PostfixはDisk fullになると、Insufficient system storage"と言ってくるのね。

解決策は、もちろんDiskに空きを作ること。まずは削除対象のファイルを探すべく、du -sh * をルートディレクトリから使っていって、大きめのファイルを探していく。/var、log、fetchmail.log・・・12G！このサーバにはHDDとして14GBしか割り当ててなかったので、fetchmail.logファイルたった1つが肥大化したことによって障害が発生したわけだ。

このfetchmailは最近入れたもの。i.softbank.jp宛のメールをgmailから読めるように転送するために導入した。最初動作状況を見るためにログファイルを作る設定をしてたのがそのまま残ってしまってて、すごい勢いでログファイルが大きくなってしまった。

fetchmailのログ出力を停止して、一件落着。
