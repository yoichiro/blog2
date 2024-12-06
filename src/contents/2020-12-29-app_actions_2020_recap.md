---
layout: post
title: App Actions 2020 Recapの日本語訳です
categories:
- actions on google
---

今年は新型コロナウイルスの流行のために、開発者イベントなどが軒並みキャンセルされ、またオンラインイベントでの発信となり、情報を得るための難易度が昨年までよりも少し上がってしまった印象があります。しかし、物理的な移動をせずとも多くの一次情報に触れる機会が増えたことも今年の特徴であり、注意深くウォッチし続けてさえいれば、今年も着実に様々な技術に進化が見られたことがわかるはずです。

Googleアシスタントに関しても、Androidアプリとの連携について大きなマイルストーンを迎えました。皆さん、来年はAndroidを指で操作しなくなりますよ！指の代わりに、声で操作するようになっていきます。そのための重要な技術として、App Actionsがあります。今年は、App Actionsの環境整備が整った年となりました。来年は、多くのAndroid開発者がApp ActionsをAndroidアプリに組み込んでいく番です。

以下は、Googleアシスタントを担当するDevRelの一員である [Jessica](https://twitter.com/chatasweetie) の許可を得て、[App Actions 2020 Recap](https://medium.com/google-developers/app-actions-2020-recap-c1cad7759158) を日本語訳したものです。App Actionsに関する2020年のトピックがよくまとめられていますので、ぜひ目を通していただければと思います。

---

App Actionsを使用すると、ユーザーはGoogleアシスタントを使用してAndroidアプリの特定の機能を起動できます。ユーザーの観点から、App Actionsは、アプリまたはアプリ内の特定のアクティビティに口頭でナビゲートするための迅速な方法をユーザーに提供します。


![]({{ "/images/2020/12/app-actions-2020-recap-1.webp" | prepend: site.baseurl }})


Example App Actions ユーザ問い合わせフロー

2020年には、 [30以上の組み込みインテント](https://developers.google.com/assistant/app/reference/built-in-intents) をリリースし、全体の数を60以上にしました。アプリ内の特定のアクティビティを開く「Open app feature」や、デフォルトのアプリ内検索機能を使用してコンテンツを検索する「Get thing」などの新しい一般的な組み込みインテントは、主要なアプリで使用されています。あなたのアプリのカテゴリがリストにない場合は、 [カスタムインテント](https://developers.google.com/assistant/app/custom-intents) を使用できます。カスタムインテントでは、 [クエリパターン](https://developers.google.com/assistant/app/custom-intents#query-patterns) を提供する必要があります。これは、ユーザーがこのアクティビティを表現するために言うさまざまな方法です。

[メニュー項目の注文](https://developers.google.com/assistant/app/reference/built-in-intents/food-and-drink/order-menu-item) や [運動プランの取得](https://developers.google.com/assistant/app/reference/built-in-intents/health-and-fitness/get-exercise-plan) などの組み込みインテントの場合、フルフィルメントURLはWebインベントリを活用できます。つまり、アシスタントは公開されたウェブコンテンツのGoogle検索インデックスを使用して、ネイティブのAndroidエクスペリエンスでのレンダリングを担当するアプリにURLを転送しています。  [Webインベントリ](https://developers.google.com/assistant/app/action-schema#web-inventory) は、Webサイトを使用して、App ActionフルフィルメントのURLを検出します。この機能は、強力なWebプレゼンスがあり、アプリ内ディープリンクが公開されているWebコンテンツを中心に編成されている場合に最も役立ちます。

[Foreground app invocation](https://developers.google.com/assistant/app/foreground-app) により、特定のアクティビティがデバイスのフォアグラウンドにあるときにアプリ名を指定しなくても、組み込みのインテントを一致させることができます。たとえば、ユーザーがライドシェアアプリをフォアグラウンドに持っていて、「マウンテンビューへ乗せてもらうことを注文する」と Googleアシスタントに言ったり入力したりします。アプリはこの入力を使用して、行き先フィールドをマウンテンビューに設定します。次に、ユーザーが「SFOから乗車を注文する」と言うか入力すると、アプリの状態を維持しながら、アプリでピックアップ場所を追加で設定できます。

開発者をさらにサポートするために、 [AppActionsテストツール](https://developers.google.com/assistant/app/test-tool) がリリースされました。 それは、Playストアでアプリの新しいバージョンを送信する前に、AndroidStudioでアプリのアクションをテストするメカニズムを提供します。

以下は、今年リリースされたコードサンプルや動画のリソースのリストです。

_新しいサンプル_

* [App Actions to-do list sample](https://github.com/actions-on-google/appactions-common-biis-kotlin) ユーザは、やることリストにアイテムを追加したり、カテゴリでアイテムを検索したり、完了したタスクに関する情報を表示したりできます。
* [Get analytics for App Actions using Google Analytics Sample](https://github.com/actions-on-google/appactions-common-biis-kotlin/tree/get-analytics-with-firebase)  Google Analytics for FirebaseSDKを使用したログイベント。Firebase Consoleを使用してトラフィックパターンを視覚化し、アプリの使用状況とユーザーエンゲージメントの洞察を得ることができます。

_新しい動画_

* [Extend your Android application with Google Assistant](https://www.youtube.com/watch?v=fV54rTntWX4)
* [A Conversation about Android app](https://www.youtube.com/watch?v=pIiO-nPC7kI)

# 優れた学習リソース


* ドキュメント: [goo.gle/assistant-app-docs](https://goo.gle/assistant-app-docs)
* サンプル: [App Actions Samples](https://developers.google.com/assistant/app/samples)
* コードラボ: [Extend an Android app to Google Assistant with App Actions](https://codelabs.developers.google.com/codelabs/appactions)
* プレイリスト: [App Action Video Playlist](https://www.youtube.com/playlist?list=PLOU2XLYxmsILJWy1k3BO7dScDSPL4KM2e)

# その他のリソース

* [Stack Overflow](https://stackoverflow.com/questions/tagged/app-actions) で技術的な質問をする
* [Twitter](https://goo.gle/assistant-twitter) でフォローして、最新のお知らせを入手する
* [Redditコミュニティ](https://goo.gle/assistant-reddit) で他のアシスタント開発者とつながる
* [全てのAssistantの動画再生リスト](https://goo.gle/assistant-videos) を確認する

会話型アクションまたはスマートホームアクションに興味がありますか？以下にリストされているすべての新機能とリソースを備えた独自のブログをチェックしてください。

* [Conversational Actions 2020 Recap Blog](https://medium.com/google-developers/conversational-actions-2020-recap-dc486871535b)
* [Smart Home 2020 Recap Blog](https://medium.com/google-developers/smart-home-actions-2020-update-resources-3c503b143c8d)

読んでくれてありがとう！あなたの考えや質問を共有するには、 [r/GoogleAssistantDev](https://www.reddit.com/r/GoogleAssistantDev/) のRedditに参加してください。

Twitterで [@ActionsOnGoogle](https://twitter.com/ActionsOnGoogle) をフォローしてチームの最新情報を確認し、AoGDevs を使用してツイートして作業内容を共有してください。あなたが作ったものを見るのが待ちきれません！

