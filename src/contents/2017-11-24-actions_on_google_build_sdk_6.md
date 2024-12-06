---
layout: actions_on_google
title: Build with Actions SDK "Submit Your App"の日本語訳です
categories:
- actions on google
---
Actions SDKを使ったGoogleアシスタント向けアプリの開発が完了した後は、実機でのテストを経て、承認プロセスを通過するためにレビュー依頼を提出することになります。それらには、gactionsコマンドを使います。

Actions on Googleのドキュメントにある[Build with Actions SDK - Submit Your App](https://developers.google.com/actions/sdk/submit-app)にて、それらの手順が説明されています。以下はその日本語訳です。

---

# アプリを提出する

Dialogflowエージェントとフルフィルメントがデプロイされたら、実際のハードウェアデバイスやアクションシミュレータでアプリをテストすることができます。

徹底的なテストに加えて、提出する前に、アプリが公開前のチェックリストを確実に通過するようにしてください。これにより、迅速な承認が保証され、承認プロセスで私たちが目にする多くの問題をキャッチできます。

* [設計チェックリスト](https://developers.google.com/actions/design/checklist)
* [UIチェックリスト](https://developers.google.com/actions/assistant/responses#ui_checklist)

## 実際のハードウェアでのテスト

音声認識可能なスピーカーやAndroid端末などのサポートされている端末で、Actions on Googleデベロッパープロジェクトでアクションを作成した時と同じアカウントで、端末にログインします。

## アクションシミュレータでアプリをテストする

アプリをテストするために、以下を行います。

1. アクションパッケージをデベロッパープロジェクトにアップロードします。
 ```bash
 gactions update --action_package PACKAGE_NAME --project PROJECT_ID
 ```
1. gactionsを使ってプロジェクトをテストします。
 ```bash
 gactions test --action_package PACKAGE_NAME --project PROJECT_ID
 ```

## 承認のためにアプリを提出する

承認のためのアプリ提出の準備ができたら、アクションパッケージのコピーを使ってデベロッパープロジェクトをアップロードし、そしてレビューのためにアプリを提出するために、アクションコンソールを使います。

1. デベロッパープロジェクトにアクションパッケージをアップロードします。
 ```bash
 gactions update --action_package PACKAGE_NAME --project PROJECT_ID
 ```
1. [Actions on Google Developer Console](https://console.actions.google.com/)に行き、プロジェクトを選択します。承認に向けてアプリを提出するために必要とされる情報を入力します。より詳しい情報は、[Registering and Publishing](https://developers.google.com/actions/console/publishing)をご覧ください。

> **注:** アクションパッケージを更新した時は、毎回別の承認サイクルを通る必要があります。

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/sdk/submit-app)
