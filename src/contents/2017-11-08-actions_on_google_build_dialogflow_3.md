---
layout: actions_on_google
title: Build with Dialogflow "Create a Project and Dialogflow Agent"の日本語訳です
categories:
- actions on google
---
[会話の設計]を終えた後は、Dialogflowを使ってアプリを作成していきます。Actions on GoogleのBuild with Dialogflowにある[Create a Project and Dialogflow Agent](https://developers.google.com/actions/dialogflow/project-agent)では、サンプルのスターターアプリをダウンロードし、Dialogflowにてプロジェクトを作成するまでの手順が書かれています。以下は、その日本語訳です。

---

# アプリの作成

Dialogflowを使用して開発するには、まず、Actions on GoogleデベロッパープロジェクトとDialogflowエージェントを作成する必要があります。プロジェクトを持つことで、デベロッパーコンソールにアクセスしてアプリを管理し配布することができます。Dialogflowエージェントには、アプリを定義するすべてのインテントが含まれています。

> **注:** 新しいプロジェクトを作成するとき、Actions on Googleは、Googleのアプリ開発者サービスのプラットフォームである[Firebase](https://firebase.google.com/)を有効にします。関連する利用規約の情報については、[こちら](https://developers.google.com/actions/console/terms-explanation)をご覧ください。

## デベロッパープロジェクトとDialogflowエージェントの作成

1. [Actions on Googleデベロッパーコンソール](http://console.actions.google.com/)に行きます。
1. **Add Project** をクリックし、プロジェクト名として `YourAppName` を入力し、 **Create Project** をクリックします。
1. **Overview** 画面にて、Dialogflowカード上の **BUILD** をクリックし、その後アクションの構築を始めるために **CREATE ACTIONS ON Dialogflow** をクリックします。
1. Dialogflowコンソールが表示され、エージェント内に情報が自動的に入力されています。エージェントを保存するために、 **Save** をクリックします。

## Dialogflowスターターアプリを使って始める

あなたの最初のアプリとして、Dialogflowスターターアプリから始めることをお勧めします。これには、使用する可能性の高いインテントを持つDialogflowエージェントと、フルフィルメント用のGoogle Cloud Functionが含まれています。

1. Dialogflowのスターターエージェントを[ダウンロード](https://developers.google.com/actions/downloads/apiai-starter-app.zip)してunzipします。このディレクトリをここでは `<sample_dir>` で参照します。
1. YourAppNameエージェント内に `<sample_dir>/agent.zip` をリストアします。
   1. 左のナビゲーションにて、エージェント名の右にある **ギアのアイコン** をクリックします。
   1. **Export and Import** タブをクリックします。
   1. **RESTORE FROM ZIP** をクリックして、 `<sample_dir>/agent.zip` ファイルを選択します。
   1. テキストボックスに `RESTORE` とタイプし、 **RESTORE** をクリックします。その後、 **DONE** をクリックします。 リストアが終わると、以下の画面が表示されます。あなたのアプリのプロジェクトIDをメモしておいてください。サンプルのフルフィルメントをデプロイする際に必要となります。<br> ![](https://developers.google.com/actions/images/get-started-project-id.png){:width="500px"}

## フルフィルメントを有効にする

フルフィルメントを有効にすると、Dialogflowの応答機能を使用する代わりに、ウェブ フルフィルメント コードを使ってユーザーに応答を返すことができます。これを行うには、エージェント全体のフルフィルメントを有効にしてから、フルフィルメントを使用したいインテントを選択する必要があります。フルフィルメントを有効にするには、以下を行います。

1. 左のナビゲーションにある **Fulfillment** をクリックします。
1. **Webhook** オプションを有効にします。
1. **URL** フィールドに、ダミーのURLを入力します。
   > **注:** あなたはまだ実際のフルフィルメントのURLを持っていませんが、この設定によって、インテントエディタ内でフルフィルメントのオプションを有効にします。そのオプションは、このチュートリアルの次のセクションで使用する必要があります。フルフィルメントのURLは、HTTPSアドレスでなければなりません。
1. **Save** をクリックします。

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/dialogflow/project-agent)
