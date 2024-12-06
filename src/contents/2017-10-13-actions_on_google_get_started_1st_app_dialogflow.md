---
layout: actions_on_google
title: Build Your First App with Dialogflowを日本語訳しました
categories:
- actions on google
---
[前回のエントリ](https://www.eisbahn.jp/yoichiro/2017/10/actions_on_google_get_started_basics.html)では、Actions on Googleの基本を説明したドキュメントの日本語訳を紹介しました。ここでは、Dialogflowを使ったアプリのチュートリアル（[GET STARTED - Build Your First App - with Dialogflow](https://developers.google.com/actions/dialogflow/first-app)）の日本語訳を作ってみましたので、以下に紹介いたします。

Dialogflowは、自然言語および人間の言語を解析するNLU（自然言語理解）エンジンも含まれているため、自然言語処理を自分で構築する必要がなく、比較的手軽にアプリを作ることができます。

---

# Dialogflowで最初のアプリを構築する

Googleアシスタントのアプリは自然な会話的であり、アシスタントの機能をあなた自身の会話で拡張して、予定の予約、ゲームのプレイ、最新のニュースに関するアップデートの取得などをユーザができるようにします。

このガイドでは、ユーザーに好きな色と幸運な番号を入力することによってユーザーの愚かな名前を返すGoogleアシスタント向けのシンプルな **Silly Name Maker** アプリを構築する方法を説明します。また、Actions on GoogleとGoogleアシスタントの基本概念と、Dialogflowを使用してこれらのプラットフォーム向けに構築する方法についても学習します。

## アプリアーキテクチャ

Googleアシスタントの一般的なアプリは、次の3つの主要な部分から構成されています。

**アクションと呼び出し**

Actions on Googleは、ユーザーが「乗車予約」や「ゲームをする」などのアクションベースのコマンドを通じて、アシスタントのアプリを呼び出すための方法を定義します。すべてのアプリは、ユーザが名前でアプリを呼び出せるようにするメインアクションを定義する必要があります。さらに、すべてのアプリは、異なるエントリポイントを通じてアプリに深くリンクする追加のアクションを定義できます。

このチュートリアルでは、デフォルトのアクションを1つだけ定義します。これにより、ユーザーは「_Ok Google, talk to **Silly Name Maker**_」のような言葉でアプリを呼び出すことができます。

ユーザーがアクションを使ってアプリケーションを呼び出す方法を定義したら、次にアクションをActions on Googleに公開します。ユーザーは、AndroidのGoogleアプリまたはiOSのアシスタントアプリ内のアシスタントディレクトリからアクションを発見できます。

**会話**

これはアプリのUIであり、Actions on Googleによって呼び出された後に、ユーザがアプリとどのようにやり取りするかを定義します。Dialogflowを使用して、会話の文法（ユーザーがあなたのアプリに言うことができるもの）を定義します。

さらに、Dialogflowは組み込みの機械学習と自然言語の理解を提供するので、完全に網羅するように文法を定義する必要はありません。Dialogflowでユーザーの入力から型付きパラメータをパースする方法を定義することができ、ユーザーが何を言ったかをさらに簡単に理解させます。Dialogflowで会話の文法を構築した後は、_フルフィルメント_でユーザーにどのように応答するかを定義します。

このチュートリアルでは、ユーザーが好きな色と幸運な数字を提供する方法を定義する非常に単純な文法を作成します。

**フルフィルメント**

Dialogflowは、自然言語を理解し、ユーザーの入力から必要なデータ解析の大部分を処理します。ただし、ユーザーとの会話では、通常、要求された処理を完了してユーザに応答するためのビジネスロジックを実行する必要があります。Dialogflow WebHookを実装するウェブサービス（フルフィルメントと呼ばれる）でこれを行います。これは、アプリをDialogflowでアシスタントに統合する方法を定義するJSONベースのプロトコルです。

ユーザーが何かを言うと、生のテキストと、その入力からパースされたパラメーターのセットを受け取ります。このデータを使用してレスポンスを返し、会話を前に進めます。ユーザーとの会話が終わったら、要求されたアクションが達成されます。

このチュートリアルでは、Dialogflowがユーザーの入力から色と数値を解析し、フルフィルメントでこのデータを受け取り、ユーザーの愚かな名前で応答する方法を定義します。

> **Conversation WebHook vs Dialogflow WebHook**
>
> 会話のWebhookは、Googleアシスタントのための会話をどのように作成するかを定義します。Dialogflowは、アシスタントのConversationWebhookを実装して拡張し、独自の追加機能を追加します。Dialogflowを使用してアプリを構築すると、DialogflowのWebHook形式で実際に通信し、次にConversation WebHook形式でアシスタントと通信します。私たちのNode.jsクライアントライブラリを使用することが推奨されています。なぜなら、これらの面倒な詳細が隠蔽され、生のJSONを解析したり構築したりする必要がないからです。

## 会話をデザインする

アプリの作成を始める前に、サンプルのダイアログ（対話）を書き出して、常に会話の構造がわかるようにしておくべきです。これらのダイアログは、最終的にアプリの構築を始める際に良い参照ポイントになります。Silly Name Makerアプリでは、以下に説明する簡単なダイアログをいくつか用意しました:

### 正常パス

**アプリ**: _Hi! Welcome to Silly Name Maker! Let's get started. What is your lucky number?_<br>
**ユーザ**: _5_<br>
**アプリ**: _What is your favorite color?_<br>
**ユーザ**: _Yellow_<br>
**アプリ**: _Alright, your silly name is Yellow 5! I hope you like it. See you next time._

### ユーザが間違った入力を与えた場合

**アプリ**: _Hi! Welcome to Silly Name Maker! Let's get started. What is your lucky number?_<br>
**ユーザ**: _Dog_<br>
**アプリ**: _Hmm that doesn't sound like an number to me. What's your lucky number again?"_<br>
**ユーザ**: _22_<br>
**アプリ**: _What is your favorite color?_<br>
**ユーザ**: _Blue_<br>
**アプリ**: _Alright, your silly name is Blue 22! I hope you like it. See you next time._

> **会話デザインのヒント**
>
> この例では、あなた自身のアプリのために書くべきダイアログのいくつかを示しています。作成するダイアログの例については、[Design Walkthrough](https://developers.google.com/actions/design/walkthrough#write_dialogs)を参照してください。

## アクションプロジェクトとDialogflowエージェントを作成する

Actions on Google向けに開発を行うためには、アクションプロジェクトとDialogflowエージェントを作成する必要があります。アクションプロジェクトでは、アクションを登録したり、承認プロセスを通じてアプリを管理したり、アプリの統計情報を表示したりすることができます。Dialogflowエージェントは、ユーザーがあなたのアプリに言うことができるものと、アプリがユーザーの入力をどのように処理するかを定義します。

プロジェクトとエージェントを作成するには：

1. [Actions on Google Developer Console](http://console.actions.google.com/)に移動します。
1. **Add Project**をクリックし、プロジェクト名として`SillyNameMaker`を入力して、**Create Project**をクリックします。
1. **Overview**画面で、Dialogflowカード上の**BUILD**をクリックし、次に**CREATE ACTIONS ON Dialogflow**をクリックしてアクションの構築を開始します。
1. Dialogflowコンソールが表示され、エージェントに自動的に情報が入力されます。*Save*をクリックしてエージェントを保存します。
1. あなたのエージェントは、後でビルドするいくつかのフルフィルメントロジックを呼び出して、ユーザーの愚かな名前で応答を返します。エージェントのフルフィルメントを有効にすると、後で必要となるUIフィールドがいくつか用意されます。以下を行いましょう。
  * **Fulfillment**メニュー項目をクリックします。
  * *Disabled*スライダを右に移動して*Enabled*にします。
  * ダミーのURL（`https：//silly.name.maker`）を入力します。 フルフィルメントをビルドおよびデプロイする際にこのURLを更新します。
    ![](https://developers.google.com/actions/dialogflow/images/apiai-get-started-07.png){:height=="500px"}
1. **Save**をクリックします。

## アクションと呼び出しを定義する

Dialogflowエージェントを作成すると、**Default Welcome intent**が自動的に作成されます。このインテントは、会話のメインエントリポイントまたはアプリのメインアクションを表します。Actions on Googleがアプリを呼び出す方法を知るために、すべてのアプリで、このメインアクションが定義されている必要があります。このインテントには、次の特徴があります。

* インテントの**Events**セクションには、`WELCOME`イベントが指定されています。これは、このインテントがアプリのデフォルトエントリポイントであることを示しています。各エージェントは、この`WELCOME`イベントを宣言する唯一のインテントを持つ必要があります。「_Ok Google, talk to Silly Name Maker_」のように、ユーザーがアプリを名前で呼び出す際に、Googleアシスタントはアプリを作動させるためにこのイベントを使用します。。
* **Action**フィールドには、`input.welcome`名を指定します。インテントが作動されると、このアクション名がフルフィルメントに送信されることで、実行するタスクにアクションの名前を関連付けできます。このウェルカムインテントはフルフィルメントを使用する必要はないので（静的なレスポンスで応答します）、使用されていないためそのまま残すことができます。

> **注:** Dialogflowアクションは、Actions on Googleのアクションとは異なる意味を持ちます。したがって、2つを同じものとして混同しないでください。Dialogflowアクションは、処理するデータを定義するインテントのコンポーネントです。Actions on Googleのアクションは、あなたのアプリへのエントリーポイントです。

* **レスポンス**セクションには、デフォルトの静的テキストレスポンスがあります。これにより、フルフィルメントを呼び出さずにユーザーに応答することができます。**既定のウェルカムインテント**には、あなた自身で追加するために削除すべきな事前入力済みのレスポンスが含まれています。

> **Dialogflowレスポンスとフルフィルメントレスポンス**
>
> Dialogflowを使用すると、フルフィルメントコードを記述せずにDialogflow内で直接レスポンスを定義できるため、フルフィルメントを一切持たないアプリを作成することができます。これは、生成するロジックを必要としない単純なメッセージに便利です。しかし、あなたのインテントのほとんどは、おそらくフルフィルメントを使用してレスポンスを返すでしょう。
>
> Dialogflowでレスポンス機能を使用する別の便利な方法は、開発中に「プレースホルダ」レスポンスを返すことです。これにより、定義した文法でインテントが適切に作動されるようにすることができます。

あなたがウェルカムインテントについて少し知ることができたので、私たちのニーズに合うように修正しましょう。

1. 左のナビゲーションメニューで**Intents**をクリックし、**Default Welcome Intent**を選択します。
  ![](https://developers.google.com/actions/dialogflow/images/apiai-get-started-08.png){:width="600px"}
1. **Response**セクションで、**Text response**のフィールドにある入力済みのレスポンスを右上の**trash can**アイコンをクリックして削除します。
  ![](https://developers.google.com/actions/dialogflow/images/apiai-get-started-14.png){:width="600px"}
1. **ADD MESSAGE CONTENT > Text response**をクリックし、代わりに次のレスポンスを指定して**Save**をクリックします。
    ```text
    Hi! Welcome to Silly Name Maker! Let's get started. What is your lucky number?
    ```
  ![](https://developers.google.com/actions/dialogflow/images/apiai-get-started-11.png){:width="600px"}
1. **Save**をクリックします。

今、ユーザーがあなたのアプリを呼び出すと、彼らはあなたのアプリの体験が始まり、次に言うことを知ります。一般的に、あなたのレスポンスは、次に何を言うのか、そして会話の文法を超えないことをユーザに案内すべきです。これについては、次のセクションでさらに定義します。

## 会話の文法を定義する

Dialogflowインテントは、会話の文法と、ユーザーが文法の特定のフレーズを話すときに実行するタスクを定義します。Dialogflowには自然言語理解（NLU）エンジンが組み込まれているため、フレーズを定義するときに網羅する必要はありません。Dialogflowは、あなたが提供するフレーズを自動的に展開し、より多くのバリエーションを理解します。

愚かな名前を生成するために、ユーザーが幸運な数字と好きな色を提供するために必要となる方法を定義するインテントを作成します。

1. 左のナビゲーションで、**+**アイコンを**Intents**メニュー項目でクリックします。
1. **Intent name**フィールドに`make_name`と入力します。
1. **User says**フィールドに`My lucky number is 23`と入力して**Enter**キーを押します。
1. 番号**23**をクリックして強調表示し、表示されるドロップダウンメニューで`@sys.number`エンティティを割り当てます。Dialogflowは、これを自動的に`@sys.age`などの別のエンティティに自動的に割り当てます。いつでも他のエンティティを推測するように変更することができます。<br>
  ![](https://developers.google.com/actions/dialogflow/images/apiai-get-started-12.png){:width="600px"}
  > **注:** **ユーザーの発言**の一部分をエンティティに割り当てることによって、Dialogflowはユーザー入力からパラメーターを抽出し、パラメーターを検証し、それを変数としてフルフィルメントに提供することができます。エンティティを割り当てなければ、自分で入力を解析し、必要なパラメータを見つける必要があるでしょう。また、**Actions**セクションでは、パラメータがnumberパラメータに対して自動的に作成されていることにも注意してください。後でこれを修正します。
1. **ユーザーの発言**のフレーズのバリエーションを増やし、そのフレーズ内の任意の数字を`@sys.number`エンティティに割り当てます。
  * 23
  * The luckiest number I have is 12.<br>
   ![](https://developers.google.com/actions/dialogflow/images/apiai-get-started-13.png){:width="600px"}
1. **Actions**セクションで、`number`パラメータの**REQUIRED**チェックボックスをオンにします。これはDialogflowに、パラメータがユーザによって適切に提供されるまでインテントを作動させないように指示します。
1. パラメータの**Default prompts**をクリックして、再確認のフレーズを入力します。このフレーズは、Dialogflowがユーザー入力の数字を検出するまで繰り返しユーザーに話されます。<br>
  ![](https://developers.google.com/actions/dialogflow/images/apiai-get-started-15.png){:width="600px"}
1. In the Actions section, enter a new parameter with the following information:
1. **Actions**セクションで、次の情報を含む新しいパラメータを入力します。
  * **Required** - チェックボックスをオンにします
  * **Parameter name** - `color`
  * **Entity** - `@sys.color`
  * **Value** - `$color`
  * **Prompts** - `What is your favorite color?`<br>
  > **注意:** この追加のパラメータは、Dialogflowに組み込まれている「slot-filling」機能を使用しています。これにより、それぞれに対してインテントを作る必要なしに、そして一つのフレーズですべての要求さえる入力をユーザに話させる必要なしに、ユーザから追加の入力パラメータを得ることができます。また、Slot-fillingにより、ユーザーが必要なすべてのパラメーターを提供したレスポンスまでエージェントが入力を処理しないように、パラメーターを必須として設定することができます。

   ![](https://developers.google.com/actions/dialogflow/images/apiai-get-started-16.png){:width="600px"}
1. 名前に`make_name`を入力し、**SAVE**をクリックします。<br>
  ![](https://developers.google.com/actions/dialogflow/images/apiai-get-started-17.png){:width="600px"}
1. **Fulfillment**セクションで、**Use webhook**チェックボックスをオンにします。Dialogflowのレスポンス機能を使用する代わりに、ユーザーへのレスポンスを生成するためにDialogflowがフルフィルメントを呼び出すように指示します。
  ![](https://developers.google.com/actions/dialogflow/images/apiai-get-started-19.png){:width="400px"}
1. **Actions on Google**セクションで、**End conversation**チェックボックスをオンにします。これにより、Dialogflowは、フルフィルメントからユーザーにレスポンスが返った後に、Googleアシスタントに対して制御を返すことを指示します。
  ![](https://developers.google.com/actions/dialogflow/images/apiai-get-started-18.png){:width="400px"}
1. インテント全体を保存するために、**SAVE**をクリックします。

これで終わりです！Dialogflowのインテントを使って会話の文法を正しく宣言しました。また、Dialogflowの組み込みレスポンス機能を使用して、ユーザーがアプリを呼び出すときに静的なレスポンスを返すようにしました。そして、フルフィルメントを使用してレスポンスを返すインテントを作成しました。今、フルフィルメントを構築する必要があります。

## フルフィルメントの構築

フルフィルメントのロジック向けに、FirebaseにCloud Functionを構築します。あなたのフルフィルメントは、Dialogflowが返す解析済みのユーザー入力を受け取り、ユーザーの愚かな名前で応答します。

1. [Node.jsをダウンロードし、インストールします。](https://nodejs.org/)
1. 次のコマンドを使用して、Firebase CLIを設定し初期化します。
  > **注:** 次のコマンドがEACCESエラーで失敗した場合は、[npmパーミッションを変更する](https://docs.npmjs.com/getting-started/fixing-npm-permissions)必要があります。

    ```bash
    npm install -g firebase-tools
    ```
1. 次のコマンドを使用して、Googleアカウントでfirebaseツールを認証します。
    ```bash
    firebase login
    ```
1. `sillynamemaker`というディレクトリを作成し、次のコマンドでディレクトリを初期化します。
    ```bash
    mkdir sillynamemaker
    cd sillynamemaker
    firebase init
    ```
  あなたは以下の画面を見るはずです。
    ```bash
    Which Firebase CLI features do you want to setup for this folder? Press Space
    to select features, then Enter to confirm your choices.
     ◯ Database: Deploy Firebase Realtime Database Rules
    ❯◯ Functions: Configure and deploy Cloud Functions
     ◯ Hosting: Configure and deploy Firebase Hosting sites
    ```
1. インタラクティブなCLIメニューから**Functions**を選択し、**Enter**キーを押します。
1. アクションプロジェクトIDを画面から選択します（文字列には`silly-name-maker`が必要です）。Dialogflowエージェントの設定でプロジェクトIDを見つけることもできます。
1. `firebase init`コマンドは、`package.json`と`index.js`の2つのファイルを作成しました。それらが以下の例のようになるように編集します。
  **sillynamemaker/functions/package.json**<br>
  このファイルは、あなたのフルフィルメントのパッケージ依存関係を宣言します（Actions on GoogleおよびFirebaseのCloud Function Node.jsクライアントライブラリを含む）。
    ```json
    {
      "name": "silly-name-maker",
      "description": "Find out your silly name!",
      "version": "0.0.1",
      "author": "Google Inc.",
      "engines": {
        "node": "~4.2"
      },
      "dependencies": {
        "actions-on-google": "^1.0.0",
        "firebase-admin": "^4.2.1",
        "firebase-functions": "^0.5.7"
      }
    }
    ```
  **sillynamemaker/functions/index.js**<br>
  このファイルは、あなたのフルフィルメントのロジックを定義します。ここで、あなたが知る必要があるいくつかの主要なことがあります:
  * Dialogflowのインテントが作動されると、インテントのアクション名（インテントのアクション領域で宣言された）が、フルフィルメントへのリクエストにて提供されます。このアクション名を使用して、実行するロジックを決定します。
  * フルフィルメントへのすべての要求の中で、Dialogflowがユーザー入力からパラメーターを解析した場合、名前によってパラメーターにアクセスできます。ここでは、後でそれらのパラメータにアクセスするために、パラメータの名前を宣言します。
  * この関数は、愚かな名前を生成することによってアクションを達成します。その関数は、make_nameインテントが作動されるたびに呼び出されます。ユーザー入力からのパラメータを使用して、名前を生成します。
  * Node.jsクライアントライブラリのアクションマップでは、インテント名を関数にマップすることで、フルフィルメントがリクエストを受け取ると、そのアクション名に基づいて関数を自動的に呼び出すことができます。前に宣言したアクション名の変数を使用し、その名前を`make_name`関数にマップします。

    ```javascript
    'use strict';

    process.env.DEBUG = 'actions-on-google:*';
    const App = require('actions-on-google').DialogflowApp;
    const functions = require('firebase-functions');


    // a. the action name from the make_name Dialogflow intent
    const NAME_ACTION = 'make_name';
    // b. the parameters that are parsed from the make_name intent
    const COLOR_ARGUMENT = 'color';
    const NUMBER_ARGUMENT = 'number';


    exports.sillyNameMaker = functions.https.onRequest((request, response) => {
      const app = new App({request, response});
      console.log('Request headers: ' + JSON.stringify(request.headers));
      console.log('Request body: ' + JSON.stringify(request.body));


    // c. The function that generates the silly name
      function makeName (app) {
        let number = app.getArgument(NUMBER_ARGUMENT);
        let color = app.getArgument(COLOR_ARGUMENT);
        app.tell('Alright, your silly name is ' +
          color + ' ' + number +
          '! I hope you like it. See you next time.');
      }
      // d. build an action map, which maps intent names to functions
      let actionMap = new Map();
      actionMap.set(NAME_ACTION, makeName);


    app.handleRequest(actionMap);
    });
    ```

1. `sillynamemaker/functions`ディレクトリから、次のコマンドを実行してプロジェクトに依存しているものを取得します。
    ```bash
    npm install
    ```

## フルフィルメントをデプロイする

1. `sillynamemaker/functions`ディレクトリから、次のコマンドを使用してフルフィルメントをクラウドにデプロイします。
  ```bash
  firebase deploy --only functions
  ```
  デプロイにはしばらく時間がかかります。完了すると、次のような出力が表示されます。Function URLをDialogflowに入力する必要があります。

    ```bash
    ✔  Deploy complete!

    Project Console: https://console.firebase.google.com/project/sillynamemaker-abb1d/overview
    Function URL (sillyNameMaker): https://us-central1-sillynamemaker-abb1d.cloudfunctions.net/sillyNameMaker
    ```
  > **注:** Cloud Functionのデプロイが完了していたとしても、エンドポイントURLが実際に新しいコードを使用するまでには、数分かかる場合があります。
1. Dialogflowの左側のナビゲーションで、**Fulfillment**メニュー項目をクリックします。
1. **URL**フィールドに関数のURLを入力し、**Save**をクリックします。

## アプリをプレビューする

あなたのフルフィルメントがデプロイされ、Dialogflowで適切に設定されましたので、アクションシミュレータでアプリをプレビューできます。

アプリをプレビューするには:

1. Googleアカウントの[Activity controls](https://myaccount.google.com/activitycontrols)ページで、**Web & App Activity**、**Device Information**、**Voice & Audio Activity**の権限をオンにします。これは、アクションシミュレータを使用するために必要であり、ハードウェアデバイスなしでWeb上でアクションをテストできるようになります。
1. Dialogflowの左側のナビゲーションで**Integrations**をクリックし、**Actions on Google**カード上のスライダーバーを移動します。
1. **Actions on Google**カードをクリックして統合画面を表示し、**TEST**をクリックします。Dialogflowは、エージェントをサーバーにアップロードします。そして、アクションシミュレータでテストすることができます。
  > **注:** **TEST**ボタンが表示されない場合は、**AUTHORIZE**ボタンを最初にクリックして、DialogflowにGoogleアカウントとアクションプロジェクトへのアクセス権を付与する必要があります。
1. 統合画面の**Test your Assistant app**セクションで、**Actions Simulator**リンクをクリックして、アクションシミュレータ用の新しいブラウザを開きます。
  > **注:** アクションシミュレータのスプラッシュ画面が表示されている場合は、**Start**をクリックしてGoogleアカウントで認証します。
1. アクションシミュレータで、アプリのテストのために、シミュレータの入力領域に`talk to my test app`と入力します。
1. あなたの愚かな名前を受け取るように求められたら、番号と色を入力します。

## 次のステップ

このアプリは、開発に使用するアカウントでのみテストできます。自分のアプリを作成してすべてのユーザーが利用できるようにするには、レビューチームの[承認のためにアプリを提出する](https://developers.google.com/actions/distribute/)必要があります。

Dialogflowを使用したアシスタントのアプリを構築する方法の詳細については、[Dialogflow開発者ガイド](https://developers.google.com/actions/dialogflow)を参照してください。

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/dialogflow/first-app)
