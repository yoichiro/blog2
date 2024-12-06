---
layout: actions_on_google
title: Build with Actions SDK "Deploy Fulfillment"の日本語訳です
categories:
- actions on google
---
Actions SDKを使ったGoogleアシスタント向けのアプリにおいて、作成したフルフィルメントを呼び出すためには、フルフィルメントをデプロイしてインターネット上から利用可能な状態にして、そのエンドポイントURLをアクションパッケージ内に記載することが必要です。Firebase Cloud Functionsとしてフルフィルメントを開発していた場合には、Firebaseにデプロイ後に決定されるURLを登録することになります。

Actions on Googleのドキュメントにある[Build with Actions SDK - Deploy Fulfillment](https://developers.google.com/actions/sdk/deploy-fulfillment)にて、この手順が説明されています。以下は、その日本語訳です。

---

# フルフィルメントのデプロイ

本番環境用の[Cloud Functions for Firebase](https://developers.google.com/actions/tools/assistant-firebase-services)にデプロイするように環境を設定する方法を説明します。

> **注:** あなたのフルフィルメントは、5秒以内に応答する必要があります。そうでなければ、アシスタントがタイムアウトにより会話を終了します。

1. [Node.jsをダウンロードし、インストールします。](https://nodejs.org/)
1. Firebase CLIをセットアップし、初期化します。もし、以下のコマンドが `EACCES` エラーにより失敗する場合は、[npmパーミッションを変更](https://docs.npmjs.com/getting-started/fixing-npm-permissions)する必要があるでしょう。
 ```bash
 npm install -g firebase-tools
 ```
1. あなたのGoogleアカウントを使って、firebaseツールを認証します。
 ```bash
 firebase login
 ```
1. あなたのアクションプロジェクトをfirebaseツールに関連付けします。
 ```bash
 cd <cloud_function_dir>/functions
 firebase use PROJECT_ID
 ```
 > **注:** あなたのPROJECT_IDは、 **myprojectname-ab123** のような何かに見えるでしょう。あなたのアクションプロジェクトの **Settings** エリアで、IDを見つけることができます。また、あなたのGoogleアカウントに関連付けられているプロジェクトを一覧表示するために **firebase list** を実行することで、IDを閲覧できます。
1. フルフィルメントに依存しているものを入手し、フルフィルメントをデプロイします。
 ```bash
 cd <cloud_function_dir>/functions
 npm install
 firebase deploy --only functions
 ```
 デプロイ作業は、数分かかります。完了した際には、以下のような出力が表示されます。Dialogflow内で、 **Function URL** を入力する必要があるでしょう。

   ```bash
   ✔  Deploy complete!

   Project Console: https://console.firebase.google.com/project/myprojectname-ab123/overview
   Function URL (cloudFunctionName): https://us-central1-myprojectname-ab123.cloudfunctions.net/cloudFunctionName
   ```
1. アクションパッケージ内でフルフィルメントを宣言している `conversations` オブジェクト内に、オブジェクトを作成します。
 ```bash
 "conversations": {
   "myFulfillmentFunction": {
     "name": "myFulfillmentFunction",
     "url": "(cloudFunctionName): https://us-central1-myprojectname-ab123.cloudfunctions.net/cloudFunctionName"
   }
 }
 ```

> **注:** 開発マシン上で反復を早くするために、ローカルで開発やテストを行いたい場合があります。しかし、あなたのフルフィルメントは、インターネットから起動できなければなりません。この問題を回避するには、[ngrok](https://ngrok.io/)のようなツールを使用します。これは、インターネットから開発マシンにトンネルを作成することを可能にします。これにより、あなたのマシンが攻撃に対して脆弱になることに注意してください。この開発モデルを選択した場合は、注意が必要です。

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/sdk/deploy-fulfillment)
