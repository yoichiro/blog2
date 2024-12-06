---
layout: actions_on_google
title: Conversations "Helpers"の日本語訳です
categories:
- actions on google
---
Googleアシスタント向けアプリを開発している中で、「あ、この情報欲しいな」と思うことがあります。それは、ユーザの氏名だったり、
住所だったりです。また、ユーザにGoogleサインインしてもらって、その結果のアクセストークンを得たいこともあるでしょう。そのような
情報を入手するために、Actions on Googleは共通的な機能を提供しています。それらを「ヘルパー（Helpers）」と呼びます。

Actions on Googleのドキュメントにある
[Conversations - Helpers](https://developers.google.com/actions/assistant/helpers)にて、
各ヘルパーの利用方法が説明されています。以下はその日本語訳です。

---

# ヘルパー

ヘルパーは、ユーザーのフルネーム、日付と時間、または配達先アドレスなどの共通的なデータを取得するために会話を
一時的に引き継ぐようアシスタントに指示します。ヘルパーをリクエストすると、アシスタントは標準の一貫したUIをユーザーに提示して、
この情報を取得します。したがって、独自のUIを設計する必要はありません。

## 使用法の概要

DialogflowとActions SDKでヘルパーを使用するための一般的なプロセスについては、DialogflowとActions SDKについて後述します。
各ヘルパーの詳細については、特定のヘルパーのセクションを参照してください。

### Dialogflow

**NODE.JS**

1. 適切なパラメータを指定して `askFor...()` または `askWith...()` ヘルパー関数の1つを呼び出します。ヘルパー関数は、[built-in intents](https://developers.google.com/actions/assistant/helpers#built-in_helper_intents)で記述されたインテントの1つを実行するようにアシスタントに要求します。ヘルパー関数を呼び出すと、クライアントライブラリはこれらのインテントの1つを含む応答をアシスタントに送信します。インテントに基づいて、アシスタントは対応するヘルパーのダイアログを実行することになります。
1. 組み込みのヘルパーインテントの1つに対応するイベントを指定するDialogflowインテントを宣言します。サポートされているイベントのリストについては、[built-in helperintents](https://developers.google.com/actions/assistant/helpers#built_in_intents)セクションを参照してください。 このインテントでは、 **User says** フレーズを持つ必要はありません。イベントが発生したときに常にトリガーされるからです。
1. アシスタントが後続のリクエストにおいてヘルパーの結果をあなたのフルフィルメントに返すと、対応するDialogflowインテントがトリガーされ、インテントを正常に処理します。必要なデータを取得するには、ヘルパーに対応するgetter関数を使用します。

**JSON**

1. アシスタントに応答するときに、[possibleIntents](https://developers.google.com/actions/reference/rest/Shared.Types/AppResponse#ExpectedInput.FIELDS.possible_intents)オブジェクトにヘルパーのインテントを指定します。アシスタントは応答を受け取ると、ヘルパーのダイアログを実行する必要があることを認識します。達成するためにどのようなインテントを要求できるかについては、[built-in helper intents](https://developers.google.com/actions/assistant/helpers#built-in_helper_intents)を参照してください。
1. 組み込みのヘルパーインテントの1つに対応するイベントを指定するDialogflowインテントを宣言します。サポートされているイベントのリストについては、[built-in helper intents](https://developers.google.com/actions/assistant/helpers#built-in_helper_intents)のセクションを参照してください。このインテントでは、 **User says** フレーズを持つ必要はありません。イベントが発生したときに常にトリガーされるからです。
1. アシスタントが後続のリクエストでヘルパーの結果をあなたのフルフィルメントに返す際には、リクエストと必要なデータを解析してください。

### Actions SDK

**NODE.JS**

1. 適切なパラメータを指定して `askFor...()` または `askWith...()` ヘルパー関数の1つを呼び出します。ヘルパー関数は、[built-in helper intents](https://developers.google.com/actions/assistant/helpers#built-in_helper_intents)で記述されたインテントの1つを実行するようにアシスタントに要求します。ヘルパー関数を呼び出すと、クライアントライブラリはこれらのインテントの1つを含む応答をアシスタントに送信します。インテントに基づいて、アシスタントは対応するヘルパーのダイアログを実行することになります。
1. アシスタントは、後続のリクエストでヘルパーの結果をフルフィルメントに返すと、リクエストに対応するインテントを受け取ります。これにより、ヘルパーが結果を返したことを検出できます。必要なデータを取得するには、ヘルパーに対応するgetter関数を使用します。

**JSON**

1. アシスタントに応答するときに、[possibleIntents](https://developers.google.com/actions/reference/rest/Shared.Types/AppResponse#ExpectedInput.FIELDS.possible_intents)オブジェクトにヘルパーのインテントを指定します。アシスタントは応答を受け取ると、ヘルパーのダイアログを実行する必要があることを認識します。達成するためにどのようなインテントを要求できるかについては、[built-in helper intents](https://developers.google.com/actions/assistant/helpers#built-in_helper_intents)を参照してください。
1. アシスタントが後続のリクエストでヘルパーの結果をあなたのフルフィルメントに返す際には、リクエストと必要なデータを解析してください。

## 組み込みのヘルパーインテント

次の表は、アシスタントに実行を依頼できる、サポートされているインテントについて説明しています。Dialogflowを使用している場合は、ヘルパーインテントに対応するイベントを指定するDialogflowインテントを作成する必要があります。

<table style="background-color: #78909c;">
<tr style="color: white;">
<td style="padding: 5px;">インテント名</td>
<td style="padding: 5px;">Dialogflowイベント名</td>
<td style="padding: 5px;">使用方法</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>actions.intent.PERMISSION</b>
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>actions_intent_PERMISSION</b>
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
ユーザのフルネーム、荒い位置、または正確な位置、またはそれら3つ全てを取得する。
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>actions.intent.OPTION</b>
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>actions_intent_OPTION</b>
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
リストまたはカルーセルUIから選択された項目を受け取る。
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>actions.intent.PLACE</b>
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>actions_intent_PLACE</b>
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
ユーザから位置を得る。
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>actions.intent.DATETIME</b>
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>actions_intent_DATETIME</b>
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
ユーザから日付や時間の入力を得る。
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>actions.intent.SIGN_IN</b>
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>actions_intent_SIGN_IN</b>
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
Requests an account linking flow to link a user's account.
ユーザアカウントにリンクするために、アカウントリンクフローを要求する。
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>actions.intent.DELIVERY_ADDRESS</b>
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>actions_intent_DELIVERY_ADDRESS</b>
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
ユーザから配送先の入力を得る。
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>actions.intent.CONFIRMATION</b>
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>actions_intent_CONFIRMATION</b>
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
ユーザから確認を得る（例として、YesまたはNoの質問の答え、など）
</td>
</tr>
</table>

以下のセクションでは、使用可能なヘルパーと、ヘルパーを使用するために要求する必要のあるインテントについて説明します。

## ユーザ情報

このヘルパーを使って、以下のユーザ情報を得ることができます。

* Display name
* Given name
* Family name
* 粗めのデバイス位置 (ZIP code および city)
* 正確なデバイス位置 (座標および Street address)

### ヘルパーの呼び出し

以下のように、Node.js クライアントライブラリの `askForPermission()` または `askForPermissions()` のどちらかを使って、
ヘルパーを呼び出すことができます。

```js
/*
 * @param {string} context Context why permission is asked; it's the TTS
 *     prompt prefix (action phrase) we ask the user.
 * @param {string} permission One of the permissions Assistant supports, each of
 *     which comes from AssistantApp.SupportedPermissions.
 * @param {Object=} dialogState JSON object the app uses to hold dialog state that
 *     will be circulated back by Assistant.
 * @return {(Object|null)} A response is sent to the Assistant to ask for the user's permission;
 *     for any invalid input, we return null.
 * @actionssdk
 * @dialogflow
*/
askForPermission (context, permission, dialogState)
```

```js
/*
 * @param {string} context Context why the permission is being asked; it's the TTS
 *     prompt prefix (action phrase) we ask the user.
 * @param {Array} permissions Array of permissions App supports, each of
 *     which comes from AssistantApp.SupportedPermissions.
 * @param {Object=} dialogState JSON object the app uses to hold dialog state that
 *     will be circulated back by Assistant. Used in {@link ActionsSdkApp}.
 * @return {(Object|null)} A response is sent to Assistant to ask for the user's permission.
 *     For any invalid input, we return null.
 * @actionssdk
 * @dialogflow
*/
askForPermissions (context, permissions, dialogState)
```

以下のJSONでは、Actions SDKを使用したWebhookリクエストについて説明しています。

**NODE.JS**

```js
// Choose one or more supported permissions to request:
// app.SupportedPermissions.NAME
// app.SupportedPermissions.DEVICE_PRECISE_LOCATION
// app.SupportedPermissions.DEVICE_COARSE_LOCATION

let namePermission = app.SupportedPermissions.NAME;
let preciseLocationPermission = app.SupportedPermissions.DEVICE_PRECISE_LOCATION

// Ask for one permission
app.askForPermission('To address you by name', namePermission);
// Ask for more than one permission. User can authorize all or none.
app.askForPermissions('To address you by name and know your location',
    [namePermission, preciseLocationPermission]);
```

**JSON**

```json
{
  "conversationToken": "{\"state\":null,\"data\":{}}",
  "expectUserResponse": true,
  "expectedInputs": [
    {
      "inputPrompt": {
        "initialPrompts": [
          {
            "textToSpeech": "PLACEHOLDER_FOR_PERMISSION"
          }
        ],
        "noInputPrompts": []
      },
      "possibleIntents": [
        {
          "intent": "actions.intent.PERMISSION",
          "inputValueData": {
            "@type": "type.googleapis.com/google.actions.v2.PermissionValueSpec",
            "optContext": "To deliver your order",
            "permissions": [
              "NAME",
              "DEVICE_PRECISE_LOCATION"
            ]
          }
        }
      ]
    }
  ]
}
```

### ヘルパーの結果の取得

> **注:** 現在のところ、正確なデバイス位置は、電話機の場合は緯度/経度座標を、音声認識可能なスピーカーの場合は緯度/経度座標とジオコーディングされたアドレスのみを返します。粗めのデバイス位置は、音声で起動するスピーカーでのみ動作します。

ユーザーがヘルパーに応答すると、あなたはフルフィルメントへのリクエストを受け取り、 `isPermissionGranted()` を呼び出してユーザーが情報を許可したかどうかを確認し、 `getUserName()` または `getDeviceLocation()` でデータにアクセスできます。

**NODE.JS**

```js
if (app.isPermissionGranted()) {
  let displayName = app.getUserName().displayName;
  let deviceCoordinates = app.getDeviceLocation().coordinates;
}
```

**JSON**

```json
{
  "user": {
    "userId": "user123",
    "profile": {
      "displayName": "Jane Smith",
      "givenName": "Jane",
      "familyName": "Smith"
    }
  },
  "conversation": {
    "conversationId": "1494884577894",
    "type": "ACTIVE",
    "conversationToken": "{\"state\":null,\"data\":{}}"
  },
  "inputs": [
    {
      "intent": "actions.intent.PERMISSION",
      "rawInputs": [
        {
          "inputType": "KEYBOARD",
          "query": "yes"
        }
      ],
      "arguments": [
        {
          "name": "PERMISSION",
          "rawText": "yes",
          "textValue": "true"
        }
      ]
    }
  ],
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
  "device": {
    "location": {
      "coordinates": {
        "latitude": 37.422366,
        "longitude": -122.084406
      },
      "formattedAddress": "1600 Amphitheatre Parkway, Mountain View, CA 94043, United States",
      "zipCode": "94043",
      "city": "Mountain View"
    },
    "locale": "en-US"
  },
  "isInSandbox": false
}
```

ユーザーの情報を取得したら、この情報を保持しておくことをお勧めします。つまり、再び訪ねないでください。Firebaseを使用してユーザ情報を保存する方法については、[Name Psychic sample](https://github.com/actions-on-google/dialogflow-name-psychic-nodejs)を参照してください。 ユーザーの情報を保存する場合は、[レビューのためにアプリを提出する](https://developers.google.com/actions/distributing-your-apps#submitting-your-apps-for-approval)際に、アクションのプライバシーポリシーにその情報を開示してください。

## リストとカルーセルの選択肢

リストまたはカルーセルUIを表示し、 `actions.intent.OPTION` インテントを使用して、選択した項目をユーザーから取得できます。

**NODE.JS**

```js
function list (app) {
  app.askWithList(app.buildRichResponse()
    .addSimpleResponse('Alright')
    .addSuggestions(
      ['Basic Card', 'List', 'Carousel', 'Suggestions']),
    // Build a list
    app.buildList('Things to learn about')
    // Add the first item to the list
    .addItems(app.buildOptionItem('MATH_AND_PRIME',
      ['math', 'math and prime', 'prime numbers', 'prime'])
      .setTitle('Math & prime numbers')
      .setDescription('42 is an abundant number because the sum of its ' +
        'proper divisors 54 is greater…')
      .setImage('http://example.com/math_and_prime.jpg', 'Math & prime numbers'))
    // Add the second item to the list
    .addItems(app.buildOptionItem('EGYPT',
      ['religion', 'egpyt', 'ancient egyptian'])
      .setTitle('Ancient Egyptian religion')
      .setDescription('42 gods who ruled on the fate of the dead in the ' +
        'afterworld. Throughout the under…')
      .setImage('http://example.com/egypt', 'Egypt')
    )
    // Add third item to the list
    .addItems(app.buildOptionItem('RECIPES',
      ['recipes', 'recipe', '42 recipes'])
      .setTitle('42 recipes with 42 ingredients')
      .setDescription('Here\'s a beautifully simple recipe that\'s full ' +
        'of flavor! All you need is some ginger and…')
      .setImage('http://example.com/recipe', 'Recipe')
    )
  );
}
```

**JSON**

```json
{
  "conversationToken": "{\"state\":null,\"data\":{}}",
  "expectUserResponse": true,
  "expectedInputs": [
    {
      "inputPrompt": {
        "richInitialPrompt": {
          "items": [
            {
              "simpleResponse": {
                "textToSpeech": "Alright! Here are a few things you can learn. Which sounds interesting?"
              }
            }
          ],
          "suggestions": [
            {
              "title": "Basic Card"
            },
            {
              "title": "List"
            },
            {
              "title": "Carousel"
            },
            {
              "title": "Suggestions"
            }
          ]
        }
      },
      "possibleIntents": [
        {
          "intent": "actions.intent.OPTION",
          "inputValueData": {
            "@type": "type.googleapis.com/google.actions.v2.OptionValueSpec",
            "listSelect": {
              "title": "Things to learn about",
              "items": [
                {
                  "optionInfo": {
                    "key": "MATH_AND_PRIME",
                    "synonyms": [
                      "math",
                      "math and prime",
                      "prime numbers",
                      "prime"
                    ]
                  },
                  "title": "Math & prime numbers",
                  "description": "42 is an abundant number because the sum of its proper divisors 54 is greater…",
                  "image": {
                    "url": "http://example.com/math_and_prime.jpg",
                    "accessibilityText": "Math & prime numbers"
                  }
                },
                {
                  "optionInfo": {
                    "key": "EGYPT",
                    "synonyms": [
                      "religion",
                      "egpyt",
                      "ancient egyptian"
                    ]
                  },
                  "title": "Ancient Egyptian religion",
                  "description": "42 gods who ruled on the fate of the dead in the afterworld. Throughout the under…",
                  "image": {
                    "url": "http://example.com/egypt",
                    "accessibilityText": "Egypt"
                  }
                },
                {
                  "optionInfo": {
                    "key": "RECIPES",
                    "synonyms": [
                      "recipes",
                      "recipe",
                      "42 recipes"
                    ]
                  },
                  "title": "42 recipes with 42 ingredients",
                  "description": "Here's a beautifully simple recipe that's full of flavor! All you need is some ginger and…",
                  "image": {
                    "url": "http://example.com/recipe",
                    "accessibilityText": "Recipe"
                  }
                }
              ]
            }
          }
        }
      ]
    }
  ]
}
```

### ヘルパーの結果の取得

ユーザーがアイテムを選択すると、選択したアイテムの値が引数として渡されます。 クライアントライブラリを使用して、 `app.getContextArgument()` を呼び出して値を読み取ることができます。戻り値では、選択した項目のキー識別子が取得されます。

```js
function itemSelected (app) {
  // Get the user's selection
  const param = app.getContextArgument('actions_intent_option',
    'OPTION').value;

  // Compare the user's selections to each of the item's keys
  if (!param) {
    app.ask('You did not select any item from the list or carousel');
  } else if (param === 'MATH_AND_PRIME') {
    app.ask('42 is an abundant number because the sum of its…');
  } else if (param === 'EGYPT') {
    app.ask('42 gods who ruled on the fate of the dead in the ');
  } else if (param === 'RECIPES') {
    app.ask('Here\'s a beautifully simple recipe that\'s full ');
  } else {
    app.ask('You selected an unknown item from the list or carousel');
  }
}
```

## 日付と時刻

`actions.intent.DATETIME` インテントのフルフィルメントを要求することによって、ユーザーから日時を取得できます。

### ヘルパーをリクエストする

`askForDateTime()` Node.jsクライアントライブラリ関数を使用してヘルパーを呼び出すことができます。

```js
/*
 * @param {string=} initialPrompt The initial prompt used to ask for a
 *     date and time. If undefined or null, Google will use a generic
 *     prompt.
 * @param {string=} datePrompt The prompt used to specifically ask for the
 *     date if not provided by user. If undefined or null, Google will use a
 *     generic prompt.
 * @param {string=} timePrompt The prompt used to specifically ask for the
 *     time if not provided by user. If undefined or null, Google will use a
 *     generic prompt.
 * @param {Object=} dialogState JSON object the app uses to hold dialog state that
 *     will be circulated back by Assistant. Used in {@link ActionsSdkApp}.
 * @return {(Object|null)} HTTP response.
 * @actionssdk
 * @dialogflow
*/
askForDateTime (initialPrompt, datePrompt, timePrompt, dialogState)
```

ユーザーに日付と時刻を尋ねるときにカスタムプロンプトを指定することができます。この関数の3つの文字列パラメータは、ユーザが日付と時刻、日付、および時刻を尋ねるためにアクションが使用するカスタムプロンプトです。

アシスタントのデフォルトのプロンプトを使用してユーザーに日時を尋ねるには、パラメータを指定せずに関数を呼び出します（以下の `datetimeWithoutPrompt` を参照）。

以下のJSONでは、Actions SDKを使用したWebhookリクエストについて説明しています。

**NODE.JS**

```js
function datetime (app) {
  app.askForDateTime('When do you want to come in?',
    'What is the best date to schedule your appointment?',
    'What time of day works best for you?');
}

function datetimeWithoutPrompt (app) {
  app.askForDateTime();
}
```

**JSON**

```json
{
  "conversationToken": "{\"state\":null,\"data\":{}}",
  "expectUserResponse": true,
  "expectedInputs": [
    {
      "inputPrompt": {
        "initialPrompts": [
          {
            "textToSpeech": "PLACEHOLDER_FOR_DATETIME"
          }
        ],
        "noInputPrompts": []
      },
      "possibleIntents": [
        {
          "intent": "actions.intent.DATETIME",
          "inputValueData": {
            "@type": "type.googleapis.com/google.actions.v2.DateTimeValueSpec",
            "dialogSpec": {
              "requestDatetimeText": "When do you want to come in?",
              "requestDateText": "What is the best date to schedule your appointment?",
              "requestTimeText": "What time of day works best for you?"
            }
          }
        }
      ]
    }
  ]
}
```

### ヘルパーの結果の取得

ユーザーがヘルパーに応答すると、あなたはフルフィルメントへのリクエストを受け取り、 `getDateTime()` を呼び出してユーザーが情報を許可したかどうかを確認できます。

**NODE.JS**

```js
function dateTimeHandler (app) {
  if (app.getDateTime()) {
    app.tell({speech: 'Great, see you at your appointment!',
      displayText: 'Great, we'll see you on ' + app.getDateTime().date.month
      + '/' + app.getDateTime().date.day
      + ' at ' + app.getDateTime().time.hours
      + (app.getDateTime().time.minutes || '')});
  } else {
    app.tell('I\'m having a hard time finding an appointment');
  }
}
```

**JSON**

```json
{
  "user": {
    "userId": "user123"
  },
  "conversation": {
    "conversationId": "1494884466160",
    "type": "ACTIVE",
    "conversationToken": "{\"state\":null,\"data\":{}}"
  },
  "inputs": [
    {
      "intent": "actions.intent.DATETIME",
      "rawInputs": [
        {
          "inputType": "VOICE",
          "query": "tuesday at 9"
        }
      ],
      "arguments": [
        {
          "name": "DATETIME",
          "datetimeValue": {
            "date": {
              "year": 2017,
              "month": 5,
              "day": 16
            },
            "time": {
              "hours": 9
            }
          }
        }
      ]
    }
  ],
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
  "device": {
    "locale": "en-US"
  },
  "isInSandbox": false
}
```

## アカウントサインイン

あなたのサービスに関連付けられているアカウントにサインインするように、 `actions.intent.SIGN_IN` インテントの達成を要求することができます。

### ヘルパーをリクエストする

`askForSignIn()` Node.jsクライアントライブラリ関数を使用してヘルパーを呼び出すことができます。

```js
/*
 * @param {Object=} dialogState JSON object the app uses to hold dialog state that
 *     will be circulated back by Assistant. Used in {@link ActionsSdkApp}.
 * @return {(Object|null)} HTTP response.
 * @actionssdk
 * @dialogflow
*/
askForSignIn (dialogState)
```

以下のJSONでは、Actions SDKを使用したWebhookリクエストについて説明しています。

**NODE.JS**

```js
function signIn (app) {
  app.askForSignIn();
}
```

**JSON**

```json
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

### ヘルパーの結果の取得

ユーザーがヘルパーに応答すると、フルフィルメントへのリクエストが送信され、 `getSignInStatus()` を呼び出してユーザーが情報を許可したかどうかを確認できます。

**NODE.JS**

```js
function signInHandler (app) {
  if (app.getSignInStatus() === app.SignInStatus.OK) {
    let accessToken = app.getUser().accessToken;
    // access account data with token
  } else {
    app.tell('You need to sign-in before using the app.');
  }
}
```

**JSON**

```json
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

## 確認

ユーザーからの一般的な確認（yes/noの質問）を求めて、結果の回答を得ることができます。"yes"と"no"の文法は自然に"Yea"や"Nope"のようなものに拡張され、多くの状況で使えるようになります。

### ヘルパーをリクエストする

`askForConfirmation()` Node.jsクライアントライブラリ関数を使用してヘルパーを呼び出すことができます。

```js
/*
 * @param {string=} prompt The confirmation prompt presented to the user to
 *     query for an affirmative or negative response. If undefined or null,
 *     Google will use a generic yes/no prompt.
 * @param {Object=} dialogState JSON object the app uses to hold dialog state that
 *     will be circulated back by Assistant. Used in {@link ActionsSdkApp}.
 * @return {(Object|null)} HTTP response.
 * @actionssdk
 * @dialogflow
*/
askForConfirmation (prompt, dialogState)
```

ユーザーに確認を求めるときに、カスタムプロンプトを指定することができます。関数の文字列パラメータは、ユーザーが確認を求めるためにアクションが使用するカスタム・プロンプトです。

アシスタントのデフォルトのプロンプトを使用して確認を求めるには、パラメータを指定せずに関数を呼び出します（下の `confirmationWithoutPrompt` のように）。

以下のJSONでは、Actions SDKを使用したWebhookリクエストについて説明しています。

**NODE.JS**

```js
function confirmation (app) {
  app.askForConfirmation('Will you marry me?');
}

function confirmationWithoutPrompt (app) {
  app.askForConfirmation();
}
```

**JSON**

```json
{
  "conversationToken": "{\"state\":null,\"data\":{}}",
  "expectUserResponse": true,
  "expectedInputs": [
    {
      "inputPrompt": {
        "initialPrompts": [
          {
            "textToSpeech": "PLACEHOLDER_FOR_CONFIRMATION"
          }
        ],
        "noInputPrompts": []
      },
      "possibleIntents": [
        {
          "intent": "actions.intent.CONFIRMATION",
          "inputValueData": {
            "@type": "type.googleapis.com/google.actions.v2.ConfirmationValueSpec",
            "dialogSpec": {
              "requestConfirmationText": "Will you marry me?"
            }
          }
        }
      ]
    }
  ]
}
```

### ヘルパーの結果の取得

ユーザーがヘルパーに応答すると、フルフィルメントへの要求を受け取り、ユーザーが確認したかどうかを確認できます。

**NODE.JS**

```js
function confirmationHandler (app) {
  if (app.getUserConfirmation()) {
    app.tell('Yes!');
  } else {
    app.tell('Bye!');
  }
}
```

**JSON**

```json
{
  "user": {
    "userId": "user123"
  },
  "conversation": {
    "conversationId": "1494884269122",
    "type": "ACTIVE",
    "conversationToken": "{\"state\":null,\"data\":{}}"
  },
  "inputs": [
    {
      "intent": "actions.intent.CONFIRMATION",
      "rawInputs": [
        {
          "inputType": "VOICE",
          "query": "yes"
        }
      ],
      "arguments": [
        {
          "name": "CONFIRMATION",
          "boolValue": true
        }
      ]
    }
  ],
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
  "device": {
    "locale": "en-US"
  },
  "isInSandbox": false
}
```

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/assistant/helpers)
