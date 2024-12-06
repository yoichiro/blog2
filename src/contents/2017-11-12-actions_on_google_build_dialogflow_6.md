---
layout: actions_on_google
title: Build with Dialogflow "Deploy Fulfillment"の日本語訳です
categories:
- actions on google
---
[フルフィルメントの構築](https://www.eisbahn.jp/yoichiro/2017/11/actions_on_google_build_dialogflow_5.html)では、FirebaseのCloud Functionとしてフルフィルメントを作りました。これをFirebaseにデプロイするための方法が、Actions on Googleの[Deploy Fulfillment](https://developers.google.com/actions/dialogflow/deploy-fulfillment)にて説明されています。以下は、その日本語訳です。

---

# フルフィルメントをデプロイする

本番環境用の[Cloud Functions for Firebase](https://developers.google.com/actions/tools/assistant-firebase-services)にデプロイするように環境を設定する方法を説明します。ただし、フルフィルメントをホストするHTTPSリクエストおよびレスポンスをサポートするウェブホスティング・プラットフォームを選択することができます。

> **注:** あなたのフルフィルメントは、5秒以内に応答する必要があります。応答しなかった場合は、アシスタントがタイムアウトのために会話を終了します。

1. [Node.jsをダウンロードし、インストールします](https://nodejs.org/)。
1. Firebase CLIを設定して初期化します。次のコマンドが`EACCES`エラーで失敗する場合は、[npmパーミッションを変更する](https://docs.npmjs.com/getting-started/fixing-npm-permissions)必要があります。
   ```bash
   npm install -g firebase-tools
   ```
1. Googleアカウントでfirebaseツールを認証します。
   ```bash
   firebase login
   ```
1. firebaseツールをActionsプロジェクトに関連付けます。
   ```bash
   cd <cloud_function_dir>/functions
   firebase use PROJECT_ID
   ```
   > **注:** PROJECT_IDは、 **myprojectname-ab123** のようになります。Actionsプロジェクトの設定領域でIDを見つけたり、firebaseリストを実行してGoogleアカウントに関連付けられているプロジェクトをリストしたり、IDを参照したりすることができます。
1. フルフィルメントに依存するものを取得し、フルフィルメントをデプロイします。
   ```bash
   cd <cloud_function_dir>/functions
   npm install
   firebase deploy --only functions
   ```
   展開には数分かかります。完了すると、次のような出力が表示されます。Dialogflowに入力するには、 **Function URL** が必要です。
   ```bash
   ✔  Deploy complete!

   Project Console: https://console.firebase.google.com/project/myprojectname-ab123/overview
   Function URL (cloudFunctionName): https://us-central1-myprojectname-ab123.cloudfunctions.net/cloudFunctionName
   ```
1. Dialogflowの左側のナビゲーションで、 **Funfillment** をクリックし、 **ENABLED** スライダを右に移動し、 **URL** フィールドに **Function URL** を入力します。例えば、Dialogflowのフルフィルメント設定は、このスクリーンショットのようになります。
   ![](https://developers.google.com/actions/images/get-started-fulfillment.png){:width="600px"}
   > **注:** 開発マシン上で反復を早くするために、ローカルで開発やテストを行いたい場合があります。しかし、あなたのフルフィルメントは、インターネットから起動できなければなりません。この問題を回避するには、[ngrok](https://ngrok.io/)のようなツールを使用します。これは、インターネットから開発マシンにトンネルを作成することを可能にします。これにより、あなたのマシンが攻撃に対して脆弱になることに注意してください。この開発モデルを選択した場合は、注意が必要です。

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/dialogflow/deploy-fulfillment)
