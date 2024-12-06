---
layout: actions_on_google
title: Build with Dialogflow "Define Actions"の日本語訳です
categories:
- actions on google
---
Dialogflowを使ったGoogleアシスタント向けのアプリ開発において、[Create a Project and Dialogflow Agent](https://www.eisbahn.jp/yoichiro/2017/11/actions_on_google_build_dialogflow_3.html)にてプロジェクトを作成できたら、次はアクションを定義していきます。アクションの作成方法は、Actions on Googleの[Define Actions](https://developers.google.com/actions/dialogflow/define-actions)に書かれています。以下は、その日本語訳です。

---

# アクションを定義する

Dialogflowでアクションを定義するには、Dialogflowエージェントでインテントを作成し、エントリポイントとしてアプリに割り当てます。次のタイプのアクションを作成できます。

* **デフォルトのアクション:** すべてのDialogflowエージェントには、ユーザーがあなたのアプリを名前で尋ねたときに呼び出される唯一のアクションが1つ必要です（例えば、"_OK, Google. Talk to Facts about Google_" または "_Ok, Google. Let me talk to Facts about Google_"）
* **アプリに深くリンクするための追加アクション:** ユーザーが名前でアプリを呼び出してアクションを指定すると、これらのアクションが作動されます（例えば、"talk to Facts about Google for history facts" など）。

これらの呼び出しモデルの仕組みの詳細については、[Invocation and Discovery](https://developers.google.com/actions/discovery/)を参照してください。

# デフォルトアクションの定義

すべてのDialogflowエージェントは、 **GOOGLE_ASSISTANT_WELCOME** イベントを処理する唯一のインテントを持つ必要があります。このインテントは、ユーザーがアプリ名で呼び出すと起動されます（例えば、"Ok Google, talk to Facts about Google"）。

> **Dialogflow Tip**
>
> Dialogflowは多くの製品をサポートするため、アシスタントアプリの **GOOGLE_ASSISTANT_WELCOME** など、特定のプラットフォーム向けのウェルカムイベントトリガーがあります。一般的な **WELCOME** イベントを使用すると、多くのプラットフォームにデプロイする場合でも、多くのプラットフォーム間で呼び出しを処理できます。

以下の方法で、 **GOOGLE_ASSISTANT_WELCOME** イベントを定義することができます。

**インテントエディタから:**

1. 左のナビゲーションの **Intents** をクリックします。
1. ウェルカムイベントを参照したいインテントを選択します。
1. インテントエディアの **Events** セクション内で、 `GOOGLE_ASSISTANT_WELCOME** と入力します。

**Actions on Google統合画面から:**

1. 左のナビゲーションの **Integrations** をクリックし、その後 **Actions on Google** カードをクリックします。Actions on Googleビューが表示されます。
1. **Welcome Intent** フィールド内で、ウェルカムイベントをセットしたいインテントを選択します。インテントエディア内でインテントが表示された時、Dialogflowが **GOOGLE_ASSISTANT_WELCOME** イベントを自動的にセットしているのを見るはずです。

あなたは、インテントのためにフルフィルメントを有効にしたいと思う可能性が最も高いでしょう。インテントが作動されると、フルフィルメントはインテントを処理し、応答を返します。デフォルトでは、Dialogflowはフルフィルメントを呼び出さず、静的な応答を返すことができます。これは、単純なアクションや、呼び出されたときに常に同じメッセージで応答するアクションのためには十分でしょう。レスポンスを生成するためのフルフィルメントが必要ない場合は、インテントの **Response** 領域で静的レスポンスを指定できます。

**ウェルカムインテントのフルフィルメントを有効にするには:**

1. ウェルカムインテントのインテントエディタの一番下までスクロールします。
1. **Fulfillment** をクリックし、 **Use webhook** を選択します。

このインテントが作動されたときの処理方法については、[Build Fulfillment](https://developers.google.com/actions/dialogflow/fulfillment)を参照してください。

## 追加のアクションを定義する

アプリにエントリポイントとして機能する追加のアクションを提供することができます。これにより、ユーザーは、自分が何をしたいのかについての詳細を指定できるようになり（例えば、"_talk to Facts about Google to find me history facts_" など）、インテントの違いを明らかにすることができます。

Dialogflowにて追加のアクションを定義するには:

1. 作成したいアクションごとに、インテントを作成します。
1. **User says** フィールドでアクションを作動させるパターンを宣言します。例えば、ユーザーが次のアクション呼び出しを行うことができるようにする場合は、次のようにします。
 * 呼び出し1: "_talk to Facts about Google to find me history facts_"

   ユーザーは次のようなフレーズを使用してインテントを宣言します。<br>
   "find me history facts", "look up history facts", "get me facts about history of the company", "read me some headquarters facts", "tell me a few facts about its headquarters"<br>
   アクションを定義するそれぞれのインテントの **User says** フレーズは、約10個を推奨します。
1. ウェルカムインテントのフルフィルメントを有効にするには、ウェルカムインテントのインテントエディタの一番下までスクロールし、 **Fulfillment** をクリックしてから、 **Use webhook** を選択します。<br>
このインテントが作動されたときの処理方法については、[Build Fulfillment](https://developers.google.com/actions/dialogflow/fulfillment)を参照してください。
1. アクション呼び出しに使用したいインテントを得た際には、Dialogflowで **Additional triggeringintents** として設定する必要があります。
   1. 左側のナビゲーションで **Integrations** をクリックし、**Actions on Google** カードをクリックします。Actions on Googleビューが表示されます。
   1. **Additional triggering intents** 領域に、作成した全てのディープリンク呼び出しインテントを追加します。

アクションを定義するときは、次の制限とガイドラインが適用されます。

* 名前の呼び出しやアクションの呼び出し、"trigger phrase"から"reserved（予約された）"言葉を指定しないでください。例えば、"talk to <app name>", "talk to", "let me talk me to" などは使用しないでください。
* `@sys.any` エンティティのみを含む **Users says** フレーズを指定しないでください。 `@sys.any` のレシピを見つけるなど、もっと具体的にすべきです。

## ユーザーがあなたのアプリを起動したときに認識されないアクションを処理する

アプリが認識できないアクションをユーザが指定すると、Dialogflowはこれらのケースを処理する特定のフォールバックインテントを作動させることができます。この動作を有効にするには、入力コンテキストを `google_assistant_welcome` に設定して、ユーザーに適切な応答を指定するフォールバックインテントを作成します。

1. Dialogflowの左側のナビゲーションで、 **Intents** をクリックします。
Click the menu item by the Create Intent button and select Create Fallback Intent.
1. **Create Intent** ボタンでメニュー項目をクリックし、 **Create Fallback Intent** を選択します。
1. **Contexts** セクションで、 `google_assistant_welcome` を入力コンテキストとして指定します。コンテキストが正しく設定されるように、Enterキーを押してください。
1. **Response** セクションで、認識できないアクションに適した応答を指定します。
1. このインテントを処理するためにフルフィルメントを使用する場合は、次のようにします。
   1. **Action** セクションで、アクション名を指定します。
   1. **Fulfillment** セクションのヘッダーをクリックし、 **Use webhook** を選択します。
When Dialogflow triggers this intent, you can call the getRawInput() method of the client library to obtain the unrecognized action phrase. You can then return responses in your fulfillment logic based on the unrecognized action phrase or other logic. See Build Responses for more information on how to handle intents in your fulfillment.
1. Dialogflowがこのインテントを作動させると、クライアントライブラリの `getRawInput()` メソッドを呼び出して、認識できないアクションフレーズを取得できます。認識できないアクションフレーズやその他のロジックに基づいて、フルフィルメントのロジックで応答を返すことができます。あなたのフルフィルメントにおけるインテントを処理する方法の詳細については、[Build Responses](https://developers.google.com/actions/dialogflow/fulfillment#building_fulfillment_responses)を参照してください。

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/dialogflow/define-actions)
