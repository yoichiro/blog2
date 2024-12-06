---
layout: actions_on_google
title: Conversations "App Exits"の日本語訳です
categories:
- actions on google
---
VUI(Voice User Interface)において、ユーザがアプリをいつでも終わることができる、ということが結構重要だったりします。
会話を終えるためのことをユーザが明確に言うこともあるでしょうし、いつの間にかユーザが何も言わなくなっていることもあるでしょう。
少なくとも、ユーザが明確に会話を終了させようとしてきた時には、それをフルフィルメントにて受け取り、適切な後処理を行うことができます。

Actions on Googleのドキュメントにある
[Conversations - App Exits](https://developers.google.com/actions/assistant/app-exits)にて、
ユーザがアプリの終わりを宣言した際の処理の仕方が説明されています。以下はその日本語訳です。

---

# アプリの終了

ユーザーは会話の途中でアプリを終了したいときはいつでも次のようなことを言うことができます。

* _"exit"_
* _"cancel"_
* _"stop"_
* _"nevermind"_
* _"goodbye"_

デフォルトでは、アシスタントはあなたのアプリを終了し、イヤコン（使用者に対するコンピュータからの音声信号）を再生します。この動作を書き換えるには、一つ前のリクエストを受け取り、フルフィルメントロジックをクリーンアップして、ユーザーに最後の応答をすることができます。最終的な応答は、 `textToSpeech` と `displayText` の値として60文字の制限がある一つの[シンプルレスポンス](https://developers.google.com/actions/assistant/responses#simple_responses)でなければなりません。

アプリの終了リクエストに許可される最大実行時間は、2秒です。応答が受信されない場合、アプリはデフォルトの終了イヤコンで終了します。アプリのカスタムキャンセル応答が失敗した場合（文字数制限や不適切なレスポンスタイプなどにより）、アプリはやはりデフォルトの終了イヤコンで終了します。

## Dialogflow

アプリ終了イベントを受け取るために以下を行います。

1. 左のナビゲーションにて、 **Intents** メニュー項目の **+** アイコンをクリックします。
1. インテント名を入力し、 **Events** セクションをクリックします。
1. **Add event** フィールドに `actions_intent_CANCEL` を入力します。
1. **Actions** セクションにて、アクション名を入力します。この例では、 `say.bye` と入力します。
1. Dialogflowの **Response** セクションでレスポンスを指定するか、フルフィルメントコードでレスポンスを返します。
1. **Save** をクリックします。
1. 左のナビゲーションにて、  **Integrations** をクリックします。
1. **Google Assistant** を選択して、 **UPDATE DRAFT** をクリックします。その後、あなたのアプリに変更が反映されたかどうかを確認するために、 `TEST` をクリックします。

ユーザーがアプリの終了をリクエストすると、作成したインテントがトリガーされ、レスポンスがユーザーに返されます。たとえば、クライアントライブラリを使用してキャンセルインテントを処理し、レスポンスを返すフルフィルメントコードを次に示します。

```js
const CANCEL_EVENT = 'say.bye'; //name of the action

...

function sayBye (app) {
    app.tell(`Okay, let's try this again later.`);
}

let actionMap = new Map();

...

actionMap.set(CANCEL_EVENT, sayBye);
app.handleRequest(actionMap);
```

## Actions SDK

アプリ終了インテントを受け取るために以下を行います。

1. アクションパッケージ内の会話オブジェクトで、ユーザーが会話中のアプリを終了したいときにいつでも `actions.intent.CANCEL` インテントを受け取るように宣言します。<br>
    ```json
    "conversations": {
      "myConversation": {
        "name": "myConversation",
        "url": "https://my.fullfillment.com/conversation",
        "inDialogIntents": [
          {
            "name": "actions.intent.CANCEL"
          }
        ]
      }
    }
    ```
1. `actions.intent.CANCEL` インテントでリクエストを受け取ったら、必要なフルフィルメントロジックをクリーンアップし、適切な終了フレーズをユーザーに返します。たとえば、キャンセルのインテントを処理するためにクライアントライブラリを使用するフルフィルメントコードを次に示します。<br>
    ```js
    function sayBye (app) {
        app.tell(`Okay, let's try this again later.`);
    }
    
    let actionMap = new Map();
    
    ...
    
    actionMap.set(app.StandardIntents.CANCEL, sayBye);
    app.handleRequest(actionMap);
    ```

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/assistant/app-exits)
