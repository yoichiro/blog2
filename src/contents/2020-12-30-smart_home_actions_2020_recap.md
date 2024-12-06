---
layout: post
title: Smart Home Actions 2020 Updates & Resourcesの日本語訳です
categories:
- actions on google
---

今年は新型コロナウイルスの流行のために、開発者イベントなどが軒並みキャンセルされ、またオンラインイベントでの発信となり、情報を得るための難易度が昨年までよりも少し上がってしまった印象があります。しかし、物理的な移動をせずとも多くの一次情報に触れる機会が増えたことも今年の特徴であり、注意深くウォッチし続けてさえいれば、今年も着実に様々な技術に進化が見られたことがわかるはずです。

Googleアシスタントのスマートホーム対応について、今年もいくつかの新機能や改善がありました。 以下は、 [Smart Home Actions 2020 Updates & Resources](https://medium.com/google-developers/smart-home-actions-2020-update-resources-3c503b143c8d) を日本語訳したものです。Smart Home Actionsに関する2020年のトピックがよくまとめられていますので、ぜひ目を通していただければと思います。

---

Googleスマートホームプラットフォームを使用すると、ユーザーはGoogle HomeアプリとGoogleアシスタントを介して接続されたデバイスを制御でき、ユーザーにとって便利な家を作ることができます。スマートホームプラットフォームは過去1年間成長し続けていて、独自のスマートホームアクションの開発に役立つ追加のツールとリソースが作成されています。以下を確認してください。


![]({{ "/images/2020/12/smart-home-actions-2020-recap-1.webp" | prepend: site.baseurl }})

# 強化された分析とロギング

スマートホームアクション内のイベントの監視とログ記録を支援するために、6月30日に [Enhanced analytics and logging](https://developers.google.com/assistant/smarthome/develop/monitoring-logging) がリリースされました。これらの事前入力されたメトリクスチャートはアクションコンソールから直接アクセスされ、Google Cloud Monitoring機能を活用します。ユーザーが最も一般的にアクセスするデバイスの特性、ユーザークエリ応答の待ち時間、ユーザーエンゲージメントの成功率を追跡し、クラウドとローカルフルフィルメント間の相互作用を比較します。さらに、Google Cloud Loggingを通じて提供されるログイベントは、インテントタイプ、デバイスタイプ、フルフィルメント経路、リクエストのロケールに関する情報を含むイベントログにアクセスすることで、スマートホームアクションのすばやいデバッグに役立ちます。

これらの機能は、スマートホームアクション内の問題をすばやく特定するのに役立ち、エンドユーザーエクスペリエンスの継続的な開発と改善をガイドするのに役立つ品質情報を提供します。

_強化された分析とロギングビデオ_

* [How to use error logs](https://www.youtube.com/watch?v=RC1dAtizak0)
* [Enhanced Smart home Analytics](https://www.youtube.com/watch?v=NMKTeLJeurA&list=PLOU2XLYxmsIL32BnYVuSqwgS0ClB_csKd&index=11)

# Local Home SDK

[Local Home SDK](https://developers.google.com/assistant/smarthome/reference/local) は4月6日に開発者プレビューを終了し、バージョン1.0がすべての開発者に公開されました。開発者プレビューからのフィードバックに基づいて、アクションコンソールに複数のスキャン設定を入力する機能を追加しました。また、コンソールは、ローカルフルフィルメントアプリのJavascriptバンドルのアップロードをサポートするようになりました。

1.0のリリース以降、Google Nest WiFiルーターにNode.jsランタイムサポートを含むいくつかの追加機能も追加しました。これは、Local Home SDKが完全なNest WiFiシステムと互換性があることを意味します。 Node.jsオンデバイステストURLをコンソールに追加して、Chrome Dev Toolsを使用してNode.jsアプリをテストおよびデバッグすることもできます。その他の小さな修正も追加されました - 詳細については [こちら](https://github.com/actions-on-google/local-home-sdk) のソースコードをご覧ください。

フルフィルメントアプリ内にLocal Home SDKを実装することで、バックアップとしてクラウドフルフィルメントパスを維持しながら、ローカルエリアネットワーク経由でコマンドをルーティングすることにより、ユーザーリクエストのフルフィルメント中のアクションの信頼性を高めることができます。

_Local Home SDK サンプル_

* [Smart Home Local](https://github.com/actions-on-google/smart-home-local)

_Local Home SDK コードラボ_

* [Enable local fulfillment for smart home Actions](https://codelabs.developers.google.com/codelabs/smarthome-local/)

# Smart Home Entertainment Devices（SHED）

[Smart Home Entertainment Devices（SHED）のタイプとトレイト](https://developers.google.com/assistant/smarthome/changelog) - エンターテインメントに焦点を当てたデバイスのパートナーをサポートするために、4月と7月に複数の新しいエンターテインメントデバイスタイプとメディア関連のトレイトを発表しました。これらにより、アシスタント対応デバイスから、完全な音声制御と家庭内のエンターテインメントデバイスとのエンゲージメントが可能になります。サポートされているトレイトとタイプの完全なリストを確認するには、 [リファレンスドキュメント](https://developers.google.com/assistant/smarthome/traits) を確認してください。

# App Flip

ユーザーがアクションに参加するためのアカウントリンクプロセスを容易にするために、7月に [App Flip](https://developers.google.com/assistant/smarthome/develop/implement-app-flip) がリリースされました。この easy-to-develop 機能により、ユーザーは手動で再度ログインしなくても、パートナーアカウントをGoogleアシスタントに簡単にリンクできるため、モバイル経由でのアカウントリンクのプロセスが簡素化されます。

App Flipは、既存のOAuth 2.0サーバーの実装を強化するのに役立ち、最小限のコード更新でAndroidまたはiOSアプリ内でこの機能を簡単に利用できます。

# Scheduled Smart Home Actions

11月に [Scheduled Smart Home Actions](https://developers.google.com/assistant/smarthome/develop/scheduling) が開始されました。これにより、ユーザーはデバイスコマンドをスケジュールして後でトリガーすることができます。この機能はすべてのスマートホームアクションでサポートされており、追加の開発作業は必要ありません。現在、トレイト `LightEffects` 、`OnOff` 、および `StartStop` はスケジューリングをサポートしています。

# Light Effects Emulation

9月にリリースされたもう1つのユーザー向け機能は、Gentle Wake/Sleepライト効果でした。この機能拡張により、ユーザーは、事前に設定された期間にわたって、ライト、スイッチ、およびプラグをゆっくりと明るくしたり暗くしたりできます。これらのデバイス機能は、  `LightEffects` トレイトを通じてデフォルトでサポートされています。スマートホームアクションが今「明るさ」トレイトのみを使用している場合でも、 [エミュレートされたライトエフェクトのサポート](https://developers.google.com/assistant/smarthome/traits/lighteffects#emulation) により、エンドユーザーはGentle Wake/Sleepの恩恵を受けることができます。この機能では、アクションと互換性を持たせるためにチームによる開発作業も必要ありません。

# 一般的なプラットフォームリソース

最後に、スマートホームアクションの開発に役立ついくつかの一般的なリソースが公開されました。スマートホームアクションの開発をすべてのプログラミング言語に開放するために、 [Smart Home Schemas](https://github.com/actions-on-google/smart-home-schema) リポジトリをリリースしました。これには、 [Googleスマートホームプラットフォーム](https://developers.google.com/assistant/smarthome/) を定義するJSONスキーマが含まれています。これらのスキーマを反映するようにドキュメントを更新し、デバイスガイドの完全なサンプルインテント、およびトレイトの参照内のより詳細なペイロードパラメータで精度を向上させました。

_新しいプラットフォーム動画_

* [How to self-certify Smart Home Actions for the Google Assistant](https://www.youtube.com/watch?v=6gbbGQ26OGc)
* [Account linking verification for smart home](https://www.youtube.com/watch?v=dcW3dPKhjC4)
* [Inspecting Home Graph](https://www.youtube.com/watch?v=1Tv2YhtosAA)
* [Test suite for smart home](https://www.youtube.com/watch?v=LHk4syomWcc)
* [Offline handling for smart home](https://www.youtube.com/watch?v=bu71sE4sxjE)

_新しいプラットフォームブログ_

* [Account Linking Verification for Smart Home](https://medium.com/google-developers/aog-protips-account-linking-verification-for-smart-home-1fc5a9273971)
* [Inspecting Home Graph](https://medium.com/google-developers/aog-protips-inspecting-home-graph-503df93b7a91)
* [Test Suite for Smart Home](https://medium.com/google-developers/aog-protips-test-suite-for-smart-home-929a181e91e9)
* [Handing Offline Devices](https://medium.com/google-developers/aog-protips-handing-offline-devices-f0c7cd15ffe4)

# 優れた学習リソース

* ドキュメント: [goo.gle/assistant-smarthome-docs](https://goo.gle/assistant-smarthome-docs)
* サンプル: [Samples and libraries - Actions on Google Smart Home](https://developers.google.com/assistant/smarthome/samples)
* コードラボ: [All Smart Home Codelabs](https://developers.google.com/assistant/smarthome/codelabs)
* プレイリスト: [Smart Home video Playlist](https://www.youtube.com/playlist?list=PLOU2XLYxmsIL32BnYVuSqwgS0ClB_csKd)

# その他のリソース

* [Stack Overflow](https://stackoverflow.com/questions/tagged/google-smart-home) で技術的な質問をする
* [Twitter](https://goo.gle/assistant-twitter) でフォローして、最新のお知らせを入手する
* [Redditコミュニティ](https://goo.gle/assistant-reddit) で他のアシスタント開発者とつながる

会話型アクションまたはApp Actionsに興味がありますか？以下にリストされているすべての新機能とリソースを備えた独自のブログをチェックしてください。

* [Conversational Actions 2020 Updates & Resources](https://medium.com/google-developers/conversational-actions-2020-recap-dc486871535b)
* [App Actions 2020 Updates & Resources](https://medium.com/google-developers/app-actions-2020-recap-c1cad7759158)

読んでくれてありがとう！あなたの考えや質問を共有するには、 [r/GoogleAssistantDev](https://www.reddit.com/r/GoogleAssistantDev/) のRedditに参加してください。

Twitterで [@ActionsOnGoogle](https://twitter.com/ActionsOnGoogle) をフォローしてチームの最新情報を確認し、AoGDevs を使用してツイートして作業内容を共有してください。あなたが作ったものを見るのが待ちきれません！

