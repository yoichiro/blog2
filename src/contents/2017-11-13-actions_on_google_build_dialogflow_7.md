---
layout: actions_on_google
title: Build with Dialogflow "Deploy Your App"の日本語訳です
categories:
- actions on google
---
[フルフィルメントのデプロイ](https://www.eisbahn.jp/yoichiro/2017/11/actions_on_google_build_dialogflow_6.html)まで済んでいれば、あとはアプリをデプロイして承認プロセスに進むことになります。Actions on Googleの[Deploy Your App](https://developers.google.com/actions/dialogflow/submit-app)では、アプリをデプロイして実機（Google HomeやAndroid端末など）でテストを行い、レビューに提出する手順が説明されています。以下は、その日本語訳です。

---

# アプリをデプロイする

Dialogflowエージェントとフルフィルメントがデプロイされたら、実際のハードウェアデバイスやアクションシミュレータでアプリをテストできます。

徹底的なテストに加えて、提出する前に、アプリが公開前チェックリストを確実に通過するようにしてください。これにより、迅速な承認が保証され、承認プロセスで私たちが目にする多くの問題をキャッチできます。

* [設計チェックリスト](https://developers.google.com/actions/design/checklist)
* [UIチェックリスト](https://developers.google.com/actions/assistant/responses#ui_checklist)

## 実際のハードウェアでのテスト

音声認識可能なスピーカーやAndroid端末などのサポートされている端末で、Actions on Googleデベロッパープロジェクトでアクションを作成した時と同じアカウントで、端末にログインします。

## アクションシミュレータでアプリをテストする

1. Dialogflowの左側のナビゲーションで **Integrations** をクリックし、 **Google Assistant** カードのスライダバーを移動します。
1. **Google Assistant** カードをクリックし、ポップアップウィンドウで **TEST** をクリックします。Dialogflowは、エージェントをサーバーにアップロードして、アクションシミュレータでテストすることができます。
 > **注:** **TEST** ボタンが表示されない場合は、 **AUTHORIZE** ボタンを最初にクリックして、DialogflowにGoogleアカウントとアクションプロジェクトへのアクセス権を付与する必要があります。
1. 統合画面の **Test your Assistant app** セクションで、 **Actions Simulator** リンクをクリックして、アクションシミュレータ用の新しいブラウザを開きます。
 >**注:** アクションシミュレータのスプラッシュ画面が表示されている場合は、 **Start** をクリックしてGoogleアカウントで認証します。
1. アクションシミュレータで、シミュレータの入力領域に `talk to my test app` と入力して、アプリをテストします。

## 承認のためにアプリを提出する

デベロッパープロジェクトをエージェントのコピーで更新して、レビューのために提出できるようにする場合は、Dialogflowの更新機能を使用してください。これにより、レビュー向けにデベロッパープロジェクトにアクションパッケージがアップロードされます。

1. Dialogflowの左側のナビゲーションで、 **Integrations** をクリックし、 **Actions on Google** カードをクリックします。
1. **UPDATE** ボタンをクリックします。
Go to the Actions on Google Developer Console and select your project. Fill out the information requested to submit your app for approval. See Registering and Publishing for more information.
1. [Actions on Googleデベロッパーコンソール](https://console.actions.google.com/)に移動し、プロジェクトを選択します。承認のために、あなたのアプリを提出するために要求される情報を記入してください。詳細については、[Registering and Publishing](https://developers.google.com/actions/console/publishing)を参照してください。

> **注:** アクションパッケージを更新するたびに、別の承認サイクルを経なければなりません。

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/dialogflow/submit-app)
