---
layout: post
title: Azure Functions Node.jsでActions on Google Client Libraryを使う方法
categories:
- actions on google
---

もしあなたがAzure Functions Node.jsを普段使っている開発者であれば、Azure Functions Node.jsを使ってGoogleアシスタント向けのアクションを作ることが可能です。Googlは、Node.js向けにActions on Google Client Libraryを提供しています。

[actions-on-google/actions-on-google-nodejs](https://github.com/actions-on-google/actions-on-google-nodejs)

Azure Functions上でクライアントライブラリを使ってフルフィルメントコードを実装することができますが、いくつかのトリックが必要です。このエントリでは、Azure Functions Node.js環境にてActions on Google Client Libraryを使うための方法について紹介します。

# アーキテクチャ

以下のように、Dialogflowからのリクエストを処理するためのフルフィルメントコードをAzure Functions上で実装することを仮定します。

![]({{ "/images/2019/01/aog_client_library_azure_function_nodejs_01.webp" | prepend: site.baseurl }})

残念ながら、Azure Functions Node.jsは、 [express](https://www.npmjs.com/package/express) との互換性を持っていません。expressとActions on Google Client Libraryとの間を橋渡しするための何かを使う必要があります。しかし、良いニュースをあなたに伝えることができます。それらをブリッジするために、azure-function-expressライブラリを使うことが可能です。

[yvele/azure-function-express](https://github.com/yvele/azure-function-express)

# 事前準備

以下を準備する必要があります。

* すでにあなたのアカウントがAzureに登録されていること。
* すでに [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) がインストールされていて、Azureにサインインされていること。
* すでに [Azure Functions Core Tools](https://docs.microsoft.com/en-us/azure/azure-functions/functions-run-local#install-the-azure-functions-core-tools) がインストールされていること。
* Google Cloud PlatformもしくはAmazon Web Services上でのGoogleアシスタント向けアクションの構築方法についてすでに理解していること。
* Actions on Googleプロジェクトおよびそれに接続されているDialogflowエージェントをすでに持っていること。

もしアクションの構築方法を知らない場合は、コードラボを使って学習することができます。

[Build Actions for the Google Assistant (Level 1)](https://codelabs.developers.google.com/codelabs/actions-1/index.html#0)

[Build Actions for the Google Assistant (Level 2)](https://codelabs.developers.google.com/codelabs/actions-2/index.html#0)

# 関数の作成

では、Azureでの関数の作成を始めましょう。もしリソースグループとストレージをまだ持っていない場合は、以下のコマンドを使ってそれらを作成してください。

```bash
$ az group create --name <RESOURCE_GROUP_NAME> --location <LOCATION_NAME>
$ az storage account create --name <STORAGE_ACCOUNT_NAME> --resource-group <RESOURCE_GROUP_NAME> --location <LOCATION_NAME> --sku Standard_LRS
```

次に、以下のコマンドにて、関数を作成します。

```bash
$ az functionapp create --resource-group <RESOURCE_GROUP_NAME> --consumption-plan-location <LOCATION_NAME> --name <FUNCTION_NAME> --storage-account <STORAGE_ACCOUNT_NAME> --runtime node
$ func init <FUNCTIONAPP_NAME>
$ cd <FUNCTIONAPP_NAME>
$ func new --name <FUNCTION_NAME> --template "HttpTrigger"
```

# 依存ライブラリのインストール

関数の作成後、 `package.json` ファイルを作成するための、以下のコマンドを実行します。

```bash
$ cd <FUNCTION_NAME>
$ npm init
```

そのコマンドからいくつかの質問を尋ねられますが、基本的にすべての質問に ENTER キーを押して回答することができます。その後、以下のコマンドによって依存ライブラリをインストールします。

```bash
$ npm install --save actions-on-google express azure-function-express
```

`node_modules` が作成され、依存ライブラリがそのディレクトリ内にインストールされるでしょう。

# フルフィルメントコードの記述

上記のコマンドにて、 `<FUNCTION_NAME>` ディレクトリ内に `index.js` ファイルがすでに作成されています。 `index.js` ファイルの内容を以下のコードで置き換えます。

```js
const {dialogflow} = require('actions-on-google');
const express = require('express');
const {createHandler} = require('azure-function-express');

const app = dialogflow();

app.intent('Default Welcome Intent', conv => {
    conv.close('Hello, Azure!');
});

// Put other intent handlers here.

const expressApp = express();
expressApp.post('/api/<FUNCTION_NAME>', app);

module.exports = createHandler(expressApp);
```

また、テストのために、認証モードを `anonymous` に変更する必要があります。 `function.json` ファイルにて、 `authLevel` 値を `anonymous` に置き換えます。

```json
{
  ...
  "bindings": [
    {
      "authLevel": "anonymous",
      ...
    }
  ]
}
```

# 問題を避けるためのProxyの設定

残念なのですが、現在のAzure Functionsには、文字セットの処理に関する問題があります。そのために、上記のコードは機能しません。

[The charset of content-type request header is ignored #168](https://github.com/Azure/azure-functions-nodejs-worker/issues/168)

上記の問題に対する代替案として、強制的に `Accept-Charset` リクエストヘッダを上書きするためのプロキシを設定します。それをするために、 `<FUNCTIONAPP_NAME>` ディレクトリ内に新しい `proxies.json` を作成して、ファイル内に以下のコードを記載します。

```json
{
  "$schema": "http://json.schemastore.org/proxies",
  "proxies": {
    "proxy1": {
      "matchCondition": {
        "methods": [
          "POST"
        ],
        "route": "/api/<FUNCTION_NAME>"
      },
      "backendUri": "https://localhost/api/<FUNCTION_NAME>",
      "requestOverrides": {
        "backend.request.headers.Accept-Charset": "utf-8"
      }
    }
  }
}
```

このプロキシは、 `Accept-Charset` リクエストヘッダ値を "utf-8" のみに上書きします。結果として、関数が UTF-8 文字列を返すようになります。

もちろん、問題が解決されれば、このプロキシは必要なくなります。

# デプロイとテスト

最後に、以下のコマンドを使って、Azureにフルフィルメントをデプロイします。

```bash
$ func azure functionapp publish <FUNCTIONAPP_NAME>
```

デプロイ後、コマンドは関数のエンドポイントURLを表示します。そのURLをDialogflowエージェントのフルフィルメントWebhook URLとして登録します。その後、Actionsシミュレータ上でアクションを呼び出します。

# まとめ

この記事では、Azure Functions Node.js上でActions on Google Client Libraryを使う方法を紹介しました。残念ながら、問題があり、代替案（つまりプロキシの利用です）を使って問題を避けることが必要です。しかし、問題が解決されたあとであれば、プロキシを単純に削除することができます。

もしAzure Functionsをお使いでしたら、ぜひAzureでのGoogleアシスタント向けアクションの構築を試してみてください！
