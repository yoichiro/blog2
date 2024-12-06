---
layout: post
title: コンポーサビリティ - Yeomanジェネレータの作成
categories:
- yeoman
---

何かを作りたい際に、ゼロから自分で作っていくのは、単純に大変な作業です。何か土台的なものがあって、自分でやりたいことだけを追記していければ、それに越したことはありません。

[Yeoman](https://yeoman.io/) は、さまざまな土台を作ってくれるツールです。 `yo` というコマンドを打ち込んでプロジェクトの雛形を作成した経験のある方は多いと思いますが、まさに `yo` が Yeroman です。　`yo generator-name` と打ち込むことで、ジェネレータが呼び出され、対話形式でいくつかの質問に答えるだけで、土台となるファイル群を生成させることが可能です。

そのジェネレータは、自作することも可能です。僕も、とあるジェネレータを自分で作ってみたくなりました。Yeoman のウェブサイトには、じぇねれーたのつくりかたはが複数ページに渡って説明されています。勉強の意味も兼ねて、日本語訳を作ってみました。

今回は、 [Composability](https://yeoman.io/authoring/composability.html) です。

---

> コンポーサビリティは、小さい部分を組み合わせて1つの大きなものを作る方法です。いわば、 [Voltron®](http://25.media.tumblr.com/tumblr_m1zllfCJV21r8gq9go11_250.gif) のようなものです。

Yeomanは、ジェネレータを共通の基盤の上に構築するための複数の方法を提供しています。同じ機能を再度書く意味はありませんので、他のジェネレータ内でジェネレータを使用するためのAPIが提供されています。

Yeomanでは、コンポーサビリティは2つの方法で開始することができます。

* ジェネレータはそれ自身を他のジェネレータと合成することを決めることができます（例えば、 `generator-backbone` は `generator-mocha` を使用します）。
* エンドユーザーも合成を開始できます（例えば、Simon は SASS と Rails を使用して Backbone プロジェクトを生成したい）。注: エンドユーザーが開始した合成は計画された機能であり、現在使用できません。

# `this.composeWith()`

`composeWith` メソッドを使用すると、ジェネレータを別のジェネレータ（またはサブジェネレータ）と並べて実行できます。つまり、それ自身で全てのことをしなければならない代わりに、他のジェネレータから得られる機能を使うことができます。

合成するときは、 [実行中のコンテキストと実行ループ](https://yeoman.io/authoring/running-context.html) について忘れないでください。与えられた優先グループの実行時には、すべての合成されたジェネレータがそのグループ内の機能を実行します。その後、これは次のグループでも繰り返されます。ジェネレータ間の実行は、 `composeWith` が呼び出されたのと同じ順序です。 [実行例](https://yeoman.io/authoring/composability.html#order) を参照してください。

## API

`composeWith` は2つのパラメータを受け取ります。

1. `generatorPath` 合成したいジェネレータを指すフルパス（通常は `require.resolve()` を使用）。
1. `options` 実行されたときに合成ジェネレータに渡すオプションを含むオブジェクト。

`peerDependencies` ジェネレータと合成する際には、以下となります。

```js
this.composeWith(require.resolve('generator-bootstrap/generators/app'), {preprocessor: 'sass'});
```

`require.resolve()` は、Node.jsが提供されたモジュールをロードする場所からのパスを返します。

注: 1.0よりも古いバージョンの `yeoman-generator` に基づいてGeneratorに `arguments` を渡す必要がある場合は、 `options.arguments` キーとして `Array` を指定することでそれを実行できます。

これは推奨される方法ではありませんが、 `composeWith` にジェネレータ名前空間を渡すこともできます。その場合、Yeomanはそのジェネレータが `peerDependencies` としてインストールされているか、グローバルにエンドユーザーシステムにインストールされているかを検索します。

```js
this.composeWith('backbone:route', {rjs: true});
```

## Generatorクラスとの合成

`composeWith` は、最初の引数としてオブジェクトを使用することもできます。オブジェクトには、次のプロパティを定義する必要があります。

* `Generator` - 合成するジェネレータクラス。
* `path` - ジェネレータファイルへのパス。

これにより、プロジェクトで定義されたジェネレータクラス、または他のモジュールからインポートされたジェネレータクラスで構成できます。 `composeWith` の2番目の引数として `options` を渡すと、期待どおりに機能します。

```js
// Import generator-node's main generator
const NodeGenerator = require('generator-node/generators/app/index.js');

// Compose with it
this.composeWith({
  Generator: NodeGenerator,
  path: require.resolve('generator-node/generators/app')
});
```

## 実行例

```js
// In my-generator/generators/turbo/index.js
module.exports = class extends Generator {
  prompting() {
    this.log('prompting - turbo');
  }

  writing() {
    this.log('writing - turbo');
  }
};

// In my-generator/generators/electric/index.js
module.exports = class extends Generator {
  prompting() {
    this.log('prompting - zap');
  }

  writing() {
    this.log('writing - zap');
  }
};

// In my-generator/generators/app/index.js
module.exports = class extends Generator {
  initializing() {
    this.composeWith(require.resolve('../turbo'));
    this.composeWith(require.resolve('../electric'));
  }
};
```

`yo my-generator` を実行すると、次のようになります。

```bash
prompting - turbo
prompting - zap
writing - turbo
writing - zap
```

`composeWith` の呼び出しを逆にすることで、関数呼び出しの順序を変えることができます。

npm で利用可能な他の公開されているジェネレータも合成できることを覚えておいてください。

より複雑な合成可能性の例としては、 [generator-node](https://github.com/yeoman/generator-node) と合成されている [generator-generator](https://github.com/yeoman/generator-generator/blob/master/app/index.js) を見てください。

## dependencies または peerDependencies

_npm_ は3種類の依存関係を許容します。

* `dependencies` はジェネレータのローカルにインストールされます。使用される依存関係のバージョンを制御するのが最善の選択肢です。これは推奨オプションです。
* `peerDependencies` は、兄弟としてジェネレータと一緒にインストールされます。例えば、もし `generator-backbone` が `generator-gruntfile` をピア依存関係として宣言した場合、フォルダツリーは次のようになります。

```text
 ├───generator-backbone/
 └───generator-gruntfile/
```

* `devDependencies` はテストや開発のためのユーティリティです。これはここでは必要ではありません。

`peerDependencies` を使用するときは、他のモジュールも要求されたモジュールを必要とする可能性があることに注意してください。特定のバージョン（または狭い範囲のバージョン）を要求してバージョンの競合を引き起こさないように注意してください。Yeomanによる `peerDependencies` の推奨事項は、常に以上（>=）または利用可能なすべてのバージョン（*）を要求することです。例えば以下となります。

```json
{
  "peerDependencies": {
    "generator-gruntfile": "*",
    "generator-bootstrap": ">=1.0.0"
  }
}
```

**注:** npm@3 以降、 `peerDependencies` は自動的にインストールされなくなりました。これらの依存関係をインストールするには、手動でインストールする必要があります。 `npm install generator-yourgenerator generator-gruntfile generator-bootstrap@">=1.0.0"`
