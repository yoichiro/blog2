---
layout: post
status: publish
published: true
title: mixi Check button extensionをリリースしました
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1148
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1148
date: '2011-11-04 09:17:26 +0900'
date_gmt: '2011-11-04 00:17:26 +0900'
categories:
- Social web
---

「mixiチェックをいつでもすぐに」をキーワードに、Chromeブラウザ向けの拡張機能である"mixi Check button extension"を作ってみました。Chromeウェブストアからインストールすることができます。

[
[mixi Check button extension](http://goo.gl/jTaz5)] - Chromeウェブストア

![](http://www.eisbahn.jp/yoichiro/images/2011/11/capture_440.png)

今まではmixiチェックの仕様として「許可URLの指定が必須」でした。つまりmixiチェックボタンは、あるWebサイトの運営者が自分のページ群のどこかにmixiチェックを配置する、つまり「チェックされるページのURLはあるドメインの範囲内に限られる」ということをベースにしたホワイトリスト方式でした。mixiチェックの開始時点では、様々考えられる悪用のリスクをできるだけ軽減することが優先されるべきであり、この制限は一定の効果をあげていただろうと想像できます。

今回この制限が外れたことにより、どのURLに対してもmixiチェックのフィードを投稿できるようになりました。ミクシィ社からは「
[運営者からのお知らせ](http://mixi.jp/release_info.pl?mode=item&id=1500)」によってこのことが告知され、同時にブックマークレットもその告知内にて紹介されています。

「ブックマークレットが作れるなら、Chrome拡張機能としても作れるはず」と考えて、さくっと作ってみたのが今回のmixi Check button extensionとなります。ソースコードは以下に置いてあります。

[
[github.com/yoichiro/mixi_check_for_chrome](https://github.com/yoichiro/mixi_check_for_chrome)]

ネットサーフィン中に「あれ、このページ、mixiチェックボタンないのか」と残念に思ったことも少なくないと思います。mixi Check button extensionを使うことで、もうそんな思いをすることはなくなります。

Enjoy net-surfing!
