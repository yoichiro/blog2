---
layout: post
status: publish
published: true
title: OpenSocial State of the Unionイベントに参加しました
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 990
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=990
date: '2011-06-01 09:26:28 +0900'
date_gmt: '2011-06-01 00:26:28 +0900'
categories:
- OpenSocial
---

Google I/O 2011の次の日、Google San Franciscoオフィスにて「OpenSocial State of the Union event」が開催され、それに参加してきました。「今年はやらないの〜？」とMLにて聞いたところ、めでたく今年も開催される運びとなりました。

現在OpenSocial v2.0の策定が進んでいます。これは結構意欲的な試みが含まれており、OpenSocial Foundation的にも「この日は"Open App Revolution"が開始された日として記憶されるだろう」と言ってしまうほど、重要なイベントとして位置づけられています。だいたい30〜40人くらいの参加者だったと思います。その中に僕もいることができたことは嬉しいことです（別に参加資格があるとか、そういうことじゃないけど）。ロゴからして、「革命だ！」って感じでしょ？

![](http://www.eisbahn.jp/yoichiro/images/2011/06/OpenSocial_fulldesign.png)

Mark Weitzelによる挨拶

昨年は2時間くらい遅刻したんですけど、今年は最初から最後まで会場にいました。まずはMark Weitzelの挨拶からスタートです。OpenSocialは2007年11月に生まれました。2008年は主要SNSがOpenSocial対応を遂げたことで1年間で6億人にOpenSocialがリーチしたこと、2010年はOpenSocialがB2CだけでなくB2B、つまりエンタープライズ領域にも進出したことが振り返りとして紹介されます。その間にも、OpenSocialに貢献してきた主要メンバーの紹介が行われていきます。そして、2011年のキーワードとして、以下が取り上げられました。

* OpenSocial based App Markets

* Activity Streams

* OAuth 2.0

* Embedded Experience

* Mobile

* Monetization

最後のスライドにて、宣言がされました。「BE A REVOLUTIONARY! （革命家になれ！）」

ActivityStreams

次に、Activity Streamsについてのセッションが開始されました。最初にActivity Streamsの簡単な紹介と、実際にOpenSocial v2.0にてどう適用されるのかの説明が行われました。衝撃的な言葉としては「Portable Contactsはもう古い！これからはActivity Streamsだ！」でしたね。え、比較対象として違くない？と思いましたが、まぁ部分的にはあっているかもしれないのでよしとします。簡単に言うと、Activities APIにおける各Activity Entityの表現をActivity Streams仕様にしましょう、ということです。IBMの中の人によって、このストリームを直接取り込んで表示するデモが行われました。

[http://opensocial-resources.googlecode.com/svn/spec/2.0/Social-Data.xml#ActivityEntry](http://opensocial-resources.googlecode.com/svn/spec/2.0/Social-Data.xml#ActivityEntry)

ここは、すでにmixi Graph APIでもUpdates APIにてActivity Streamsをサポートしていたこともあり、まぁそうだよねASだよね、とすんなり理解することができました。相互運用の可能性を高めてくれるActivity Streamsのサポートは、非常に期待できます。

Apps on mobile devices

OpenSocial v1.1の議論からすでに始まっていたモバイルデバイス対応について、IBMの中の人から説明がありました。ここで言うモバイルとは、スマートフォンのことです。v1.1までの議論では、アーキテクチャはPCのものを採用し、View名を"mobiledevice"にする、といった提案がされていました。これはさすがにどうか、という話になって、v1.1では見送りになっています。

今回のデモでは、いくつかの異なるモバイルデバイス上にて、同じアプリケーションが実際に動いていました。これを実現するための基盤として「RoadTrip」というものが紹介されていたのですが、肝心のRoadTripが何者かさっぱりわからず、実はこのセッションはほとんど理解できませんでした。セッション中にググっても全くそれらしいものはヒットせず、英語を聞いててもよくわかりません。

プレゼンの中には、RoadTripについて以下のキーワードが出てきていました。

* chassis経由の高いパフォーマンスのゼロインストールハイブリッドフレームワーク

* コンポーネント間の横断的なシームレスナビゲーション

* ボタンのための位置固定なツールバー

* ネイティブライクなスクロールサポート

* 方向サポート（landscape, portrait）

* タブレット形状へのビュー適用

これを見ると、アプリケーションのためのフレームワークであり、ツールバーの提供によるUIの規定やコンポーネント間の調停などをやってくれるようなもの、ってことが言えそうです。これ以外の特徴として「ネイティブアプリとのコンテキストリンク」ということがあげられているところを見ると、RoadTripはWebブラウザ上で動作するアプリ向けの基盤？と想像することもできますが、真意は謎です。もしかしたら、SDK的なもの、かもしれません。うーん、後者かな。カメラとかGPSとか、そういうデバイス依存な機構にアクセス可能、とも言われているので、きっとそうですね。

では、ShindigとRoadTripとの関係ですが、実際のガジェットはShindigが配信しRoadTrip Container上で動くものとなるため、RoadTrip向けのFeatureのガジェットへの供給がShindigの担当範囲ということになりそうです。そのFeature、つまり供給されたJavaScriptコードは、RoadTrip Containerの機能を呼び出すことができるのでしょう。そして、デバイス固有の機能、カメラや電話帳など、そういったものにアプリからアクセスできる、っていうアーキテクチャだと思います。

さらに、PubSub2によって、非同期のメッセージングをガジェット間で行える、という話もありました。AndroidであればIntentに相当するようなものだと思われます。というか、実際の実装はAndroidにおいてはIntentが使われていると想像できます。これによって、営業成績サマリーアプリから、得意先別売り上げグラフ表示アプリに連携する、といった利用シーンを描くことができるようになるでしょう。

このRoadTripですが、オープンソースにし、そしてShindigにもコミットすることがこの場で公表されました。拍手が起きたのは言うまでもありません。

Apache Shindig

OpenSocialに関心のある方であれば誰でも気になるApache Shindigの状況ですが、このイベントにおいてもShindigのUpdatesは最も注目されるニュースです。メインコミッタのPaul Linderから、Shindigのロードマップなどについて発表がありました。現在の最新バージョンは2.0.2であり、これは昨年の11月1日にリリースされました。現在は3.0.0-beta1がビルドされていますが、まだ公開はされていません。

面白い話として、オープンソースならではなんですが、新しいコミッタが6人増えたことと、コミット回数のランキングが公表されていました。トップのPaulが441、2位が202、3位が89、というように、やはりPaulのアクティブさが光っています。そして、ソースコードの各分野に誰がコミットしたかを時系列に視覚化された動画が流れました。Paulがさまざまな分野に攻撃（Paulのノードからコミット対象の分野に線が引かれるのがいかにもビーム光線）していることがよくわかります。movファイルはPaulのローカル環境にしかなかったみたいなので今は見れないのですが、もしSubversionのログからそのような表示を自動的に視覚化するツールがあれば、とても面白いものが見れることでしょう。

ここ最近は、JavaScript featuresの再構成、クロージャコンパイラによる動的なJavaScriptコンパイル、Cajaパーサーの変更（Nekoに）、そしてgadgets.rpcトランスポートのリライトなどが行われました。もちろん、OpenSocial 2.0（プレゼンのロゴがOS/2だったのがうけた）への対応を進めなければならない段階に来ているのですが、いくつかの問題もShindigは持っています。たとえば、

* Nixトランスポートに関する問題への対処

* Java 1.5やServlet 2.4、ifpcなどのレガシーサポート

* 複雑：カスタマイズの困難さ、良い例の欠如、古くなったドキュメント

といったものがあります。

ロードマップとしては、以下の発表が行われました。

* 3.0 Release Q2: Activity Streams, OAuthのサポートなど

* 3.1 Release Q3: Draft 2.0のサポート

* 4.0 Release Q4

そして、新しいFeatureの実装や2.0.xへのバックポートをしてくれるコミッタを募集していること、ソースコードがgithubに移ること、ShindigサイトをHTML5にすること、そしてShindig hack-meetupを開催したいと思っていること、なども参加者に伝えられました。Node.jsへの対応やMySpaceによって実装されたNegroni（C# .NetでのShindig実装）がオープンソースとなることなどもニュースでしたね。

Be a revolutionary

他にも、ChrisによるOS-Templatesなどの紹介や、Embedded Experienceのセッション、そしてOpenSocialをエンタープライズ領域で適用しているメンバーによるパネルディスカッションなど、かなり内容は濃いイベントでした。着実にバージョンアップをオープンな形で進めているFoundationですので、OpenSocialは今後もしばらく死ぬことはありません。それよりも、これだけ世界的に使われている仕様が、たった数人のコアメンバーで支えられていることに驚きです。そのメンバーに対しての貢献、そしてOpenSocial全体への貢献を今後も続けていかなければ、と決意を新たにした1日でした。

![](http://www.eisbahn.jp/yoichiro/images/2011/06/IMG_0305.jpg)

プレゼン資料やイベントの概要は、以下のURLから見ることができます。

[https://docs.opensocial.org/x/eYFC](https://docs.opensocial.org/x/eYFC)

来年のイベント内で名前が出てくるくらいの貢献をしたい！ホントにそう思っています。
