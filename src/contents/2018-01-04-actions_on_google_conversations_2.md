---
layout: actions_on_google
title: Conversations "Surface Capabilities"の日本語訳です
categories:
- actions on google
---
Googleアシスタントは、「音声のみ」「画面のみ」「音声と画面の両方」のいずれかをサポートしたデバイス上で動作することが想定されています。
これらの種別のことを「サーフェス」と呼んでいて、サーフェスが違えば、アシスタント向けアプリが応答可能なメッセージも変わってきます。

Actions on Googleのドキュメントにある
[Conversations - Surface Capabilities](https://developers.google.com/actions/assistant/surface-capabilities)にて、
サーフェスの利用方法が説明されています。以下はその日本語訳です。

---

# サーフェス機能

あなたのアプリは、オーディオやディスプレイの体験をサポートするモバイルデバイスや、オーディオのみの体験をサポートするGoogle Home
デバイスなど、さまざまなサーフェスで登場できます。

すべてのサーフェスでうまく動作する会話を適切に設計し構築するために、サーフェス機能を使用して会話を適切に制御し、範囲を設定します。

## アプリサーフェス機能

アプリサーフェスの機能を使用すると、ユーザが使用しているサーフェスに基づいて、ユーザーがアプリを呼び出すことができるかどうかを制御できます。
サポートされていないサーフェスでユーザがアプリを呼び出そうとすると、ユーザはサポートされていないデバイスを示すエラーメッセージを受け取ります。

Actions on Google開発者プロジェクトにて、アプリのサーフェスサポートを定義します。

![](https://developers.google.com/actions/images/app-surfaces.png){:width="800px"}

あなたのアクションは、携帯端末（AndroidやiOS）やGoogle Homeなど、アシスタントがサポートするさまざまなサーフェスで登場できます。

## ランタイムサーフェス機能

主に2つの方法で、あなたはランタイムサーフェス機能を備えたユーザー体験をまかなうことができます。

* **レスポンス分岐** - ユーザーに異なる応答を提供しますが、異なるサーフェス間で会話の構造とフローが同じです。たとえば、天気アプリでは、
携帯端末で画像付きのカードを表示し、Google Homeで音声ファイルを再生することがありますが、会話の流れはサーフェス全体で同じです。
* **会話の分岐** - 各サーフェスで完全に異なる会話をユーザに提供します。たとえば、食品注文アプリを構築する場合は、Google Homeで再注文の
フローを提供することができますが、携帯電話ではカート全体の組み立てフローが必要になる場合があります。会話の分岐を行うには、Dialogflowの
コンテキストを使って、特定のサーフェス機能にDialogflowのインテントトリガーを適用します。特定のサーフェス機能が満たされない限り、実際の
Dialogflowのインテントはトリガされません。
  > **注:** ユーザのデバイスのサーフェス機能は、会話の途中で変更されるかもしれません。会話が始まった際には、一定の機能セットに依存しないで
    ください。
* **複数のサーフェスでの会話** - 1つのサーフェス上にある会話から、別のサーフェスへの途中の会話の移行をユーザに提供します。たとえば、
ユーザーがGoogle Homeのようなオーディオ専用のサーフェスで画像を持つアプリを呼び出す場合、視覚機能を備えた別のサーフェスを探すことを
アプリに構築し、可能であれば会話を移動することができます。

## 応答分岐

あなたのフルフィルメントがGoogleアシスタントからリクエストを受信する度に、現在のサーフェス（GoogleホームやAndroid搭載端末など）機能を
問い合わせることができます。

**NODE.JS**

クライアントライブラリは、インテントがトリガーされた後に機能をチェックする `hasSurfaceCapability` 関数を提供します。

```js
let hasScreen =
    app.hasSurfaceCapability(app.SurfaceCapabilities.SCREEN_OUTPUT)
let hasAudio =
    app.hasSurfaceCapability(app.SurfaceCapabilities.AUDIO_OUTPUT)
```

**JSON**

レスポンスの分岐を行うには、リクエストで受け取った
[`surface.capabilities`](https://developers.google.com/actions/reference/rest/Shared.Types/AppRequest#surface)
フィールドをチェックし、適切なレスポンスを提供します。

```json
"surface": {
    "capabilities": [
        {
            "name": "actions.capability.AUDIO_OUTPUT"
        },
        {
            "name": "actions.capability.SCREEN_OUTPUT"
        }
    ]
}
```

## 会話の分岐

Dialogflowのインテントは、事前に定義されたDialogflowのコンテキストを使用して、特定の機能だけをトリガーするように設定できます。
インテントが一致するたびに、Dialogflowはデバイスが使用可能な一連のサーフェス機能からコンテキストを自動的に生成します。
これらのコンテキストの1つ以上を、インテントの「input contexts」として指定できます。これにより、様式に基づいてインテントトリガーを
ゲートでコントロールすることができます。

具体的には、画面出力を持つデバイスでトリガするインテントのみを必要とする場合は、インテントの入力コンテキストを
`actions_capability_screen_output` に設定できます。以下のコンテキストが利用できます。

* `actions_capability_audio_output` - デバイスにスピーカーがあります。
* `actions_capability_screen_output` - デバイスに出力表示画面があります。

画面付きのサーフェスでのみトリガーされるインテントの例を次に示します。

![](https://developers.google.com/actions/images/capabilities.png){:width="800px"}

## マルチサーフェスの会話

アプリのフローのどの時点でも、ユーザーが特定の機能を持つ他のサーフェスを持っているかどうかを確認できます。要求された機能を持つ
別のサーフェスが利用可能な場合は、現在の会話をその新しいサーフェスに転送できます。

> 現時点では、転送先のサーフェスとして、Android携帯電話が持つ画面機能に基づいた会話の転送のみが可能です。
askForNewSurfaceヘルパーは現在、 [en-* locales](https://developers.google.com/actions/localization/languages-locales) 
でのみ使用できます。

サーフェス移動のフローは次のように動作します。

1. **ユーザーが利用可能なサーフェスを持つかどうかを確認する** <br>
  Webhookハンドラでは、ユーザが特定の機能を持つサーフェスを利用できるかどうかを問い合わせることができます。このサーフェスは、元の
  サーフェスと同じGoogleアカウントに関連付けられている必要があります。<br>
  **NODE.JS** <br>
  `hasAvailableSurfaceCapabilities` 関数は、要求されたすべての機能を持つサーフェスをユーザが持っている場合に `true` を返します。<br>
  ```js
  const screenAvailable = app.hasAvailableSurfaceCapabilities(app.SurfaceCapabilities.SCREEN_OUTPUT);
  ```
  **JSON** <br>
  必要とされる機能のために、 `availableSurfaces` フィールドをチェックします。<br>
  `hasAvailableSurfaceCapabilities` 関数は、要求されたすべての機能を持つサーフェスをユーザが持っている場合に `true` を返します。<br>
  ```json
{
      "surface": {
        "capabilities":
           ["actions.capability.AUDIO_OUTPUT"] // current surface is eyes-free
      },
      "availableSurfaces": [{ // user has a surface with a screen, eg. a phone
        "capabilities": [
          { "name": "actions.capability.SCREEN_OUTPUT"},
          {"name": "actions.capability.AUDIO_OUTPUT"}
        ]
      }],
      ...
}
  ```
1. **ユーザーを新しいサーフェスに転送することを要求する**<br>
  必要な機能を備えた利用可能なサーフェスがある場合、アプリは会話を転送するかどうかをユーザーに尋ねる必要があります。
  **NODE.JS** <br>
  もし `hasAvailableSurfaceCapabilities` が `true` を返した場合は、転送理由（コンテキストとして）、新しいサーフェス上で表示される通知、および要求された機能を渡して、 `askForNewSurface` 関数を呼び出します。<br>
  ```js
  let context = 'Sure, I have some sample images for you.';
  let notif = 'Sample Images';
  if (screenAvailable) {
    app.askForNewSurface(context, notif, [app.SurfaceCapabilities.SCREEN_OUTPUT]);
  } else {
    app.tell("Sorry, you need a screen to see pictures");
  };
  ```
  **JSON** <br>
  `NEW_SURFACE` インテントを呼び出すために、Webhookレスポンスは以下のようになるでしょう。<br>
  ```json
{
  "conversationToken": "null",
  "expectUserResponse": true,
  "expectedInputs": [
        {
          "inputPrompt": {
            "richInitialPrompt": {
              "items": [
                {
                  "simpleResponse": {
                    "textToSpeech": "PLACEHOLDER_FOR_NEW_SURFACE"
                  }
                }
              ]
            }
          },
          "possibleIntents": [
            {
              "intent": "actions.intent.NEW_SURFACE",
                "inputValueData": {
                  "@type": "type.googleapis.com/google.actions.v2.NewSurfaceValueSpec",
                  "context": "Sure, I have some sample images for you.",
                  "notificationTitle": "Sample Images",
                  "capabilities": [
                    "actions.capability.SCREEN_OUTPUT"
                  ]
                }
            }
          ]
        }
  ]
}
  ```
  > **注:** `conversationToken` は新しいサーフェスに転送されるため、必要な状態を維持する必要があります。Dialogflowの
  出力コンテキスト内に格納されたデータも自動的に新しいサーフェスに転送され、会話IDは変わりません。 `app.data` も同様に維持されます。<br>
  > **警告:** ユーザーの使用可能なサーフェスに存在しない機能を要求すると、実行時にエラーが表示されます。
1. **ユーザーの応答を処理する** <br>
  あなたのリクエストに対するユーザーの応答に基づいて、アプリは引き継ぎを促進したり、会話を元のサーフェスに戻したりすることができます。
  いずれにしても、エンドポイントへの次の要求には `actions.intent.NEW_SURFACE` インテントが含まれているので、ああなたのWebhook内の
  対応するハンドラで、そのイベントをトリガーするインテントをビルドする必要があります。ハンドラコードでは、転送が成功したかどうかを
  チェックする必要があります。<br>
  **NODE.JS** <br>
  ユーザが移動する要求を受け入れて、現在新しいサーフェスにいるかどうかを判断するために、 `isNewSurface` メソッドを使います。<br>
  ```js
  const actionMap = new Map();
  actionMap.set('new_surface_intent',  function (app) {
    if (app.isNewSurface()) {
      showPicture(app, pictureType);
    } else {
      app.tell('Ok, I understand. You don\'t want to see pictures. Bye');
    }
  });
  app.handleRequest(actionMap);
  ```
  **JSON** <br>
  サーフェスの転送が成功したかどうかを判断するために、 `NEW_SURFACE` ステータスの値を見てください。<br>
  ```json
{
  "user": {
      "userId": "1234",
      "locale": "en-US"
  },
  "conversation": {
      "conversationId": "1234",
      "type": "ACTIVE",
      "conversationToken": ""
  },
  "inputs": [
      {
        "intent": "actions.intent.NEW_SURFACE",
        "rawInputs": [
          {
            "inputType": "VOICE",
            "query": "[request notification]"
          }
        ],
        "arguments": [
          {
            "name": "NEW_SURFACE",
            "extension": {
              "@type": "type.googleapis.com/google.actions.v2.NewSurfaceValue",
              "status": "OK"
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
  }
}
  ```

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/assistant/surface-capabilities)
