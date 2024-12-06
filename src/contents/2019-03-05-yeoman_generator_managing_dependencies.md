---
layout: post
title: 依存関係管理 - Yeomanジェネレータの作成
categories:
- yeoman
---

何かを作りたい際に、ゼロから自分で作っていくのは、単純に大変な作業です。何か土台的なものがあって、自分でやりたいことだけを追記していければ、それに越したことはありません。

[Yeoman](https://yeoman.io/) は、さまざまな土台を作ってくれるツールです。 `yo` というコマンドを打ち込んでプロジェクトの雛形を作成した経験のある方は多いと思いますが、まさに `yo` が Yeroman です。　`yo generator-name` と打ち込むことで、ジェネレータが呼び出され、対話形式でいくつかの質問に答えるだけで、土台となるファイル群を生成させることが可能です。

そのジェネレータは、自作することも可能です。僕も、とあるジェネレータを自分で作ってみたくなりました。Yeoman のウェブサイトには、じぇねれーたのつくりかたはが複数ページに渡って説明されています。勉強の意味も兼ねて、日本語訳を作ってみました。

今回は、 [Managing dependencies](https://yeoman.io/authoring/dependencies.html) です。

---

ジェネレータを実行したら、ジェネレータに必要な追加の依存関係をインストールするために、 [npm](https://www.npmjs.com/) （または [Yarn](https://yarnpkg.com/) ）と [Bower](http://bower.io/) を実行したいことがよくあります。

これらの作業は非常に頻繁に行われるので、Yeomanはすでにそれらを抽象化しています。他のツールからインストールを開始する方法についても説明します。

Yeomanが提供するインストールヘルパーは、 `install` キューの一部として自動的に1回実行するようにインストールをスケジュールします。実行後に何かを実行する必要がある場合は、 `end` キューを使用してください。

# npm

`npm` インストールを実行するには、 `this.npmInstall()` を呼び出すだけです。Yeomanは、複数のジェネレーターによって複数回呼び出されたとしても、 `npm install` コマンドが一度だけ実行されることを保証します。例えば、あなたが dev dependency として lodash をインストールしたいとします。

```js
class extends Generator {
  installingLodash() {
    this.npmInstall(['lodash'], { 'save-dev': true });
  }
}
```

これは、プロジェクトのコマンドラインでの以下の呼び出しと等価です。

```bash
npm install lodash --save-dev
```

## npm依存関係をプログラムで管理する

テンプレートを使用したくないが、依存関係のバージョンを修正したいような場合は、 `package.json` ファイルをプログラムで作成または拡張することができます。Yeomansファイルシステムツールは、この仕事を行うのに役に立ちます。

dev dependency として `eslint` を定義し、 `react` を dependency として定義する例が以下です。

```js
class extends Generator {
  writing() {
    const pkgJson = {
      devDependencies: {
        eslint: '^3.15.0'
      },
      dependencies: {
        react: '^16.2.0'
      }
    };

    // Extend or create package.json file in destination path
    this.fs.extendJSON(this.destinationPath('package.json'), pkgJson);
  }

  install() {
    this.npmInstall();
  }
};
```

# Yarn

インストールを開始するには、 `this.yarnInstall()` を呼び出すだけです。Yeomanは、複数のジェネレータによって複数回呼び出されても、 `yarn install` コマンドが1回だけ実行されるようにします。例えば、あなたが dev dependency として lodash をインストールしたいとします。

```js
generators.Base.extend({
  installingLodash: function() {
    this.yarnInstall(['lodash'], { 'dev': true });
  }
});
```

これは、プロジェクトのコマンドラインでの以下の呼び出しと等価です。

```bash
yarn add lodash --dev
```

# Bower

インストールを開始するには、 `this.bowerInstall()` を呼び出すだけです。Yeomanは、複数のジェネレータによって複数回呼び出されたとしても、 `bower install` コマンドが一度だけ実行されることを保証します。

# 組み合わせて利用する

`this.installDependencies()` を呼び出すと、デフォルトで npm と bower が実行されます。それぞれのパッケージマネージャにブール値を渡すことで、どちらを使うかを決めることができます。

Yarn と Bower を使う例です。

```js
generators.Base.extend({
  install: function () {
    this.installDependencies({
      npm: false,
      bower: true,
      yarn: true
    });
  }
});
```

# 他のツールを利用する

Yeomanは、ユーザーが任意のCLIコマンドを `spawn` できるように抽象化を提供します。この抽象化は、Linux、Mac、およびWindowsシステムでシームレスに実行できるように、コマンドは正規化されます。

例えば、あなたがPHP愛好家で、 `composer` を実行したいのであれば、次のように書いてください。

```js
class extends Generator {
  install() {
    this.spawnCommand('composer', ['install']);
  }
}
```

`install` キュー内で `spawnCommand` メソッドを呼び出すようにしてください。ユーザーはインストールコマンドが完了するのを待ちたくありません。
