---
layout: post
status: publish
published: true
title: "「OpenSocial 3.0 Summit 議事録」から読み取れる流れ"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1336
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1336
date: '2012-03-04 11:32:55 +0900'
date_gmt: '2012-03-04 02:32:55 +0900'
categories:
- OpenSocial
---

2月28日に、メジャーバージョンアップとなるOpenSocial 3.0をテーマにしたサミットが行われました。その議事録が公開されています。

![](http://www.eisbahn.jp/yoichiro/images/2012/03/global.logo_.png)

[OpenSocial 3.0 Summit Minutes](http://goo.gl/sZKnT)

参加者の所属企業を抜き出してみると、以下のような感じになります。ちなみに括弧内の数字は参加人数です。

* [Jive Software](http://www.jivesoftware.com/) (5)

* [Mitre](http://www.mitre.org/) (2)

* [IBM](http://www.ibm.com/) (6)

* [Sutro Software](http://sutrosoftware.com/) (1)

* [Sugar CRM](http://www.sugarcrm.com/) (1)

* [EPFL](http://www.epfl.ch/) (1)

* Office of the DoD (1)

* [Atlassian](http://www.atlassian.com/) (1)

* [AppFusions](http://www.appfusions.com/) (1)

これを見ればわかる通り、すっかりエンタープライズなメンバーが揃ってます。一般的にソーシャルメディア、ソーシャルネットワーキングサービスと言われるサービスをやってる企業は一つもない。昨年の5月に行われたOpenSocial Union eventでは、Googleやいくつかの国のSNSが展示などを行っていて、エンターテイメント向けのOpenSocial利用がテーマとして扱われていました。しかし、エンタープライズ向けの用途を目指した仕様策定に完全にシフトしていることが、ここから見て取れます。

そういえば、OpenSocial fundationの現Board leaderであるMark Weitzelが、IBMからJive Softwareに転職していました。いつの間に・・・。そう言えばMySpaceのChris Coleも名前がありませんでした。ちょっと前までは皆勤賞レベルだったけどなぁ。

扱われたテーマごとに、簡単に議事内容を紹介してみましょう。

Discussion about spec release timelines and scope

まずはバージョンアップのロードマップの共有から行われました。現在検討されているリリースバージョンは、2.5と3.0の2つがあります。それぞれのリリース目標は以下だそうです。

* 2.5 - 2012年5月

* 3.0 - 2013年3月

3.0はだいぶ先ですね。議事録の中では、Shindigのプロトタイプを開発タスクと、仕様策定タスクをJIRA上でリンクしながら進めましょう、ということが記載されています。

今知ったのですが、2.5では以下の3つの仕様がDeprecate扱いにされようとしています。

* Tabs

* Skins

* OpenSocial WAP Extension

個人的にも同意なのですが、ちょっと残念な気持ちもあります。もはや遅いのですが、当時WAPといったデバイスに限定せず、Proxyモデルでのガジェットレンダリング方式として仕様提案しようとしたのですが、残念ながらそれだとなかなか理解してもらえなかった、という背景があり、でももっと頑張っておけば良かったなと。

Metadata Service

Deprecatedのためのルール？APIを廃止する前に交換されるAPIをインキュベートする必要がある、と議事録に残ってますが、よくわかりません。

Metadata Service自体は新しく提案されている仕様であり、2.5での採用を予定している模様です。ただし、名前がGadget metadata serviceと紛らわしいため、別の名前を募集中とのこと。

Common container discussion

Shindigにおいて解決されるべきいくつかのバグがあり、それをクリーンアップすべきであるが、Container API自体は準備完了らしいです。更に、元のAPIの仕様がこの提案にて使われていないことを気にする必要がある、と言ってる人もいて、これは2.5から除外されるかもしれません。

Embedded Experiences

これは2.0から見送られた比較的大きな提案です。どうやらEEについて6個の違ったデモが存在するらしく、コントロールすることが難しくなっている模様です。Jiveが更に実装を開始しているらしく、おそらくJive陣営がこの仕様のイニシアティブを握りたい考えがあるっぽい。ApplicationやContainerのコンテキストに情報を多く持たせるために、親子関係を持ち込もうとしているみたいですが、ともかくEEの議事録には「まだTBDだね」で締めくくられていました。

Spaces proposal

SpaceのIDとしてIRIを使うべき？とか、osapi.apps.*を足すべき？とか、まずはJavaScript APIにフォーカスしてREST APIは再検討すべきじゃね？とか、Spaceをフォローしているという情報はソーシャルグラフに入れるべきじゃね？いや違くね？とか、まだまだ決めきれない部分がある模様です。結局は、Spaces提案について、リソースとは何なのか、についてもっと一般的なアイディアが必要だ、ということになったようです。これは、OpenSocialにおいて、いかなるリソースもActivityStreamsに関連づけられるべきだ、という考えから来ているものです。

Editor discussion

仕様の編集者が求められている模様です。つまり、仕様について既に編集可能になった問題を誰かが仕様に書き込むことが求められている、ということです。これは、技術的なことがある程度わかっていないとできないことです。Foundationの新しい問題としてあげられました。ただ、このSummitの中で、すでに2名ほど推薦されているみたいです。

Versioning

これは順当に2.5に入る模様です。バージョンを示すためにLinkヘッダを使うこと、ソーシャルデータの書式に対するバージョニングを行うためにMIME Typeを使うこと、という基本方針が共通認識となったようです。REST APIについては問題なく、Gadget APIについては更なるディスカッションが必要だね、ということで議事録が終わっています。

REST API

後方互換を保ちながらREST APIをどのように改訂していくか、についてのアイディアが出されました。例としてあがっていたのは、どのように"/user1, user2/ (friends,) followers を返すか"といったことだそうです。おそらく、SpacesやEEについて、これをREST APIに取り込む際に、今までの /people/@me/@friends 的なシンプルなパスでは賄えないものが出てきて、でも昔の仕様は崩せず、どうしたらいいか、という悩みから来ていると思われます。

これは3.0に向けて継続的に話していこう、となった模様ですが、新しいURI定義でパッチをサポートする、といったことも書かれていて、実は後方互換性が崩されるような懸念も議事録を読んで個人的に思いました。。。

Adding group models

Fine. ということなので、問題なく2.5に取り込まれるでしょう。

Deprecate XML support

新しいAPIを定義した時に、リクエストとレスポンスでXMLをこれからもサポートするの？という問題提起です。もちろん世の中はJSONにすっかり傾いているので、個人的にもXMLはもう廃止でいいと思ってます。

2.5でdeprecatedにして、3.0で廃止になる予定、となった模様です。

Adding label to Plural Fields

全ての複数のフィールドにはラベルを持つようにしよう、という提案です。ただ、OpenSocialを拡張するたびに登録が必要、とか、OpenSocialモデルへの拡張についてどのように機能すべきか定義しないと、とか、もう少しディスカッションは必要そうです。ただ、2.5には入れよう、という方向は強いようです。

Simplification of the Entire Spec

OpenSocialも登場してから数年経っていて、そろそろ仕様を再構成する必要があるよね、という話です。これは細かくブレイクダウンしていくことが必要ですが、OpenSocialの手引き書とかチュートリアルとかがかなり必要だよね、とも書かれています。

現在2.5に向けてハードワーク状態なので、これが過ぎたらディスカッションのテーブルを設けましょう、となった模様です。

When do we get together again?

次いつ会える？という話です。Google I/Oじゃね？と。多くの開発者も来ることですし。

Areas for Future Discussion

直近で話すこととして、以下があげられました。

* 3.0において@ownerを取り入れられる？それは他になるべき？

* 3.0においてAppDataを再検討できる？

* 僕らは、今日のSummitでのように、より会議をファシリテートする必要があります。

* OAuth 2.0 (data pipelining, templating

所感

「あー、なんかますます違うところに行ってしまったかなぁ」と思ってしまいました。というか、エンタメ向けのOpenSocial利用は枯れた、というか、海外ではソーシャルアプリケーションについておそらくFacebookがシェアのほとんどを得てしまっているため、OpenSocialとしては別の道に行くことが存続意義になるのかな、と。その流れを表している良い例として、Portable Contactsは実際OpenSocialに取り込まれましたが、最近ではPortable ContactsのみでAPIを策定するってことも行われてきています。Google+ APIがそうです。つまり、ソーシャルメディアやソーシャルネットワーキングサービスにおけるAPIの仕様策定においては、（特にガジェットレンダリングが主眼に置かれた）OpenSocialは重量級すぎていて、より細かな仕様が使われることが多くなってきている、という流れなんじゃないかと思っています。

ただし、企業システムからすると、部品化や可搬性といった点は長年の夢であり、それを満たしてくれそうな仕様は歓迎されてきたという歴史があります。これにOpenSocial specは比較的合っていることにBoard memberは気がついていて、どんどんそっちの流れにシフトしてきている、ということでしょう。これは、今回のSummitの参加メンバーの所属企業が行っている各サービスやソリューションを見ていけばわかることですね。

多くの人々が「OpenSocialはソーシャルアプリのための標準仕様」だと思っていることでしょう。もちろん、それは今も正しいです。しかし、そろそろその「適用されているドメイン」が変化してきている、と考えるべきですね。簡単に言えば、エンタメからエンタープライズへ、と捉えれば正解かと思います。
