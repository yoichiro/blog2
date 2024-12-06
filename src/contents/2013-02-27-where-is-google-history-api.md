---
layout: post
status: publish
published: true
title: Google+ History APIはどこに行ってしまったのか？
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2299
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2299
date: '2013-02-27 23:23:24 +0900'
date_gmt: '2013-02-27 14:23:24 +0900'
categories:
- Google Plus
---

その答えが今日発表されました。結局、App Activitiesという新しい機能に統合されました。

[App Activities](https://developers.google.com/+/features/app-activities) - Google+ Platform

[Google Moments Preview](https://groups.google.com/forum/?hl=ja&fromgroups#!forum/google-moments-preview)というGoogle Groupがあったのですが、そこに今日投稿された内容を翻訳したものが以下になります。

>こんにちは、

Google+ Historyの開発者向けプレビュープログラムの貴重なメンバーとして、あなたにGoogle+の今後の方針のプレビューをお伝えします。今日、私たちはGoogle+ Historyのプレビューを終了とし、さらに強力な新しい機能に移行しています。この変更の一部として、私たちはv1momentsエンドポイントを使用停止とし、momentメソッドを
[v1 API](https://developers.google.com/+/api/latest/)に移動しています。あなたは
[Google+ Sign-Inボタン](https://developers.google.com/+/features/sign-in)や、
[アプリアクティビティ](https://developers.google.com/+/features/app-activities)を、他の新しい機能と同じようにあなたのプロダクションアプリにて使用することができます。

私たちは、
[developers.google.com/+/](https://developers.google.com/+/)にて、Google+ developer platformの新規および改善点についての全てを読むことをあなたに推奨します。そして、何かあれば
[Google+コミュニティ](https://plus.google.com/communities/113527920160449995981)にて質問をしてください。

繰り返しますが、この開発者向けプレビュープログラムを実施している間でのあなたからのフィードバックに感謝します。それは、今後のプロダクトの方向性を具体化する上で助けになりました。私たちは、コミュニティがこの新しい機能を使って何を開発するのか、それに出会うことを待つことができません！

Google+ Developer Relations


Google+ PlatformのAPIリファレンスを見てみると、Momentを投稿するAPIは削除されてはいません。しかし、Google+のUIから「History(Dev)」はすでに消えています。APIによって投稿されたMomentはどこに行ってしまうのか？その答えがApp Activitiesになります。

Google+ Sign-Inボタンや直接OAuth2を使って認証認可を行うと、各ユーザのGoogle+ Profileページにそのアプリのリンクが「Apps」という欄に掲載されます。各アプリのリンクをクリックした先が、Momentの一覧が表示されるページになります。Google+ Historyでは、投稿されたMomentは投稿主しか見れない「History(Dev)」というページに一旦掲載され、知人に知らせたいときは改めてShareをする必要がありました。今回の変更で、Momentの投稿に関する認可をユーザから得る際に、誰に（どのサークルに）対してMomentを見せるか、それとも自分だけしか見れないようにするか、選択することができるようになりました。

![](http://www.eisbahn.jp/yoichiro/images/2013/02/google_plus_app_activities_authz.png)

Momentの投稿は、APIでも可能ですし、iOSやAndroid向けのSDKからも投稿することができます。ゲームでハイスコアを取ったときや、ニュース記事を閲覧したとき、何かを気に入ったときや購入したとき、といった「瞬間」をアプリから投稿するというコンセプトは、Google+ Historyの頃と基本的には変わりません。公開範囲の指定ができるようになったなど、機能的に進化して再公開されたということですね。

興味のある方は、ぜひアプリに組み込んでみてください！
