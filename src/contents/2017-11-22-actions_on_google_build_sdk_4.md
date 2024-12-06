---
layout: actions_on_google
title: Build with Actions SDK "Build Fulfillment"の日本語訳です
categories:
- actions on google
---
Googleアシスタント向けのアプリにて、実際にユーザからの入力を処理して返事を返すのは、フルフィルメントの役目です。Actions SDKを使ったアプリにおいても、フルフィルメントを作ることになります。

Actions on Googleの[Build with Actions SDK - Build Fulfillment](https://developers.google.com/actions/sdk/fulfillment)では、フルフィルメントをどう実装すれば良いかが説明されています。以下は、その日本語訳です。

---

# フルフィルメントの構築

> **レスポンス構築についての詳細を学ぶ**
>
> このドキュメントでは、フルフィルメントを一般的に構築する方法について説明しています。会話の特定の部分を処理する方法の詳細については、[Surface Capabilities](https://developers.google.com/actions/assistant/surface-capabilities)および[Responses](https://developers.google.com/actions/assistant/responses)を参照してください。

フルフィルメントは、ユーザーの入力と、入力を処理し、最終的にアクションを実行するロジックを獲得するための、アプリの会話インターフェースを定義します。

## 概要

フルフィルメントは、アシスタントからリクエストを受け取り、リクエストを処理して応答します。この前後の要求および応答プロセスは、最終的に最初のユーザー要求を満たすまで会話を進めます。

次の手順では、Node.jsクライアントライブラリを使用して、フルフィルメントを構築する方法を説明します。

1. [ActionsSdkApp オブジェクトを初期化します。](https://developers.google.com/actions/sdk/fulfillment#initialize_the_actionssdkapp_object)
1. フルフィルメントのロジックにて、[要求を処理する関数を作成します](https://developers.google.com/actions/sdk/fulfillment#create_functions_to_handle_requests)。
1. インテントを関数にマップする[アクションマップを構築し、初期化します](https://developers.google.com/actions/sdk/fulfillment#build_and_initialize_an_action_map)。エンドポイントが要求を受け取ると、クライアントライブラリは要求に関連付けられたインテントをチェックし、適切な関数を自動的に呼び出します。

## ダイアログ（対話）の構築

### ActionsSdkApp オブジェクトを初期化する

次のコードは、[`ActionsSdkApp`](https://developers.google.com/actions/reference/nodejs/ActionsSdkApp#ActionsSdkApp)をインスタンス化し、Google Cloud Functionsの定型的なNode.js設定を行います。

```js
'use strict';

const ActionsSdkApp = require('actions-on-google').ActionsSdkApp;

exports.<insertCloudFunctionName>= (req, res) => {
  const app = new ActionsSdkApp({request: req, response: res});

  // Create functions to handle requests here
```

### 要求を処理する関数を作成する

ユーザーがフレーズを話すと、Googleアシスタントからリクエストが届きます。リクエストに含まれるインテントを実行するには、作動されたインテントを処理する関数を作成します。

> **注:** フルフィルメントを提供できるインテントの詳細については、[intents](https://developers.google.com/actions/reference/rest/intents)を参照してください。

リクエストを以下のように処理します。

1. ユーザー入力を処理するために必要なロジックを実行します。通常、次の `get` 関数を呼び出して、要求からユーザー入力を取得します。
 * `getArgument()` - [`StandardIntents.MAIN`](https://developers.google.com/actions/reference/nodejs/ActionsSdkApp#StandardIntents)インテントのクエリパターン引数から値を取得します。
 * `getRawInput()` - [`StandardIntents.TEXT`](https://developers.google.com/actions/reference/nodejs/ActionsSdkApp#StandardIntents)インテントからユーザーの生の入力を文字列として取得します。
 > **注:** **get** 関数の完全なリストについては、[ActionsSdkApp](https://developers.google.com/actions/reference/nodejs/ActionsSdkApp)のリファレンスを参照してください。生のリクエスト形式を見るには、[conversation webhook request format](https://developers.google.com/actions/reference/rest/Shared.Types/AppRequest)を参照してください。
1. [`buildInputPrompt()`](https://developers.google.com/actions/reference/nodejs/ActionsSdkApp#buildInputPrompt)で入力プロンプトを構築して、ユーザーに話し返すテキストを作成します。
1. 入力プロンプトで[`ask()`](https://developers.google.com/actions/reference/nodejs/ActionsSdkApp#ask)関数を呼び出します。

以下のコードは、シンプルなレスポンスの構築方法を示しています。

```js
function mainIntent (app) {
  let inputPrompt = app.buildInputPrompt(false,
    'Hi! Say something, and I\'ll repeat it');
  app.ask(inputPrompt);
}

function respond (app) {
  let inputPrompt = app.buildInputPrompt(false,
    'Hi! Say something, and I\'ll repeat it.');
  app.ask(inputPrompt);
}
```

### アクションマップの構築と初期化

作動されたインテントを処理するすべての関数を得たら、インテントを関数に関連付けするアクションマップを作成します。

```js
let actionMap = new Map();
actionMap.set(app.StandardIntents.MAIN, mainIntent);
actionMap.set(app.StandardIntents.TEXT, respond);

//add more intents to the map
...

app.handleRequest(actionMap);
```

ハンドラ関数が1つしかない場合は、これを行う必要はなく、関数名を `handleRequest` に渡すだけです。この場合、作動されたインテントはすべてこの関数を呼び出すため、適切な処理を行うために
作動されるインテントをチェックする必要があります。

```js
function responseHandler (app) {
  // intent contains the name of the intent you defined in `initialTriggers`
  let intent = app.getIntent();
  switch (intent) {
    case app.StandardIntents.MAIN:
      app.ask('Welcome! Say a number.');
      break;

    case app.StandardIntents.TEXT:
      let number = app.getArgument(NUMBER_ARGUMENT);
      app.tell('You said ' + number);
      break;
  }
}
// you can add the function name instead of an action map
app.handleRequest(responseHandler);
```

### 会話の終了

もうユーザーの入力を待つことなく会話を終了したい場合は、[`tell()`](https://developers.google.com/actions/reference/nodejs/ActionsSdkApp#tell)関数を呼び出します。この機能は、Googleアシスタントがユーザーにテキストを話し返すように指示し、マイクを閉じて会話を終了します。この機能は、会話アクションで少なくとも1回は呼び出す必要があります。

## メインの呼び出しインテントの処理

ユーザーが `app.intent.action.MAIN` インテントを作動させる場合、通常はユーザー入力処理を行う必要はありません。アクションパッケージに多くのアクションが含まれていて、多くのユースケースをカバーしている場合、ユーザができることをいくつか教えて指導することをお勧めします。

1. ユーザを方向付けし、あなたのアクションが行うことができるいくつかのことを説明する 'buildInputPrompt()' を使って、入力プロンプトを構築します。
1. 入力プロンプトで `ask()` 関数を呼び出します。Googleアシスタントは、応答としてユーザーへの入力プロンプトを話し、ユーザーが指定したインテントの1つが作動されるのを待ちます。
 > **注:** この配列には、通常、アクションパッケージ内に使用可能なすべての **initialTriggers** インテントが含まれているため、ユーザはウェルカムインテントハンドラ関数で挨拶を送信した後に作動させられます。

以下のコードスニペットは、シンプルなウェルカムインテントを処理する方法を示しています。

```js
// handle the initialTrigger
function mainIntent (app) {
  let inputPrompt = app.buildInputPrompt(
    false,
    'Hi, I\'m Do It All. You can ask to do this or that. What would you like to do?');
  app.ask(inputPrompt);
}
```

### 会話の状態

あなたとGoogleアシスタント間を行き来するJSON形式のオブジェクトを使用して、会話の状態を維持することができます。

`app.data` フィールドに直接書き込み、および読み込みを行うことができます。このフィールドは、[`handleRequest()`](https://developers.google.com/actions/reference/nodejs/AssistantApp#handleRequest)が呼び出され、エンドポイントが要求を受け取る準備ができたときに、自動的に前後に渡されます。

```js
app.data = { something:10 }
...
let value = app.data.something
```

もしくは、JSONオブジェクトとしてGoogleアシスタントにダイアログの状態を渡すために、[`ask()`](https://developers.google.com/actions/reference/nodejs/ActionsSdkApp#ask) 関数を使うことができ、その後レスポンスを受ける時は、[`getDialogState()`](https://developers.google.com/actions/reference/nodejs/ActionsSdkApp#getDialogState) を使ってそれを読むことができます。

```js
// get state object and modify it
let state = app.getDialogState();
...
// return it in the response
app.ask(inputPrompt, state);
```

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/sdk/fulfillment)
