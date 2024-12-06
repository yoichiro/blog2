---
layout: actions_on_google
title: Conversations "Reprompts"の日本語訳です
categories:
- actions on google
---
ユーザは気まぐれです。アプリ開発者の想定通りに会話をしてくれることは、むしろ希です。何らかの割り込みが入れば、
ユーザは会話を途中で放棄してしまうことも多々起き得ます。こういった事態にも、アシスタント向けアプリは備えておかなければ
なりません。ユーザが一定時間発言をしなかった際に、アシスタント向けアプリはユーザに何度か問いかけることができます。この
問いかけのことを、再プロンプト（Reprompts）と呼びます。

Actions on Googleのドキュメントにある
[Conversations - Reprompts](https://developers.google.com/actions/assistant/reprompts)にて、
再プロンプトの方法が説明されています。以下はその日本語訳です。

---

# 再プロンプト

ユーザーがアプリに入力を提供しないケース（入力なしエラー）を処理する場合は、次の機能を使用できます。

* **システムデフォルト再プロンプト** - これらは、すべてのケースで事前に準備された一般的な再プロンプトを使用して自動的に再送信されます。
* **スタティック再プロンプト** - Googleアシスタントへの応答の一環として、再プロンプトの集合を指定します。これにより、最大3つまでの異なる再プロンプトのフレーズを指定することができ、アシスタントにあなたのための再プロンプトを処理させます。
* **ダイナミック再プロンプト** - あなた自身で再プロンプトを処理したいことを宣言し、入力がないことが発生する度にインテント（Actions SDK）
またはイベント（Dialogflow）を受信することで、あなたは状況に応じてそれらを処理できます。

## システムデフォルト再プロンプト

初期状態では、アシスタントに応答を返すとき、システムはデフォルトの再プロンプトを使用して、ユーザーに入力を繰り返すか再入力するかを尋ねます。
アシスタントは、ユーザからの入力を得るために最大2回試み、全部で3回試行します。入力を受け取っていない3回目に、アシスタントはアプリケーションを
終了します。

## スタティック再プロンプト

ユーザーに応答を返すとき、いかなる入力も検出できないときにアシスタントがユーザーに話す静的な再プロンプトの集合を提供できます。
アシスタントはこれらの再プロンプトを順番に話し、2つの再プロンプトメッセージと1回の最後の（Goodbye）メッセージを指定することができます。

たとえば、次のNode.jsクライアントライブラリコードでは、アシスタントはユーザーに"Guess a number"と尋ねます。もしいかなる入力も
検出されない場合は、提供されたプロンプトを使用して、2回までユーザに再プロンプトします。それでも入力がない場合、
"Let's play again soon"という最後のフレーズを話して、アプリを終了します。

```js
app.ask(`Guess a number`,
  ['I didn\'t hear a number', 'If you\'re still there, what\'s your guess?',
    'We can stop here. Let\'s play again soon.']);
```

> **注:** 独自のJSONを構築する場合は、
[`noInputPrompts[]`](https://developers.google.com/actions/reference/rest/Shared.Types/AppResponse#InputPrompt)
配列を使用して再プロンプトを指定してください。

## ダイナミック再プロンプト

あなたのアプリが入力を受け取らない度に、インテントまたはDialogflowのイベントを受け取ることができます。これにより、必要に応じて、
あるロジックに基づいて異なる応答を返すことができ、ユーザーを適切に再プロンプトすることができます。

### Dialogflow

2つの種類の入力なしインテントを作成することができます。

* **通常のインテント** - この方法はいかなるコンテキストも適用しないため、別の、トリガーされるより文脈的なインテントがない場合に
トリガーされます。 これは、ほとんどの場合に適用可能な一般的な再プロンプトとして役立ちます。
* **フォローアップインテント** - フォローアップインテントは、Dialogflowのコンテキストを通じて強制され、再プロンプトは会話がある程度
進んだ後にのみトリガーされます。これは、特定の状況に適用したい場合に適した再プロンプトに役立ちます。

入力なしのイベントを処理するために、以下を行います。

1. 左のナビゲーションから、 **Intents** をクリックします。
1. 通常インテントもしくはフォローアップインテントを作成します。
  * **通常インテントの場合:** **Intent** メニュー項目の側にある **+** をクリックします。<br>
    ![](https://developers.google.com/actions/images/no-input-2.png){:width="300px"}
  * **フォローアップインテントの場合:** 入力なしの再プロンプトをカスタマイズしたいインテントにカーソルを合わせ、
    **Add follow-up intent > custom** をクリックします。元のインテントの下に新しいインテントが作成されます。<br>
    ![](https://developers.google.com/actions/images/no-input-1.png){:width="600px"}
1. 新しく作成されたインテントをクリックして、インテントエディタを開きます。
1. **Events** セクションをクリックし、 **Add event** フィールドに `actions_intent_NO_INPUT` と入力します。
1. **Actions** セクションに、アクション名を入力するか、デフォルトで提供されているアクション名を使用します。
  この例では、 `no.input` を使用します。<br>
  ![](https://developers.google.com/actions/images/no-input-3.png){:width="600px"}
1. **Save** をクリックします。
1. 左側のナビゲーションで、 **Integrations** をクリックします。
1. **Google Assistant** を選択し、 **UPDATE DRAFT** をクリックしてから **TEST** をクリックして、変更がアプリに
  反映されていることを確認します。

このインテントに対する入力なしが発生する場合はいつでも、フルフィルメントを使用して、Dialogflowで適切な応答を返すか
応答を作成することができます。たとえば、クライアントライブラリを使用して入力なしインテントを処理するフルフィルメントコードを次に示します。   
   
```js
// a normal intent's action name
const NO_INPUT_EVENT = 'no.input';

...

function noInput (app) {
  if (app.getRepromptCount() === 0) {
    app.ask(`What was that?`);
  } else if (app.getRepromptCount() === 1) {
    app.ask(`Sorry I didn't catch that. Could you repeat yourself?`);
  } else if (app.isFinalReprompt()) {
    app.tell(`Okay let's try this again later.`);
  } 
}

let actionMap = new Map();

...

actionMap.set(PROVIDE_GUESS_NO_INPUT_EVENT, provideGuessNoInput);
actionMap.set(NO_INPUT_EVENT, noInput);
app.handleRequest(actionMap);
```    
    
### Actions SDK

入力なしのイベントを処理するために、以下を行います。

1. アクションパッケージ内の会話オブジェクトで、ユーザーが入力を提供しないときはいつでも `actions.intent.NO_INPUT` インテントを
受け取ることを宣言します。
  ```json
"conversations": {
    "conversationName": {
      "name": "conversationName",
      "url": "https://my.fulfillment.com/conversation",
      "in_dialog_intents": [
        {
          "name": "actions.intent.NO_INPUT"
        }
      ]
    }
}
  ```
  > **注:** これは、ユーザーとの会話全体に適用されるグローバル設定です。従って、システムのデフォルトまたはスタティックな再プロンプトを動的な再プロンプトと共に使用することはできません。
1. アシスタントがユーザーからのいかなる入力も受け取らなかったとき、次のリクエストであなたのフルフィルメントは入力なしインテントを
受け取ります。その後、インテントを処理し、適切な再プロンプトの応答を返すことができます。ここに例があります。
 {% highlight js %}function noInput (app) {
  if (app.getRepromptCount() === 0) {
    app.ask(`What was that?`);
  } else if (app.getRepromptCount() === 1) {
    app.ask(`Sorry I didn't catch that. Could you repeat yourself?`);
  } else if (app.isFinalReprompt()) {
    app.tell(`Okay let's try this again later.`);
  } 
}

let actionMap = new Map();

...

actionMap.set(app.StandardIntents.NO_INPUT, noInput);
app.handleRequest(actionMap);{% endhighlight %}
  
> **注:** 適切な再プロンプトを返すのを手助けするために、最後の応答や良い再プロンプトを作成するために必要なその他のデータを覚えておくことで、
会話の状態を追跡すると便利です。その後、インテントを処理する関数で、このデータをチェックして適切な再プロンプトを返します。

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/assistant/reprompts)
