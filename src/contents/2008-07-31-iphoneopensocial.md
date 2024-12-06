---
layout: post
status: publish
published: true
title: iPhoneとOpenSocialは相性がよい？
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 572
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=572
date: '2008-07-31 09:48:07 +0900'
date_gmt: '2008-07-31 00:48:07 +0900'
categories:
- OpenSocial
---

既存の大手SNSが、iPhone向けのアプリケーションを続々と公開している。App Storeに「ソーシャルネットワーキング」というカテゴリがあるくらいの勢いである。FacebookやMySpace、そして先日mixiもiPhoenアプリの公開を始めた。
「
[mixiのiPhoneアプリ公開](http://www.itmedia.co.jp/news/articles/0807/30/news052.html)」 - IT media
このmixiアプリは、多くのmixiユーザに使われてるだけでなく、内部でAtomPubを使ってmixiにアクセスしていることが多くの技術者の心を掴んだ。つまり、超閉鎖的SNSだと思われているmixiも、AtomPubでの外部との連携手段が存在していた、ということだ。
OpenSocialでは、v0.8からRESTful APIが規定された。これにより、SNSのWebサイト外の任意の場所から、SNSが持つ情報を取得したり更新したりできるようになる。そして、その手順（プロトコル）がOpenSocial RESTful APIによって標準化されたということである。もちろん、OAuthの実装によって、RESTful APIで取得可能な情報に制限がかかるので、SNS側のポリシーがちゃんとリソース管理に反映される。
海外のSNSでは、「知られたくない情報はそもそも入力すんなボケ」という思想が強いっぽく、何の認可もなく、多くの会員情報をSNSから引っこ抜くことは、そう難しい話じゃないし、ものすごい悪意のある行為と見なされるわけでもないらしい。しかし、mixiを初めとする日本のSNSでは、その思想は真逆となる。SNS運営側に大きな情報管理責務が要求されてしまうのだ。
そのため、海外のOpenSocial対応SNSにおいては、
[OAuth Consumer Request 1.0 Draft 1](http://oauth.googlecode.com/svn/spec/ext/consumer_request/1.0/drafts/1/spec.html)による「アクセスユーザを特定しない」認可でも採用の可能性は高いと考えられる。それに対して、例えばmixiであれば、OAuth Consumer Requestが採用されたとしても、xoauth_requestor_idパラメータでのユーザ特定は必須となってしまうだろう。
OAuth問題はともかく、OpenSocialに対応するということは、オープンで標準化された規約に則ってSNSのWebサービスインタフェースが構築できるという点で、非常に有効な選択となる。そして、SNSが管理する情報へのアクセス頻度および他の会員の動向を常に気にするというユーザ行動の特徴を持つSNSにおいて、モバイル端末は格好のデバイスである。そして、標準化されたWebサービスインタフェースであれば、それを利用するアプリケーションが開発される可能性も高くなり、結果として、今後のモバイル端末の方向性となるiPhoneとOpenSocialは相性がとても良い、と考えられる。ちょっと短絡的かもしれないけど。
というわけで、日本のSNSにおける今後のOpenSocial対応の動向が楽しみ。
