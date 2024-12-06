---
layout: post
title: ファイルシステムでの作業 - Yeomanジェネレータの作成
categories:
- yeoman
---

何かを作りたい際に、ゼロから自分で作っていくのは、単純に大変な作業です。何か土台的なものがあって、自分でやりたいことだけを追記していければ、それに越したことはありません。

[Yeoman](https://yeoman.io/) は、さまざまな土台を作ってくれるツールです。 `yo` というコマンドを打ち込んでプロジェクトの雛形を作成した経験のある方は多いと思いますが、まさに `yo` が Yeroman です。　`yo generator-name` と打ち込むことで、ジェネレータが呼び出され、対話形式でいくつかの質問に答えるだけで、土台となるファイル群を生成させることが可能です。

そのジェネレータは、自作することも可能です。僕も、とあるジェネレータを自分で作ってみたくなりました。Yeoman のウェブサイトには、じぇねれーたのつくりかたはが複数ページに渡って説明されています。勉強の意味も兼ねて、日本語訳を作ってみました。

今回は、 [Working with the file system](https://yeoman.io/authoring/file-system.html) です。

---

# ロケーションコンテキストとパス

Yeomanファイルユーティリティは、常にディスク上に2つのロケーションコンテキストがあるという考えに基づいています。これらのコンテキストは、ジェネレータが読み書きする可能性が最も高いフォルダです。

## Destination コンテキスト

最初のコンテキストは、 _destination コンテキスト_ です。 destination は、Yeomanが新しいアプリケーションを足場にするフォルダです。それはユーザのプロジェクトフォルダであり、そこはあなたが足場の大部分を書くことになる場所です。

destination コンテキストは、現在の作業ディレクトリ、または `.yo-rc.json` ファイルを含む最も近い親フォルダとして定義されています。 `.yo-rc.json` ファイルは、Yeomanプロジェクトのルートを定義します。このファイルにより、ユーザーはサブディレクトリでコマンドを実行し、それらをプロジェクトで機能させることができます。これは、エンドユーザーに対して一貫した動作を保証します。

`this.destinationRoot()` を使用するか、または `this.destinationPath('sub/path')` を使用してパスを結合することによって、  _destination パス_ を **取得** できます。

```js
// Given destination root is ~/projects
class extends Generator {
  paths() {
    this.destinationRoot();
    // returns '~/projects'

    this.destinationPath('index.js');
    // returns '~/projects/index.js'
  }
}
```

そして、 `this.destinationRoot('new/path')` を使って、手動で設定することができます。しかし、一貫性を保つために、デフォルトの保存先を変更すべきではありません。

ユーザーが `yo` を実行している場所を知りたい場合は、 `this.contextRoot` を使用してパスを取得できます。これは、 プロジェクトルートを `.yo-rc.json` を使って決定する前の、 `yo` が呼び出された元のパスです。

## テンプレートコンテキスト

テンプレートコンテキストは、テンプレートファイルを保存するフォルダです。それは通常あなたが読み込んでコピーするフォルダです。

テンプレートコンテキストは、デフォルトで `./templates/` として定義されています。  `this.sourceRoot('new/template/path')` を使用して、このデフォルトを上書きできます。

`this.sourceRoot()` を使用するか、または `this.templatePath('app/index.js')` を使用してパスを結合することによって、パス値を取得することができます。

```js
class extends Generator {
  paths() {
    this.sourceRoot();
    // returns './templates'

    this.templatePath('index.js');
    // returns './templates/index.js'
  }
};
```

# "インメモリ"ファイルシステム

Yeomanは、ユーザーファイルの上書きに関しては非常に慎重です。基本的に、既存のファイルに対して行われるすべての書き込みは競合解決プロセスを経由します。このプロセスでは、コンテンツをそのファイルに上書きするすべてのファイル書き込みをユーザーが検証する必要があります。

この振る舞いは悪いサプライズを防ぎ、エラーのリスクを制限します。一方、これはすべてのファイルがディスクに非同期的に書き込まれることを意味します。

非同期APIは使いにくいので、Yeomanはすべてのファイルが [インメモリファイルシステム](https://github.com/sboudrias/mem-fs) に書き込まれ、Yeomanの実行が終了したときに一度だけディスクに書き込まれる同期ファイルシステムAPIを提供します。

このメモリファイルシステムは、すべての [合成されたジェネレータ](https://yeoman.io/authoring/composability.html) 間で共有されています。

# ファイルユーティリティ

ジェネレータは、 `mem-fs editor` のインスタンスである `this.fs` 上のすべてのファイルメソッドを公開します。利用可能なすべてのメソッドについて、 [モジュールのドキュメント](https://github.com/sboudrias/mem-fs-editor) を必ず確認してください。

`this.fs` は `commit` を公開していますが、ジェネレータでそれを呼び出すべきではないということは注目に値します。Yeomanは、実行ループの衝突段階の後に、これを内部的に呼び出します。

## 例: テンプレートファイルをコピーする

これは、テンプレートファイルをコピーして処理する例です。

`./templates/index.html` の内容は次のとおりです。

```html
<html>
  <head>
    <title><%= title %></title>
  </head>
</html>
```

次に、 [`copyTpl`](https://github.com/sboudrias/mem-fs-editor#copytplfrom-to-context-templateoptions--copyoptions) メソッドを使用して、コンテンツをテンプレートとして処理しながらファイルをコピーします。  `copyTpl` は [ejsテンプレート構文](http://ejs.co/) を使用しています。

```js
class extends Generator {
  writing() {
    this.fs.copyTpl(
      this.templatePath('index.html'),
      this.destinationPath('public/index.html'),
      { title: 'Templating with Yeoman' }
    );
  }
}
```

ジェネレータの実行が完了すると、 `public/index.html` には次の内容が含まれます。

```html
<html>
  <head>
    <title>Templating with Yeoman</title>
  </head>
</html>
```

非常に一般的なシナリオは、ユーザーの回答を [promptingの段階](https://yeoman.io/authoring/user-interactions.html) で保存し、それらをテンプレート化に使用することです。

```js
class extends Generator {
  async prompting() {
    this.answers = await this.prompt([{
      type    : 'input',
      name    : 'title',
      message : 'Your project title',
    }]);
  }

  writing() {
    this.fs.copyTpl(
      this.templatePath('index.html'),
      this.destinationPath('public/index.html'),
      { title: this.answers.title } // user answer `title` used
    );
  }
}
```


# ストリームを介して出力ファイルを変換する

ジェネレータシステムでは、ファイルの書き込みごとにカスタムフィルタを適用できます。自動的にファイルを美化したり、空白などを正規化したりすることが全て可能です。

Yeomanプロセスごとに、修正されたすべてのファイルをディスクに書き込みます。このプロセスは（[gulp](http://gulpjs.com/) のように）[vinyl](https://github.com/wearefractal/vinyl) オブジェクトストリームを通過します。ジェネレータの作成者は誰でもファイルパスやコンテンツを変更するために `transformStream` を登録できます。

新しい修飾子の登録は `registerTransformStream()` メソッドを通して行われます。例を示しましょう。

```js
var beautify = require("gulp-beautify");
this.registerTransformStream(beautify({ indent_size: 2 }));
```

**あらゆる種類のあらゆるファイルが、このストリームを通して渡される** ことに注意してください。変換ストリームがサポートしていないファイルを通過することを確認してください。 [`gulp-if`](https://github.com/robrich/gulp-if) や [`gulp-filter`](https://github.com/sindresorhus/gulp-filter) のようなツールは、無効な型をフィルタリングして通過させるのを助けます。

あなたは基本的にYeoman変換ストリームで任意の _gulp_ プラグインを使って、writing段階で生成されたファイルを処理することができます。

# Tip: 既存のファイルの内容を更新する

既存のファイルを更新することは、必ずしも簡単な作業ではありません。そのための最も信頼できる方法は、ファイルAST（ [抽象構文木](http://en.wikipedia.org/wiki/Abstract_syntax_tree) ）を解析してそれを編集することです。このソリューションの主な問題は、ASTを編集することは冗長で、把握するのが少し難しいことです。

いくつかの人気のあるASTパーサーがあります。

* HTMLを解析するための [Cheerio](https://github.com/cheeriojs/cheerio) 。
* JavaScriptを解析するための [Esprima](https://github.com/ariya/esprima) - あなたは、Esprima構文ツリーを編集するための低レベルAPIを提供する [AST-Query](https://github.com/SBoudrias/ast-query) に興味があるかもしれません。
* JSONファイルの場合は、 ネイティブの [`JSON` オブジェクトメソッド
(https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON) を使用できます。 
* Gruntfileを動的に修正するための [Gruntfile Editor](https://github.com/SBoudrias/gruntfile-editor) 。

RegExを使用してコードファイルを解析するのは危険なパスです。その前に、 [このCSの人類学的回答](http://stackoverflow.com/questions/1732348/regex-match-open-tags-except-xhtml-self-contained-tags#answer-1732454) を読み、RegExの解析の問題点を把握する必要があります。ASTツリーではなくRegExを使用して既存のファイルを編集する場合は、注意して完全な単体テストを行ってください。どうか、ユーザーのコードを破壊しないでください。
