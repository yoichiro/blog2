---
layout: actions_on_google
title: Transactions "Add Transactions to Your App"の日本語訳です
categories:
- actions on google
---
Transactions APIを利用して、Googleアシスタント向けアプリに対して、購買や予約などの機能を組み込むことができるようになりました。手順としては少し多めで複雑ですが、一般的なそのようなトランザクションを扱う際の組み込み方と、そう大差はありません。既に何らかのECや予約サイトなどを運営されている方は、ぜひGoogleアシスタント向けアプリを開発し、Transactions APIを使った購買、予約機能の組み込みを検討してみてください。

Actions on Googleのドキュメントにある
[Transactions - Add Transactions to Your App](https://developers.google.com/actions/transactions/dev-guide)にて、
Transactions APIの組み込み方が説明されています。以下はその日本語訳です。

---
# トランザクションを使ったアプリの構築

## 始める前に

始める前に、必ず以下のセクションを確認してください。

### ポリシーとガイドラインを読む

アプリをユーザーに配布する方法については、[ポリシーとガイドライン](https://developers.google.com/actions/transactions/policies-guidelines)のドキュメントを参照してください。

### 支払方法の選択

アプリを作成する前に、使用する支払方法を決定する必要があります。一般に、3つの主要なカテゴリがあります。

* **独自の支払方法を使用する** - 自分のウェブアプリやモバイルアプリがある場合は、そこでユーザーが支払方法を設定して保存できるようにすることができます。アイデンティティストアがOAuth 2.0対応の場合、Actions Consoleでアカウントリンク設定を構成し、 `actions.intent.SIGN_IN` インテントを使用してユーザーをサインインし、そしてトランザクションを作成する際に独自のAPIを呼び出して支払方法をチャージすることができます。
* **Googleが提供する支払方法を使用する** - ユーザーはGoogleアシスタントの設定で支払方法をセットアップして、アシスタントとアシスタントアプリとのやり取りの中で支払いを行うことができます。
  * **Googleがサポートする支払ゲートウェイ** - Stripe、Braintree、Adyen、Vantivのいずれかのアカウントを持っている場合は、ユーザーのGoogleが提供する支払方法を使用して、支払をリクエストすることができ、支払処理向けの支払われるべき決済のトークンを送ります。このオプションが利用できるかどうかを知るために、支払ゲートウェイがPay with Googleをサポートしているかどうかを確認してください。
  * **自分で支払トークン復号化を管理する** - 自分でトークン復号化を管理する場合は、Googleに公開鍵を送ることができます。キーは、支払情報を含む暗号化されたトークンを生成するために使用されます。暗号化されたトークンを受け取ったら、秘密鍵を使用して支払情報を復号化し、支払ゲートウェイに渡して請求することができます。このオプションを使用するには、[ここ](https://assistant.google.com/developer/eap/)にサインアップし、 **Vertical Specific** ドロップダウンメニューで _Shopping_ を選択してください。私たちはあなたの要求をレビューして、次のステップで戻します。
* **支払方法なし** - 一部のユースケース（主に予約）では、トランザクションに関連付けられた支払がない場合があります。この場合、支払方法が指定されていないトランザクションを登録することができます。

> **注:** 支払方法を選択したら、サンドボックスモードをサポートする **必要がある** ことに注意してください。サンドボックスモードはデフォルトで有効になっており、フルフィルメントに送信される **isInSandbox** 論理値によって示されます。サンドボックスモードが有効な間に作成されたトランザクションの場合、ユーザーに請求したり、実際に注文を実行したりすべきではありません。Googleが提供する支払方法を使用している場合は、サンドボックスがオンの場合には、支払処理のサンドボックストークナイゼーションパラメータを渡す必要があります。Actions Simulatorで、サンドボックスモードを切り替えることができます。

### トランザクションインテントについて学ぶ

アプリをトランザクションに組み込む際には、会話全体で以下のインテントを実行してトランザクションを完了させることができます。

* `actions.intent.TRANSACTION_REQUIREMENTS_CHECK` - ユーザーが実際にトランザクションに参加できることを保証します。これは、ユーザーが注文を作成しておらず、後で注文を処理できないことを確認するのに便利です。これはサイレントインテントです。つまり、トリガーされたときにユーザーと会話しません。このチェックは、支払が必要で、「支払方法がない」が求められていない場合にのみ必要です。
* `actions.intent.DELIVERY_ADDRESS` - ユーザーの配送先住所を取得します。これは、会話の開始時に近くの場所を提示したり、トランザクションプロセスの終了時に住所を取得したりする場合などに便利です。
* `actions.intent.TRANSACTION_DECISION` - ユーザーとのトランザクションを確認します。支払が求められる際に、ユーザーがトランザクションに参加できるように、このインテントが自動的に `actions.intent.TRANSACTION_REQUIREMENTS_CHECK` をトリガーします。トランザクションを完了するには、このインテントを要求する必要があります。このインテントと共に、支払方法の設定の詳細をGoogleに渡します。
* `actions.intent.SIGN_IN` - サービスを使うアカウントにサインインし、Googleアカウントにリンクするようにユーザーに指示します。ユーザーはサービスで新しいアカウントにサインアップして、このフローで既存のアカウントにリンクすることもできます。これは、トランザクションが成功した後にサービス上のアカウントにサインアップしたり、トランザクションが発生して支払方法を取得する前に既存のアカウントをリンクするようユーザーに依頼する場合に便利です。

以下のセクションでは、これらのインテントをNode.jsクライアントライブラリで使用する方法（強くお勧めします）、または会話のWebhook用のJSONペイロードを構築する方法を示します。

> **注:** Actions Web Simulatorを Transactions API のテストに使用すべきではありません。アシスタント対応のAndroidまたはiOS端末を使用して、開発中のアプリを正確にテストしてください。

## トランザクションフロー

あなたのアプリがトランザクションを処理するときは、次のフローを使用します。

**注文を構築する**

トランザクションは通常、2つのユースケースに該当します。新しい注文は、ユーザに「ショッピングカート」の構築をさせてトランザクションを実行します。また、再注文は、以前のトランザクションを使用して、それに基づいて迅速に新しいトランザクションを実行します。再注文は、時間を超えてユーザートランザクションを追跡するために、ある種のユーザー識別を必要とします。

注文を構築するには、支払が最初に行われる場合、ユーザーが[取引を行うことができるかどうかを確認](https://developers.google.com/actions/transactions/dev-guide#check_for_transaction_requirements)し、その後[注文を作成します](https://developers.google.com/actions/transactions/dev-guide#build_an_order)。必要に応じて、[配送先住所のヘルパー](https://developers.google.com/actions/transactions/dev-guide#obtain_a_delivery_address)を使用してユーザーの配送先住所を取得することができます。

**注文を提案する**

ProposedOrderを作成したら、必要に応じて支払方法を使用して、ユーザーとのトランザクションを確認し、トランザクションを実行します。

必要に応じてアカウントをリンクして、[ユーザーに注文を提案する](https://developers.google.com/actions/transactions/dev-guide#propose_the_order_to_the_user)方法については、[アカウントリンクの実行](https://developers.google.com/actions/transactions/dev-guide#perform_account_linking)を参照してください。

**注文を確認する**

ユーザーがトランザクションを確認したら、それを実行し、レシートで注文を確認します。

詳細については、[注文の確認とレシートの送信](https://developers.google.com/actions/transactions/dev-guide#confirm_the_order_and_send_a_receipt)を参照してください。

**アップデートを送信する**

トランザクションが実行された後、注文が完了するまで（たとえば、配送またはキャンセルされるまで）、注文期間を通してアップデートを提供する必要があります。注文の状態は、ユーザがウェブサイトやアプリで目にする状態を常に反映する必要があります。

詳細は、[注文のアップデートの送信](https://developers.google.com/actions/transactions/dev-guide#send_order_updates)を参照してください。

## トランザクションを使ったアプリの構築

### アクションプロジェクトの作成

アプリを作成するときは、Transactions APIを使用するように指定する必要があります。これにより、特定の機能を有効にしてアプリでトランザクションを有効にすることができます。また、承認のためにアプリを送信する際に特定のトランザクション機能を確認することもできます。

開始するには、Actions Consoleの次の手順を実行する必要があります。

* 新しいプロジェクトを作成するか、既存のプロジェクトをインポートします。
* "App Information" セクションを全て記入します。
* "Privacy and Consent" では、"Does your app perform Transactions?" というチェックボックスをオンにする必要があります。それを行わないと、Transactions APIを使用しようとすると失敗します。

Dialogflowを使用してアプリを構築することをおすすめします。高品質の会話で注文の体験を簡単に作成できます。

完全なトランザクションアプリの例については、GitHubで[transactions sample](https://github.com/actions-on-google/dialogflow-transactions-nodejs)をダウンロードまたはcloneしてください。

### トランザクション要件を確認する

**ユーザー体験**

ユーザーが取引を希望するとすぐに、取引に支払が含まれている場合は、トランザクションを実行できるように `actions.intent.TRANSACTION_REQUIREMENTS_CHECK` インテントを使用すべきです。たとえば、アプリを呼び出すと、「シューズを注文するか、アカウントの残高を確認しますか？」という質問を尋ねることがあります。ユーザーが「シューズを注文する」と言った場合は、すぐにこのインテントをリクエストして、続行できることを確認し、そしてトランザクションが続行されることをユーザが抑止する設定に修正する機会を与える必要があります。

インテントをリクエストすると、アシスタントはユーザーのロケール、デバイスの種類などの厳しい要件をチェックします。これらの条件を満たす場合、ユーザーの支払設定のような変更可能な要件をチェックします。アシスタントは、ユーザーが設定を修正して要件を満たすことができるようにガイドする場合があります。

要件が満たされると、インテントは成功の条件でアプリに送り返され、ユーザーの注文を作成することができます。

要件の1つ以上が満たされない場合、インテントは失敗条件付きでアプリに返されます。この場合、会話をトランザクションの体験から遠ざけたり、会話を終了したりする必要があります。

> **注:** 上記で説明したビルトインのユーザー体験フローのいくつかは、この開発者プレビューではまだ利用できません。具体的には、 `actions.intent.TRANSACTION_REQUIREMENTS_CHECK` インテントは、ユーザーの支払設定とロケールに関係なく、成功状態を返します。失敗状態のシナリオをテストするには、音声対応スピーカーでインテントを要求します。

**API**

支払を含むトランザクションの場合、ユーザーがトランザクション要件を満たしていることを確認するには、次のプロパティを定義する `TransactionRequirementsCheckSpec` データオブジェクトを使用して `actions.intent.TRANSACTION_REQUIREMENTS_CHECK` インテントのフルフィルメントをリクエストします。

* `orderOptions` - アプリがトランザクションに必要とする顧客情報
  * `requestDeliveryAddress` - trueの場合、注文に配信先住所が必要です
  * `customerInfoOptions.customerInfoProperties[]` - `CustomerInfoProperty` 列挙型の配列です。現在、有効な値は `"EMAIL"` のみです。
* `paymentOptions` - Googleが提供する支払方法を使用する場合にのみ必要です。
  * `googleProvidedOptions.supportedCardNetworks[]` - ユーザーが支払に使用する有効なカードネットワーク。有効な値 `"AMEX"` 、 `"DISCOVER"` 、 `"MASTERCARD"` 、 `"VISA"` 、 `"JCB"` を持つ `CardNetwork` 列挙体の配列です。
  * `googleProvidedOptions.prepaidCardDisallowed` - ユーザーがGoogleで保存されたプリペイドカードで支払できるかどうかを示すブール値

> **注:** このインテントのフルフィルメントは、 `actions.intent.TRANSACTION_DECISION` のフルフィルメントの間に自動的に要求されます。ユーザが注文の作成に進まず、ユーザが実際にトランザクションを実行することができないことがわかることを確認する前に、あなたは `actions.intent.TRANSACTION_REQUIREMENTTS_CHECK` のフルフィルメントにいつでも尋ねることができます。

**Google支払方法を使った要件の確認をする**

次の方法で、ユーザーがGoogle提供の支払方法のトランザクション要件を満たしているかどうかを確認できます。

**DIALOGFLOW NODE.JS**

```js
function transactionCheckGooglePayment (app) {
  app.askForTransactionRequirements({
    // These will be provided by payment processor, like Stripe, Braintree, or
    // Vantiv
    tokenizationParameters: {},
    cardNetworks: [
      app.Transactions.CardNetwork.VISA,
      app.Transactions.CardNetwork.AMEX
    ],
    prepaidCardDisallowed: false,
    deliveryAddressRequired: false
  });
}
```

**DIALOGFLOW JSON**

```json
{
  "google": {
    "systemIntent": {
      "intent": "actions.intent.TRANSACTION_REQUIREMENTS_CHECK",
      "data": {
        "@type": "type.googleapis.com/google.actions.v2.TransactionRequirementsCheckSpec",
        "paymentOptions": {
          "googleProvidedOptions": {
            "prepaidCardDisallowed": false,
            "supportedCardNetworks": [
              "VISA", "AMEX"
            ]
          }
        },
        "orderOptions": {
          "requestDeliveryAddress": true
        }
      }
    }
  }
}
```

**独自の支払方法で要件を確認する**

独自の支払方法を使用する際には、以下の方法でユーザーが取引要件を満たしているかどうかを確認することができます。

**DIALOGFLOW NODE.JS**

```js
function transactionCheckActionPayment (app) {
  app.askForTransactionRequirements({
    type: app.Transactions.PaymentType.PAYMENT_CARD,
    displayName: 'VISA-1234',
    deliveryAddressRequired: false
  });
}
```

**DIALOGFLOW JSON**

```json
{
  "google": {
    "systemIntent": {
      "intent": "actions.intent.TRANSACTION_REQUIREMENTS_CHECK",
      "data": {
        "@type": "type.googleapis.com/google.actions.v2.TransactionRequirementsCheckSpec",
        "paymentOptions": {
          "actionProvidedOptions": {
            "paymentType": "PAYMENT_CARD",
            "displayName": "VISA-9876"
          }
        },
        "orderOptions": {
          "requestDeliveryAddress": true
        }
      }
    }
  }
}
```

**要件チェックの結果を受け取る**

アシスタントがインテントを実行すると、チェックの結果と一緒に `actions.intent.TRANSACTION_REQUIREMENTS_CHECK` インテントのリクエストがフルフィルメントに送信されます。

この要求を適切に処理するには、 `actions_intent_TRANSACTION_REQUIREMENTS_CHECK` イベントによってトリガーされるDialogflowインテントを宣言します。 トリガーされたときには、あなたのフルフィルメントで次のように処理してください。

**DIALOGFLOW NODE.JS**

```js
function transactionCheckComplete (app) {
  if (app.getTransactionRequirementsResult() === app.Transactions.ResultType.OK) {
    // Normally take the user through cart building flow
    app.ask('Looks like you\'re good to go! Try saying "Get Delivery Address".');
  } else {
    app.tell('Transaction failed.');
  }
}
```

### 配送先住所を取得する

トランザクションにユーザーの配送先住所が必要な場合は、 `actions.intent.DELIVERY_ADDRESS` インテントのフルフィルメントを要求することができます。これは、総額、配送/ピックアップの場所を決定したり、ユーザーがサービス地域内にいることを確認するのに役立ちます。

このインテントが実行されるように要求するときは、 `reason` オプションを渡します。このオプションでは、アシスタントが文字列で住所を取得するように要求することができます。たとえば、"to know where to send the order" と指定すると、アシスタントはユーザーに次のように質問します。

```text
"To know where to send the order, I'll need to get your delivery address"
```

**配送先住所の要求**

**DIALOGFLOW NODE.JS**

```js
function deliveryAddress (app) {
  app.askForDeliveryAddress('To know where to send the order');
}
```

**DIALOGFLOW JSON**

```json
{
  "google": {
    "systemIntent": {
      "intent": "actions.intent.DELIVERY_ADDRESS",
      "data": {
        "@type": "type.googleapis.com/google.actions.v2.DeliveryAddressValueSpec",
        "addressOptions": {
          "reason": "To proceed with your order"
        }
      }
    }
  }
}
```

**配達先住所を受け取る**

アシスタントはインテントを実行した後、 `actions.intent.DELIVERY_ADDRESS` インテントを使用して、フルフィルメントにリクエストを送信します。

この要求を適切に処理するには、 `actions_intent_DELIVERY_ADDRESS` イベントによってトリガーされるDialogflowインテントを宣言します。トリガーされたときには、あなたのフルフィルメントで次のように処理してください。

**DIALOGFLOW NODE.JS**

```js
function deliveryAddressComplete (app) {
  if (app.getDeliveryAddress()) {
    console.log('DELIVERY ADDRESS: '
      + app.getDeliveryAddress().postalAddress.addressLines[0]);
    app.ask('Great, got your address! Now say "confirm transaction".');
  } else {
    app.tell('Transaction failed.');
  }
}
```

### 注文を作成する

**ユーザー体験**

必要なユーザー情報を取得したら、ユーザーに注文を誘導する「カート組み立て」体験を構築する必要があります。すべてのアプリは、あなたの製品やサービスに応じて、わずかに異なるカートのアセンブリフローを持つ可能性があります。

素晴らしいカート組み立ての体験は、単純なイエスまたはノーの質問によって最も最近の購入を再注文することを可能にするような単純なものになり得ます。ユーザーには、トップの「特集」アイテムまたは「おすすめ」アイテムを、カルーセルまたはリストカードで提示することができます。[リッチレスポンス](https://developers.google.com/actions/assistant/responses)を使用してユーザーの選択肢を視覚的に提示することを強くお勧めします。

優れたカート組み立て体験を構築する方法の詳細については、[トランザクション設計ガイドライン](https://developers.google.com/actions/transactions/design)を参照してください。

**API**

会話を通して、ユーザが注文に追加したいアイテムを集めて、以下から構成される `ProposedOrder` を構築する必要があります。

* `merchant` - あなたをIDと名前で識別します。
* `lineItems` - 注文カート内のアイテムのコレクションです。
* `totalPrice` - カート内のすべての商品の価格です。
* `otherItems` - 注文が完了したときにユーザーのレシートに表示される税および小計です。
* `extension.locations` - 注文に関連付けられた場所（ピックアップ、配送など）です。

また、お好みの支払方法や配送場所など、トランザクションリクエストの一環としてユーザーに「提案」されるその他の情報を収集することもできます。

詳細については、[conversation webhook documentation](https://developers.google.com/actions/reference/rest/Shared.Types/ProposedOrder)と[Node.jsクライアントライブラリリファレンス](https://developers.google.com/actions/reference/nodejs/Order)を参照してください。

**注文の構築**

```js
let order = app.buildOrder('<UNIQUE_ORDER_ID>')
  .setCart(app.buildCart().setMerchant('book_store_1', 'Book Store')
    .addLineItems([
      app.buildLineItem('memoirs_1', 'My Memoirs')
        .setPrice(app.Transactions.PriceType.ACTUAL, 'USD', 3, 990000000)
        .setQuantity(1)
        .addSublines('Note from the author'),
      app.buildLineItem('memoirs_2', 'Memoirs of a person')
        .setPrice(app.Transactions.PriceType.ACTUAL, 'USD', 5, 990000000)
        .setQuantity(1)
        .addSublines(['Special introduction by author', 'Something else from the author']),
      app.buildLineItem('memoirs_3', 'Their memoirs')
        .setPrice(app.Transactions.PriceType.ACTUAL, 'USD', 15, 750000000)
        .setQuantity(1)
        .addSublines(
          app.buildLineItem('memoirs_epilogue', 'Special memoir epilogue')
            .setPrice(app.Transactions.PriceType.ACTUAL, 'USD', 3, 990000000)
            .setQuantity(1)
        ),
      app.buildLineItem('memoirs_4', 'Our memoirs')
        .setPrice(app.Transactions.PriceType.ACTUAL, 'USD', 6, 490000000)
        .setQuantity(1)
    ]).setNotes('The Memoir collection'))
  .addOtherItems([
    app.buildLineItem('subtotal', 'Subtotal')
      .setType(app.Transactions.ItemType.SUBTOTAL)
      .setQuantity(1)
      .setPrice(app.Transactions.PriceType.ESTIMATE, 'USD', 32, 220000000),
    app.buildLineItem('tax', 'Tax')
      .setType(app.Transactions.ItemType.TAX)
      .setQuantity(1)
      .setPrice(app.Transactions.PriceType.ESTIMATE, 'USD', 2, 780000000)
  ])
  .setTotalPrice(app.Transactions.PriceType.ESTIMATE, 'USD', 38, 990000000);
```

### アカウントリンクを実行する

独自の支払方法を使用してユーザーに請求する場合は、そこに保存されている支払方法を検索、提示、請求するために、自分のGoogleアカウントを自分のサービスで使用しているアカウントにリンクする必要があります。

この要件を満たすために、OAuth 2.0アカウントリンクを提供しています。アカウントリンクの詳細については、[こちら](https://developers.google.com/actions/identity/)をご覧ください。OAuth 2.0アサーションフローを有効にすることをお勧めします。これにより、非常に効率的なユーザー体験が可能になります。

ユーザーが会話の途中でアカウントをリンクすることを要求するために、私たちは `actions.intent.SIGN_IN` インテントを提供します。

Webhookリクエストの `User` オブジェクトにて `accessToken` を見つけることができない場合は、このAPIを呼び出す必要があります。これは、ユーザーが自分のアカウントをまだリンクしていないことを意味します。

`actions.intent.SIGN_IN` インテントを要求した後、 `"OK"` 、 `"CANCELED"` 、 `"ERROR"` のいずれかの値を持つ `SignInStatus` を含む引数を受け取ります。ステータスが `"OK"` の場合は、 `User` オブジェクト内に `accessToken` を見つけることができるはずです。

`actions.intent.SIGN_IN` インテントを使用するには、Actions Consoleでアカウントリンクを設定する必要があります。

**API**

**サインインリクエスト**

**DIALOGFLOW NODE.JS**

```js
function signIn (app) {
  app.askForSignIn()
}
```

**DIALOGFLOW JSON**

```json
{
  "google": {
    "systemIntent": {
      "intent": "actions.intent.SIGN_IN",
      "data": {
        "@type": "type.googleapis.com/google.actions.v2.SignInValueSpec"
      }
    }
  }
}
```

**サインインの結果を受け取る**

**DIALOGFLOW NODE.JS**

```js
if (app.getSignInStatus() == app.SignInStatus.OK) {
  let accessToken = app.getUser().accessToken;
  // use the accessToken to access your API
}
```

### 注文をユーザーに提案する

注文を作成したら、それを確認または拒否するためにユーザーに提示する必要があります。これを行うには、 `actions.intent.TRANSACTION_DECISION` インテントを要求し、作成したオーダーを提供します。

**ユーザ体験**

`actions.intent.TRANSACTION_DECISION` インテントをリクエストすると、アシスタントは、渡された `ProposedOrder` が「カートプレビューカード」に直接レンダリングされるビルトインの体験を開始します。ユーザーは「注文する」と言ったり、トランザクションを辞退したり、クレジットカードや住所などの支払オプションを変更することができます。

> **注:** 「カートプレビューカード」に表示されるデータは、 `ProposedOrder` に従ってそのまま表示されます。私たちの方針では、提示されたデータが項目化され、正確で、ユーザーがダイアログ（対話）で要求した内容と一貫性があることが要求されます。Googleでは、税金や手数料などの項目を計算することはなく、 `actions.intent.TRANSACTION_DECISION` インテントに渡すものから直接受け取ることに注意してください。

ユーザーは、「私のラテをトリプルに変更して」という私たちのビルトインの体験とはマッチしないことを言っているかもしれません。この場合、問い合わせは通常のクエリのようにアプリに送信されます。このような注文変更要求の準備が整うように、Dialogflowインテントを設定する必要があります。

**API**

`actions.intent.TRANSACTION_DECISION` インテントをリクエストするときは、 `TransactionDecisionValueSpec` を提供する必要があります。これは、 `OrderOptions` および `PaymentOptions` だけでなく、 `ProposedOrder` も含まれます。

> **注:** Googleが提供する支払方法で支払をリクエストする場合は、この時点で `isInSandbox` 論理値をチェックし、テスト/サンドボックス支払プロセッサのトークナイゼーションパラメータがtrueに設定されている場合は渡す必要があります

ユーザーがトランザクションを受け入れるか拒否すると、 `TransactionDecisionValue` を含む `Argument` が返されます。これには以下が含まれます。

* `transactionRequirementsCheckResult.resultType` - カートプレビューカードをユーザに提示する前に、アシスタントは自動的に `actions.intent.TRANSACTION_REQUIREMENTS_CHECK` インテントに含まれる同じロジックを実行します。この結果は `resultType` フィールドに含まれています。可能な値は、 `"OK"` 、 `"USER_ACTION_REQUIRED"` 、 `"ASSISTANT_SURFACE_NOT_SUPPORTED"` および `"REGION_NOT_SUPPORTED"` です。このチェックは、支払を含まないトランザクションにのみ適用されます。
* `userDecision` - 提案された注文に関するユーザーの決定です。可能な値は、 `"ORDER_ACCEPTED"` 、 `"ORDER_REJECTED"` 、 `"DELIVERY_ADDRESS_UPDATED"` 、および `"CART_CHANGE_REQUESTED"` です。
* `deliveryAddress` - ユーザーが配送先住所を変更した場合の、更新された住所です。この場合、 `userDecision` `"DELIVERY_ADDRESS_UPDATED"` になることに注意してください。

**`userDecision` が `"ORDER_ACCEPTED"` と仮定:**

* `order.finalOrder` - 最初に渡された `ProposedOrder` のコピーです。
* `order.googleOrderId` - 後で注文を参照するためにGoogleが生成した注文IDです。
* `order.orderDate` - 注文が作成された日時です。
* `order.paymentInfo` - ユーザーに課金するために使用する必要がある支払方法に関する詳細です。
  * `paymentType` - `"PAYMENT_CARD"` 、 `"BANK"` 、 `"LOYALTY_PROGRAM"` 、 `"ON_FULFILLMENT"` 、 `"GIFT_CARD"` のいずれか。支払方法のすべてのタイプで返されます。
  * `googleProvidedPaymentInstrument.instrumentToken` - サードパーティの支払処理プログラムによって提供されたBase64でエンコードされた支払トークンを含みます。Google提供の支払方法の場合のみ返されます。
  * `displayName` - レシートに表示された手段の名前です。あなたのアプリのみが提供する支払方法のために返されます。
* `order.customerInfo` - リクエストされた任意の顧客情報（メールアドレスなど）です。

**Google支払方法の使用**

**DIALOGFLOW NODE.JS**

```js
app.askForTransactionDecision(order, {
  // These will be provided by payment processor, like Stripe,
  // Braintree, or Vantiv
  tokenizationParameters: {
    "gateway": "stripe",
    "stripe:publishableKey": (app.isInSandbox() ? "pk_test_key" : "pk_live_key"),
    "stripe:version": "2017-04-06"
  },
  cardNetworks: [
    app.Transactions.CardNetwork.VISA,
    app.Transactions.CardNetwork.AMEX
  ],
  prepaidCardDisallowed: false,
  deliveryAddressRequired: false
  });
```

**DIALOGFFLOW JSON**

```json
{
  "google": {
    "systemIntent": {
      "intent": "actions.intent.TRANSACTION_DECISION",
      "data": {
        "@type": "type.googleapis.com/google.actions.v2.TransactionDecisionValueSpec",
        "proposedOrder": ...,
        "paymentOptions": {
          "googleProvidedOptions": {
            "tokenizationParameters": {
              "tokenizationType": "PAYMENT_GATEWAY",
              "parameters": {
                "gateway": "stripe",
                "stripe:publishableKey": "pk_test_key",
                "stripe:version": "2017-04-06"
              }
            }
          }
        },
        "orderOptions": {
          "customerInfoOptions": {
            "customerInfoProperties": [
              "EMAIL"
            ]
          }
        }
      }
    }
  }
}
```

**支払トークン復号化の管理**

**DIALOGFLOW NODE.JS**

```js
app.askForTransactionDecision(order, {
  tokenizationType: TransactionValues.PaymentMethodTokenizationType.DIRECT,
  tokenizationParameters: {
    "publicKey": "base64encoded..."
  },
  cardNetworks: [
    app.Transactions.CardNetwork.VISA,
    app.Transactions.CardNetwork.AMEX
  ],
  prepaidCardDisallowed: false,
  deliveryAddressRequired: false
  });
```

**DIALOGFLOW JSON**

```json
{
  "google": {
    "systemIntent": {
      "intent": "actions.intent.TRANSACTION_DECISION",
      "data": {
        "@type": "type.googleapis.com/google.actions.v2.TransactionDecisionValueSpec",
        "proposedOrder": ...,
        "paymentOptions": {
          "googleProvidedOptions": {
            "tokenizationParameters": {
              "tokenizationType": "DIRECT",
              "parameters": {
                "publicKey": "base64encoded..."
              }
            }
          }
        },
        "orderOptions": {
          "customerInfoOptions": {
            "customerInfoProperties": [
              "EMAIL"
            ]
          }
        }
      }
    }
  }
}
```

**あなた自身の支払方法の提供**

**DIALOGFLOW NODE.JS**

```js
app.askForTransactionDecision(order, {
  type: app.Transactions.PaymentType.PAYMENT_CARD,
    displayName: 'VISA-1234',
    deliveryAddressRequired: true
  });
```

**DIALOGFLOW JSON**

```json
{
  "google": {
    "system_intent": {
      "intent": "actions.intent.TRANSACTION_DECISION",
      "data": {
        "@type": "type.googleapis.com/google.actions.v2.TransactionDecisionValueSpec",
        "proposedOrder": ...,
        "paymentOptions": {
          "actionProvidedOptions": {
            "paymentType": "PAYMENT_CARD",
            "displayName": "VISA-1234"
          }
        }
      }
    }
  }
}
```

**ユーザーの決定を処理する**

アシスタントはインテントを実行した後、トランザクションの決定に対するユーザーの回答とともに、 `actions_intent_TRANSACTION_DECISION` インテントを持つリクエストをフルフィルメントに送信します。

この要求を適切に処理するには、 `actions_intent_TRANSACTION_DECISION` イベントによってトリガーされるDialogflowインテントを宣言します。トリガーされたときには、あなたのフルフィルメントで次のように処理してください。

**DIALOGFLOW NODE.JS**

```js
if (app.getTransactionDecision()
&& app.getTransactionDecision().userDecision ===
  app.Transactions.ConfirmationDecision.ACCEPTED) {
    let googleOrderId = app.getTransactionDecision().order.googleOrderId;
  }
```

### 注文を確認し、レシートを送信する

`actions.intent.TRANSACTION_DECISION` インテントが `"ORDER_ACCEPTED"` の `userDecision` を返したときは、注文を "確認" するために必要な処理（ユーザー自身のデータベースに保存してユーザーに請求する可能性が高い）を即時に実行する必要があります。次に、次のレスポンスで `OrderUpdate` オブジェクトで `Receipt` を送信しなければなりません。ダイアログを終了するか、レシートを含む更なるプロンプトを表示するかを選択できます。注文が行われると、Googleは注文IDを提供します。同じオーダーを再度配置しようとすると、結果は得られません。

> **注:** アプリでレシートを表示することに加えて、トランザクションを行う際に必要なすべての法的要件（たとえば、顧客サービスの連絡先情報などの詳細情報が記載された電子メールで送信されたレシート）を満たす正式なレシートを送信する必要があります。

この最初の `OrderUpdate` を提供すると、残りのレスポンスと共に「折りたたまれたレシートカード」が表示されます。これは、ユーザーが注文履歴で見つけることができるレシートを反映しています。

> **注:** 注文が作成されたときに `isInSandbox` が `true` の場合は、注文を実行したり、ユーザーに請求したりしないでください（テスト用の支払方法のみが請求される支払サンドボックス以外の場合）。独自のデータベースで注文を永続化することをお勧めしますが、必ずサンドボックスでの注文としてフラグを立ててください。


注文確認中は、 `confirmedActionOrderId` を渡す必要があります。これは、提供された `googleOrderId` になることもできますし、または独自のカスタム識別子になることもできます。ユーザーにはそのレシートが表示されることに注意してください。

最後に、 `OrderUpdate` の一環として、 `orderManagementActions` を提供する必要があります。これらのマニフェストは、ユーザーがアシスタントの注文履歴で見つけることができる注文詳細の下部にあるURLのボタンとして現れます。 `VIEW_DETAILS` `OrderManagementAction` を最低でも各注文に提供する必要があります。これは、アプリまたはウェブサイト上での注文表示へのディープリンクが含まれている必要があります。 `OrderManagementActions` は、Conversation Send API（下記参照）経由で送信する `OrderUpdate` の一部として提供することもできます。

> **注:** 現在、あなたが提供する電話番号を使ってユーザの携帯端末アプリにディープリンクしたり、あなたが提供する電子メールアドレスを使って電子メールアプリにディープリンクする電子メールアクションを使って、携帯端末のアクションを使うことができます。私たちは間もなくあなたのアプリのインテントにディープリンクするインテントアクションを追加します。

**API**

**DIALOGFLOW NODE.JS**

```js
app.tell(app.buildRichResponse().addOrderUpdate(
    app.buildOrderUpdate(googleOrderId, true)
      .setOrderState(app.Transactions.OrderState.CREATED, 'Order created')
      .setInfo(app.Transactions.OrderStateInfo.RECEIPT, {
        confirmedActionOrderId: ''
      }))
    .addSimpleResponse('Transaction completed! You\'re all set!'));
```

### 注文のアップデートを送信する

注文が確定した後、注文の期間中に注文のアップデートを送信する必要があります。たとえば、注文は以下の手順を経ることができ、それぞれの手順にてアップデートが必要です。

1. `CONFIRMED` - 注文はアプリで確認されています。つまり、有効であり、履行のために処理されています。
1. `IN_TRANSIT` - 注文は出荷され、配送中です。
1. `FULFILLED` - 注文が配送されました。
1. `RETURNED` - 配送後にユーザによって注文が返されました。

その他の可能な注文状態は次のとおりです。

REJECTED - if your app was unable to process, charge, or otherwise "activate" the order
CREATED - order is accepted by the user and "created" from the perspective of your app but not yet confirmed, for example if manual processing is required
CANCELLED - order was cancelled by the user
* `REJECTED` - あなたのアプリが注文を処理、請求、または他の方法で「アクティブ化」できなかった場合。
* `CREATED` - 注文はユーザーによって承認され、アプリの観点からは「作成」されていますが、まだ確認されていません。たとえば、手動による処理が必要な場合。
* `CANCELLED` - 注文がユーザーによってキャンセルされました。

> **注:** 各注文のアップデートには、 `orderManagementActions` をフォローアップする必要があります。

Conversation send APIにHTTP POSTリクエストを送信して、注文のアップデートを提供します。重要な注文のアップデートは、ユーザーのGoogleアシスタント対応携帯端末のプッシュ通知として表示されます。

Conversation send APIへの注文アップデート要求は、Actions Consoleプロジェクトに関連付けられたサービスアカウントキーと交換できるアクセストークンによって認可されます。

注文のアップデート自体は、Googleが生成したID、またはトランザクションが最初に作成されて確認されている間にあなたから提供されたIDのいずれかによって、注文を参照します。

`actions.intent.TRANSACTION_DECISION` 中にユーザーが受け入れた直後でレシートを提供する最初の「注文のアップデート」には、 `CREATED` 状態が含まれます。ステップ2〜5の注文の状態は、会話がすでに終了した後に提供する必要があります。そのために、注文のアップデートをPOSTできる非同期HTTP APIを提供します。これは、Conversation Send APIと呼ばれます。

いくつかの注文のアップデートによっては、ユーザーのアシスタント対応モバイルデバイスにプッシュ通知が送信されます。これは、アップデートの重要性に基づいて、Googleによって決定されます。

**Conversation Send APIへのリクエストの認可**

Conversation Send APIに注文アップデートをPOSTするには、Actions Consoleプロジェクトに関連付けられたJSONサービスアカウントキーをダウンロードする必要があります。次に、Conversation Send APIを呼び出す前に、HTTPリクエストの `Authorization` ヘッダーに渡されるBearerトークンにこのキーを交換することができます。この交換は、Google APIクライアントライブラリを使用して実行できます。[例として、Node.jsクライアントライブラリのドキュメント](https://github.com/google/google-api-nodejs-client#using-jwt-service-tokens)を参照してください。

サービスアカウントキーを得るには、次の手順を行います。

1. ID "example-project-1" のActions Consoleプロジェクトがある場合は、次のリンクにアクセスしてください: [https://console.developers.google.com/apis/api/actions.googleapis.com/overview?project=example -project-1](https://console.developers.google.com/apis/api/actions.googleapis.com/overview?project=example -project-1)
1. Enable ボタンが表示されている場合は、そのボタンをクリックします。それ以外の場合は、手順3に進みます。
1. Actions ConsoleのプロジェクトIDが同じと仮定して、このリンクにアクセスしてください: [https://console.developers.google.com/apis/credentials?project=example-project-1](https://console.developers.google.com/apis/credentials?project=example-project-1)
1. Create credentials &gt; Service Account Key をクリックします。
1. Service Account の選択ボックスをクリックし、 New Service Account をクリックします。
1. サービスアカウントに "orderupdater" のような名前を付け、Project Ownerのロールを付与します。
1. JSONキータイプを選択します。
1. Create をクリックします。
1. JSONサービスアカウントキーがローカルマシンにダウンロードされます。非同期の注文アップデートを送信するアプリでこのキーを読み取る必要があります

OAuth Bearerトークンからサービスアカウントキーを交換したら、これを使用して、Conversation Send APIが認可されたリクエストを行うことができます。Conversation Send APIのURLは次のとおりです。

```text
POST https://actions.googleapis.com/v2/conversations:send
```

以下のヘッダーを指定する必要があります。

1. `"Authorization: Bearer $token"` 。 `$token` は、サービスアカウントから交換したOAuth Bearerトークンです。
2. `"Content-Type: application/json"`

POSTリクエストは、以下のフォーマットのJSONボディを持ちます。

```json
{
  "customPushMessage": {
    "orderUpdate": OrderUpdate
  }
}
```



`OrderUpdate` は、[ここ](https://developers.google.com/actions/reference/rest/Shared.Types/AppResponse#OrderUpdate)に記載されている形式に従います。トップレベルの必須フィールドは次のとおりです。

* `googleOrderId` または `actionOrderId` **のいずれか** - 以前に `confirmedActionOrderId` を持つレシートを含む場合は、後者を使用できます。
* `orderState` - 実際の注文の状態です。
* `updateTime` - 状態が変更された正確な時刻（1970/01/01以降の合計秒数）です。
* `orderManagementActions` - これらは各注文のアップデートとともにリセットされます。
* `userNotification`

## トラブルシューティング

もしトランザクションの履行に何らかの理由で失敗する場合は、何が悪いかを解決するために、[Actions Console](https://console.actions.google.com/)のログが良い調査開始場所になります。Actions Consoleの[ここ](https://developers.google.com/actions/console/troubleshooting)にて、トラブルシューティングについての詳細を学ぶことができます。

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/transactions/dev-guide)
