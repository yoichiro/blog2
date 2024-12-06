---
layout: actions_on_google
title: Conversations "Best Practices"の日本語訳です
categories:
- actions on google
---
Googleアシスタント向けアプリによってユーザに提供される会話のUIは、比較的歴史が浅く、まだまだ新しい分野です。今後使いやすい会話型UIとは何かが研究され、そして多くの開発者に広まっていくことでしょう。ただ、現時点でも、明らかに気をつけておくべきことはいくつかわかっています。

Actions on Googleのドキュメントにある
[Conversations - Best Practices](https://developers.google.com/actions/assistant/best-practices)にて、
会話型UIにおけるベストプラクティスが説明されています。以下はその日本語訳です。

---

# 一般的なベストプラクティス

素晴らしい会話デザインを確実にするために、これらのベストプラクティスに従ってください。

## バリエーションを期待する

Dialogflowの "User says" 入力でこれを処理します。また、同じアクションにマップできる複数のインテントを使用してください。それぞれのインテントは、異なる "User says" のフレーズでトリガーできます。

## 役立つ再プロンプトを提供し、正常に失敗する

これを行うには、 `app.data` オブジェクトの `fallbackCount` 変数を初期化し、0に設定します。3つのフォールバックプロンプト（明確にエスカレートする）の配列と、会話を終了させる最後のフォールバックプロンプトを準備します。

次に、フォールバックインテントを作成します（理想的には、エージェントにて実行可能なインテントごとに1つ）。インテントハンドラで、app.data オブジェクトからフォールバックカウントを引き出し、インクリメントし、3より小さい場合は3つの配列からプロンプトを引き出します。カウントが4より大きい場合は、最後のプロンプトを使って会話を閉じます。フォールバックではないすべてのインテントで、フォールバックカウントを 0 にリセットします。理想的には、特定のインテントがそれらに固有のフォールバックをテンプレート化します。

> **注:** ユーザがDialogflowでスロット充填している場合は、インテントで "webhook for slot-filling" を有効にし、入ってきたリクエストの "action_incomplete" フラグをチェックして、スロットパラメータのエスカレートする再プロンプトを適切に処理する必要があります。Node.jsクライアントライブラリはこれをまだ処理していません。

```js
const GENERAL_FALLBACK: [
    "Sorry, what was that?",
    "I didn't quite get that. I can tell you all about IO, like date or location, or about the sessions. What do you want to know about?",
    "I'm really sorry, I can't understand. Would you like to know where IO is or maybe hear about some sessions?"
  ];
const LIST_FALLBACK: [
    "Sorry, what was that?",
    "I didn't catch that. Could you tell me which one you liked?",
    "I'm having trouble understanding you. Which one of those did you like?"
  ];
const FINAL_FALLBACK: "I'm sorry I'm having trouble here. Maybe we should try this again later.";

function handleFallback (app, promptFetch, callback) {
  app.data.fallbackCount = parseInt(app.data.fallbackCount, 10);
  app.data.fallbackCount++;
  if (app.data.fallbackCount > 3) {
    app.tell(promptFetch.getFinalFallbackPrompt());
  } else {
    callback();
  }
}

// Intent handlers

function generalFallback (app) {
  handleFallback(app, promptFetch, () => {
    app.ask(GENERAL_FALLBACK[app.data.fallbackCount],
      getGeneralNoInputPrompts());
  });
}

function listFallback (app) {
  handleFallback(app, promptFetch, () => {
    app.ask(LIST_FALLBACK[app.data.fallbackCount],
      getGeneralNoInputPrompts());
  });
}

function nonFallback (app) {
  app.data.fallbackCount = 0;
  app.ask(...);
}
```

## いつでも助ける準備がされている

"what can I do?", "what can you tell me", "help" のようなヘルプフレーズを聞くインテントを作成します。このインテントのために、エージェントが何を行うことができるかの概要を提供し、ユーザに可能なアクションを指示する、いくつかの（ローテートする）応答を提供します。理想的には、Dialogflowのフォローアップヘルプインテントを使用して、さまざまな実行可能なインテントに対するいくつかのヘルプシナリオを作成することもできます。

```js
CONST HELP_PROMPTS: [
    "There's a lot you might want to know about IO, and I can tell you all about it, like where it is and what the sessions are. What do you want to know?",
    "IO can be a little overwhelming, so I'm here to help. Let me know if you need any help figuring out the event, like when it is, or what the sessions are. What do you want to know?"
  ];

// Intent handler

function help (app) {
    ask(app, promptFetch.getHelpPrompt(), // fetches random entry from HELP_PROMPTS
      promptFetch.getGeneralNoInputPrompts());
}
```

## ユーザーが情報をリプレイできるようにする

すべての `app.ask(output)` メソッドを `app.data.lastPrompt` に出力を追加するプロキシ関数でラップします。"what?", "can you repeat that?", "say that again" といったユーザーから繰り返されるプロンプトを聞くリピートインテントを作成します。繰り返しのプレフィックスの配列を作成します。これは、ユーザーが繰り返し何かを要求したことを確認するために使用できます。リピートインテントハンドラで、繰り返しのプレフィックスと `app.data.lastPrompt` の値を連結した文字列で `ask()` を呼び出します。最後のプロンプトで使用する場合は、ssmlの開始タグをシフトする必要があることに注意してください。

```js
const REPEAT_PREFIX: [
    "Sorry, I said ",
    "Let me repeat that. "
  ];

function ask (app, inputPrompt, noInputPrompts) {
  app.data.lastPrompt = inputPrompt;
  app.data.lastNoInputPrompts = noInputPrompts;
  app.ask(inputPrompt, noInputPrompts);
}

// Intent handlers

function normalIntent (app) {
  ask(app, "Hey this is a question", SOME_NO_INPUT_PROMPTS);
}

function repeat (app) {
    let repeatPrefix = promptFetch.getRepeatPrefix(); // randomly chooses from REPEAT_PREFIX
    // Move SSML start tags over
    if (app.data.lastPrompt.startsWith(promptFetch.getSSMLPrefix())) {
      app.data.lastPrompt =
        app.data.lastPrompt.slice(promptFetch.getSSMLPrefix().length);
      repeatPrefix = promptFetch.getSSMLPrefix() + repeatPrefix;
    }
    app.ask(repeatPrefix + app.data.lastPrompt,
      app.data.lastNoInputPrompts);
}
```

## ユーザーの好みで会話をパーソナライズする

あなたのアプリは、ユーザーに設定を依頼して後で使用できるように覚えておくことで、そのユーザーとの今後の会話をパーソナライズすることができます。

このサンプルアプリは、ユーザーに郵便番号の天気予報を提供します。次のコード例は、後の会話のために郵便番号を覚えておくかどうかをユーザーに尋ねます。

```js
appMap.set("weather_report", function(app) {
   let zip = app.getArgument("zipcode");
   app.data.zip = zip;
   app.askForConfirmation(app.buildRichResponse()
      .addSimpleResponse(getWeatherReport(zip))
      .addSimpleResponse("Should I remember " + zip + " for next time?"));
});

appMap.set("remember_zip", function(app) {
   if (app.getUserConfirmation()) {
      app.userStorage.zip = app.data.zip;
      app.tell("Great! See you next time.");
   } else app.tell("Ok, no problem.");
})
```

ユーザーに最初のダイアログで郵便番号を尋ねた後、次の呼び出し時にそのプロンプトをスキップして、同じ郵便番号を使用することができます。エスケープルート（たとえば、別の郵便番号を選択できる候補チップ）を提供する必要がありますが、一般的なケースでは会話の回数を減らすことで、よりシームレスな体験を作ることができます。

```js
appMap.set("weather_report", function(app) {
   let zip = app.getArgument("zipcode");
   if (zip) app.tell(getWeatherReport(zip))
   else if (app.userStorage.zip) 
       app.ask(app.buildRichResponse()
             .addSimpleResponse(getWeatherReport(app.userStorage.zip))
             .addSuggestions("Try another zipcode"));
   else app.ask("What's your zip code?")
})

appMap.set("provide_zip", function(app) {
   app.userStorage.zip = app.getArgument("zipcode");
   app.tell(getWeatherReport(app.userStorage.zip));
})
```

## ユーザーが復帰したときにユーザを異なる迎え方をする

会話の間でいくつかの状態を維持することは、リターンユーザにとってより自然な体験を保証します。この経験を生み出すための第一歩は、リターンユーザーに異なる挨拶をすることです。たとえば、過去の会話に基づいて、挨拶を減らしたり、有用な情報を表示することができます。これを行うには、入ってきた [AppRequest.User](https://developers.google.com/actions/reference/rest/Shared.Types/AppRequest#User) `lastSeen` プロパティを使用して、以前にユーザーがあなたのアプリとやりとりしたかどうかを判断します。 `lastSeen` プロパティがリクエストペイロードに含まれている場合、通常とは異なる挨拶を使用できます。以下のコードは、Node.jsクライアントライブラリを使用して `lastSeen` の値を取得します。

```js
// This function is used to handle the welcome intent
// In Dialogflow, the Default Welcome Intent ('input.welcome' action)
// In Actions SDK, the 'actions.intent.MAIN' intent
function welcome (app) {
  if (app.getLastSeen()) {
    app.ask(`Hey you're back...`);
  } else {
    app.ask('Welcome to Number Genie!...');
  }
}
```

`lastSeen` の実際の値に対するレスポンスを調整することによって、この挨拶をさらに高めることができます。たとえば、現在の対話の数ヶ月前に最後のインタラクションが発生したユーザーは、前日にアプリを使用したユーザーとは異なる挨拶を受け取るようにすることができるでしょう。

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/assistant/best-practices)
