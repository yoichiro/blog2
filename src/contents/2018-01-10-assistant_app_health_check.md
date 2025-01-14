---
layout: post
title: Googleアシスタントアプリの裏側にエラーが頻発した時の挙動
categories:
- actions on google
---
昨年末に、自作のGoogleアシスタント向けアプリ「イートアンドバイト」を公開しています。簡単な3桁の数字を
あてるゲームなのですが、アシスタント向けアプリの開発から審査、公開、運用を一通り体験するためには手頃な
サイズでした。Google HomeやAndroidのGoogleアシスタントなどに対して「イートアンドバイトにつないで」
と話しかけることで遊べますので、ぜひ試してみてください。

さて、審査を無事通過してアシスタント向けアプリを公開した後は、Actions on Googleのコンソール上で、
統計値を見ることができるようになります。その中に、Healthという項目があります。

![aog_health_check_1.png]({{ "/images/2018/01/aog_health_check_1.png" | prepend: site.baseurl }})

ここでは、どれだけエラーが検出されたかを見ることができます。Actions on GoogleとDialogflow間で何か
エラーが起きた時や、DialogflowからFulfillmentの呼び出しで失敗したなど、正しく処理されなかったことが
どれだけあったかをHealth項目で把握できるわけです。

# エラー頻発になったらどうなるか

イートアンドバイトは、Dialogflowを使っていて、フルフィルメントはFirebase Cloud Functionsで実装して
います。そのため、エラーが出るような事態にはならないだろう、と思っていました。しかし、今日、突然Google
から以下のようなメールが届きました。

![aog_health_check_2.png]({{ "/images/2018/01/aog_health_check_2.png" | prepend: site.baseurl }})

簡単に訳してみると、以下のようなことが書かれていました。

> Oh ノー、アシスタントアプリに問題があるようです。プロジェクトID hitandblow-4e1bd の Bulls and Cows の
バージョン3のエンドポイントは、現在応答していません。その結果、あなたのアプリはユーザーに一時的に利用
できなくなります。Stackdriverログには、アプリのエラーの詳細が表示されます。不健康なアプリの詳細をご覧ください。<br>
> エンドポイントが再び応答すると、すぐにアプリの配信を再開します。その際には、電子メールが届きます。
アクションコンソールでこのバージョンのステータスを追跡することもできます。さらに、アプリのHealthアナリティクスを
表示することもできます。

アシスタント向けアプリの裏側のどこかでエラーが連続で発生すると、どうやらそのアプリはユーザから一時的に利用
できなくなってしまう模様です。そのことがメールで通知された、ということのようでした。

# エラーが出なくなったらどうなるか

とはいえ、特にコードや構成を変えていませんので、自分では原因に心当たりは全くありません。何でだぁ？
と途方に暮れていたら、もう一通メールが届きました。

![aog_health_check_3.png]({{ "/images/2018/01/aog_health_check_3.png" | prepend: site.baseurl }})

> エンドポイントが応答しているように見えるので、プロジェクトID hitandblow-4e1bd の Bulls and Cows の
バージョン3を再度利用できます。この変更が有効になるまでに最大30分かかる場合があります。

エラーが出なくなれば、自動的に復活（再度ユーザが利用できるようになる）するようです。メールが来てすぐに利用は
できませんでしたが、確かに30分程度待った後に試してみたら、利用できるようになってました。

# まとめ

このように、メールで知らせてくれるので、うまく動作していない時に気がつくことができます。そして、エラーが頻発した
状況では、自動的にユーザからの呼び出しをGoogleアシスタント側でブロックする挙動になります。ユーザからしたら、
よく分からない返事や、そもそも返事が来ない、といった応答ではなく、「利用できません」と言われた方が体験的には
良く、Googleアシスタントがそうやってユーザからのリクエストに対応してくれている間に、アプリ開発者は迅速に復旧を
行う、って感じになると思います。
