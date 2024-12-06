---
layout: post
title: ユーザインタラクション - Yeomanジェネレータの作成
categories:
- yeoman
---

何かを作りたい際に、ゼロから自分で作っていくのは、単純に大変な作業です。何か土台的なものがあって、自分でやりたいことだけを追記していければ、それに越したことはありません。

[Yeoman](https://yeoman.io/) は、さまざまな土台を作ってくれるツールです。 `yo` というコマンドを打ち込んでプロジェクトの雛形を作成した経験のある方は多いと思いますが、まさに `yo` が Yeroman です。　`yo generator-name` と打ち込むことで、ジェネレータが呼び出され、対話形式でいくつかの質問に答えるだけで、土台となるファイル群を生成させることが可能です。

そのジェネレータは、自作することも可能です。僕も、とあるジェネレータを自分で作ってみたくなりました。Yeoman のウェブサイトには、じぇねれーたのつくりかたはが複数ページに渡って説明されています。勉強の意味も兼ねて、日本語訳を作ってみました。

今回は、 [Interacting with the user](https://yeoman.io/authoring/user-interactions.html) です。

---
あなたのジェネレータはエンドユーザとたくさん対話するでしょう。デフォルトでは、Yeomanはターミナル上で動作しますが、さまざまなツールが提供できるカスタムユーザーインターフェースもサポートします。たとえば、Yeomanジェネレータがエディタやスタンドアローンアプリのようなグラフィカルツールの内部で実行されることを妨げるものは何もありません。

この柔軟性を可能にするために、Yeomanは一連のユーザーインターフェース要素の抽象化を提供します。エンドユーザーと対話をするときに、これらの抽象作用のみを使用するのは、作者としてのあなたの責任です。他の方法を使用すると、ジェネレータがさまざまなYeomanツールで正しく動作しなくなる可能性があります。

例えば、 `console.log()` や `process.stdout.write()` を使って内容を出力しないことが重要です。それらを使用すると、端末を使用していないユーザーから出力が隠されます。代わりに、常にUIジェネリックな `this.log()` メソッドに頼ってください。ここで、 `this` は、現在のジェネレータのコンテキストです。

# ユーザインタラクション

## プロンプト

プロンプトは、ジェネレータがユーザーと対話する主要な方法です。 `prompt` モジュールは、 [Inquirer.js](https://github.com/SBoudrias/Inquirer.js) によって提供されており、利用可能なpromptオプションのリストについては [そのAPI](https://github.com/SBoudrias/Inquirer.js) を参照する必要があります。

`prompt` メソッドは非同期であり、Promiseを返します。次のタスクを実行する前に、タスクの完了を待つためにタスクからそのPromiseを返す必要があります（ [非同期タスクの詳細](https://yeoman.io/authoring/running-context.html)）。

```js
module.exports = class extends Generator {
  async prompting() {
    const answers = await this.prompt([
      {
        type: "input",
        name: "name",
        message: "Your project name",
        default: this.appname // Default to current folder name
      },
      {
        type: "confirm",
        name: "cool",
        message: "Would you like to enable the Cool feature?"
      }
    ]);

    this.log("app name", answers.name);
    this.log("cool feature", answers.cool);
  }
};
```

ここで、ユーザからのフィードバックを尋ねるために、 [`prompting` queue](https://yeoman.io/authoring/running-context.html) を使っていることに注意してください。

## 後の段階でユーザの回答を使用する

とても一般的であるシナリオは、後の段階でユーザーの回答を使用することです。例えば、 [`writing` queue](https://yeoman.io/authoring/file-system.html) です。これは、それらを `this` コンテキストに追加することで、簡単に実現できます。

```js
module.exports = class extends Generator {
  async prompting() {
    this.answers = await this.prompt([
      {
        type: "confirm",
        name: "cool",
        message: "Would you like to enable the Cool feature?"
      }
    ]);
  }

  writing() {
    this.log("cool feature", this.answers.cool); // user answer `cool` used
  }
};
```

## ユーザー設定の記憶

ユーザーは、ジェネレータを起動するたびに、特定の質問に同じ入力を与えることがあります。これらの質問については、おそらく、ユーザーが以前に回答した内容を覚えておき、その回答を新しい `default` として使用したくなるでしょう。

Yeomanは、質問オブジェクトに `store` プロパティを追加することによって、Inquirer.js APIを拡張します。このプロパティを使用すると、ユーザーが指定した回答を将来のデフォルトの回答として使用するように指定できます。これは次のようにして行うことができます。

```js
this.prompt({
  type: "input",
  name: "username",
  message: "What's your GitHub username",
  store: true
});
```

注: デフォルト値を指定すると、ユーザーは空の回答を返すことができなくなります。

直接プロンプトに縛られずにデータを保存することだけを検討している場合は、 [Yeomanストレージのドキュメント](https://yeoman.io/authoring/storage.html) をチェックしてください。

## 引数

引数は、コマンドラインから直接渡されます。

```bash
yo webapp my-project
```

この例では、 `my-project` が最初の引数になります。

引数が必要であることをシステムに通知するには、 `this.argument()` メソッドを使用します。このメソッドは、 `name` (String) と任意のオプションのハッシュを受け入れます。

`name` 引数は、 `this.options[name]` として利用可能になります。

optionsハッシュは、複数のキーと値のペアを受け入れます。

* `desc` 引数の説明。
* `required` 必須かどうかのブール値。
* `type` String、Number、Array（生の文字列値を受け取り、それを解析するカスタム関数にすることもできます）。
* `default` この引数のデフォルト値。

このメソッドは、 `constructor` メソッド内で呼び出す必要があります。そうでなければ、ユーザーがヘルプオプションを指定してジェネレータを呼び出したときに（例; `yo webapp --help` ）、Yeomanは関連するヘルプ情報を出力することができません。

以下が例となります。

```js
module.exports = class extends Generator {
  // note: arguments and options should be defined in the constructor.
  constructor(args, opts) {
    super(args, opts);

    // This makes `appname` a required argument.
    this.argument("appname", { type: String, required: true });

    // And you can then access it later; e.g.
    this.log(this.options.appname);
  }
};
```

`Array` 型の引数には、ジェネレータに渡される残りのすべての引数が含まれます。

## オプション

オプションは、引数とよく似ていますが、コマンドラインフラグとして書かれます。 

```bash
yo webapp --coffee
```

オプションが必要であることをシステムに通知するには、 `this.option()` メソッドを使用します。このメソッドは、 `name` (String) と任意のオプションのハッシュを受け入れます。

`name` 値は、一致するキーにあるオプションを取得するために使用されます `this.options[name]` 。

options ハッシュ（2番目の引数）は、複数のキーと値のペアを受け入れます。

* `desc` オプションの説明。
* `alias` オプションの短縮名。
* `type` Boolean、String、またはNumber（生の文字列値を受け取って解析するカスタム関数にすることもできます）。
* `default` デフォルト値。
* `hide` ヘルプから隠すかどうかのブール値。

以下は例です。

```js
module.exports = class extends Generator {
  // note: arguments and options should be defined in the constructor.
  constructor(args, opts) {
    super(args, opts);

    // This method adds support for a `--coffee` flag
    this.option("coffee");

    // And you can then access it later; e.g.
    this.scriptSuffix = this.options.coffee ? ".coffee" : ".js";
  }
};
```

# 情報の出力

情報の出力は、 `this.log` モジュールによって処理されます。

あなたが使用する主な方法は、単に `this.log` です（例えば、 `this.log('Hey！Welcome to my awesome generator')` ）。文字列を受け取り、それをユーザーに出力します。基本的には、ターミナルセッション内で使用されると、 `console.log()` を模倣します。あなたは以下のようにそれを使うことができます。

```js
module.exports = class extends Generator {
  myAction() {
    this.log("Something has gone wrong!");
  }
};
```

[API documentation](https://yeoman.github.io/environment/TerminalAdapter.html) から、いくつかの他のヘルパーメソッドを見つけることもできます。
