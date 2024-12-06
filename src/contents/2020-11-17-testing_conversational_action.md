---
layout: post
title: Googleアシスタント向けに3種類のテストが整備されました
categories:
- actions on google
---

Googleアシスタント向けに開発することが可能な会話側アクションは、既に多くのアクションがリリースされ、そして多くのユーザに利用されています。これらのアクションを開発する際に、テストは非常に重要な開発フェーズです。特にGUIとは違い、会話側アクションはUIが目に見えません。そのため、自然な会話を通じてユーザに価値をちゃんと届けられるかどうか、何度も何度も検証しなければなりません。また、一度リリースされた会話に対して部分的に手を加えた場合に、会話が壊れてしまったかどうかに気がつくことも難しく、結果としてユーザに不利益を与えてしまう可能性すらあります。そのような事態を避けるためにも、継続的な自動テストが重要になります。

現在では、手動テスト、自動テスト、そしてユーザテストの3段階のテストフェーズが、アクションコンソールにて整備されました。従来では、一度リリースしてしまったアクションについて開発中の会話をテストすることが難しかったのですが、このほどリリースされた自動テストライブラリでは、プレビュー環境にて開発中のアクションをテストすることができるようになりました。

以下は、 [Testing your Conversational Action](https://developers.google.com/assistant/conversational/testing#automated_testing) の日本語訳（2020年11月17日時点）となります。
# 会話側アクションのテスト


ActionsBuilderおよびActionsSDK開発環境は、インタラクティブな開発テストからエンドユーザーのベータテストまで、開発サイクルの各段階でActionsプロジェクトをテストするための複数の方法を提供します。

次の機能を使用して、初期開発からリリースまでのアクションの品質と信頼性を向上させます。

* シミュレーターを介した [手動テスト](https://developers.google.com/assistant/conversational/testing#manual_testing) では、開発中にアクションコンソールでアクションをテストできます。
* テストフレームワークとActionsAPIを使用した [自動テスト](https://developers.google.com/assistant/conversational/testing#automated_testing) は、自動化された反復可能なテストの生成に役立ちます。
* 限定リリースのアルファチャネルとベータチャネルを使用した [ユーザーテスト](https://developers.google.com/assistant/conversational/testing#user_testing) は、実際のテストとフィードバックを得るのに役立ちます。

# シミュレータを使った手動テスト

[アクションコンソール](https://console.actions.google.com/) のシミュレーターを使用すると、ハードウェアデバイスとその設定をシミュレートする使いやすいWebインターフェイスを介してアクションを手動でテストできます。コンソールで、Test をクリックして、現在選択しているプロジェクトでテストするためのシミュレーターをアクティブにします。


![]({{ "/images/2020/11/testing-conversational-action-1.webp" | prepend: site.baseurl }})

図1. 入力オプションを表示するアクションコンソールシミュレータインターフェイス。

シミュレーターを使用すると、ターゲットデバイス（スマートディスプレイ、電話、スピーカー）、入力方法（タッチ、音声、キーボード）、言語、および手動テスト環境の場所を指定できます。これらのオプションを使用すると、さまざまなデバイスや地域でアクションの機能をすばやくインタラクティブにテストできます。

アクションコンソール [シミュレータ](https://developers.google.com/assistant/console/simulator) の詳細をご覧ください。

# Actions APIを使った自動テスト

アクションの自動テストを作成すると、新しいコードが変更された場合でも、毎回手動でコードをテストしなくても、アクションの会話ロジックが意図したとおりに機能していることを確認できます。

[Node.jsテストライブラリ](https://github.com/actions-on-google/assistant-conversation-testing-nodejs) は、Actions APIを使用してプロジェクトコードを操作します。テストライブラリを使用すると、アクションにクエリを送信し、その出力に対してアサーションを作成して、アクションの会話状態に固有の情報を検証するテストスイートを定義できます。

Actions APIは、アクションのドラフトを作成し、クエリを使用してプログラムでアクションを操作し、特定のクエリに一致するインテントデータを見つけることができる [RESTエンドポイント](https://developers.google.com/assistant/actions/api/reference/rest) を提供します。これらのエンドポイントを直接使用する場合は、 [Node.js ActionsAPIクライアントライブラリ](https://github.com/actions-on-google/assistant-actions-nodejs) をダウンロードできます。

テストライブラリを使用すると、sendQuery 関数を使用してクエリを送信することにより、1回の会話をテストできます。このメソッドは、テキストクエリを入力として受け取り、ユーザーに提示されたプロンプトの内容と、要求の処理方法を説明するその他の診断情報を含む構造化された出力を生成します。出力テストオブジェクトに組み込まれているアサーションメソッドを使用して、一致したインテントや現在のシーンなど、アクションの動作を確認できます。

次の例は、適切なウェルカム応答でアクションがトリガーされることを表明するテストを示しています。


```
it('starts Action and enters the Welcome scene', async function() {
    testManager.setTestSurface('SMART_DISPLAY');
    await testManager.sendQuery('Talk to my test app');
    testManager.assertSpeech('Hello World!');
    testManager.assertText('Hello World!');
    testManager.assertIntent('actions.intent.MAIN');
    testManager.assertScene('Welcome');
    await testManager.sendStop();
    testManager.assertConversationEnded();
});
```

[会話コンポーネントのコードサンプル](https://github.com/actions-on-google/actions-builder-conversation-components-nodejs) については、Node.jsで記述されたテストスイートの完全な動作例を参照してください。

このテストフレームワークの使用を開始する方法の詳細については、[Node.jsテストライブラリ](https://github.com/actions-on-google/assistant-conversation-testing-nodejs) の手順を参照してください。

# アルファおよびベータリリースを使ったユーザテスト

アクションコンソールには、選択した一連のユーザーによるテストのために、アクションを限定リリースチャネルにリリースするためのオプションが用意されています。コンソール内で、アクションをアルファチャネルとベータチャネルにリリースできるため、限られた数のユーザーがアクションをテストし、開発プロセスの早い段階でフィードバックを提供できます。この機能により、アクションを本番環境にリリースする前に、技術的またはユーザーエクスペリエンスの問題を発見して修正する機会が得られます。


![]({{ "/images/2020/11/testing-conversational-action-2.webp" | prepend: site.baseurl }})

図2. アクションコンソールのリリースインターフェイスを使用すると、アクションのアルファリリースチャネルとベータリリースチャネルを構成できます。

アルファリリースチャネルを使用すると、開発者が指定した20人のユーザーにアクションをリリースできます。ユーザーはGoogleアシスタントデバイスを介してアクションをテストできます。アルファリリースは、Googleのアクションレビュープロセスを必要としないため、内部テストに最適です。アルファテスターは、アクションがこのチャネルにリリースされると、すぐにアクションにアクセスできます。

ベータリリースチャネルを使用すると、開発者が指定した200人のユーザーにアクションをリリースして、Googleアシスタントデバイスを介してアクションをテストできます。ベータリリースは、プロジェクトが完全なGoogleレビューを経たときに、組織外のユーザーにアクセスを許可するのに理想的です。ベータテストの結果に満足したら、ベータ版を本番環境にリリースできます。これは、すでにGoogleのポリシーレビューに合格しているためです。

リリースチャネルの詳細については、アクションコンソールの [リリース](https://developers.google.com/assistant/console/releases) ドキュメントを参照してください。

