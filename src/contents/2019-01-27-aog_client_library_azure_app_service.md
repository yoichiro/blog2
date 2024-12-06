---
layout: post
title: Azure App ServiceでActions on Google Client Libraryを使う方法
categories:
- actions on google
---

以前のエントリにて、Azure Functions Node.jsでActions on Google Client Libraryの使い方を紹介しました。

[Azure Functions Node.jsでActions on Google Client Libraryを使う方法](https://medium.com/@yoichiro/how-to-use-actions-on-google-client-library-on-azure-functions-node-js-178d5ecd04e3)

実際には、Azure上でアクションを構築するもう一つの方法があります。それは、Azure App Serviceです。

[Azure App Service Documentation - Tutorial, API Reference](https://docs.microsoft.com/en-us/azure/app-service/)

You can deploy your express app into the Azure App Service without any touches, basically. Actions on Google Client Library can be used with the express.
基本的に、expressアプリを何も手を加えずに、Azure App Serviceにデプロイすることができます。Actions on Google Client Libraryは、expressで利用することが可能です。

[express](https://www.npmjs.com/package/express)

[actions-on-google/actions-on-google-nodejs](https://github.com/actions-on-google/actions-on-google-nodejs)

この記事では、Azure App Service上でActions on Google Client Libraryを使うための方法を紹介します。

# アーキテクチャ

以下のように、Dialogflowからのリクエストを処理するフルフィルメントコードをAzure App Serviceで実装したいと仮定します。

![]({{ "/images/2019/01/aog_client_library_azure_app_service_01.webp" | prepend: site.baseurl }})

# 事前準備

以下の準備が必要です。

* Azureに登録されたアカウントを持っていること。
* Azure CLIがすでにインストールされていて、Azureにサインインしていること。
* Google Cloud PlatformとAmazon Web Service上でGoogleアシスタント向けのアクションを構築する方法をすでに理解していること。
* Actions on Googleプロジェクトと、それに接続されたDialogflowエージェントをすでに持っていること。

もしアクションの構築方法を知らない場合は、コードラボにて学習することが可能です。

[Build Actions for the Google Assistant (Level 1)](https://codelabs.developers.google.com/codelabs/actions-1/index.html#0)

[Build Actions for the Google Assistant (Level 2)](https://codelabs.developers.google.com/codelabs/actions-2/index.html#0)

# App Serviceの作成

では、AzureでのApp Serviceの作成を始めましょう。もしまだリソースグループを持っていない場合は、以下のコマンドを使って作成してください。

```bash
$ az group create --name <RESOURCE_GROUP_NAME> --location <LOCATION_NAME>
```

次に、以下のコマンドを使ってApp Serviceを作成します。ここでは、フリープランを使います。

```bash
$ az appservice plan create --name <PLAN_NAME> --resource-group <RESOURCE_GROUP_NAME> --sku FREE
$ az webapp create --resource-group <RESOURCE_GROUP_NAME> --plan <PLAN_NAME> --name <APP_NAME>
```

Actions on Google Client Libraryが機能するために、Node.js環境を準備する必要があります。以下のコマンドにて、App ServiceにNode.jsバージョンをセットしてください。

```bash
$ az webapp config appsettings set --resource-group <RESOURCE_GROUP_NAME> --name <APP_NAME> --settings WEBSITE_NODE_DEFAULT_VERSION=8.11.1
```

# Node.jsプロジェクトの作成と依存ライブラリのインストール

Actions on Google Client Libraryを使って、アクションを作ることができます。それをAzure App Serviceにデプロイするためには、HTTPリクエストとレスポンスを処理するためのexpressを使うことが必要です。

最初に、 `npm` コマンドを使って、Node.jsプロジェクトを作成します。

```bash
$ mkdir <APP_NAME>
$ cd <APP_NAME>
$ npm init
```

そのコマンドからいくつかの質問を聞かれますが、基本的にすべての質問に対して EMTER キーを押して答えることができます。その後、以下のコマンドにて依存ライブラリをインストールします。

```bash
$ npm install --save actions-on-google express
```

`node_modules` が作られ、そのディレクトリ内に依存ライブラリがインストールされます。

# フルフィルメントコードの記述

では、フルフィルメントコードを書き始めましょう。 `index.js` という名前の新しいファイルを作成し、以下の内容で記述してください。

```js
const express = require('express');
const bodyParser = require('body-parser');
const {dialogflow} = require('actions-on-google');

const port = process.env.PORT || 4567;

const app = dialogflow();

app.intent('Default Welcome Intent', conv => {
    conv.close('Hello, Azure!');
});

const expressApp = express().use(bodyParser.json());
expressApp.post('/', app);

expressApp.listen(port);
```

# Web Configファイルの作成

App Serviceは、"Microsoft Internet Information Services (IIS)"と呼ばれるウェブサーバを持っています。そして、フルフィルメントコードは、Node.jsで動作します。IISは、nodeプロセスにリクエストを委譲しなければなりません。つまり、 `web.config` という名前のWeb Configファイルを作成しなければなりません。これは、XML書式で書かれたIISのための設定ファイルです。

"web.config"という名前の新しいファイルを作成して、以下の内容を記述してください。

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <webSocket enabled="false" />
    <handlers>
      <add name="iisnode" path="index.js" verb="*" modules="iisnode" />
    </handlers>
    <rewrite>
      <rules>
        <rule name="DynamicContent">
          <conditions>
            <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="True" />
          </conditions>
          <action type="Rewrite" url="index.js" />
        </rule>
      </rules>
    </rewrite>
    <security>
      <requestFiltering>
        <hiddenSegments>
          <remove segment="bin" />
        </hiddenSegments>
      </requestFiltering>
    </security>
    <httpErrors existingResponse="PassThrough" />
  </system.webServer>
</configuration>
```

# パック、デプロイ、そしてテスト

これで、Azure App Serviceにコードをパックしてデプロイすることができます。最初に、以下のコマンドを実行して、ファイルをパックします。

```bash
$ zip -r files.zip .
```

その後、Azure App Serviceにzipファイルをデプロイします。

```bash
$ az webapp deployment source config-zip --resource-group <RESOURCE_GROUP_NAME> --name <APP_NAME> --src <ZIP_FILE_PATH>
```

デプロイに成功した場合、フルフィルメントWebhookエンドポイントのURLが以下になります。

```txt
https://<APP_NAME>.azurewebsites.net/
```

DialogflowエージェントにフルフィルメントWebhook URLとして登録します。その後、Actionsシミュレータ上でアクションを呼び出します。

# まとめ

この記事では、Azure App Service上でActions on Google Client Libraryを使うための方法を紹介しました。もしAzure App Serviceをお使いの場合は、ぜひAzure上でのGoogleアシスタント向けアクションの開発を試してみてください！
