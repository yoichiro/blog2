---
layout: actions_on_google
title: Build with Actions SDK "Overview"の日本語訳です
categories:
- actions on google
---
Actions on Googleでは、Dialogflowが提供するNLPやその他のリッチな機能を使ったGoogleアシスタント向けアプリの開発だけでなく、Actions SDKを使って、低レベルな（NLPを含むほとんどの処理を自前で）アプリ開発も行うことができます。例えば、コマンド型の簡単なリクエスト/レスポンス形式のアプリであったり、NLPを行うための機構を既に持っていてそれを使いたい場合は、Actions SDKを採用すると良いでしょう。

Actions on Googleのドキュメントは、Actions SDKを使ったアプリ開発の手順も、しっかりと掲載されています。以下は、[Build with Actions SDK - Overview](https://developers.google.com/actions/sdk/)の日本語訳です。

---

# アプリの基本

Actions on Googleでは、Googleアシスタントの機能をアクションで拡張できます。アクションでは、ユーザーは簡単なコマンドからいくつかのライトをオンにする、またはトリビアゲームをプレイするなどのより長い会話に及ぶ会話型インターフェイスを通じて、物事を成し遂げることができます。

Googleアシスタントを拡張するために、以下の手順を使ってアプリを構築します。

## Actions on Googleデベロッパープロジェクトを作成する

Actions on Google Developer Consoleに移動し、デベロッパープロジェクトを作成します。このプロジェクトでは、承認プロセスを通じてアプリに関するメタデータを定義し、アプリを管理および追跡することができます。プロジェクトを作成したら、すべてのアクションを定義するアクションパッケージを作成できます。

![](https://developers.google.com/actions/images/aog-project-apiai-actions.png)

## アクションパッケージにアクションを作成する

インテントをフルフィルメントにマッピングすることによって、アクションパッケージ内にアクションを作成します。アクションは、アプリの機能へのエントリポイントを定義し、インテントを処理するフルフィルメントに、アクションを説明するインテントをマッピングします。

たとえば、商品の購入、注文状況の確認、日々の取引を表示するためのアクションを含むアプリを作成したいとします。次のように言うことで作動されるインテントを定義できます。

* _"Ok Google, talk to My Favorite Shoe Store"_
* _"Ok Google, talk to My Favorite Shoe Store to buy some shoes"_
* _"Ok Google, talk to My Favorite Shoe Store to check on my order"_
* _"Ok Google, talk to My Favorite Shoe Store to show me today's deals"_

アクションパッケージは、次のようになります。

```json
{
  "actions": [
    {
      "name": "MAIN",
      "intent": {
        "name": "actions.intent.MAIN"
      },
      "fulfillment": {
        "conversationName": "sekai-app"
      }
    },
    {
      "name": "BUY",
      "intent": {
        "name": "com.example.sekai.BUY",
        "parameters": [{
          "name": "color",
          "type": "SchemaOrg_Color"
        }],
        "trigger": {
          "queryPatterns": [
            {"queryPattern": "find some $SchemaOrg_Color:color sneakers"},
            {"queryPattern": "buy some blue suede shoes"},
            {"queryPattern": "get running shoes" }
          ]
        }
      },
      "fulfillment": {
        "conversationName": "sekai-app"
      }
    },
    {
      "name": "ORDER_STATUS",
      "intent": {
        "name": "com.example.sekai.ORDER_STATUS",
        "trigger": {
          "queryPatterns": [
            {"queryPattern": "check on my order"},
            {"queryPattern": "see order updates"},
            {"queryPattern": "check where my order is" }
          ]
        }
      },
      "fulfillment": {
        "conversationName": "sekai-app"
      }
    },
    {
      "name": "DAILY_DEALS",
      "intent": {
        "name": "com.example.sekai.DAILY_DEALS",
        "trigger": {
          "queryPatterns": [
            {"queryPattern": "hear about daily deals"},
            {"queryPattern": "buying some daily deals"},
            {"queryPattern": "get today's deals" }
          ]
        }
      },
      "fulfillment": {
        "conversationName": "sekai-app"
      }
    }
  ],
  "conversations": {
    "sekai-app": {
      "name": "sekai-app",
      "url": "https://sekai.example.com/sekai-app"
    }
  }
}
```

## フルフィルメントの構築とデプロイ

あなたのアプリのアクションが呼び出されると、アクションを満たすためのユーザとの会話を始めるために、フルフィルメントが呼び出されます。

フルフィルメントへの要求ごとに、ユーザー入力をテキスト文字列として受け取ります。インテントを処理するには、通常、テキスト入力を解析して応答を返します。この前後のやりとりは、あなたのフルフィルメントがアクションを完了するのに十分な情報を得るまで何度も起こります。

## 承認のためにアプリを提出し、ユーザーに利用可能にする

![](https://developers.google.com/actions/images/aog-project-info.png)

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/sdk/)
