---
layout: post
title: Conversational Actions 2020 Recapの日本語訳です
categories:
- actions on google
---

今年は新型コロナウイルスの流行のために、開発者イベントなどが軒並みキャンセルされ、またオンラインイベントでの発信となり、情報を得るための難易度が昨年までよりも少し上がってしまった印象があります。しかし、物理的な移動をせずとも多くの一次情報に触れる機会が増えたことも今年の特徴であり、注意深くウォッチし続けてさえいれば、今年も着実に様々な技術に進化が見られたことがわかるはずです。

Googleアシスタントに関しても、特にアクションの開発環境に関して大きなリリースがありました。また、Interactive CanvasやAssistant Linksなどについても、着実に進化を遂げています。

以下は、Googleアシスタントを担当するDevRelの一員である [Jessica](https://twitter.com/chatasweetie) の許可を得て、[Conversational Actions 2020 Recap](https://medium.com/google-developers/conversational-actions-2020-recap-dc486871535b) を日本語訳したものです。会話型アクションに関する2020年のトピックがよくまとめられていますので、ぜひ目を通していただければと思います。

---

会話型アクションは、Googleアシスタントのユーザー向けにカスタムエクスペリエンスまたは会話を作成できるようにすることで、Googleアシスタントの機能を拡張します。年間を通じて、いくつかのツールとリソースをローンチしました。会話型アクションの領域にて、主要なトピック別に整理されたすべてが、以下の一覧になります。

* Actions Builder & Actions SDK
* Interactive Canvas
* Discovery

# Build with Actions Builder & Actions SDK

[ActionsBuilderとActionsSDK](https://developers.google.com/assistant/conversational/build) は6月にリリースされ、ゲームなどのスマートディスプレイ向けのアシスタントでの会話型アクションの設計と構築が容易になりました。 [Actions Builder](https://developers.google.com/assistant/conversational/build#actions_builder) は、Actionsコンソールで開発、テスト、およびデプロイするための強力で使いやすいWebベースのIDEを提供します。グラフィカルインターフェイスを使用して、会話の流れを視覚化し、自然言語理解（NLU）トレーニングデータを管理し、高度なツールを使用してデバッグできます。

更新された [ActionsSDK](https://developers.google.com/assistant/conversational/build#actions_sdk) は、Actionsプロジェクトのファイルベースの表現を提供します。更新されたコマンドラインインターフェイス（CLI）を使用して、会話を構築し、トレーニングデータの一括インポートおよびエクスポートを行うことができます。

[ActionsBuilderとActionsSDK](https://developers.google.com/assistant/conversational/build) は同じテクノロジーに基づいて構築されているため、お好みのツールを使用してアクションを開発できます。そして、チームが同じプロジェクトで共同作業できるようにします。


![]({{ "/images/2020/12/conversational-actions-2020-recap-1.webp" | prepend: site.baseurl }})


_ローンチ後の改善_

アクション開発の管理を改善するために、バージョン管理、テスト、分析を使用してActionsBuilderとActionsSDKの改善を続けてきました。 現在では、Actions SDKで [バージョンを一覧表示](https://developers.google.com/assistant/actionssdk/gactions/guide.md#listing_versions) し、ダウンロードする [バージョンを指定](https://developers.google.com/assistant/actionssdk/gactions/guide.md#download_actions_from_actions_console) できるようになりました。これにより、Actionsプロジェクトをローカルファイルシステム上に構成できます。 VC Codeを使用して開発している場合は、 [VS Code extension for Actions SDK](https://github.com/actions-on-google/actionssdk-vscode-extension) で、すべてのActions SDK YAMLファイルのオートコンプリートやエラーチェックなどの機能を確認してみてください。 Actions SDKは、現在では [Node.js Testing Library](https://github.com/actions-on-google/assistant-conversation-testing-nodejs) を使用したテストをサポートするようになったため、自動テストを構築して、アクションが期待どおりに応答していることを確認できます。アクションのアルファ版、ベータ版、または製品版を公開すると、Actionsコンソールで、リリースの種類ごとの [分析](https://developers.google.com/assistant/console/analytics) を確認できるようになります。これは、本番環境に移行する前にアルファアクションとベータアクションを評価するのに理想的です。

スマートディスプレイの普及に伴い、それらは通常共有エリアに配置されているため、家庭内のすべての人に役立つサポートが [Home storage](https://developers.google.com/assistant/conversational/storage-home) に追加されました。Home storage を使用すると、開発者は（ホームグラフに基づく）同じ世帯内の複数のセッションにわたって値を保存できます。この機能は、家庭でのゲームに最適です。

_Actions Builder & Actions SDKへのマイグレート_

Dialogflowを使用してアクションをビルドしている場合は、Actionsコンソール内でプロジェクトをActions Builderに移行できます。 [Dialogflow to Actions Builder migration tool](https://developers.google.com/assistant/conversational/project-migration) は、DialgoflowエージェントをActionsBuilderプロジェクトに移行する処理を行います。その後、 [Fulfillment Migration](https://developers.google.com/assistant/conversational/fulfillment-migration) ガイドを使用してフルフィルメントコードを更新できます。概要については、 [Migration from Dialogflow to Actions Builder video](https://www.youtube.com/watch?v=ULphCg5NZrA) をご覧ください。

Actions BuilderやActions SDKのコードサンプル、コードラボ、動画の一覧が以下となります。

* [Hello World sample](https://github.com/actions-on-google/actions-builder-hello-world-nodejs)
* 自動テストを含む [Conversation Components](https://github.com/actions-on-google/actions-builder-conversation-components-nodejs)
* [Transaction](https://github.com/actions-on-google/actions-builder-transactions-nodejs)
* [Action Builder Patterns](https://github.com/actions-on-google/actions-builder-patterns-nodejs)
* [Custom NLU](https://github.com/actions-on-google/actions-builder-custom-nlu-nodejs)
* [Account Linking](https://github.com/actions-on-google/actions-builder-account-linking-nodejs)
* [Localization](https://github.com/actions-on-google/actions-builder-question-l10n-nodejs)
* [Facts about Google](https://github.com/actions-on-google/actions-builder-facts-about-google-nodejs)

_Actions Builder & Actions SDKコードラボ_

* Build Actions using Actions Builder [Level 1](https://codelabs.developers.google.com/codelabs/actions-builder-1), [Level 2](https://codelabs.developers.google.com/codelabs/actions-builder-2)
* Build Actions using Actions SDK [Level 1](https://developers.google.com/codelabs/actions-sdk-1), [Level 2](https://developers.google.com/codelabs/actions-sdk-2)

_Actions Builder & Actions SDK動画_

* [Overview of Actions Builder & Actions SDK](https://www.youtube.com/watch?v=IKoyKMKTHag)
* [Actions Project overview](https://www.youtube.com/watch?v=BbT5Q9QxpiI)
* [Intents overview](https://www.youtube.com/watch?v=HAsFJTNIwTg)
* [Scenes overview](https://www.youtube.com/watch?v=CI-dSFP6_g4)
* [Creating an Action with Actions Builder](https://www.youtube.com/watch?v=Z1hxvniJ18s)
* [Creating an Action Actions SDK](https://www.youtube.com/watch?v=V6s5vp4l4WE)

# Interactive Canvas

ActionsBuilderとActionsSDKのリリースに伴い、 [Interactive Canvas](https://developers.google.com/assistant/interactivecanvas/reference) APIが更新され、改善されました。 Actionsプロジェクト、フルフィルメント、Interactive Canvas Webアプリ間でデータを送信する方法はいくつかあります。 [setCanvasState](https://developers.google.com/assistant/interactivecanvas/web-apps#setcanvasstate) ()を使用して、Interactive Canvas Webアプリからフルフィルメントにデータを送信できます。また、フルフィルメントの実装を行わなくても、Interactive Canvas Webアプリに [Actionsプロジェクトからデータを送信](https://developers.google.com/assistant/interactivecanvas/prompts#use_actions_builder_to_pass_data) できます。 Canvas 応答へのURLの追加の繰り返しを最小限に抑えるために、Actionsコンソールで [デフォルトのWebアプリのURLを追加](https://developers.google.com/assistant/interactivecanvas/prompts#render_the_web_app) できるようになりました。

_Action Builder & ActionsSDKのリリース以降の改善_

Interactive Canvasアクションのデバッグは、ソフトウェアバージョン30以降のGoogle HomeまたはGoogleNestスマートディスプレイ向けに [ChromeDevToolsを使用](https://developers.google.com/assistant/interactivecanvas/debug) することで簡単になりました。または、シミュレーターで [クライアント側のエラーからフィードバックを受け取る](https://developers.google.com/assistant/interactivecanvas/web-apps#troubleshooting) ようになりました。

指定した単語のリストを聴きながら、デバイスのマイクを一定時間開いたままにしておくことができる [Continuous Match Mode](https://developers.google.com/assistant/games/tech-access) を使用するいくつかのゲームがリリースされています。

[Games Portal](http://goo.gle/assistant-games) は9月に公開され、アシスタントで優れたゲームを構築するための豊富なリソースを提供しています。新しいゲームデザインガイド、コードサンプル、ゲームデザイナーや開発者へのインタビュー、ケーススタディなどがあります。

Interactive Canvasに関するコードサンプル、コードラボ、動画の一覧が以下となります。

_新しいInteractive Canvasコードサンプル_

* [Snowpal Canvas sample](https://github.com/actions-on-google/actions-builder-snowpal-nodejs) は、従来の [Hangman](https://en.wikipedia.org/wiki/Hangman_(game)) ゲームのバリエーションです。ゲームは単語を表すいくつかの空白スペースを表示し、単語に含まれていると思われる文字を推測します。
* [Cookie Detective](https://github.com/actions-on-google-labs/cookie-detective-game-nodejs) は、子供たちが10回の推測でCookieの友達を見つけることで、キッチンの探偵になるためのゲームです。ゲームは、NestHubおよびNestHub Maxスマートディスプレイ用のインタラクティブキャンバスを使用して実装されます。
* [Gnome Garden](https://github.com/actions-on-google-labs/gnome-garden-game-nodejs) では、声で禅の庭を彫ることができます。このゲームは、NestHubおよびNestHub Maxスマートディスプレイ用に設計されており、インタラクティブキャンバスを使用して実装されています。

_新しいInteractive Canvasコードラボ_

* [Build an Interactive Canvas Action with Actions Builder](https://developers.google.com/codelabs/builder-canvas)

_新しいInteractive Canvasブログ_

* [Synchronize animations with the Text-To-Speec](https://medium.com/google-developers/aogprotips-synchronize-animations-with-the-text-to-speech-e9bb64860b44)

_新しいInteractive Canvas動画_

* [Building games on smart displays](https://www.youtube.com/watch?v=XcMitBgweM4)
* [Game design process](https://www.youtube.com/watch?v=P3-Btcdwzbs)
* [Game development and tools](https://www.youtube.com/watch?v=GPBImsQM-OA)
* [Continuous Match Mode party games](https://www.youtube.com/watch?v=FsQTL3CGV4s)
* [Game personas and visuals](https://www.youtube.com/watch?v=Crl4-F82mso)
* [Opportunities and next steps](https://www.youtube.com/watch?v=8CWrlSTAa60)

# Discovery

10月に、Actions linksは [Assistant Links](https://developers.google.com/assistant/engagement/assistant-links) に名前が変更されました。Assistant Links は、アクションの特定の目的に直接リンクするURLです。メールマーケティングマテリアルに最適です。Assistant Links は [Rich Assistant Links](https://developers.google.com/assistant/engagement/assistant-links#rich_assistant_links) で拡張されました。これにより、assist.jsを使用して、ウェブサイトからアクションの発見にアクセスできます。

![]({{ "/images/2020/12/conversational-actions-2020-recap-2.webp" | prepend: site.baseurl }})


# 優れた学習リソース

* ドキュメント: [goo.gle/assistant-conversational-docs](https://goo.gle/assistant-conversational-docs)
* サンプル: [Conversational Actions Samples](https://developers.google.com/assistant/conversational/samples)
* コードラボ: [Conversational Actions Codelabs](https://developers.google.com/assistant/conversational/codelabs)
* 動画: [Conversational Action video playlist](https://www.youtube.com/playlist?list=PLOU2XLYxmsIJ5qQKAYt45zZNMU9h1Grpm)

# その他のリソース

* [StackOverflow](http://goo.gle/assistant-stack-overflow) で技術的な質問をする
* [Twitter](https://goo.gle/assistant-twitter) でフォローして、最新のお知らせを入手する
* [Redditコミュニティ](https://goo.gle/assistant-reddit) で他のアシスタント開発者とつながる

App ActionsまたはSmart Home Actionsに興味がありますか？以下にリストされているすべての新機能とリソースを備えた独自のブログをチェックしてください。

* [App Actions 2020 Recap Blog](https://medium.com/google-developers/app-actions-2020-recap-c1cad7759158)
* [Smart Home 2020 Recap Blog](https://medium.com/google-developers/smart-home-actions-2020-update-resources-3c503b143c8d)

読んでくれてありがとう！あなたの考えや質問を共有するには、 [r/GoogleAssistantDev](https://www.reddit.com/r/GoogleAssistantDev/) のRedditに参加してください。

Twitterで [@ActionsOnGoogle](https://twitter.com/ActionsOnGoogle) をフォローしてチームの最新情報を確認し、AoGDevs を使用してツイートして作業内容を共有してください。あなたが作ったものを見るのが待ちきれません！

