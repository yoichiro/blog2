---
layout: post
title: Actions on Googleの用語集です
categories:
- actions on google
---

Googleアシスタント向けに、Actions on Googleというプラットフォームを使うことで、開発者が独自に機能を拡張することができます。この「機能
を拡張する」ということについて、「アプリを開発する」と表現するのか、「アクションを開発する」と表現するのか、今まで実ははっきりとしていません
でした。おそらく人によって「アプリ」と言ったり「アクション」と言ったり違いがあったでしょうし、文脈によって使い分けている人もいたかと思います。
長らく「どっちなんだ問題」としてふわふわしていましたが、Actions on Googleの公式ドキュメントに「Glossary」のページが追加されまして、
この問題が解決しました。実際には「アクション」が正解となります。

[Actions on Google Glossary](https://developers.google.com/actions/glossary)

よく使う言葉が並んでいますので、Actions on Googleにてアクションを開発する方々は、ぜひ上記のページを見て用語の意味を押さえておくと
よいでしょう。以下に、2018/07/30 時点の内容の日本語訳を掲載しておきますので、ご参考になればと思います。

## A

### Action (アクション)

特定のインテントをサポートするアシスタント向けに構築されたインタラクションであり、インテントを処理する関連したフルフィルメントを持ちます。

### [Actions on Google](https://developers.google.com/actions/extending-the-assistant)

Googleアシスタントを通じて任意のユーザが呼び出し可能なアクションをあなたが定義し実行することができる開発者プラットフォームです。

### [Actions console (アクションコンソール)](https://console.actions.google.com/)

アクションを作成、メンテナンス、テスト、そして公開するためのウェブツールです。

### [Actions simulator (アクションシミュレータ)](https://developers.google.com/actions/tools/simulator)

リアルタイムにアクションをテストしデバッグするためのウェブツールです。シミュレータを使って、物理的なデバイスなしで、アシスタントがサポートする全てのサーフェースのためにあなたのアクションをテストすることができます。

### Action package (アクションパッケージ)

あなたのアクションを定義するJSONファイルです。このファイルは、Actions directory一覧、account linking情報、アクションが処理可能なインテントの一覧、そして実際のフルフィルメントのエンドポイントを含んでいます。

### Actions project (アクションプロジェクト)

アクションのコレクションを管理、テスト、そして公開するために [Actions console](https://developers.google.com/actions/glossary#actions-console) にてあなたが作成するプロジェクトです。Googleは、Googleのクラウドインフラストラクチャにて、あなたの Actions project を運用します。

### [Actions directory](https://assistant.google.com/explore/) (アクションディレクトリ)

アシスタント向けのアクションを発見し使い方を学ぶためのユーザ向けのGoogleのリポジトリです。

* __シノニム__: Assistant explorer, Actions explorer, Assistant directory, Explore page, web directory

### Action phrase (アクションフレーズ)

ユーザがアクション特定の機能にディープリンク可能なフレーズです。ユーザは、アクションがサポートする特定のタスクを呼び出すために、アクションプロジェクトの名前に続けてアクションフレーズを話すことができます（例えば、"Hey Google, talk to Geek Num to learn about the number 42"）。ユーザはまた、あなたのアクションを発見するために、アクションフレーズ自身を話すかも知れません（例えば、"Hey Google, learn about the number 42"）。

* __シノニム__: Action invocation phrase, implicit invocation intent

### agent (エージェント)

特定の会話体験を処理するためにDialogflowにて作成可能なプロジェクトであり、各エージェントは自身のインテントマッピングや
エンティティ定義を持つこと、複数の言語を処理することができ、そして、バージョン（例、"dev" や "prod"）を持ちます。


## B

### [built-in intent (ビルトイン インテント)](https://developers.google.com/actions/discovery/built-in-intents)

あなたのアクションが、ゲームで遊ぶことやチケットの注文など、ユーザの要求の特定のカテゴリを満たすことに適しているかどうかをGoogleに伝えるためのユニークな識別子です。Googleは、ビルトインインテントおよびそれらのインテントに関連付けられたアクションを呼び出すことができるフレーズを定義します。


## C

### [conversation HTTP/JSON webhook API](https://developers.google.com/actions/build/json/)

Actions on Googleとフルフィルメントの間でデータを交換するためのHTTPメッセージプロトコルで利用されるAPIです。

* __シノニム__: conversation API, HTTP/JSON API

### conversation request (会話リクエスト)

あなたのフルフィルメントとの会話が開始された際にユーザが行う会話の要求です。会話のWebhook書式において、これらのリクエストは通常 `actions.intent.TEXT` インテント（ユーザからの生のテキスト応答を表現する）に対応します。

### conversation response (会話レスポンス)

あなたのフルフィルメントによってアシスタントに送られる応答であり、データペイロードをスピーチやグラフィカルユーザインタフェースといった出力に変換し、そしてユーザにこの出力が伝わります。

### custom Action (カスタム アクション)

アクションの呼び出し文法をあなたが定義したアクションです。

### custom entity (カスタム エンティティ)

ドメイン特有の単語やフレーズ向けの開発者により定義された [エンティティ](https://developers.google.com/actions/glossary#entity) です。


## D

### [Dialogflow](https://dialogflow.com/)

Googleアシスタント向けに会話体験を構築するための、Googleの[自然言語理解](https://developers.google.com/actions/glossary#natural-language-understanding)のための開発者ツールです。ユーザが最も有益と思える応答となるために、機械学習を使って、ユーザが言ったことの意図やコンテキストを理解しています。

### [Dialogflow console](https://console.dialogflow.com/)

[Dialogflowエージェント](https://developers.google.com/actions/glossary#agent)を作成しメンテナンスするためのウェブツールです。


## E

### entity (エンティティ)

[会話リクエスト](https://developers.google.com/actions/glossary#conversation-request)や[会話レスポンス](https://developers.google.com/actions/glossary#conversation-response)において使われることが可能な現実世界のモノの表現です。ユーザの発言からどのようにパラメータを解析するかをDialogflowに指示するために、エンティティはあなたのトレーニングフレーズの中で使うことが可能です。

* __関連する用語__: [system entity](https://developers.google.com/actions/glossary#system-entity), [custom entity](https://developers.google.com/actions/glossary#custom-entity)

### explicit invocation (明示的な呼び出し)

ユーザがアクションプロジェクトの名前を使用する呼び出しです（例えば、"Hey Google, talk to Movie Time"）。

* __関連する用語__: [Action phrase](https://developers.google.com/actions/glossary#action-phrase)


## F

### fulfillment (フルフィルメント)

インテントを処理し、対応するアクションを実行するためのサービス、アプリ、フィード、会話、またはその他のロジックです。


## G

### [Google Assistant (Google アシスタント)](https://assistant.google.com/)

何かを成し遂げるためにユーザとGoogleの間で会話が可能なGoogleのパーソナル仮想アシスタントです。


## I

### [implicit invocation (暗黙的呼び出し)](https://developers.google.com/actions/discovery/implicit)

An invocation where users utters an Action phrase without an Actions project name (for example, "Hey Google, I want to buy movie tickets").

ユーザがアクションプロジェクトの名前なしに[アクションフレーズ](https://developers.google.com/actions/glossary#action-phrase)を発声する呼び出しです（例えば、"Hey Google, I want to buy movie tickets"）。

### inline-editor (インラインエディタ)

Editor in the Dialogflow console for implementing your fulfillment; Google deploys this code using Cloud Functions for Firebase.
あなたのフルフィルメントを実装するためのDialogflowコンソール内のエディタ。GoogleはこのコードをCloud Functions for Firebaseを使ってデプロイします。

### intent (インテント)

コーヒーの注文や音楽の検索など、ユーザが行いたいことのゴールやタスクです。Actions on Googleにおいて、これはユニークな識別子として表現され、インテントを呼び出し可能な対応するユーザの発話です。

### [invocation (呼び出し)](https://developers.google.com/actions/discovery/)

ユーザによってアクションとインタラクションが開始される際の行動です。

* __シノニム__: triggering
* __関連する用語__: [explicit invocation](https://developers.google.com/actions/glossary#explicit-invocation), [implicit invocation](https://developers.google.com/actions/glossary#implicit-invocation)


## N

### natural language understanding (自然言語理解)

ユーザの入力を理解し解析するためのソフトウェアの能力です。開発者は、DialogflowのNLUソリューションの利用もしくは開発者自身のNLUソリューションをアクションの作成時に選択することができます。


## S

### [surface (サーフェス)](https://developers.google.com/actions/assistant/surface-capabilities)

Googleアシスタントへのアクセスをユーザに提供する任意のデバイスであり、Wear OSデバイス、アシスタントを利用可能なヘッドフォン、Chromebook、Android TV、Android携帯端末やタブレット、スマートディスプレイやスマートスピーカー、そしてiPhoneを含みます。

### system entity

Dialogflowが知っている組み込みの[エンティティ](https://developers.google.com/actions/glossary#entity)です（例えば、"color" や "date"）。


## W

### webhook

会話のためのフルフィルメントとしてあなたが実装することができるHTTPコールバックです。
