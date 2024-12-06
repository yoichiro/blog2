---
layout: actions_on_google
title: Build with Dialogflow "Overview"の日本語訳です
categories:
- actions on google
---
Googleアシスタント向けのアプリは、基本的にはActions SDKを使って開発を行います。しかし、Dialogflowを使うことで、直接Actions SDKを使わなくても、使いやすいIDEや自然言語理解（NLU）、そして機械学習などの追加機能が一通り揃った上で開発を行うことができるようになります。以下は、[Build with Dialogflow - Overview](https://developers.google.com/actions/dialogflow/)の日本語訳です。Dialogflowを使ったアプリ開発の概要が説明されています。

---

# 概要

Actions on Googleでは、 **アクション** を含むアシスタントアプリでGoogleアシスタントの機能を拡張することができます。アクションによって、ユーザーは、いくつかのライトをつける簡単な命令や、トリビアゲームをプレイするなどの、より長い会話に及ぶことが可能な会話型インターフェイスを通じて、物事を行うことができます。

<iframe class="devsite-embedded-youtube-video" data-autohide="1" data-showinfo="0" frameborder="0" allowfullscreen="" id="ytplayer0" data-enablejsapi="1" src="//www.youtube.com/embed/3NIopUsI4ik?autohide=1&amp;showinfo=0&amp;enablejsapi=1" origin="https://developers.google.com" style="border: 0;"></iframe>

Dialogflowは、[Actions SDK](https://developers.google.com/actions/develop/sdk)の機能をラッピングし、使いやすいIDE、自然言語理解（NLU）、機械学習などの追加機能を提供することで、アクションを設計して構築できる会話型プラットフォームです。

Googleアシスタントを拡張するには、次の手順でアプリを作成します。

## 会話をデザインする

開発する前に、会話や、アプリのユーザーインターフェイスを設計する必要があります。この会話では、ユーザーがあなたのアプリをどのように呼び出すか、あなたのアプリに言うことができる有効なこと、そしてあなたのアプリがそれらにどのように反応するかについて説明します。

[より詳しく学ぶ](https://developers.google.com/actions/dialogflow/design-conversation)

## Actions on GoogleデベロッパープロジェクトとDialogflowエージェントを作成する

Actions on Google Developer Consoleに移動し、デベロッパープロジェクトを作成します。このプロジェクトでは、承認プロセスを通じてアプリに関するメタデータを定義し、アプリを管理および追跡することができます。プロジェクトを作成したら、すべてのアクションを定義するDialogflowエージェントを作成することができます。

![](https://developers.google.com/actions/images/aog-project-apiai-actions.png)

[より詳しく学ぶ](https://developers.google.com/actions/dialogflow/project-agent)

## アクションを定義する

Dialogflowエージェントのアクションは、アプリの機能へのエントリポイントを定義するインテントを使って定義します。例えば、商品を購入したり、注文状況を確認したり、日々の取引を表示するアプリを構築したいとします。あなたは、次のように言うことで作動するインテントを定義することができます。

* _"Ok Google, talk to My Favorite Shoe Store to purchase some shoes" （オッケー、Google、靴を購入するためにお気に入りの靴店と話をします。）_
* _"Ok Google, talk to My Favorite Shoe Store to check on my order" （オッケー、Google、注文を確認するために、お気に入りの靴店と話をします。）_
* _"Ok Google, talk to My Favorite Shoe Store to show me today's deals" （オッケー、Google、今日のお得な情報を知るために、お気に入りの靴店と話をします。）_

[より詳しく学ぶ](https://developers.google.com/actions/dialogflow/define-actions)

## フルフィルメントの構築とデプロイ

あなたのアプリのアクションが呼び出されると、ユーザとの会話を始めるためにフルフィルメントが呼び出され、アクションが実行されます。

会話を作成するには、Dialogflowのインテントを定義して、会話の文法を作成するか、ユーザーがあなたのアクションに実際に言うことができることを作成します。ユーザーが実際にインテントを作動させる何かを発言すると、あなたのフルフィルメントが呼び出され、ユーザーにレスポンスが返されます。

この前後の要求と応答は、あなたのフルフィルメントがアクションを完了するのに十分な情報を持つまで繰り返されます。

[より詳しく学ぶ](https://developers.google.com/actions/dialogflow/fulfillment)

## 承認のためにアプリを提出し、ユーザーに利用可能にする

![](https://developers.google.com/actions/images/aog-project-info.png)

[より詳しく学ぶ](https://developers.google.com/actions/dialogflow/submit-app)

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/dialogflow/)
