---
layout: post
title: ジェネレータ実行コンテキスト - Yeomanジェネレータの作成
categories:
- yeoman
---

何かを作りたい際に、ゼロから自分で作っていくのは、単純に大変な作業です。何か土台的なものがあって、自分でやりたいことだけを追記していければ、それに越したことはありません。

[Yeoman](https://yeoman.io/) は、さまざまな土台を作ってくれるツールです。 `yo` というコマンドを打ち込んでプロジェクトの雛形を作成した経験のある方は多いと思いますが、まさに `yo` が Yeroman です。　`yo generator-name` と打ち込むことで、ジェネレータが呼び出され、対話形式でいくつかの質問に答えるだけで、土台となるファイル群を生成させることが可能です。

そのジェネレータは、自作することも可能です。僕も、とあるジェネレータを自分で作ってみたくなりました。Yeoman のウェブサイトには、じぇねれーたのつくりかたはが複数ページに渡って説明されています。勉強の意味も兼ねて、日本語訳を作ってみました。

今回は、 [Generator Runtime Context](https://yeoman.io/authoring/running-context.html) です。

---
ジェネレータを書くときに把握するべき最も重要な概念の1つは、メソッドがどのように実行されているか、そしてどのコンテキストで実行されているかです。

# アクションとしてのプロトタイプメソッド

Generatorのプロトタイプに直接関連付けられている各メソッドは、タスクと見なされます。各タスクは、Yeoman環境の実行ループによって順番に実行されます。

つまり、 `Object.getPrototypeOf(Generator)` によって返されるオブジェクトの各関数が、自動的に実行されます。

## ヘルパーメソッドとプライベートメソッド

プロトタイプメソッドはタスクと見なされることがわかったところで、自動的に呼び出されないヘルパーメソッドやプライベートメソッドを定義する方法について疑問に思うかもしれません。これを実現するには3つの方法があります。

(1) メソッド名の前にアンダースコアを付ける (例. `_private_method` )。

```js
class extends Generator {
  method1() {
    console.log('hey 1');
  }

  _private_method() {
    console.log('private hey');
  }
}
```

(2) インスタンスメソッドを使用する。

```js
class extends Generator {
  constructor(args, opts) {
    // Calling the super constructor is important so our generator is correctly set up
    super(args, opts)

    this.helperMethod = function () {
      console.log('won\'t be called automatically');
    };
  }
}
```

(3) 親のジェネレータを拡張する。

```js
class MyBase extends Generator {
  helper() {
    console.log('methods on the parent generator won\'t be called automatically');
  }
}

module.exports = class extends MyBase {
  exec() {
    this.helper();
  }
};
```

# 実行ループ

単一のジェネレータがあれば、タスクを順番に実行しても大丈夫です。しかし、あなたが複数のジェネレータを一緒に構成し始めたとしたら、それは十分ではありません。

Yeomanが **実行ループ** を使用している理由が、それです。

実行ループは、優先順位をサポートするキューシステムです。実行ループを処理するために,
 [Grouped-queue](https://github.com/SBoudrias/grouped-queue) モジュールを使用します。
 
優先順位は、コード内で特別なプロトタイプメソッド名として定義されます。メソッド名が優先順位名と同じ場合、実行ループはそのメソッドをこの特別なキューにプッシュします。メソッド名が優先順位と一致しない場合は、 `default` グループにプッシュされます。 

コードでは、このようになります。

```js
class extends Generator {
  priorityName() {}
}
```

単一のメソッドの代わりにハッシュを使用して、キュー内で複数のメソッドをまとめて実行するようにグループ化することもできます。

```js
Generator.extend({
  priorityName: {
    method() {},
    method2() {}
  }
});
```

(この最後のテクニックは、JSの `class` 定義とうまくいかないことに注意してください。)

(実行順序における)利用可能なプロパティは以下です、

1. `initializing` - あなたの初期化メソッド（現在のプロジェクトの状態の確認、設定の取得など）。
1. `prompting`  - ユーザにオプションの入力を促す場所（ `this.prompt()` を呼び出す場所）。
1. `configuring`  - 設定の保存とプロジェクトの設定（ `.editorconfig` ファイルと他のメタデータファイルの作成）。
1. `default` - メソッド名が優先順位と一致しない場合は、このグループにプッシュされます。
1. `writing` - ジェネレータ固有のファイル（ルート、コントローラなど）を書き込む場所。
1. `conflicts` - 競合が処理される場所（内部的に使用される）。
1. `install` - インストールが実行される場所（npm、bower）。
1. `end` - 最後に呼び出され、クリーンアップし、 _good bye_ と言うなど。

これらの優先順位のガイドラインに従うと、あなたのジェネレータは他のものとうまく動くでしょう。

# 非同期タスク

タスクが非同期的に作業を終了するまで実行ループを一時停止する方法は複数あります。

最も簡単な方法は、 **Promiseを返すこと** です。Promise が解決すると、ループは継続します。失敗した場合は、例外が発生して停止します。

あなたが依存している非同期APIがPromiseをサポートしていない場合は、レガシーな方法である `this.async()` に頼ることができます。 `this.async()` を呼び出すと、タスクが完了した後に呼び出す関数が返されます。例えば、以下です。

```js
asyncTask() {
  var done = this.async();

  getUserEmail(function (err, name) {
    done(err);
  });
}
```

`done` 関数がエラーパラメータを伴って呼び出された場合、実行ループは停止し、例外が発生します。

# ここからどこに行きますか？

あなたはYeomanの実行コンテキストについて知ることができました。 [ユーザーインタラクション](https://www.eisbahn.jp/yoichiro/2019/03/yeoman_generator_user_interactions.html) に進むことができます。
