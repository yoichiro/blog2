---
layout: actions_on_google
title: Account Linking "Implementing Account Linking"の日本語訳です
categories:
- actions on google
---
Googleアカウント向けアプリでは、Googleアシスタントを利用している際のGoogleアカウントと、アシスタント向けアプリの裏にあるサービスでのアカウントとを紐付けるための仕組みが提供されています。これは、アカウントリンクと呼ばれ、その仕組みはOAuth 2.0をベースに組み立てられています。

Actions on Googleのドキュメントにある
[Account Linking - Implementing Account Linking](https://developers.google.com/actions/identity/account-linking)にて、
アカウントリンクの実装方法が説明されています。以下はその日本語訳です。

---

# アカウントリンクの実装

Googleを使ったアカウントリンクの実装は、3つのシンプルな手順で行われます。

## Step 1. あなたのサーバの設定

アカウントは業界標準のOAuth 2.0フローとリンクしています。Actions on Googleは、[implicit](https://developers.google.com/actions/identity/oauth2-implicit-flow)および[authorization code](https://developers.google.com/actions/identity/oauth2-code-flow)フローをサポートしています。

さらに、ユーザーが既存のアカウントにサインインしたり、新しいアカウントを作成したりするための優れた体験を提供する合理化されたアイデンティティフローを実装することを強くお勧めします。この経験は、ユーザーから2回のクリックのみで良く、ユーザー名、パスワード、または長い構成手順は必要ありません。既存のOAuth 2.0サーバーで合理化されたアイデンティティ体験を有効にするには、[Streamlined Identity Flows](https://developers.google.com/actions/identity/oauth2-assertion-flow)を参照してください。

## Step 2. アカウントリンクの有効化

Actions on Googleコンソールを使って、アカウントリンクパラメータの設定が必要です。

1. [Actions console](http://console.actions.google.com/)に移動します。
1. プロジェクトを選択、あるいは新規にプロジェクトを作成します。
1. プロジェクトの **Overview** に行き、 **Account Linking** ステップをクリックします。
  ![](https://developers.google.com/actions/images/account-linking-5.png){:width="800px"}
1. 順々に手順に従っていって、アカウントリンクをセットアップします。
  ![](https://developers.google.com/actions/images/account-linking-2.png){:width="800px"}
1. あなたのアプリがシームレスなアカウントリンクをサポートする場合:
   1. **Add quick account linking** を選択して、フィールドに入力します。
      ![](https://developers.google.com/actions/images/account-linking-3.png){:width="500px"}
   1. **Testing Instructions** で、アプリの審査プロセス中にシームレスなアカウントのリンクをテストするためのサンプルの **username** と **password** を入力して、保存を押してください。

アカウントリンクが設定されると、アプリが呼び出されたときやアプリ内の会話の間に、アプリがアカウントリンクをトリガーできるようにすることができます。以下のセクションでは、これを行う方法について説明します。

### 呼び出し時のアカウントリンク

呼び出し時のアカウントリンクを有効にするために、完全な手順は以下です。

1. 以下のリダイレクトURLをホワイトリストに追加します。<br> `https://oauth-redirect.googleusercontent.com/r/<google developer project ID>`
1. Dialogflowプロジェクトにて、左メニューから **Integrations** をクリックします。
1. **Google Assistant** カードをクリックします（まだ有効にしていない場合は、カードを有効にします）。
1. Welcome intentやサインインを必要とする他のインテントに対して、 **Sign in Required** を選択します。
1. 拡張されたOAuth 2.0フォームにて、OAuth 2.0クライアント設定のフィールドに必要事項を入力します。scopeを入力する際には、空白区切りです。
1. 認可フローをプレビューし、テストします。

**デバイスでのテスト**

1. Googleアプリを開いて、Discoverタブに行きます。
1. アカウントリンクフローを起動するアカウントリンクカードをクリックします。Googleアプリとそのデバイスは、あなたがアプリをテストするために使うアカウントと同じアカウントでサインインされている必要があります。

**Actions simulatorでのテスト**

1. あなたのアプリを呼び出します。あなたのGoogleアカウントでアプリを初めて起動するので、アシスタントはアカウントをリンクする必要があることを通知します。
1. **Log** エリアで、 `debugInfo.sharedDebugInfo.debugInfo` フィールドのURLをコピーして、Webブラウザに貼り付けます。DialogflowまたはActions Consoleで指定されたあなた自身のサーバーの認証URLにリダイレクトされます。
1. 完了するために、OAuthのログインフローに従います。 `result_code=SUCCESS` のURLパラメータを使用してgoogle.comにリダイレクトされた場合、アカウントのリンクが機能します。
1. Actions simulatorで、あなたのアプリをもう一度起動してください。あなたのアプリはユーザーに応答し、フルフィルメントへのすべてのHTTPリクエストで `access_token` を受け取ります。Node.jsクライアントライブラリ関数の `DialogflowApp.getUser().access_token` を使用してトークンを取得します。

### 会話中

ユーザーは、会話中に `actions.intent.SIGN_IN` のインテントを要求することによって、サービスに関連付けられたアカウントにサインインすることができます。

このドキュメントの[Request the signin helper](https://developers.google.com/actions/identity/account-linking#request_the_signin_helper)セクションでは、このインテントをリクエストする方法を示しています。このドキュメントの[Getting the results of the helper](https://developers.google.com/actions/identity/account-linking#getting_the_results_of_the_helper)セクションでは、リクエスト後にユーザーがアカウントを正常にリンクしているかどうかを確認する方法を示しています。

> 注: Signin helperは現在[en-*locales](https://developers.google.com/actions/localization/languages-locales)のみで機能します。

### Request the signin helper

ユーザーオブジェクトに `accessToken` が見つからない場合は、このインテントをリクエストします。これは、ユーザーがまだアカウントをリンクしていないことを意味します。

**NODE.JS**

```js
function signIn(app) {
  app.askForSignIn();
}
```

**JSON**

```js
{
  "conversationToken": "{\"state\":null,\"data\":{}}",
  "expectUserResponse": true,
  "expectedInputs": [
    {
      "inputPrompt": {
        "initialPrompts": [
          {
            "textToSpeech": "PLACEHOLDER_FOR_SIGN_IN"
          }
        ],
        "noInputPrompts": []
      },
      "possibleIntents": [
        {
          "intent": "actions.intent.SIGN_IN",
          "inputValueData": {}
        }
      ]
    }
  ]
}
```

### Getting the results of the helper

ユーザーがヘルパーに応答すると、フルフィルメントへのリクエストが送信され、ユーザーがアカウントを正常にリンクしているかどうかを確認できます。

`actions.intent.SIGN_IN` インテントを要求した後、 `OK` , `CANCELED` または `ERROR` の値を持つ `SignInStatus` を含む引数を受け取ります。ステータスが `OK` の場合、ユーザーオブジェクト内で `accessToken` を見つけることができるはずです。

**NODE.JS**

```js
function signInHandler(app) {
  if (app.getSignInStatus() === app.SignInStatus.OK) {
    let accessToken = app.getUser().accessToken;
    // access account data with the token
  } else {
    app.tell('You need to sign-in before using the app.');
  }
}
```

**JSON**

```js
{
  "isInSandbox'": false,
  "surface": {
    "capabilities": [
      {
        "name": "actions.capability.AUDIO_OUTPUT"
      },
      {
        "name": "actions.capability.SCREEN_OUTPUT"
      }
    ]
  },
  "inputs": [
    {
      "rawInputs": [
        {
          "query": "i think so",
          "inputType": "VOICE"
        }
      ],
      "arguments": [
        {
          "name": "SIGN_IN",
          'extension': {
            "@type": "type.googleapis.com/google.actions.v2.SignInValue",
            "status": "OK"
          }
        }
      ],
      "intent': "actions.intent.SIGN_IN"
    }
  ],
  "user": {
    "userId": "user123"
  },
  "device": {
    "locale": "en-US"
  },
  "conversation": {
    "conversationId": "1494606917128",
    "type": "ACTIVE",
    "conversationToken": "[\"_actions_on_google_\"]"
  }
};
```

## Step 3. あなたの実装の検証

シームレスなアイデンティティを持たない標準のアカウントリンクのみを使用している場合は、[OAuth 2.0 Playground](https://developers.google.com/oauthplayground/)ツールを使用して、実装を検証できます。ツールにて、次の手順を実行します。

1. **Gear icon** をクリックして、OAuth 2.0 設定ウィンドウを開きます。
1. OAuth Endpoint フィールドにて、Custom を選択します。
1. あなたの OAuth 2.0 エンドポイント、Google Client ID、Client Secretを、対応するフィールドに指定します。
1. Step 1 セクションにて、何のGoogle scopeを選択しなかった場合は、代わりに、あなたのサーバで有効な scope を入力して、 **Authorize APIs** をクリックします。
1. Step 2, Step 3 セクションにて、OAuth 2.0フローの手順を実施し、各手順が期待通りに動作するかどうか確認します。

### シームレスなアイデンティティの検証

シームレスなアイデンティティを使用している場合は、上記の手順を検証し、[gala-demo](http://gala-demo.appspot.com/)ツールを使用して、アサーションフローを検証します。デモツールで、次の手順を実行します。

1. **login** ボタンをクリックすると、プロファイル情報にアクセスする権限がツールに与えられます（ツールを初めて使用する場合にのみ必要です）。
1. **service ID** フィールドに、Google Cloud Project IDと、 `_dev` 接尾辞を入力します。たとえば、プロジェクトIDが `my_gcp_project` の場合は、 `my_gcp_project_dev` と入力します。<br>
  ![](https://developers.google.com/actions/images/account-linking-1.png){:width="400px"}
1. テストに必要なすべてのスコープを次のフィールドに追加し、 **Start Demo** をクリックします。
1. アカウント選択画面が表示されます。ログインしているアカウントを使用する（シームレスなアイデンティティフローをトリガーする）、または別のアカウントをクリックして使用することができます。その場合、認証画面が表示され、標準のOAuthフローがトリガーされます。
1. フローの最後に、アクセストークンがUIに表示され、アプリをテストする際に、アプリ、電話、またはActions Simulatorのその後の呼び出しで使用されます。

> **注:** また、Actions Simulatorを使用して、シームレスなアイデンティティ統合をテストすることもできます。サインインが必要なインテントがトリガーされると、Actions Simulatorレスポンス内のデバッグ文字列に gala-demo ツールのURLが表示されます。アカウントのリンクを解除するには、ステータスを非アクティブに切り替えてアクティブに戻すことで、Actions Simulatorでアプリをリロードします。
> ![](https://developers.google.com/actions/images/account-linking-4.png){:width="150px"}

## トラブルシューティング

あなたのアカウントリンクが何らかの理由で失敗した場合、何がうまくいかないかを把握するために、[Actions Console](https://console.actions.google.com/)のログが役立ちます。トラブルシューティングの詳細については、Actions Consoleの[ここ](https://developers.google.com/actions/console/troubleshooting)を参照してください。

## ポリシー

特定のアカウントリンクポリシーについては、[Actions on Google Policies](https://developers.google.com/actions/distribute/general-policies#account_linking)をご覧ください。ユーザーがGoogle Sign-Inを使用してサービスにログインする場合は、無関係のアクセス権を要求しないなど、[API Terms of Service](https://developers.google.com/terms/api-services-user-data-policy)に準拠する必要があります。

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/identity/account-linking)
