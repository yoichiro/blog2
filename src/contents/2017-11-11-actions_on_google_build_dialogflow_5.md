---
layout: actions_on_google
title: Build with Dialogflow "Build Fulfillment"の日本語訳です
categories:
- actions on google
---
Actions on GoogleとDialogflowにてGoogleアシスタント向けアプリを開発する際に、そのアプリの本体とも言えるものがフルフィルメント（Fulfillment）になります。フルフィルメントは、DialogflowからWebhookで呼び出され、ユーザからの入力の解析結果が渡されます。そして、何か処理を行った後に、ユーザに返事をする処理を書くのも、フルフィルメントです。

Actions on Googleでは、Dialogflowでのフルフィルメントの作り方について、[Build Fulfillment](https://developers.google.com/actions/dialogflow/fulfillment)で説明がされています。以下は、その日本語訳です。

---

# フルフィルメントの構築

フルフィルメントは、ユーザーの入力と、入力を処理し、最終的にアクションを実行するロジックを獲得するための、あなたのアプリの会話インターフェースを定義します。

<iframe class="devsite-embedded-youtube-video" data-autohide="1" data-showinfo="0" frameborder="0" allowfullscreen="" id="ytplayer0" data-enablejsapi="1" src="//www.youtube.com/embed/ykSFAs6S_Kg?autohide=1&amp;showinfo=0&amp;enablejsapi=1" origin="https://developers.google.com" style="border: 0;"></iframe>

> **レスポンスの構築に関する詳細**
>
> ここでは、フルフィルメントを構築する一般的な方法について説明します。会話の特定の部分を処理する方法の詳細については、[Surface Capabilities](https://developers.google.com/actions/assistant/surface-capabilities)および[Responses](https://developers.google.com/actions/assistant/responses)を参照してください。

## あなたの会話を定義する

> **注:** インテントを使ってできることの詳細については、[Dialogflow documentation](https://www.dialogflow.com/docs/concept-intents)を参照してください。

今、アクションを定義したので、それらのアクションに対応する会話を構築することができます。これを行うには、文法を定義するDialogflowインテントを作成するか、またはインテントを作動させるためにユーザーが言う必要があるものと、作動されたときにインテントを処理するために対応するフルフィルメントを作成します。

会話の文法全体を定義するのと同じくらい多くのインテントを作成できます。

### インテントの作成

Dialogflowの左側のナビゲーションの **Intents** メニュー項目で、 **+** 記号をクリックします。インテントエディタが表示され、次の情報を入力できます。

![](https://developers.google.com/actions/images/intent-editor.png){:width="800px"}

* **Intent name** は、IDEに表示されるインテントの名前です。
* **Contexts** によって、特定のケースに対するインテントの作動を狭めることができます。これはDialogflowの高度な機能です。詳細については、[Contexts](https://www.dialogflow.com/docs/concept-contexts)に関するDialogflowのドキュメントをお読みください。
* **User says** フレーズは、インテントを作動させるためにユーザが言う必要のあること（文法）を定義します。ユーザーがインテントを作動させるために何を言えるかについて、ここにいくつかのフレーズ（5〜10個）を入力します。Dialogflowは、あなたが提供するサンプルフレーズの自然なバリエーションを自動的に処理します。
* **Events** は、ユーザーが何も言う必要性がなくても、インテントを作動させます。イベントの一例は、 `GOOGLE_ASSISTANT_WELCOME` イベントで、これによってGoogleアシスタントがアプリを呼び出すことができます。このイベントは、あなたのアプリの[デフォルトアクション](https://developers.google.com/actions/dialogflow/define-actions#define-a-default-action)として使用されます。[組み込みのヘルパーインテント](https://developers.google.com/actions/assistant/helpers#built-in_helper_intents)の詳細については、ドキュメントを参照してください。
* **Action** は、このインテント向けにフルフィルメントが有効になっている場合、フルフィルメントに渡すデータを定義します。これには、ユーザーの入力から解析されたデータと、どのインテントが作動されたかを検出するためにフルフィルメントで使用できる名前が含まれます。この名前は、後でアクションマップを作成するときに使用します。アクションマップは、インテントを対応するフルフィルメントロジックに関連付けします。アクションの定義の詳細については、Dialogflowドキュメンテーションの[Actions and Parameters](https://www.dialogflow.com/docs/concept-actions)を参照してください。
 > **注:** "Action"は、Dialogflowのインテントのこの特定のコンポーネントを指していて、Actions on Googleプラットフォームにおいて、アクションのより大きな概念を指すものではありません。

* **Response** - Dialogflowレスポンスビルダを使用すると、フルフィルメントを呼び出すことなく、Dialogflow内で直接このインテントへのレスポンスを定義できます。この機能は、静的であり、フルフィルメントを必要としない応答に役立ちます。あなたは、簡単な「歓迎」や「さようなら」のメッセージのようなもののために、これを使うかもしれません。ただし、ほとんどのインテント向けに、フルフィルメントを使用してユーザーに応答する可能性があります。
* **Fulfillment** は、このインテントが作動したときに、フルフィルメントを呼び出したいかどうかを指定します。Dialogflowエージェントのほとんどのインテントに対して、これを有効にする可能性が最も高いです。インテント内でこの項目を表示するには、 **Fulfillment** メニューのエージェントに対して、フルフィルメントが有効になっている必要があります。
* **Actions on Google** では、インテント向けのプラットフォーム固有の設定を指定できます。現在、**End conversation** だけがサポートされています。これにより、マイクが閉じられ、対応する応答がユーザに返された後に、会話が終了します。この設定は、インテント向けにフルフィルメントを使用しない場合にのみ適用されます。

## Dialogflowでの応答の構築

いくつかのインテントのために、フルフィルメントが応答を返す必要はないかもしれません。このような場合、Dialogflowでレスポンスビルダーを使用してレスポンスを作成できます。

**Response** 領域で、 **Default Text Response** または **Actions on Google Response** を指定します。デフォルトのテキスト応答は、複数のDialogflow統合全体で機能できる単純なTTSテキスト応答です。あなたが使用する可能性が最も高いGoogleアシスタント向けの応答は、[Responses](https://developers.google.com/actions/assistant/responses)ドキュメントに記載されています。

![](https://developers.google.com/actions/images/apiai-response-builder.png){:width="800px"}

## フルフィルメント応答の構築

フルフィルメントを使用するインテントが作動されると、インテントに関する情報を含むDialogflowからのリクエストを受け取ります。次に、インテントを処理して応答を返すことによって、リクエストに応答します。この要求と応答は、[Dialogflow webhook](https://developers.google.com/actions/dialogflow/webhook)によって定義されます。

ただし、Node.jsクライアントライブラリを使用して要求を処理し、応答を返すことを強くお勧めします。クライアントライブラリを使用する一般的なプロセスを次に示します。

1. [DialogflowAppオブジェクトを初期化します](https://developers.google.com/actions/dialogflow/fulfillment#initialize_the_DialogflowApp_object)。このオブジェクトは、要求の受け付けを自動的に処理して、フルフィルメントで処理できるように解析します。
1. [要求を処理する関数を作成します](https://developers.google.com/actions/dialogflow/fulfillment#create_functions_to_handle_requests)。これらの関数は、ユーザー入力およびインテントの他のコンポーネントを処理し、Dialogflowに戻るためのレスポンスを作成します。
1. インテントを関数にマップする[アクションマップを構築し、初期化します](https://developers.google.com/actions/dialogflow/fulfillment#build_and_initialize_an_action_map)。Dialogflowエージェントでインテントが作動されるたびに、それにはアクション名が含まれます。適切な関数を自動的に呼び出すために、アクション名を関数にマップします。

### DialogflowAppオブジェクトの初期化

次のコードは、[`DialogflowApp`](https://developers.google.com/actions/reference/nodejs/DialogflowApp#DialogflowApp)をインスタンス化し、Google Cloud Functionsの定型的なNode.js設定を行います。

```js
'use strict';

const DialogflowApp = require('actions-on-google').DialogflowApp;

exports.echoNumber = (req, res) => {
  const app = new DialogflowApp({request: req, response: res});

  // Create functions to handle requests here

}
```

### 要求を処理する関数を作成する

ユーザーがインテントを作動させるフレーズを話すと、あなたのフルフィルメント内の機能で処理するDialogflowからの要求を受け取ります。この関数では、一般的に以下のことを行います。

1. ユーザー入力を処理するために必要なロジックを実行します。通常、次の `get` 関数を呼び出して、要求からユーザー入力を取得します。
 * `getArgument()` - クエリパターンの引数から名前で値を取得する。
 * `getRawInput()` - ユーザーの生の入力を文字列として取得する。
 > **注:** `get` 関数の完全なリストについては、[DialogflowApp](https://developers.google.com/actions/reference/nodejs/DialogflowApp)のリファレンスを、生の要求フォーマットを見るには、[Dialogflow webhook format](https://developers.google.com/actions/dialogflow/webhook)を参照してください。
1. 作動されたインテントに応答するためのレスポンスを構築します。ユーザーが適切な応答を作成するために使用しているサーフェスを考慮してください。異なるサーフェス向けのレスポンスを提供する方法の詳細については、[surface capabilities](https://developers.google.com/actions/assistant/surface-capabilities)を参照してください。
1. レスポンスを伴って、 `ask()` または `tell()` 関数を呼び出します。
 > **注:** `ask()` は、応答を送信した後に、あなたのフルフィルメントがリクエストを期待していることをDialogflowに伝えます。 `tell()` は、ユーザーに応答を提示し、会話を終了するように、Dialogflowに指示します。これにより、Dialogflowはマイクを閉じ、ユーザがアプリを再び呼び出すまで、あなたのフルフィルメントにそれ以上のリクエストを送信しないように指示します。インテント向けの **Actions on Google > End conversation** 設定は、使用するフルフィルメント関数によって上書きされます。

次のコードは、呼び出しのインテント（`input.welcome`）と、ユーザーをあなたのアプリに歓迎し、名前を持つDialogflowインテントのためにユーザーが話した番号をエコーバックするダイアログインテント（`input.number`）を処理する2つのTTS応答を構築する方法を示しています。

```js
const app = new DialogflowApp({request: request, response: response});

const WELCOME_INTENT = 'input.welcome';  // Dialogflowインテントからのアクション名
const NUMBER_INTENT = 'input.number';  // Dialogflowインテントからのアクション名
const NUMBER_ARGUMENT = 'input.mynum'; // Dialogflowインテントからのアクション名

function welcomeIntent (app) {
  app.ask('Welcome to number echo! Say a number.');
}

function numberIntent (app) {
  let number = app.getArgument(NUMBER_ARGUMENT);
  app.tell('You said ' + number);
}
```

### アクションマップの構築と初期化

作動されたインテントを処理する全ての関数を得たら、インテントを関数にマップするアクションマップを作成します。たとえば、次のコードになります。

* 新しいアクションマップをインスタンス化する
* `input.welcome` アクション名を `welcomeIntent()` 関数に関連付ける
* `input.number` アクション名を `numberIntent()` 関数に関連付ける
* DialogflowAppオブジェクトに、入ってきたインテントをアクションマップによって定義された対応する関数にルーティングするように指示する

```js
let actionMap = new Map();
actionMap.set(WELCOME_INTENT, welcomeIntent);
actionMap.set(NUMBER_INTENT, numberIntent);
app.handleRequest(actionMap);
```

ハンドラ関数が1つしかない場合、これを行う必要はなく、関数名を `handleRequest()` に渡すだけです。この場合、作動されたインテントは、全てこの関数を呼び出すため、適切な処理を行うために作動されるインテントをチェックする必要があります。

```js
const WELCOME_INTENT = 'input.welcome';
const NUMBER_INTENT = 'input.number';
const NUMBER_ARGUMENT = 'input.mynum';

function responseHandler (app) {
  // DialogflowのActionsエリアで定義されたインテントの名前を含むインテント
  let intent = app.getIntent();
  switch (intent) {
    case WELCOME_INTENT:
      app.ask('Welcome! Say a number.');
      break;

    case NUMBER_INTENT:
      let number = app.getArgument(NUMBER_ARGUMENT);
      app.tell('You said ' + number);
      break;
  }
}
// アクションマップの代わりに、関数名を追加可能
app.handleRequest(responseHandler);
```

### 不一致再指示

Dialogflowがインテントの`User says`部分に定義されている入力文法に一致させることができなかった場合、Dialogflowはフォールバックインテントを作動させます。フォールバックインテントは、通常、ユーザーがアクションに必要な情報を入力するように再指示します。フォールバックインテントの **Response** 領域で指定することで、再指示のフレーズを提供することができます。また、Webhookを使用してレスポンスを提供することもできます。

> **注:** デフォルトでは、Dialogflowは全てのエージェントに対して **デフォルトのフォールバックインテント（Default Fallback Intent）** を自動的に作成します。このインテントによって提供されたレスポンスを修正するか、削除して、不一致のケースを適切に処理するための独自のフォールバックインテントを作成する必要があります。 **デフォルトフォールバックインテント（Default Fallback Intent）** のデフォルトの応答は、ほとんどの場合、一般的すぎます。

フォールバックインテントを作成するために、以下を行います。

1. Dialogflowの左側のナビゲーションで、 **Intents** をクリックします。
1. **Create Intent** ボタンの右にあるメニューアイコンをクリックし、 **Create Fallback Intent** を選択します。または、 **Default Fallback Intent** をクリックして編集します。
1. ユーザーに話すための再指示のフレーズを指定します。これらのフレーズは、会話型であり、可能な限りユーザの現在の文脈に対して有用であるべきです。<br>
  **フルフィルメントなしでこれを行うために:**<br>
  インテントの **Response** 領域にフレーズを指定します。Dialogflowは、より具体的なインテントが作動されるまで、このリストからフレーズをランダムに選択してユーザーに伝えます。<br>
  **フルフィルメントを使ってこれを行うために**<br>
  * インテントの **Fulfillment** 領域で、 **Use webhook** チェックボックスを選択します。
  * フルフィルメントのロジックでは、[リクエストを処理する関数の作成](https://developers.google.com/actions/dialogflow/fulfillment#create_functions_to_handle_requests)で説明したように、他のインテントと同様に作動されるフォールバックインテントを処理します。

   例えば、次の関数は、フォールバックインテントが作動された回数を追跡するカウンタを格納する `app.data` オブジェクト（状態を維持するために使用できる任意のデータペイロード）を使用します。1回以上作動されると、アクションは終了します。他のインテントが作動されたときに、カウンタをリセットすることができます。<br>
   ```js
   function defaultFallback (app) {
     app.data.fallbackCount++;
     // Provide two prompts before ending game
     if (app.data.fallbackCount === 1) {
       app.setContext(DONE_YES_NO_CONTEXT);
       app.ask('Are you done playing Number Genie?');
     } else {
       app.tell('Since I\'m still having trouble, so I\'ll stop here. Let’s play again soon.');
     }
   }
   ```
   これの実装方法の詳細は、[Number Genie sample](https://github.com/actions-on-google/dialogflow-number-genie-nodejs/)を参照してください。

### 文脈の使用

特定の状況でのみDialogflowがフォールバックインテントを作動するようにするには、文脈を使用します。これは、異なる不一致シナリオで異なるフォールバックインテントを使用する場合に役立ちます。

* フォールバックインテントに文脈を設定しない場合は、一致する他のインテントがない時に、Dialogflowが作動するグローバルフォールバックインテントと見なされます。あなたが使用することを選択した場合、これらのうちの1つだけを定義する必要があります。
* フォールバックインテントに入力の文脈を設定した場合、Dialogflowは、次の条件が成立すると、このフォールバックインテントを作動させます。
  * ユーザーの現在の文脈が、インテントで定義された文脈のスーパーセットであること。
  * 他のインテントが一致しないこと。

  これにより、さまざまな入力の文脈で複数のフォールバックインテントを使用して、特定のシナリオに対する不一致再指示をカスタマイズできます。
* フォールバックインテントに出力の文脈を設定すると、フォールバックインテントが作動して処理された後も、同じ文脈にユーザーを保持します。

より詳しい情報は、[Dialogflow Contexts](https://www.dialogflow.com/docs/concept-contexts)を参照ください。

### 入力なし再指示

ユーザーに応答を返すために `ask()` メソッドを呼び出すと、Googleアシスタントが入力を検出できない場合に、ユーザーに表示される指示の配列を指定できます。Googleアシスタントは、これらの指示を順番に発言し、あなたは3つまでのプロンプトを指定できます。例えば以下です。

```js
app.ask(`Guess a number`,
  ['I didn\'t hear a number', 'If you\'re still there, what\'s your guess?',
    'We can stop here. Let\'s play again soon.']);
```

> **注:** バグのため、入力なし指示を指定すると、3番目の指示の後にマイクが閉じられ、アクションが終了します。バグが修正されるまで、byeメッセージを3番目の指示に使用する必要があります。

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/dialogflow/fulfillment)
