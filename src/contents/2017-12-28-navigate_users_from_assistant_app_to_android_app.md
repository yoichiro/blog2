---
layout: post
title: GoogleアシスタントアプリからAndroidアプリを起動する方法
categories:
- actions on google
---
2017年12月19日に、Android開発者向けにGoogle Play APP DOJOというイベントが行われました。これは毎月行われているイベントです。

[Google Play APP DOJO 案内ページ](https://events.withgoogle.com/appdojojp/)

今回のテーマは、「Android x Dialogflow/Actions on Google」ということで、最近僕が没頭していることが内容でした。講師として
僕にもお声がかかり、資料などを準備していたのですが、残念ながら家族がインフルエンザにかかってしまうという大事件がおきまして、参加
は実現しませんでした。

せっかくなので、僕が発表するはずだった内容をここで紹介しておきたいと思います。

# アシスタントアプリからAndroidアプリを起動する方法

Android開発者向けのイベントということで、やはり何かAndroidアプリとの連携を取り上げた方が良いかな、と思って、考えたことが、
「アシスタントアプリからAndroidアプリを起動する方法」です。以下が資料となります。

<iframe src="https://docs.google.com/presentation/d/e/2PACX-1vSgkoLgYEY6QGe3YwdgceZljCy3hqqVTUWrXE3drLksAiZpCwHVLjTVNhQzzfq3rXfijUSoYZu1wdDR/embed?start=false&loop=false&delayms=3000" frameborder="0" width="960" height="569" allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true"></iframe>

ぜひ、スピーカーノートも開いて上記資料を見てください。僕がこの資料で話すつもりだったことを全て書いてあります。

簡単にポイントをあげるとするならば、以下となります。

* GoogleアシスタントにはSurfaceと呼ばれる「音声のみ、画面のみ、両方」の区別がある。
* Fulfillmentにて、ユーザが使っているSurfaceの状況と、ユーザが持っているSurfaceの状況を確認することができる。
* 画面をサポートするSurface向けに、Rich Responseという画像やリンクを伴う応答を送ることができる。
* そのリンクを上手く使えば、アシスタントアプリからAndroidアプリにユーザを誘導することができる。

これによって、アシスタントアプリの可能性はかなり広がるのではないか、と思っています。

# コードラボ: Webhookを使った初めてのアシスタント向けアプリ - ビットコイン情報

あと、ビットコインの情報を外部API使って取得して、それを教えてくれるアシスタントアプリの作り方を説明したコードラボの教材を
日本語訳してみました。

[Webhookを使った初めてのアシスタント向けアプリ - ビットコイン情報](https://docs.google.com/document/d/1v8fTgutyJ3X5i3gW9FrRlJo9t8X9hUtTwabZx4CYhrE/edit?usp=sharing)

これを一通りやると、Actions on Google, Dialogflow, Firebase Cloud FunctionsでのFulfillmentの開発、といった手順を
学ぶことができます。ぜひチャレンジしてみてください。
