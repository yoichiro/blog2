---
layout: post
status: publish
published: true
title: lighttpdで稼働しているrailsアプリでリダイレクトが効かなくなった
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 419
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=419
date: '2007-07-27 16:08:25 +0900'
date_gmt: '2007-07-27 07:08:25 +0900'
categories:
- Ruby on Rails
---

「
[約5時間かかったRuby on Railsの実行環境構築](http://www.eisbahn.jp/yoichiro/2007/07/5ruby_on_rails.html)」で頑張ってみたRails運用環境だが，今日になって不具合が出てしまった。昨日までは元気に動いていたのに。。。
昨日の夜に，配置してあるRailsアプリに機能追加を行った。実際には２つRailsアプリを配置してある。形態は「apache2 <=> mod_proxy <=> lighttpd」という組み合わせ。修正は片方のアプリのみなので，もう一つは影響はないはず。加えて，apacheやlighttpdの設定ファイルはいじっていない。
しかし，今日になって，Railsアプリケーション内でredirect_toメソッドによるリダイレクトがかかると，ブラウザ上ではリダイレクトがかからずに，「You are being redirected.」と表示されるようになってしまった。「redirected」の部分がリンクになり，そのリンクを選択すれば本来リダイレクトで飛ぶべきページを表示させることはできる。この動きはFireFoxのみであり，IE6でやってみると，「You are being redirected.」とも表示されず，404エラーとなってしまった。
しかも，修正したRailsアプリだけでなく，2つあるRailsアプリの両方とも同じ現象が出るようになってしまった。
id:tfunatoさんによると，ブラウザには302ではなく404が返されているが，レスポンスヘッダにlocationがあるため，Firefoxは丁寧に「You are being redirected.」と表示してくれている，という現象らしい。問題を切り分けるために，

* apache経由をやめて，lighttpdを直接叩いてみる(mod_proxyの検証)。

* WEBrickで試してみる。
を試してみたところ，lighttpd直叩きでも現象は再現し，WEBrickによる実行では現象は発生しないという結果だった。つまり，lighttpdの設定がよろしくないのか。
lighttpdもRuby on Railsも初めての体験なために，とりあえずいくつかのブログで紹介されている設定内容の記述を見比べることしか現時点でできていない。しかし，特段違っている箇所は見当たらない。このエントリを書いている現時点では，原因不明なままだ。
誰かこの現象を経験し，解決に至った方がいらっしゃったら，ぜひコメントを頂きたい。
ちなみに，lighttpdやめてMongrelやってみようかな，と思っていることは内緒である。。。
