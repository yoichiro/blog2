---
layout: post
title: Googleアシスタント向けアプリのEnd to Endテストライブラリの使い方
categories:
- actions on google
---

世界には、多くのアプリがあります。現在、そのほとんどは、グラフィカルユーザインタフェースを有しています。しかし、ほとんどの開発者は、
どのようにそのユーザインターフェイスをテストすれば良いか、という問題を抱えています。特に、GUIアプリのEnd-to-Endテストは難しいです。
End-to-Endテストを行うためのいくつかのソリューションが提供されていますが、End-to-Endテストは情報産業において大きなテーマです。

Googleアシスタント向けのアプリを開発している全ての開発者にとって、End-to-Endテストはとても重要なことです。Google Developer Advocate
である [Nick Felker](https://medium.com/@fleker) は、Googleアシスタント向けアプリのEnd-to-Endテストをもたらしてくれるプロジェクト
を開始しました。そのプロジェクトは、[actions-on-google-testing-nodejs](https://github.com/actions-on-google/actions-on-google-testing-nodejs) です。開発者は、そのライブラリを使って、End-to-Endテストを行うための
コードを書くことができます。Nick、素晴らしい仕事に感謝します！

ここで、このライブラリの使い方と、このライブラリを使ったEnd-to-Endテストコードの書き方について紹介したいと思います。

# 新しいディレクトリとファイルの準備

最初に、新しいディレクトリの作成が必要です。そのディレクトリは、End-to-Endテストを行うためのいくつかのファイルを配置することになります。

```bash
$ cd $HOME
$ mkdir actions-test
$ cd actions-test
```

# 新しいnodeプロジェクトの作成

次に、新しいnodeプロジェクトを作成して初期化するために、`npm` コマンドを実行してください。そのプロジェクトには任意の名前を付けてください。
基本的に、各質問については、初期値を適用することができます。

```bash
$ npm init
<Press enter, enter, ...>
```

新しいプロジェクトを作成後、`package.json` ファイルが作成されたのを見ることができます。

# 依存ライブラリのインストール

今、End-to-Endテスト環境を構築するためのnodeプロジェクトを手にしています。以下のコマンドに従って、`actions-on-google-testing`
ライブラリと他の依存ライブラリをインストールする時です。

```bash
$ npm install actions-on-google-testing --save
$ npm install chai --save
```

ここでは、各アサーション関数を使うために、`chai` ライブラリをインストールします。

# 新しいAoGプロジェクトの作成

ここで、ターミナルから離れて、ウェブブラウザに行きましょう。テスト目的でGoogleアシスタントを使うために、新しいActions on Google
プロジェクトを作成する必要があります。

[Actions on Google console](https://console.actions.google.com/) を開いてください。そして、`Add/import project` ボタンを
クリックしてください。

![]({{ "/images/2018/07/actions_on_google_testing_01.webp" | prepend: site.baseurl }})

プロジェクト名を入力して、`CREATE PROJECT` ボタンをクリックします。

![]({{ "/images/2018/07/actions_on_google_testing_02.webp" | prepend: site.baseurl }})

actions-on-google-testingライブラリは、Device registrationを適用したAoGプロジェクトを必要とします。ページの下にて、
`Device registration` メニュー項目を見つけることができます。その `Device registration` メニュー項目をクリックしてください。

![]({{ "/images/2018/07/actions_on_google_testing_03.webp" | prepend: site.baseurl }})

次のページにて、`REGISTER MODEL` ボタンをクリックしてください。

![]({{ "/images/2018/07/actions_on_google_testing_04.webp" | prepend: site.baseurl }})

ボタンのクリック後に、ポップアップダイアログが表示されます。以下のフィールドに入力してください。実際には、これらの
フィールドの値は、重要ではありません。

* Product name - 任意のプロダクト名 (例. "my product")
* Manufacturer name - 任意の製造業者名 (例. "my company")
* Device type - Light (もちろん、他の選択肢を選択することもできます)

その後、`REGISTER MODEL` ボタンをクリックします。

![]({{ "/images/2018/07/actions_on_google_testing_05.webp" | prepend: site.baseurl }})

# クレデンシャルファイルのダウンロード

もしDevice registrationに成功した場合は、次のページからクレデンシャルファイルをダウンロードすることができます。
ダウンロードするために、`Download credentials.json` ボタンをクリックしてください。

![]({{ "/images/2018/07/actions_on_google_testing_06.webp" | prepend: site.baseurl }})

そのファイルのダウンロード先のディレクトリとして、前の手順で作成したディレクトリを指定してください。その後、`NEXT`
ボタンを押します。

次のページは、トレイトを指定するためのページです。しかし、ここでは必要ありません。`SKIP` をクリックします。

![]({{ "/images/2018/07/actions_on_google_testing_07.webp" | prepend: site.baseurl }})

`SKIP` をクリック後に、以下のような次の画面にて登録されたデバイスモデルを見ることができるはずです。

![]({{ "/images/2018/07/actions_on_google_testing_08.webp" | prepend: site.baseurl }})

# Google Assistant APIの有効化

actions-on-google-testingライブラリは、Googleアシスタントと通信を行うために、Google Assistant APIを使用します。従って、
あなたのプロジェクトにて、Google Assistant APIを有効にする必要があります。AoGプロジェクトを作成した際に、同時にGCP
プロジェクトも作成されています。GCPコンソールから、そのAPIを有効にすることができます。

ウェブブラウザを使って、以下のURLにアクセスしてください。

[https://console.cloud.google.com/apis/library/embeddedassistant.googleapis.com?q=Assistant](https://console.cloud.google.com/apis/library/embeddedassistant.googleapis.com?q=Assistant)

もし対象のプロジェクトが選択されていない場合は、ページの上部にて対象のプロジェクトを選択してください。その後、`ENABLE`
ボタンをクリックして、Google Assistant APIを有効にしてください。

![]({{ "/images/2018/07/actions_on_google_testing_11.webp" | prepend: site.baseurl }})

# クレデンシャルファイルの変換

ダウンロードされたクレデンシャルファイルを変換することが必要です。具体的には、Google Assistant APIを利用するために、
アクセストークンが必要となります。actions-on-google-testingライブラリでは、変換ツールが提供されています。
クレデンシャルファイルを変換するために、以下のコマンドを実行してください。

```bash
$ mkdir test
$ node ./node_modules/actions-on-google-testing/generate-credentials.js ./credentials.json
```

`generate-credentials.js` コマンドは、Google Assistant API (actions-on-google-testingライブラリは、Google Assistant
APIを経由してGoogleアシスタントと通信します)を使うことを認可するためのURLを表示します。

```bash
Reading data for project "testing-actions-*****"
Please go to the following link to authorize, then copy the code below
https://accounts.google.com/o/oauth2/v2/auth?...
Authorization code: 
```

ウェブブラウザでそのURLを開きます。まず、Googleアカウントを選択する必要があります。

![]({{ "/images/2018/07/actions_on_google_testing_09.webp" | prepend: site.baseurl }})

次に、Google Assistant APIの利用を認可してください。

![]({{ "/images/2018/07/actions_on_google_testing_10.webp" | prepend: site.baseurl }})

認可後に、次のページで認可コードが表示されます。そのコードをコピーし、ターミナルにてペーストしてください。
アクセストークンやその他のパラメータが、`test/test-credentials.json` ファイルに書き出されます。

```bash
Saved user credentials in "test/test-credentials.json"
You are now ready to run tests
Make sure you DO NOT commit this file
```

# テストコードを書く

ついに、actions-on-google-testingライブラリを使ったEnd-to-Endテスト環境を手に入れました。今、テストコードを書き始める
ことができます。ここでは、`test.js` という名前のファイルとして、テストコードを作成します。

```bash
$ vi test.js
```

## 依存モジュールの読み込み

actions-on-google-testingにて、いくつか依存モジュールの読み込みが必要です。そして、ここでは、各結果を検証するために、
`chai` ライブラリを使います。

`test.js` ファイル内に、以下のコードを書いてください。

```js
"use strict";
  
const {ActionsOnGoogleAva} = require("actions-on-google-testing");
const {expect} = require("chai");
const testCredentials = require("./test/test-credentials.json");
const action = new ActionsOnGoogleAva(testCredentials);
```

## 会話の開始

その後、テストを開始し、そしてテストを行いたいアプリとの会話を始めます。テストを開始するために、`startTest()` 関数を
使うことができます。また、会話を始めるために、`startConversation()` 関数を使うことができます。そのコードは、以下のように
なるでしょう。

```js
action.startTest("Happy path", action => {
  return action.startConversation()
    .then(
      ...
    );
});
```

おそらく、各テストシナリオのために、`startTest()` 関数を使うことになるでしょう。例えば、上記のサンプルコードでは、
"Happy path" シナリオを定義しています。その他の "Invoke help" シナリオなども定義することができるでしょう。

もし `startConversation()` 関数に何も引数を渡していない場合は、actions-on-google-testingライブラリは、テストアプリを呼び出そうと
します。例えば、en-USロケールでは、アプリ名として "my test app" が使われます。つまり、actions-on-google-testingは ”Talk to
my test app" と言います。

### 明示的なアプリ名の指定

既に何らかのアプリ名を登録していた場合、そのアプリを "my test app" で呼び出すことはできません。その代わり、アプリ名を使ってアプリを
呼び出すために、`startConversationWith()` 関数を使うことができます。つまり、`startConversationWith()` 関数の最初の引数として
アプリ名を指定することが可能です。以下のコードは、"Personal Chef" という名前を使ってアプリを呼び出します。

```js
action.startTest("Happy path", action => {
  return action.startConversationWith("Personal Chef")
    .then(
      ...
    );
});
```

### 呼び出しフレーズの指定

もしあなたのアプリが明示的な呼び出し（Explicit Invocation）をサポートしている場合は、アプリの呼び出し時に何らかの呼び出しフレーズを
指定することが必要になります。`startConversation()` および `startConversationWith()` の両関数とも、呼び出しフレーズをサポート
しています。以下のようにそれらを利用可能です。

* `startConversation("to find a homemade cannoli recipe")` - "Talk to my test app to find a homemade cannoli recipe"
* `startConversationWith("Personal Chef", "to find a homemade cannoli recipe")` - "Talk to Personal Chef to find a homemade cannoli recipe"

## アプリへの発言

テストコードにおいて、アプリに何かフレーズを言うことが必要になります。actions-on-google-testingライブラリでは、テストコードから
アプリに何かを言うために、`send()` 関数を提供しています。`send()` 関数は、柔軟な関数です。実際、`startConversation()` や 
`startConversationWith()` 関数は、アプリの呼び出しのためのフレーズを発言するために、内部的に `send()` 関数を呼び出しています。

以下のようにして `send()` 関数を利用することができます。

```js
action.startTest("Happy path", action => {
  return action.send("Talk to Personal Chef")
    .then(
      ...
    );
});
```

それらの関数は、アプリに何らかのフレーズを送信します。そして、アプリはそれぞれ応答を返します。`then()` 関数に渡されるコールバック関数を
使って、応答を受け取ることが可能です。`then()` 関数を使って、以下のようにして次々とフレーズを送信することにより会話を続けることが
できます。

```js
action.startTest("Happy path", action => {
  return action.startConversationWith(
      "Personal Chef", "to find a homemade cannoli recipe")
    .then(response => {
      return action.send("Start the cooking");
    })
    .then(response => {
      return action.send("Next step");
    })
    .then(response => {
      return action.send("Ok, thanks");
    });
});
```

## 応答の検証

`then()` 関数に渡されるコールバック関数は、アプリからの応答を伴って呼び出されます。各応答は、`textToSpeech`, ` ssml`, `suggestions`
などを含むいくつかのプロパティを保持しています。テストコードの中で、各値を検証することができます。以下のコードは、`chai` ライブラリを
使ってそれぞれの応答の値を検証しています。

```js
action.startTest("Happy path", action => {
  return action.startConversationWith(
      "Personal Chef", "to find a homemade cannoli recipe")
    .then(response => {
      expect(response.textToSpeech[0]).to.have.string(
          "Ok, let's start the cooking of cannoli. Are you ready?"); 
      expect(response.micOpen).to.be.true;
      return action.send("Start the cooking");
    })
    .then(response => {
      expect(response.textToSpeech[0]).to.have.string(
          "Thank you. First step is ...");
      expect(response.micOpen).to.be.true;
      return action.send("Next step");
    })
    .then(response => {
      expect(response.textToSpeech[0]).to.have.string(
          "... That's all. Would you find other recipe?");
      expect(response.micOpen).to.be.true;
      return action.send("No, thanks");
    })
    .then(response => {
      expect(response.textToSpeech[0]).to.have.string("Bye");
      expect(response.micOpen).to.be.false;
    })
});
```

## 国際化

actions-on-google-testingライブラリは、国際化をサポートしています。初期値として、"en-US" ロケールが適用されています。Google
アシスタントは、多くの言語をサポートしています。もちろん、呼び出しフレーズは言語ごとに異なります。actions-on-google-testing
ライブラリは、現在22の言語をサポートしてます。その言語の一覧は以下です。

* en-US, en-GB, en-AU, en-SG, en-CA, fr-FR, fr-CA, ja-JP, de-DE, ko-KR, es-ES, es-419, pt-BR, it-IT, ru-RU, hi-IN, th-TH, id-ID, da-DK, no-NO, nl-NL, sv-SE

actions-on-google-testingライブラリは、テストしたいロケールをセットするために、`setLocale()` 関数を提供しています。もしあなたが
"fr-FR" ロケールを `setLocale()` 関数によりセットし、そして `startConversation()` 関数を使った場合は、actions-on-google-testing
はフランス語で以下の呼び出しフレーズを発言するでしょう。

* "Parler avec mon application test"

以下のコードは、`setLocale()` 関数の使い方を示しています。

```js
action.setLocale("fr-FR");
action.startTest("Happy path", action => {
  return action.startConversationWith("Chef personnel")
    .then(response => {
      expect(response.textToSpeech[0]).to.have.string(
          "Bienvenue dans l'application Personal Chef. Que veux-tu cuisiner?");
      return action.send("Du boeuf.");
    })
    ...
});
```

# テストコードの実行方法

actions-on-google-testingライブラリは、[ava](https://github.com/avajs/ava) JavaScript test runner を使っています。
`npm` コマンドを使ってactions-on-google-testingライブラリをインストールした際に、同時に ava ライブラリもインストールされています。
もし `test.js` という名前でテストのためのファイルを作成した場合は、以下のコマンドでテストコードを実行することができます。

```bash
$ ./node_modules/.bin/ava -c 1 -s ./test.js
```

もし実行が成功すれば、以下のような出力ログが表示されるでしょう。

```bash
** Starting test Happy path **
> Talk to Hexadecimal conversion about 10
{ micOpen: false,
  textToSpeech: 
   [ '<speak>The decimal number 10 is a <say-as interpret-as="char...' ],
  displayText: 
   [ 'The decimal number 10 is a a hexadecimal number.The hex...' ],
  ssml: [],
  suggestions: [] }
test passes
> cancel
{ micOpen: false,
  textToSpeech: [],
  displayText: [],
  ssml: [],
  suggestions: [] }
test ends

  1 passed
```

# まとめ

actions-on-google-testingライブラリは、まだDeveloper Previewです。そのため、いつでもアイディアを提案することができます。もちろん、
大きな変更も歓迎されます。GoogleアシスタントやActions on Googleのエコシステムにとって、このライブラリはとても重要です。多くの開発者が
多くの提案をし、多くのPull Requestを送り、そして多くのフィードバックをすることを期待しています。
