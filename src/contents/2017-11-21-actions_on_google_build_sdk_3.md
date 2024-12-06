---
layout: actions_on_google
title: Build with Actions SDK "Define Actions"の日本語訳です
categories:
- actions on google
---
[プロジェクトとアクションパッケージの作成](https://www.eisbahn.jp/yoichiro/2017/11/actions_on_google_build_sdk_2.html)が終わった後は、具体的なアクションを定義していきます。アクションは、ユーザがアプリをアプリ名によって呼び出した際に起動されるデフォルトアクションや、付帯情報（○○を使った△△と話す、のような）を伴ったアクションの呼び出し（具体的な、が転じて「深い（Deep）」という表現が使われます）、つまりディープリンクを行うためにアクションを追加することもできます。

Actions on Googleのドキュメントにある、[Define Actions](https://developers.google.com/actions/sdk/define-actions)では、Actions SDKを使ったGoogleアシスタント向けアプリでのアクションの定義方法が説明されています。以下は、その日本語訳です。

---

# アクションを定義する

アクションは、あなたのアプリ向けの呼び出しと発見を定義する、あなたのアプリへのエントリポイントです。あなたは、アクションパッケージと呼ばれるJSONファイルにてアクションを宣言します。テストや、承認のための提出を行いたい際には、最終的に、あなたはそのJSONファイルをデベロッパープロジェクトにアップロードすることになります。

アクションパッケージでアクションを定義するには、アクションの呼び出し方法を定義するインテントと、インテントが作動されたとき向けに対応するフルフィルメントのエンドポイントを作成します。次のタイプのアクションを作成できます。

* **デフォルトのアクション:** すべてのアプリには、ユーザーが名前でアプリをリクエストしたとき（たとえば、 _"Ok Google, talk to Sekai"_ ）に、Actions on Googleが作動させるデフォルトのアクションが1つだけ必要です。このアクションは、 `actions.intent.MAIN` インテントのサポートを宣言します。
* **アプリにディープリンクするための追加のアクション:** 自分で定義したインテントを使って、アクションパッケージに追加のアクションを作成することができます。これらのインテントは、アプリ内で「ディープリンク」アクションを引き起こし、ユーザーは "Ok Google, talk to Sekai to find some sneakers" のように、特定の機能を呼び出すことができます。

これらの呼び出しモデルの仕組みの詳細については、[Invocation and Discovery](https://developers.google.com/actions/discovery/)を参照してください。

## デフォルトアクションの定義

すべてのアクションパッケージには、 `actions.intent.MAIN` インテントを処理する唯一のインテントが1つ必要です。このインテントは、ユーザーがアプリを名前で呼び出したときに作動されます（例えば、"Ok Google, talk to Sekai"）。

> **注:** 承認のためにアクションパッケージを提出するときには、Actions on Googleプロジェクト内でアプリ名を指定します。テスト中は、"talk to my test app" と言ってアプリを起動します。

```json
{
  "actions": [
    {
      "name": "MAIN",
      "intent": {
        "name": "actions.intent.MAIN"
      },
      "fulfillment": {
        "conversationName": "sekaiApp"
      }
    }
  ],
  "conversations": {
    "sekaiApp": {
      "name": "sekaiApp",
      "url": "https://sekai.example.com/sekaiApp"
    }
  }
}
```

## 追加のアクションを定義する

アプリにエントリポイントとして機能する追加のアクションを提供することができます。これにより、ユーザーは自分が何をしたいのかを詳細に指定できるようになり（例えば、 _"talk to Sekai to find me some sneakers"_ ）、意図を明確にすることができます。

追加アクションを定義するには:

1. `actions` 配列で、アプリへのすべてのエントリポイントのアクションを指定します。たとえば、次のサンプルは、以下を定義する追加の "buy" アクションを示しています。
 * `com.example.sekai.BUY` のインテント名
 * `parameters` このインテントが作動されたときに、ユーザー入力から解析するためのパラメータ。これは、ユーザーがアプリを起動したときに、アクションフレーズの特定のデータが必要な場合に便利です。
 * `queryPatterns` ユーザーがインテントを作動させるために何を言う必要があるかを定義します。解析するパラメータを定義する[Schema.org types](https://developers.google.com/actions/reference/rest/Shared.Types/QueryPatterns)を含めることができます。
   ```json
   {
     "actions": [
         ...,
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
             "conversationName": "sekaiApp"
           }
         }
     ]
   }
   ```
 > **注:** 各インテントに対して、約10個の **queryPatterns** フレーズを使用することをお勧めします。これらのクエリパターンは、Googleによってアクションのランキングと発見に使用されます。それらは、アプリの機能を表現すべきであり、ユーザがアクションを呼び出す方法を示す必要があります。
1. `conversations` オブジェクト内の項目に対応する `conversationName` を指定して、このインテントのフルフィルメントを指定します。
   ```json
   {
     "actions":[
       ...,
     ],
     "conversations": {
       "sekaiApp": {
         "name": "sekaiApp",
         "url": "https://sekai.example.com/sekaiApp",
         "fulfillmentApiVersion": 2
       }
     }
   }
   ```
 > **注:** 典型的には、フルフィルメントのエンドポイントが一つだけ必要になるでしょう。しかし、アクションパッケージは複数サポートします。**fulfillmentApiVersion** として **2** を指定する必要があります。

全てを満たしたアクションパッケージの例が以下です。

```json
{
  "actions": [
    {
     "name": "MAIN",
     "intent": {
       "name": "actions.intent.MAIN"
     },
     "fulfillment": {
       "conversationName": "sekaiApp"
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
        "conversationName": "sekaiApp"
      }
    }
  ],
  "conversations": {
    "sekaiApp": {
      "name": "sekaiApp",
      "url": "https://sekai.example.com/sekaiApp",
      "fulfillmentApiVersion": 2
    }
  }
}
```

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/sdk/define-actions)
