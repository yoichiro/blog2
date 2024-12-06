---
layout: post
status: publish
published: true
title: Google+ History Codelab in GTUG Girls
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1670
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1670
date: '2012-09-12 09:34:50 +0900'
date_gmt: '2012-09-12 00:34:50 +0900'
categories:
- Google Plus
---

昨日の11日、GTUG Girlsの第8回目のイベントとなる「Google+ History Codelab」にて、講師をしてきました。



今回のCodelabは、6月末に行われたGoogle I/Oで僕が参加してきた「
[Google+ History Codelab](https://developers.google.com/events/io/sessions/gooio2012/1409/)」をベースに、いくつか説明を加えてアレンジしたものです。Google I/Oの時の講師だったTimothyから講演者用資料を送ってもらって、それを参考に作りました。Thank you, Timothy!

本当は最初に「なごみタイム」なる軽食つまみながらワイワイおしゃべりコーナーの後に本題の講義が始まる予定でしたが、なぜか頼んであったサンドウィッチが来ない。急きょピザを注文し、それが来るまで講義を先に開始しました。イベントにハプニングは付きものです。

![](http://www.eisbahn.jp/yoichiro/images/2012/09/gtug-girls-1.jpg)

Codelabと言いながら、今回コードの修正を行ったのは、たったの1行！WebサーバのセットアップやAPI ConsoleでのOAuth2セットアップなどに手こずるだろうなと予想していて、コーディングをやってる時間が後半ほとんどなくなるんじゃないか、と思っていたんです。でも実際には余裕がありましたね。もう少しコーディングをやっても良かったのかな、と。アンケートでも「え、1行だけ？もっと書くと思ってたんですけどｗｗｗ」って感じの感想がちらほらありました。ごめんなさい。

一通り終わった後に参加者の方々と少しお話できたんですけど、多くの方は「Google+ HistoryでMomentをHistoryにPOSTできることはわかりました。で？」って疑問を持ったようでした。これ、ホント正しいです。正直に告白すると、僕も実は良くわかっていません。インターネット上のユーザのActivityがプラットフォームに集まることについて、プラットフォーマー側はものすごく嬉しいんです。喉から、胃から、腸から、耳から、いろんなところから手が出るほど欲しい情報です。でも、じゃあユーザは何が嬉しいか？ここが単に「APIでMomentをG+に投稿できます」だけだと、きっと嬉しい事って限りなく「ない」んです。

これに何かプラスアルファがあって初めて、MomentのPOSTの認可にユーザは「Accept」するんじゃないかと。例えば、Momentに登録されて、それを他のユーザにShareしてくれると、Shareした人に割引券がもらえるとか、Shareされたフィードを他のユーザがクリックしたときに、そのランディングページにはクーポンがあるとか、何らかグルッと一周するアイディアを作り込んで、初めてGoogle+ Historyは生きてくるんじゃないかな、と僕は考えています。そう言う意味では、気軽に組み込む機能って感じではないかもしれません。

![](http://www.eisbahn.jp/yoichiro/images/2012/09/gtug-girls-2.jpg)

少なくとも、Google+ Historyで使われている
[「HTML Microdata」と「Schema.org」](http://www.eisbahn.jp/yoichiro/2011/10/semantec_web_seo.html)については、WebサイトのSEO対策とかソーシャルメディア対策において非常に重要かつ有用なものだったりします。これらであれば、直接的なメリットをすぐに受けることができるので、お持ちのWebサイトやサービスに採用してみてくれればと思います。

参加していただいた方々、スタッフの方々、ありがとうございました！

P.S. ロングブレス、続ければホントに効果ありますよ。
