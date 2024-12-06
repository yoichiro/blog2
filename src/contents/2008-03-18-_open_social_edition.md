---
layout: post
status: publish
published: true
title: "こみゅすけ Open Social Editionを作りました"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 525
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=525
date: '2008-03-18 09:39:21 +0900'
date_gmt: '2008-03-18 00:39:21 +0900'
categories:
- OpenSocial
---

こみゅすけには、
[RESTful API](http://www.eisbahn.jp/trac/commusuke/wiki/rest)が実装されている。つまり、他のプログラムからこみゅすけの情報を取り出したり変更できるようにしてある。これを使って、「こみゅすけ Open Social Edition」なるものを作ってみた。現在、orkut SandboxとHi5 Sandboxにて動作している。
(1) 
[こみゅすけ Open Social Edition for orkut](http://sandbox.orkut.com/Application.aspx?uid=10282491718911054278&appId=473635203448)

![commusuke-orkut.jpg](http://www.eisbahn.jp/yoichiro/images/commusuke-orkut.jpg)
(2) 
[こみゅすけ Open Social Edition for Hi5](http://207990126.sandbox.hi5.com/friend/apps/entry/www.eisbahn.jp:3001/10.xml?view=canvas&from=devhome&)

![commusuke-hi5.jpg](http://www.eisbahn.jp/yoichiro/images/commusuke-hi5.jpg)
実はこの２つ、プログラム的には全く一緒。異なるOpen Socialコンテナで、言い換えると、異なるSNSサービスで、全く同じアプリケーションがそのまま動作することの証明である。ホントにあっさりと動いてしまったので、僕も正直びっくりした。CSSの解釈の違いなどに悩まされるのかなぁ、と考えていたのだが、所詮iframe内での表示なので、問題は起きにくいということなのだろう。
残念ながら、上記の２つとも、Sandboxという位置づけのOpen Socialコンテナで動くものである。よって、普通にorkutやHi5にログインしたとしても、こみゅすけ Open Social Editionまでたどり着くことができない。
orkutの場合には、Googleアカウントを持っていればorkutにログインすることはできるのだが、Sandboxに入るためには、申請が必要となる。下記のURLにて申請すると、約1日でorkutよりメールが届くので、その後こみゅすけ Open Social Editionに出会うことができるだろう。

[http://code.google.com/support/opensocialsignup/](http://code.google.com/support/opensocialsignup/)
Hi5の場合も、下記のURLからアクセスして、自分のアカウントを開発者として登録することが必要となる。その後、(2)のURLから、こみゅすけ Open Social Editionに出会うことができる。

[http://sandbox.hi5.com/friend/apps/developer.do](http://sandbox.hi5.com/friend/apps/developer.do)
Open SocialコンテナがまだまだSandboxレベルであるため、作ってはみたものの、広く使ってもらうということにはならないのが現状。しかし、身近な人たちからは、

* 軽い！

* 使いやすい！

* 便利！

* この程度でいいんだ！
という、「
[本家](http://commusuke.eisbahn.jp/)を作るのにどれだけ頑張ったと思ってるんだ」と言いたくなるほど、Open Social Editionの出来具合は良いらしい。ま、自分でもそう思う。
Open Socialコンテナの日本での立ち上がりが来ることは確実だとは思うので、その時までにいろいろとOpen Socialアプリを作っておくことは非常に大事なことだ。「同じ人なのに違う人」といった問題もあったりするので、こみゅすけ Open Social Editionをゆっくりと育てていきたい。
こみゅすけのRESTful APIがあったとはいえ、ほぼ1日でここまで作れる。Open Socialアプリ、皆さんもぜひぜひチャレンジしてみて欲しい。
