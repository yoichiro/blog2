---
layout: post
title: Cloud Functionsで一度デプロイした関数を更新する方法
categories:
- Google Cloud Functions
---
Google Cloud PlatformにあるCloud Functionsは非常に強力です。Node.JSベースのJavaScriptで書かれた関数をCloud Functionsにデプロイするだけで、
あら不思議、スケールするAPIのエンドポイントが出来上がってしまいます。

例えば、以下のようなコードを書いて、

```js
exports.helloGET = function helloGET (req, res) {
  res.send('Hello, World!');
};
```

これをgcloudコマンドでデプロイするだけで、

```bash
$ gcloud beta functions deploy helloGET --trigger-http
```

curlコマンドで、

```bash
$ curl https://[GCP_REGION]-[PROJECT_ID].cloudfunctions.net/helloGET
Hello, World!
```

と返事をしてくれるでしょう。本来はもっと準備が必要ですが、その手順は[ここ](https://cloud.google.com/functions/docs/quickstart?hl=ja)
に書いてあります。

# コードを変更した後の再デプロイをしても反映されない

さて、上記のコードを以下のように変更したとします。

```js
exports.helloGET = function helloGET (req, res) {
  res.send('Hello, Yoichiro!');
};
```

再度gcloudコマンドでデプロイします。

```bash
$ gcloud beta functions deploy helloGET --trigger-http
Deploying function (may take a while - up to 2 minutes)...done.                                                      
availableMemoryMb: 256
entryPoint: helloGET
・・・
status: ACTIVE
timeout: 60s
updateTime: '2018-01-17T11:24:53Z'
versionId: '2'
```

お、うまくいったぞ、と思ってアクセスしてみると、

```bash
$ curl https://[GCP_REGION]-[PROJECT_ID].cloudfunctions.net/helloGET
Hello, World!
```

コードの変更が反映されていないことが分かります。ダメじゃん。

# コードの修正を反映させるためには

この問題、gcloudコマンドにオプションを指定するだけで、解決します。具体的には、ソースコードの場所を教えてあげるための
`--source` オプションを付与すれば良いです。

```bash
$ gcloud beta functions deploy helloGET --source . --trigger-http
```

試してみると、

```bash
$ curl https://[GCP_REGION]-[PROJECT_ID].cloudfunctions.net/helloGET
Hello, Yoichiro!
```

きっと反映されていることがわかると思います。

# まとめ

StackOverflowの[この記事](https://stackoverflow.com/questions/47873446/how-do-i-update-google-cloud-function-source)の
存在に気がつくまで、Cloud Functionsのコンソールから削除して上げ直して・・・を繰り返していました。どう考えても不具合だと思うので、
`--source` オプションを指定せずとも更新されるように、早く直って欲しいですね。
