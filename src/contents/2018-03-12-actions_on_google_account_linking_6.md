---
layout: actions_on_google
title: Account Linking "Accessing Digital Purchases"の日本語訳です
categories:
- actions on google
---
Googleアシスタント向けアプリにおいて、そのアプリマーケットが今後活性化するかどうかを決定するであろう重要な要素に、「購入」という機能をあげることができると思います。エコシステムが機能するためには、ユーザとアプリ開発者の間で利益の交換が正しく行われなければなりません。もちろん、VUI（Voice User Interface）の分野において、ユーザが何かを購入するための安全なフローは、まだ確立されていないと言って良いでしょう。しかし、他のデバイスと連携する方式であれば、安全にその機能を提供することができるはずです。

Actions on Googleのドキュメントにある
[Account Linking - Accessing Digital Purchases](https://developers.google.com/actions/identity/digital-goods)にて、
Androidアプリ内で購入されたデジタルグッズの情報をアシスタント向けアプリ内で取得するための方法が説明されています。以下はその日本語訳です。

---
# デジタルグッズ購入情報へのアクセス

もしあなたがデジタルグッズの購入（アプリ内購入、定期購入、有料アプリ）をサポートするAndroidアプリの開発者の場合、Actions on Googleでは、ユーザーがAndroidアプリから購入したデジタルグッズにアクセスできます。たとえば、Googleアシスタント向けの語学教育アプリでは、Androidアプリを通じてレッスンを購入したユーザーのために、スペイン語の高度なレッスンへのアクセスを提供できます。

Androidでのアプリ内購入とサブスクリプションの実装方法については、[In-app Billing](https://developer.android.com/google/play/billing/index.html)をご覧ください。

Android以外のプラットフォームの場合は、ユーザーにアカウントの[リンク](https://developers.google.com/actions/identity/account-linking)を求める必要があります。その後、ユーザーのアカウント履歴を使用して、他のサーフェスで行った購入を識別することができます。

## ユーザーの購入の取得

以前にユーザーがアプリを通じて購入したアイテムに関する情報を取得するために、あなたのアクションをAndroidアプリと接続し、アクションを有効にしてユーザーのアプリ内購入にアクセスする必要があります。

![](https://developers.google.com/actions/images/access-digital-goods.png){:width="800px"}

**図1.** Actions ConsoleでAndroidアプリを接続し、デジタルグッズにアクセスするためのユーザーインターフェース。

ユーザーの購入を取得するには、次の手順を実行します。

1. **Google Playの購入へのアクセスを設定します。**
   1. Actions Consoleで歯車アイコンをクリックし、 **Project Settings** を選択します。
   1. 設定ページで、 **Brand Verification** タブを開きます。
   1. Androidアプリのセクションで、[AndroidアプリをあなたのActionに接続します](https://developers.google.com/actions/console/brand-verification#connecting_an_android_app)。 あなたがユーザの購入データを取得したいAndroidアプリを選択するために、 **Access Play purchases** オプションを有効にします。
   1. **Connect App** をクリックして変更を保存します。
1. **リクエストJSONからユーザーの packageEntitlements 情報を読み込みます。** Androidアプリが正常に接続されている場合、Googleアシスタントからのリクエストには、リンクされているAndroidアプリのユーザの購入に関する情報が含まれます。このデータは[リクエスト](https://developers.google.com/actions/reference/rest/Shared.Types/AppRequest)JSONメッセージに含まれています。このデータを取得するために明示的なAPI呼び出しを行う必要はありません。

以下のコードスニペットは、このリクエストがどのように表現されるかの例を示しています。

```json
{
  "user": {
    "userId": "xxxx",
    "locale": "en-US",
    "lastSeen": "2018-02-09T01:49:23Z",
    "packageEntitlements": [
      {
        "packageName": "com.abcd.edfg.hijk",
        "entitlements": [
          {
            "sku": "com.abcd.edfg.hijk",
            "skuType": "APP"
          }
        ]
      },
      {
        "packageName": "com.abcd.edfg.lmno",
        "entitlements": [
          {
            "sku": "lmno_jun_2017",
            "skuType": "SUBSCRIPTION",
            "inAppDetails": {
              "inAppPurchaseData": {
                "autoRenewing": true,
                "purchaseState": 0,
                "productId": "lmno_jun_2017",
                "purchaseToken": "12345",
                "developerPayload": "HSUSER_IW82",
                "packageName": "com.abcd.edfg.lmno",
                "orderId": "GPA.233.2.32.3300783",
                "purchaseTime": 1517385876421
              },
              "inAppDataSignature": "V+Q=="
            }
          }
        ]
      }
    ]
  },
  "conversation": {
    "conversationId": "1518141160297",
    "type": "NEW"
  },
  "inputs": [
    {
      "intent": "actions.intent.MAIN",
      "rawInputs": [
        {
          "inputType": "VOICE",
          "query": "Talk to My Test App"
        }
      ]
    }
  ],
  ...
}
```

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/identity/digital-goods)
