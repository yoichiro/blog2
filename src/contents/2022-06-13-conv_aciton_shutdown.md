---
layout: post
title: Googleアシスタント向け会話型アクションが1年後にシャットダウンされます
categories:
- actions on google
---

2016年にGoogleアシスタントが登場したのとほぼ同時に、Actions on Googleというプラットフォームが公開され、世界中の開発者が独自にGoogleアシスタントを「拡張」できるようになりました。その代表が「会話型アクション」でしたが、1年後の「2023 年 6 月に会話型アクションがシャットダウンされる」ことが発表されました。

[Helping Developers Create Meaningful Voice Interactions with Android](https://developers.googleblog.com/2022/06/Helping-Developers-Create-Meaningful-Voice-Interactions-with-Android.html)

いやー、何となく僕はその予感がしていたのですが、まさか会話型アクションそのものが終了してしまうというところまでいくとは思っていませんでした。

スマートスピーカーやスマートウォッチといった「画面を持たないデバイス」でのGoogleアシスタントの利用では、会話型アクションは非常に重要かつ唯一開発者が積極的に手を出せる場所だっただけに、ちょっとショックを隠しきれません。スマートフォンの次のデバイスは「ウェアラブル」であり「声での操作」だと個人的には期待していて、そのための準備段階として会話型アクションが大きな役割を担いながらその延長線上にハードウェアデバイスが進化していく、という未来を描いていました。

上記の記事の中では、「Androidへの注力」という方針が述べられています。 [AppActions](https://developers.google.com/assistant/app) によるAndroidアプリとGoogleアシスタントの連携は、僕の肌感ではあまり進んでいません。Android開発者コミュニティの中でも（特に日本国内においては）取り上げられていないテーマのように見えます。Androidを指ではなく声で操作できるようになれば、スマートフォンの利用方法として大きな進化が起きるはずです。より簡単に、迅速に、人々が求めているものをAndroidデバイスから得られるようになり、Androidアプリ開発者としても新しいチャンスが到来していると言えるでしょう。

Androidには非常に巨大なエコシステムがあり、こんなにも毎日ユーザが利用しているプラットフォームは他にはありません。その領域に注力するというのは、自然な判断でしょう。ビジネスとしても、今後スマートフォンをどうしていくのかという点でも、当然の判断かと思います。

しかし、それらと並行して、Next Smartphones への投資、Next Smartphones への実験と検証、つまり Next Smartphones への模索や挑戦は、ぜひ続けて欲しかった。

では、Next HubやGoogle Homeなどのスマートスピーカーやスマートディスプレイがただの箱になってしまうのかというと、そうではありません。今まで通りGoogleアシスタントを呼び出すことはできますし、なによりこれから到来する「スマートホーム」の大事な UI として大きな役割を担うことになるでしょう。 [Matter](https://csa-iot.org/all-solutions/matter/) によるスマートホームデバイスの標準仕様によって、スマートホームの世界が今年大きく構築される見込みです。 [Local Home SDK](https://developers.google.com/assistant/smarthome/reference/local) といった仕組みも継続されるでしょうし、 [Google Home Device SDK](https://developers.home.google.com/device-sdk) といった Matter ベースの新しい仕組みがそろそろ登場します。

GDE（Google Developers Expert）のGoogleアシスタント担当としても、今回の発表は非常に残念です。会話型アクションの開発に参加していただいた多くの開発者に、敬意と感謝を伝えたいです。

あと1年は会話型アクションは機能します。おそらく段階的にシャットダウンに向けて動きがあると思いますので、開発者の方々をできる範囲でサポートしていきたいと思っています。

そして、ここで皆さんにお願いです。簡単な会話であれば、 [Actions on Google](https://developers.google.com/assistant) や [Dialogflow](https://cloud.google.com/dialogflow?hl=ja) を使って、プログラムを1行も書くことなしに、会話型アクションを作ることができます。そのことをまだ知らない方々も多いんじゃないか、と思っています。ぜひこの1年の間に、

「あ、自動的に会話を行ってくれるサービスを作るのって、こんなに簡単なんだ〜」

と体験してみてください。これを知っているのと知らないのとでは、間違いなく今後のコンピューティングの進化についていけるかどうか変わってきます。

また新しい情報が入り次第、お伝えいたします。

