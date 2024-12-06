---
layout: actions_on_google
title: Smart Home "Overview"の日本語訳です
categories:
- actions on google
---
Googleアシスタント向けアプリとして、会話側のアプリの他に、スマートホーム向けの各種デバイスを操作することを目的とした、昔「ダイレクトアクション」と
呼んでいたアプリの形態があります。今では「スマートホームアプリ」と呼びます。

Actions on Googleのドキュメントにある
[Smart Home - Overview](https://developers.google.com/actions/smarthome/)にて、
Smart Home向けアプリの概要が説明されています。以下はその日本語訳です。

が、以下を読んでも、正直よくわからないかもしれません。流れを把握した後に読むと「ああ、なるほどね」となるのですが。
流れをつかむために、 [Googleアシスタントを使ったスマートホーム連携](https://docs.google.com/document/d/1WR8zTadFaiYHkE-yyuLodQU8k8QgzaRgMRM9tk6yncU/edit#heading=h.72qrwxll3tkz) というコードラボがあるので、
ぜひ先にチャレンジしてから、再度ここに来ていただければと思います。

---
# はじめよう

ユーザーがGoogleアシスタントを使用してIoTデバイスを制御できるスマートホームアプリを構築できます。Smart Homeアプリを構築すると、既存のクラウドインフラストラクチャを通じてデバイスを接続、照会、制御することができます。

スマートホームアプリは、家庭とそのデバイスに関するコンテキストデータを格納して提供するデータベースであるHome Graphに依存しています。たとえば、Home Graphには、さまざまなメーカーの複数のタイプのデバイス（ライト、テレビ、およびスピーカー）を含むリビングルームのコンセプトを保存できます。この情報は、適切なコンテキストに基づいてユーザーのリクエストを実行するためにGoogleアシスタントに渡されます。

<iframe width="560" height="315" src="https://www.youtube.com/embed/XdZXpFBvTP8" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

GitHubからダウンロードまたはCloneすることで、Smart Homeアプリのエンドツーエンドのサンプルを入手できます。

[サンプルを入手する](https://github.com/actions-on-google/smart-home-nodejs)

## デバイスの同期

ユーザーはGoogleアシスタントアプリでデバイスを設定してクラウドサービスを認証し、アシスタントはOAuthトークンを受信します。この時点で、アシスタントはあなたのフルフィルメントに `action.devices.SYNC` インテントを送信して、クラウドインフラストラクチャからユーザーデバイスと機能の初期リストを取得します。

![](https://developers.google.com/actions/images/smarthome001.png){:width="800px"}<br>Figure 1 - 青色はGoogleのインフラストラクチャ、黄色はパートナーのインフラストラクチャ

`action.intent.REQUEST_SYNC` インテントを使用してGoogleアシスタントにリクエストを送信できます。このインテントは、 `action.devices.SYNC` インテントをあなたのフルフィルメントに送信して、デバイスと機能のリストを再度同期させます。

![](https://developers.google.com/actions/images/smarthome002.png){:width="420px"}<br>Figure 2

## コマンドの実行

ユーザーがデバイスにコマンドを送信するためにアシスタントに依頼すると、フルフィルメントはアクションとそのデバイスを説明するペイロードを含む `action.devices.EXECUTE` インテントを受け取ります。

![](https://developers.google.com/actions/images/smarthome003.png){:width="800px"}<br>Figure 3

## デバイスステータスのクエリ

デバイスステータスのクエリは、「キッチンに何のライトが点灯していますか？」といったユーザーの質問に答えるか、「明るさを上げる」のような相対的な輝度コマンドを処理することのいずれかで、アシスタントは `action.devices.QUERY` をあなたのフルフィルメントに向けて送信します。

![](https://developers.google.com/actions/images/query-diagram.png){:width="420px"}<br>Figure 4

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/smarthome/)
