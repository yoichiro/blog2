---
layout: actions_on_google
title: Build with Dialogflow "Number Genie"の日本語訳です
categories:
- actions on google
---
Dialogflowを使ったGoogleアシスタント向けのアプリ開発の手順について、総決算として「Number Genie」という名前の数当てゲームの作り方が、Actions on Googleの[Build with Dialogflow "Number Genie"](https://developers.google.com/actions/dialogflow/tutorials/number-genie)に掲載されています。以下は、その日本語訳です。

ここに書かれている内容が、ほとんどのアプリで必要となる実装要求となるはずです。Number Genieの仕組みと実装内容を把握することが、あなたがGoogleアシスタント向けのアプリ開発に着手する際の近道となります。

---

# Number Genie

> サンプルコードは[ここから](https://github.com/actions-on-google/dialogflow-number-genie-nodejs)得ることができます。

このサンプルでは、Dialogflowを使用して会話型アクションの乱数ゲームを開発することを目標にしています。

## 要件

乱数ゲームには次の要件があります。

* ユーザーは、アシスタントで音声コマンドを使用してゲームを開始できます。
* アクションは、呼び出し名として、 _Number Genie_ を使用します。
* ユーザーは、ゲームに勝つために、数字を推測する必要があります。
* 数字は、1〜100の範囲内で、アクションによってランダムに選択されます。
* ユーザーは、無制限に試して番号を推測することができます。
* ユーザーからの推測ごとに、その数が推測値よりも小さいか大きいか、アクションがユーザーを誘導します。

## 設計

ゲームの要件を使って、VUIの設計原則に従って、ゲームのための、[さまざまな典型的な会話を記載](https://developers.google.com/actions/design/walkthrough)します。

これらの各ユーザーフローに基づいて、設計は次の点に注意してください。

* ユーザーは、トリガーフレーズ _talk to number genie_ を使用してゲームを作動させます。これは、会話型のアクションを呼び出すためにサポートされているトリガーフレーズの1つです。
* アシスタントは、 _Sure, Here's Number Genie_ と言うことで、アクションを紹介します。次に、コンピュータが作動していることを示す電子音を再生して、会話アクションへの移行を示します。
* 会話アクションの声は、Googleアシスタントの声と同じではありません。会話アクションの音声は、設定可能であり、適切な人物のリストから音声を選択できます。
* ウェルカムイントロダクションを使って名前を確認することによって、アクションを紹介することが重要です: _Welcome to Number Genie!_ （紙の上では、アクション名の確認をユーザに重複させているように見えるかもしれませんが、実際の音声会話を聞くことによって、会話がアクションのペルソナに移動したことをユーザが知ることが重要であることが明確になるでしょう。）
* ウェルカムメッセージの直後に、ユーザはゲームに関するプロンプトと次のステップとなるものが与えられます。
* ユーザーが正しい数を推測した時に、別のラウンドをプレイしたくないときには、ゲームが終了します。

## 会話アクションの実装

Dialogflowでプロジェクトを作成し、"NumberGenie"という名前を付けます。プロジェクトのフルフィルメントを有効にします。

2つのインテントを作成します。

* **start_game** は、ユーザーフレーズ _number genie_ と _number genie game_ が含まれています。 **generate_answer** アクションを設定して、フルフィルメントを有効にします。
* **provide_guess** は、ユーザフレーズ _25_ と _is it 5_ と _how about 1_ が含まれています。 **check_guess** アクションを設定して、フルフィルメントを有効にしてください。Dialogflowは、 **check_guess** に名前を付けて必須にすることができるパラメータを自動的に追加するはずです。guessパラメータに次のプロンプトを設定します。
 * Sorry, say that again?（すみません、もう一度言ってください。）
 * Sorry, that number again?（すみません、数をもう一度言ってください。）
 * What was that number?（数は何でしたか？）
 * Sorry what was that?（すみません、それは何でしたか？）
 * I didn't hear a number. What's your guess?（数を聞くことができませんでした。あなたの推測は何ですか？）
 * Say that again.（もう一度言ってください。）

### Webhookの作成

プロンプトに従って、ゲームの背後にあるロジックを実装するために、Webhookを有効にします。

package.jsonというファイルを作成し、次のコードをファイルに貼り付けます。

```json
{
  "name": "number-genie",
  "description": "Google Actions Number Genie sample for Node.js",
  "version": "0.0.1",
  "private": true,
  "license": "Apache-2.0",
  "author": "Google Inc.",
  "scripts": {
    "start": "node app.js",
    "monitor": "nodemon app.js",
    "deploy": "gcloud app deploy"
  },
  "dependencies": {
    "body-parser": "^1.15.2",
    "express": "^4.13.4",
    "sprintf-js": "^1.0.3",
    "actions-on-google": "^1.0.0"
  }
  }
```

Node.jsのロジックでは、Dialogflowのリクエストから受信したJSONペイロードを解析し、レスポンス用のJSONペイロードを生成します。 `package.json` ファイルは、Dialogflowとの全てのやり取りを、単純化されたクライアントAPIでラップする `actions-on-google` Node.jsモジュールを使用していることに注意してください。

私たちのアクションのために、DialogflowからのHTTP POST webhookリクエストに応答するためのウェブサーバーが必要です。ウェブサーバーを作成するためのNode.js "[express](http://expressjs.com/en/api.html)" モジュールと、HTTP body JSONペイロードをパースするための "[body-parser](https://github.com/expressjs/body-parser)" モジュールを使用します。

```js
'use strict';

// Actions client library debugging を有効化
process.env.DEBUG = 'actions-on-google:*';

let Assistant = require('actions-on-google');
let express = require('express');
let bodyParser = require('body-parser');

let app = express();
app.set('port', (process.env.PORT || 8080));
app.use(bodyParser.json({type: 'application/json'}));

const GENERATE_ANSWER_ACTION = 'generate_answer';
const CHECK_GUESS_ACTION = 'check_guess';

app.post('/', function (request, response) {
  console.log('headers: ' + JSON.stringify(request.headers));
  console.log('body: ' + JSON.stringify(request.body));

  const assistant = new Assistant({request: request, response: response});
  response.sendStatus(200); // OK
});

// サーバを開始
var server = app.listen(app.get('port'), function () {
  console.log('App listening on port %s', server.address().port);
  console.log('Press Ctrl+C to quit.');
});
```

nodeを使用してこのコードをローカルでテストできますが、まずnpmを使用して必要なNode.js依存関係をインストールします。

```bash
npm install
```

ここで、ノードを使用して、コードが構文的に正しいことを確認し、コードをローカルで実行します。

```bash
node app.js
```

すべてが機能している場合は、コンソールに次のメッセージが表示されます。 `App listening on port 8080`

[Google App Engineを使用してwebhookをデプロイする](https://developers.google.com/actions/tools/fulfillment-hosting#deploying_to_google_app_engine)手順に従います。gcloud CLIからホスティングURLを取得したら、左側のサイドバーで **Fulfillment** をクリックして、DialogflowでWebhookを設定する必要があります。 **Save** をクリックするのを忘れないでください。

### インテントの取り扱い

これで、以前に作成したDialogflowインテントのサポートが実装されます。処理する必要のあるインテントは、GENERATE_ANSWER_ACTION と CHECK_GUESS_ACTION の2つです。

これらのアクションごとに、以下に示すような対応する関数を記述します。

```js
  function getRandomNumber(min, max) {
    return Math.floor(Math.random() * (max - min + 1)) + min;
  }

  function generateAnswer(assistant) {
    console.log('generateAnswer');
    var answer = getRandomNumber(0, 100);
    assistant.data.answer = answer;
    assistant.ask(
      'I\'m thinking of a number from 0 and 100. What\'s your first guess?');
  }

  function checkGuess(assistant) {
      console.log('checkGuess');
      let answer = assistant.data.answer;
      let guess = parseInt(assistant.getArgument('guess'));
      if (answer > guess) {
       assistant.ask('It\'s higher than ' + guess + '. What\'s your next guess?');
      } else if (answer < guess) {
       assistant.ask('It\'s lower than ' + guess + '. Next guess?');
      } else {
        assistant.tell('Congratulations, that\'s it! I was thinking of ' + answer);
      }
  }

  let actionMap = new Map();
  actionMap.set(GENERATE_ANSWER_ACTION, generateAnswer);
  actionMap.set(CHECK_GUESS_ACTION, checkGuess);

  assistant.handleRequest(actionMap);
```

これらの関数はそれぞれ関数を返しますが、[ECMAScript 6](https://github.com/lukehoban/es6features)では、コードを非同期で呼び出し、標準的な方法でエラーを処理するための明確な方法を提供する[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)を返すこともできるでしょう。

この関数が、 `assistant.data` を使用して、アクションのセッションごとにJSONデータを格納する方法に注意してください。このアクションに対するDialogflowの後続のリクエストごとに、 `assistant.data` 値にアクセスできます。

`assistant.ask` メソッドは、音声応答を提供し、ユーザーからの対応する応答を期待するために使用されます。

`checkGuess` 関数が、最初の関数呼び出しで格納された `assistant.data` 値を取得する方法に注意してください。入ってきた"check_guess"インテントの"guess"引数の値は、 `assistant.getArgument` メソッドを使用して取得されます。

ユーザーの応答を期待しない音声応答を提供するには、 `assistant.tell` メソッドが使用されます。

どの関数がどのインテント名にマップされているかを、クライアントライブラリに伝える必要があります。JavaScriptのMapのデータ構造を使用しています。 `assistant.handleRequest` の使用に注意してください。アシスタントは、宣言されたインテントマッピングを使用して、入ってきたPOSTリクエストを処理し、Dialogflowが期待する形式で対応するレスポンスを作成します。アクションモジュールがそれを処理するので、前のコードスニペットの `response.sendStatus` の行は、もう必要ありません。

### アクションをテストする

Dialogflow Web GUIで、ページの右側にある埋め込みシミュレータを使用して、アクションをテストします。 **start_game** インテントトリガーフレーズの1つを使用します。

<table style="background-color: #fb8c00; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>あなた</b>
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
Talk to Number Genie （Number Genieと話す。）
</td>
</tr>
</table>

シミュレータは、アクションのロジックからの応答を表示します。

<table style="background-color: #fb8c00; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<b>Number Genie</b>
</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
_Let's play Number Genie! I'm thinking of a number from 0 to 100. What's your first guess? （Number Genieと遊びましょう！私は0から100までの数を考えています。最初の推測は何ですか？）_
</td>
</tr>
</table>

ログを見るために、gcloud CLIを使います。

```bash
gcloud app logs read
```

`Request from Assistant` で始まるログエントリを探します。

```javascript
Request from Assistant: {...}
```

入ってきたリクエストの `intentName` 値が、期待どおりの `start_game` であることをログの中で注意してください。

Dialogflowシミュレータに、推測を入力します。

<table style="background-color: #fb8c00; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>あなた</b>
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
50
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<b>Number Genie</b>
</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
It's higher than 50. What's your next guess? （50よりも大きいです。次の推測は何ですか？）
</td>
</tr>
</table>

このリクエストを探すために、gloud CLIを使います。

```javascript
Request from Assistant: {...}
```

ログにおいて、入ってきたリクエストの `intentName` 値が `provide_guess` であり、パラメーターに答えの値があることに注意してください。

番号を当てるまで、ゲームを続けます。

<table style="background-color: #fb8c00; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<b>Number Genie</b>
</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
Congratulations, that's it! I was thinking of 54 （おめでとうございます、それです！私は54を考えていました。）
</td>
</tr>
</table>

Dialogflowによるアクションフルフィルメントのための受信したリクエストがわかったので、curlコマンドを使用して、受信したJSON要求をログからコピーすることで、ローカルでアクションをテストできます。

`curl` を使用して、GENERATE_ANSWER_ACTIONアクションをエミュレートします。

```bash
curl -X POST -H "Content-Type: application/json" -d '{...}' http://localhost:8080
```

curlを使用して、CHECK_GUESS_ACTIONアクションをエミュレートします。

```bash
curl -X POST -H "Content-Type: application/json" -d '{...}' http://localhost:8080
```

> **開発時間を短縮するには**、アクションのロジックをGoogle App Engineにアップロードしてホスティングする前に、アクションのロジックをローカルで確認することを強くおすすめします。

## 改善されたVUI設計

私たちはゲームをもっと楽しくしたいので、新しい要件を追加します。

* ユーザーにゲームをもう一度遊ばせます。
* ユーザーがその番号の10以内にいる場合、カスタムメッセージを与えます。
* ユーザーがその番号の3以内にいる場合は、カスタムメッセージを表示します。
* ユーザーがヒントに反する場合、カスタムメッセージを与えます。
* ユーザーが最小値または最大値を選択した場合、カスタムメッセージを表示します。
* ユーザーが番号から離れている場合（> 75）に、カスタムメッセージを表示します。
* ユーザーが2離れて、同じ方向に1離れたときに、カスタムメッセージを表示します。
* やり取りがゲームの典型的なレスポンスの範囲外にある場合（例、"what time is it? （何時ですか？)"）、ユーザーに終了の選択肢を与えます。
* 2度の認識されない応答を、ゲームを終了する方法として処理します。
* 複数の実行可能な不一致時の応答を提供します。
* 同じ値を持つ複数の推測を処理します。
* ユーザーはいつでもゲームを終了することができます。

VUIの設計原則に従い、これらの要件を満たすゲームの[設計ウォークスルー](https://developers.google.com/actions/design/walkthrough)を行います。では、以下のセクションにて、VUI設計を実装しましょう。

### いつでもゲームを終了するサポート

ユーザーがゲームを終了したい場合を処理するユーザー入力を捕捉する新しいインテントが必要です。ゲーム中にいつでもこの作業を行うことができるようにするためには、 _ゲーム_ のコンテキストを意識したインテントが必要になります。

Dialogflow GUIを使用して、 _quit_game_ という新しいインテントを、次のユーザーフレーズとともに追加します。 _i quit, i give up, stop, quit._ インテントのコンテキストを _ゲーム_ に変更します。アクションの値を _quit_ にセットして、このインテントのWebHookを有効にします。新しいインテントを **Save** します。

新しいインテントをサポートするためのアクションのロジックを更新する必要があります。既存の乱数アクションのNode.jsロジックに次のコードを追加します。

```js
  const QUIT_ACTION = 'quit';

  function quit(assistant) {
      console.log('quit');
      let answer = assistant.data.answer;
      assistant.tell('Ok, I was thinking of ' + answer + '. See you later.');
  }

  ...
  actionMap.set(QUIT_ACTION, quit);
```

Dialogflowシミュレータを使用して、会話のさまざまな段階で _quit_ と言ってみてください。

### ユーザーが再びゲームを遊べるようにする

ユーザーが数字を当てたゲームの最後に、ユーザーに再度プレイするように促します。

ユーザーの応答を処理する新しいインテントを追加する必要があります。ユーザは、「はい」または「いいえ」と言うことができるようにしますが、それぞれにインテントが必要です。私たちはまた、 _yes_ を意味する _yep, sure_ や、 _nope, not really_ といった、これらの回答に対して典型的な別の言い方をサポートする必要があります。しかし、ゲーム終了時に会話がその入力を求めた場合に、yes/noのみをサポートするようエージェントを設計する必要があります。

> **Dialogflowのコンテキストについて学ぶ:** Dialogflowがコンテキストをどのように使用するかを理解することが重要ですので、まず次の[ブログ記事](https://blog.dialogflow.com/post/what-are-contexts/)を読んでください。

会話が _provide_guess_ インテントを呼び出し、ユーザーが正しい番号を推測したときに、 _yes_no_ という名前の新しいコンテキストを使用します。フルフィルメント内のロジックのみが数値が推測されたことを認識するので、そのロジックはコンテキストを _yes_no_ に動的に変更して、Dialogflowが次に予想されるユーザ入力に対してyes/no入力をサポートするようにします。

_play-again-yes_ という新しいインテントを作成します。

* ユーザーフレーズ _yes, yep, sure_ を追加してください。
* アクションに _play_again_yes_ という名前を付けて、WebHookを有効にします。
* 入力コンテキストと出力コンテキストの両方を _game_ に設定します。
* もう一つの入力コンテキストを _yes_no_ に設定します。
* この新しいインテントを保存します。

_play-again-no_ という新しいインテントを作成します。

* ユーザーフレーズ _no, nope, not really_ を追加します。
* アクションに _play_again_no_ と名前を付け、Webhookを有効にします。
* 入力コンテキストと出力コンテキストの両方を _game_ に設定します。
* もう一つの入力コンテキストを _yes_no_ に設定します。
* この新しいインテントを保存します。

> **Small Talkをオフにする:** このエージェントのDialogflow Small Talk Domainが無効になっていることを確認してください。そうでない場合は、期待されるユーザーの応答の一部が処理されます。

ユーザに応答するようにロジックを変更します。

```js
  function checkGuess(app) {
      console.log('checkGuess');
      let answer = app.data.answer;
      let guess = parseInt(app.getArgument('guess'));
      if (answer > guess) {
        app.ask('It\'s higher than ' + guess + '. What\'s your next guess?');
      } else if (answer < guess) {
        app.ask('It\'s lower than ' + guess + '. Next guess?');
      } else {
        app.setContext('yes_no');
        app.ask('Congratulations, that\'s it! I was thinking of '
          + answer + '. Wanna play again?');
      }
  }
```

`app.setContext` メソッドを使用して新しいコンテキストを _yes_no_ に設定し、デフォルトのクエリの有効期間を1にしてください（コンテキストが次のクエリの後で自動的に削除され、yes/noのユーザー入力はもはや _play_again_yes_ または _play_again_no_ インテントを作動させません）。

次に、フルフィルメントのコードに新しいインテント名を追加します。

```js
const PLAY_AGAIN_YES_ACTION = 'play_again_yes';
const PLAY_AGAIN_NO_ACTION = 'play_again_no';
```

PLAY_AGAIN_YES_ACTION に次のアクションハンドラロジックを追加する必要があります。

```js
  function playAgainYes(app) {
      console.log('playAgainYes');
      var answer = getRandomNumber(0, 100);
      app.data.answer = answer;
      app.ask('Great! I\'m thinking of a number from 0 and 100! What\'s your guess?');
    });
  }
```

このロジックは、GENERATE_ANSWER_ACTION ハンドラにとても似ています。

PLAY_AGAIN_NO_ACTION のためにもう一つのアクションハンドラロジックを追加します。

```js
  function playAgainNo(app) {
    console.log('playAgainNo');
    app.tell('Alright, talk to you later then.');
  }
```

これは、QUIT_ACTIONロジックに似ていますが、異なる bye フレーズを使います。

最後に、アクションライブラリに、ハンドラにアクションを作る方法を教えてください。

```js
  ...
  actionMap.set(PLAY_AGAIN_YES_ACTION, playAgainYes);
  actionMap.set(PLAY_AGAIN_NO_ACTION, playAgainNo);
```

会話の"yes"および"no"の両方のパスを試すために、Dialogflowシミュレータを使います。

### 以前のクエリ値を追跡する

VUI要件のいくつかは、ユーザによって以前に入力された値を追跡することに関するものです。アクションライブラリは、app.dataを使用して、セッションに値を格納するためのサポートを提供します。私たちは、すでに推測する数値を格納するためにapp.dataを使用しました。

アクションロジックを変更して、前のユーザーの推測を格納し、ユーザにヒントを出します。

```js
app.data.hint = 'higher';
app.data.previousGuess = guess;
```

ユーザーに与えられたヒントを「higher（より高い）」または「lower（より低い）」として追跡しようとしています。

CHECK_GUESS_ACTIONハンドラロジックを変更して、ユーザーがヒントに反するかどうかを確認します。

```js
  function checkGuess(app) {
      console.log('checkGuess');
      let answer = app.data.answer;
      let guess = parseInt(app.getArgument('guess'));
      if (app.data.hint) {
        if (app.data.hint === 'higher' && guess <= app.data.previousGuess) {
         app.ask('Nice try, but it’s still higher than ' + app.data.previousGuess);
          return;
        } else if (app.data.hint === 'lower' && guess >= app.data.previousGuess) {
          app.ask('Nice try, but it’s still lower than ' + app.data.previousGuess);
          return;
        }
      }
      if (answer > guess) {
        app.data.hint = 'higher';
        app.data.previousGuess = guess;
        app.ask('It\'s higher than ' + guess + '. What\'s your next guess?');
      } else if (answer < guess) {
        app.data.hint = 'lower';
        app.data.previousGuess = guess;
        app.ask('It\'s lower than ' + guess + '. Next guess?');
      } else {
        app.data.hint = 'none';
        app.data.previousGuess = -1;
        app.setContext('yes_no');
        app.ask('Congratulations, that\'s it! I was thinking of '
          + answer + '. Wanna play again?');
      }
  }
```

同様のロジックを使用して、繰り返されるユーザー入力を処理し、ユーザーが数字に近づいたり離れたりする際にカスタムメッセージを提供してヒントを改善することができます。ユーザーが数字に近づくと、カスタムメッセージを提供することもできます。

### 認識できない入力を処理する

ユーザーは、ゲームのコンテキスト外の応答を提供する可能性があります。ユーザがゲームを続けることを望んでいる場合、ユーザーに指示する必要があります。

Dialogflow Web GUIで、 **Default Fallback Intent** を選択します。このインテントは、他のインテントまたはドメインが一致しない場合に呼び出されます。そのインテントは、さまざまなデフォルトのプロンプトを提供します。

これらのプロンプトは、もう使用しません。ロジックがプロンプトを生成するように、このインテントのWebhookを有効にします。インテントへの変更を保存することを忘れないでください。

ここで、デフォルトのフォールバックアクション _input.unknown_ を処理するように、フルフィルメントロジックを更新する必要があります。

```js
const DEFAULT_FALLBACK_ACTION = 'input.unknown';

app.data.fallbackCount = 0;

function defaultFallback(app) {
  console.log('defaultFallback');
  app.data.fallbackCount++;
  if (app.data.fallbackCount == 1) {
    app.setContext('done_yes_no');
    app.ask('Are you done playing Number Genie?');
  } else {
    app.tell('We can stop here. Let’s play again soon.');
  }
}

actionMap.set(DEFAULT_FALLBACK_ACTION, defaultFallback);
```

今度は、最初のフォールバックの質問に対するユーザーの応答を処理するための、2つの新しいインテントを作成する必要があります。これは、数字が推測された後にゲームを再開するようにユーザーに求める目的と似ていますが、別のコンテキスト _do_yes_no_ を使用します。

### 代替的な応答を提供する

応答で同じフレーズを繰り返すのではなく、会話をより自然に見せるために、さまざまな代替的な応答からランダムに選択します。

すべての応答を文字列の配列で宣言し、次にユーティリティメソッドを使用して、利用可能な選択肢からランダムに選択します。

Node.jsの `sprintf-js` モジュールを使用して、ゲーム内の動的値に基づいて、文字列を動的に作成します。

```js
let sprintf = require("sprintf-js").sprintf;

function getRandomNumber(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}

const GREETING_PROMPTS =
  ["Let's play Number Genie.", "Welcome to Number Genie!"];
const INVOCATION_PROMPT =
  ["I\'m thinking of a number from %s and %s. What's your first guess?"];

function generateAnswer(app) {
  console.log('generateAnswer');
  var answer = getRandomNumber(MIN, MAX);
  app.data.answer = answer;
  app.data.guessCount = 0;
  app.ask(
    sprintf(
      sprintf(
        getRandomPrompt(GREETING_PROMPTS), guess)
          + ' ' + getRandomPrompt(INVOCATION_PROMPT), MIN, MAX));
}
```

代替文字列の完全な一覧と修正されたプロンプトのロジックのために、最終的なゲームのソースコードを見てください。

### 入力がなかったときの処理

app.askメソッドには、ユーザーが何も言わないとき、またはユーザーの応答が聞こえなかったときに、アクションがプロンプトを提供できるようにする2番目のパラメーターがあります。優れたユーザー体験を保証するために、開発者はapp.askリクエストごとに、入力がなかった際のプロンプトを指定することを強くお勧めします。

```js
app.ask('It\'s higher than ' + guess + '. What\'s your next guess?',
  'I didn\'t hear a number',
  'If you\'re still there, what\'s your guess?',
  'We can stop here. Let\'s play again soon.');
```

### ディープリンクの処理

Number Genieゲームは、アクションの呼び出しをサポートしていません。しかし、ユーザーが「talk to number genie about frogs,（カエルについてNUmber Genieと話す）」と言った際には、それを私たちのフルフィルメントロジックでエレガントに扱うことができます。

Dialogflowで新しいフォールバックインテントを作成するには、 **Create Intent** の横にある3つのドットをクリックします。この新しいインテントを _Unknown-deeplink_ と呼びましょう。出力コンテキストを _game_ に設定し、アクションを _deeplink.unknown_ に設定します。フルフィルメントのWebhookを有効にして、インテントを保存します。

**Default Fallback Intent** を変更して、入力と出力の両方の _game_ コンテキストを含めて、そのインテントを保存します。

つまり、ユーザーが「talk to number genie about frog, （カエルについてNumber Genieと話をする）」と言うと、 **Unknown-deeplink** インテントが呼び出されますが、ゲーム中にユーザーが認識できない何かを言った際には、 **Default Fallback Intent** が呼び出されます。

新しいインテントを処理するために、フルフィルメントのコードにいくつかのロジックを追加する必要があります。

```js
const UNKNOWN_DEEPLINK_ACTION = 'deeplink.unknown';
const RAW_TEXT_ARGUMENT = 'raw_text';

function unhandledDeeplinks (app) {
  console.log('unhandledDeeplinks');
  let answer = getRandomNumber(MIN, MAX);
  app.data.answer = answer;
  app.data.guessCount = 0;
  app.data.fallbackCount = 0;
  let text = app.getArgument(RAW_TEXT_ARGUMENT);
  if (text) {
    let numberOfLetters = text.length;
    if (numberOfLetters < answer) {
      app.ask(
        sprintf(
          '%s has %s letters. It\'s higher than %s.',
          text,
          numberOfLetters,
          numberOfLetters));
    } else if (numberOfLetters > answer) {
      app.ask(
        sprintf(
          '%s has %s letters. It\'s lower than %s.',
          text,
          numberOfLetters,
          numberOfLetters));
    } else {
      app.data.hint = NO_HINT;
      app.data.previousGuess = -1;
      app.setContext(YES_NO_CONTEXT);
      app.ask(
        sprintf(
          '%s has %s letters. Wow! The number I was thinking of was %s!',
          text,
          numberOfLetters,
          answer) + ' ' +  sprintf('Wanna play again?'));
    }
  } else {
    defaultFallback(app);
  }
}
```

ユーザー入力を無視する代わりに、ユーザーによって提供されたテキストの文字数を数え、最初の推測の数として使用してゲームを開始します。例えば、ユーザーが「talk to number genie about frog,（カエルについてNumber Genieと話す」と言った場合、そのアクションは「Frog has 4 letters. It's lower than 4.（カエルは4文字です。4未満です。）」と応答します。

`raw_text` 引数の値を使用して、ユーザーが言ったテキストを取得することに注意してください。

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/dialogflow/tutorials/number-genie)
