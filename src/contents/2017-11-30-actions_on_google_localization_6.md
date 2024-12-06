---
layout: actions_on_google
title: Localization "Fulfillment"の日本語訳です
categories:
- actions on google
---
Googleアシスタントは、既に複数の言語をサポートしています。その中には、当然英語と日本語も含まれています。Actions on GoogleによるGoogleアシスタント向けアプリの開発では、同時に複数の言語に対応したアプリ開発をすることが可能です。ユーザに返事を返すのは主にフルフィルメントの役目ですので、フルフィルメントの実装には国際化のための実装が含まれることになります。

Actions on Googleのドキュメントにある[Localization - Fulfillment](https://developers.google.com/actions/localization/fulfillment)にて、それらの手順が説明されています。以下はその日本語訳です。

---
# フルフィルメント

フルフィルメントのロジックでは、各リクエストに応じて受け取ったロケール文字列を使用して、ユーザーに応答することができます。このガイドでは、FirebaseのCloud Function内でサードパーティの国際化ライブラリを使用して、ローカライズされた応答を返す方法を説明します。

## 国際化ライブラリ

特定のロケールに対するカスタマイズされた応答を生成するのに役立つライブラリをいくつかご紹介します。

* 汎用: [I18n-node](https://github.com/mashpie/i18n-node)（このサンプルコードでは、このライブラリを使用しています）
* 汎用: [format.js](https://formatjs.io/)
* タイムゾーン/時間の国際化: [moment.js](https://momentjs.com/)（サンプルコードスニペットでは、このライブラリを使用しています）
* お金/通貨: [numeral.js](http://numeraljs.com/)

## ローカライズされた応答を作成する

このセクションでは、ローカライズされた文字列を含むローカライズ文字列リソースファイルを作成する方法と、これらのリソースファイルをFirebaseのCloud Function内のフルフィルメントで使用する方法について説明します。

ローカライズされた応答を作成するには:

1. `package.json` および `index.js` ファイルと同じディレクトリに、ローカライズされた文字列のファイル向けの `locales` ディレクトリを作成します。このディレクトリを `<project-dir>/functions/locales` と呼びます。

1. サポートするロケールごとに、ローカライズされた文字列を含むリソースファイルを作成します。たとえば、 `en-US` 、`en-GB` 、および `de-DE` ロケールをローカライズされたウェルカムメッセージや日付メッセージでサポートする場合、それらのファイルは次のようになります。<br>
 `<project-dir>/functions/locales/en-US.json` <br>
 ```json
{
  "WELCOME_BASIC": "Hello, welcome!",
  "DATE": "The date is %s"
}
 ```
 `<project-dir>/functions/locales/en-AU.json` <br>
 ```json
{
  "WELCOME_BASIC": "Hello, welcome!",
  "DATE": "The date is %s"
}
 ```
 `<project-dir>/functions/locales/de-DE.json` <br>
 ```json
{
  "WELCOME_BASIC": "Hallo und wilkommen!",
  "DATE": "Das Datum ist %s"
}
 ```

1. `package.json` ファイルで、i18n-node と moment ライブラリを依存関係として宣言します。<br>
 ```json
{
  ...,
  "dependencies": {
      "actions-on-google": "^1.0.0",
      "firebase-admin": "^4.2.1",
      "firebase-functions": "^0.5.7",
      "i18n": "^0.8.3",
      "moment": "^2.18.1"
  }
}
 ```

1. `index.js` ファイル内で、i18n-node と moment ライブラリの依存関係を宣言します。<br>
 ```javascript
const i18n = require('i18n');
const moment = require('moment');
 ```

1. `index.js` ファイル内で、サポートされるロケールを持つ i18n-node を設定します。<br>
 ```javascript
i18n.configure({
  locales: ['en-US', 'en-GB', 'de-DE'],
  directory: __dirname + '/locales',
  defaultLocale: 'en-US'
});
 ```

1. `getUserLocale()` クライアントライブラリ関数を使って、ライブラリ向けのロケールをセットします。<br>
 ```javascript
i18n.setLocale(app.getUserLocale());
moment.locale(app.getUserLocale());
 ```

1. ローカライズされたレスポンスを返すために、i18n によって返されたローカライズ済み文字列を使って `ask()` を呼び出します。このスニペットはまた、ローカライズされたオブジェクトを返すために moment を使う関数も含みます。<br>
     ```javascript
   function welcome (app) {
     app.ask(i18n.__('WELCOME_BASIC'));
   }
    
   function date (app) {
     app.ask(i18n.__('DATE', moment().format('LL')));
   }
     ```

例として、完全な index.js ファイルが以下となります。

```javascript
'use strict';

process.env.DEBUG = 'actions-on-google:*';
const App = require('actions-on-google').DialogflowApp;
const i18n = require('i18n');
const moment = require('moment');
const functions = require('firebase-functions');

exports.myAssistantApp = functions.https.onRequest((request, response) => {
 const app = new App({request, response});
 console.log('Request headers: ' + JSON.stringify(request.headers));
 console.log('Request body: ' + JSON.stringify(request.body));

 i18n.configure({
   locales: ['en-US', 'en-GB', 'de-DE'],
   directory: __dirname + '/locales',
   defaultLocale: 'en-US'
 });
 i18n.setLocale(app.getUserLocale());
 moment.locale(app.getUserLocale());

 function welcome (app) {
   app.ask(i18n.__('WELCOME_BASIC'));
 }

 function date (app) {
   app.ask(i18n.__('DATE', moment().format('LL')));
 }

 let actionMap = new Map();
 actionMap.set('input.welcome', welcome);
 actionMap.set('date', date);

 app.handleRequest(actionMap);
});
```

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/localization/fulfillment)
