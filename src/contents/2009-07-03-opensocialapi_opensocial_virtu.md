---
layout: post
status: publish
published: true
title: OpenSocialでの共通課金API - OpenSocial Virtual Currency API
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 653
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=653
date: '2009-07-03 08:48:51 +0900'
date_gmt: '2009-07-02 23:48:51 +0900'
categories:
- OpenSocial
---

OpenSocialの特徴は、開発したアプリケーションをさまざまなSNSに展開することができるという可搬性にあります。つまり、アプリケーションに対するビジネスを行う市場について、特定のSNSだけでなく、世界中の多くの市場を相手にすることができるということになります。
日本のモバイル市場や、世界のいくつかのSNSでは、すでにデジタルコンテンツを売り買いすることが盛んに行われています。この売買を実現するために、モバイルやSNSといったプラットフォームは、課金を行うための仕組みを整えてコンテンツプロバイダに提供し、利用してもらいます。ただし、そのほとんどが、プラットフォームごとに独自に策定された仕様となっています。プラットフォームごとに、コンテンツプロバイダは課金のための仕様を1から理解する必要が出てきます。
例えば、OpenSocialアプリケーションでSNSが提供する課金の仕組みを使いたいとしたら。。。SNSごとに課金の仕様が違っていては、せっかくの可搬性が台無しになってしまうでしょう。
そこで、OpenSocialアプリケーションのための課金APIの共通仕様が提案されました。それが「OpenSocial Virtual Currency API」です。このAPIにより、開発者はアプリケーションコードを変更することなく、課金を行う機能を持つアプリケーションをさまざまなSNSに展開することができるようになります。
OpenSocial Virtual Currency APIのドキュメントについて、Google及川さんと共に、日本語訳を作ってみました。
[OpenSocial Virtual Currency API Proposal]

[http://docs.google.com/View?id=dhsg598c_994hdb9pwcf](http://docs.google.com/View?id=dhsg598c_994hdb9pwcf)
OpenSocial Virtual Currency APIを使ったアプリケーションを開発したい方は、上記ドキュメントの「アプリケーションドメインJavaScriptインタフェース」の項をチェックすると良いでしょう。それ以外の部分は、主にOpenSocial Virtual Currency APIを実装するOpenSocialコンテナ向けの内容となります。
このOpenSocial Virtual Currency APIは、既にhi5にて実装が完了し、RockYouなどが提供しているアプリケーションにて使われていることが公表されています。
[hi5 Coins Payment Platform Leverages OpenSocial Virtual Currency API]

[http://news.softpedia.com/news/OpenSocial-Platform-Supports-Hi5-Coins-And-Payments-113419.shtml](http://news.softpedia.com/news/OpenSocial-Platform-Supports-Hi5-Coins-And-Payments-113419.shtml)
興味のある方は、ぜひチェックしてみてください。
