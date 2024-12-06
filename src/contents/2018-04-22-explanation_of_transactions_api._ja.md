---
layout: post
title: サンプルコードからActions on GoogleのTransactions APIを理解する
categories:
- actions on google
---

Transactions APIを使うことで、Googleアシスタントアプリに購買や予約と行った機能を組み込むことができます。
しかし、いくつかの手順、いくつかのインテント、いくつかのイベント、そしていくつかのデータ構造があるため、
Transactions APIの利用方法を理解することは難しいかも知れません。つまり、複雑なのです。

幸いなことに。Transactions APIをより簡単に理解するためのサンプルコードセットがあります。ここで、アプリにTransactions API
を組み込むための、サンプルコード上の各手順を説明してみたいと思います。

[https://github.com/yoichiro/dialogflow-transactions-nodejs](https://github.com/yoichiro/dialogflow-transactions-nodejs)

上記のリポジトリは、 [https://github.com/actions-on-google/dialogflow-transactions-nodejs](https://github.com/actions-on-google/dialogflow-transactions-nodejs) を日本語対応したものです。

# サンプルコードセットを入手する

以下のコマンドをタイプすることで、サンプルコードセットをcloneすることができます。

`git clone https://github.com/yoichiro/dialogflow-transactions-nodejs`

基本的に、 `README.md` ファイルで説明されている手順によって、全てのトランザクション処理を試すための全体の環境
を構築することができます。ここで、環境の構築方法は言及しません。代わりに、Transactions APIの振る舞いについて
説明したいと思います。

# 処理フロー図

dialogflow-transactions-nodejs サンプルは、以下の処理フローとなります・

![]({{ "/images/2018/04/t_api_ja_1.webp" | prepend: site.baseurl }})

実際のトランザクションは、以下の手順で構成されます。

1. アシスタントアプリによって要求された新しいトランザクションをユーザが実行できるかどうかをチェックします。
1. 必要であれば、ユーザか配送先住所を得ます。
1. トランザクションを実際に実行するかどうかをユーザが決定します。

では、各処理を見ていきましょう。

# ウェルカムメッセージ

![]({{ "/images/2018/04/transactions_api_5.webp" | prepend: site.baseurl }})

アプリが呼び出された時、アプリはウェルカムメッセージを発言します。このウェルカムメッセージは、 `Default Welcome Intent`
インテントでセットアップされていて、ウェルカムメッセージは固定的です。また、このインテントはフルフィルメント Webhook
を使いません。

![]({{ "/images/2018/04/t_api_ja_2.webp" | prepend: site.baseurl }})

単純です。コードはここではありません。ユーザは以下のようなメッセージを読むことになります。

![]({{ "/images/2018/04/t_api_ja_3.webp" | prepend: site.baseurl }})


# ユーザが「決済なしでトランザクションを試す」と言った時

![]({{ "/images/2018/04/t_api_ja_6.webp" | prepend: site.baseurl }})

"transaction_check_nopayment" インテントは、"決済なしでトランザクションを試す" というTraining Phraseを持っています。
これは、もしそのフレーズをユーザが言った時に、このインテントが呼び出されることを意味しています。

![]({{ "/images/2018/04/t_api_ja_4.webp" | prepend: site.baseurl }})

このインテントは、フルフィルメントのWebhookを呼び出します。このインテントを処理するコードは、以下です。

```js
app.intent('transaction_check_nopayment', (conv) => {
  conv.ask(new TransactionRequirements());
});
```

このハンドラは、 `DialogflowConversation` クラスの [`ask()`](https://actions-on-google.github.io/actions-on-google-nodejs/classes/conversation.conversation-1.html#ask) 関数を引数を伴って
呼び出します。引数として渡される [`TransactionRequirements`](https://actions-on-google.github.io/actions-on-google-nodejs/classes/conversation_question.transactionrequirements.html) クラスは、ユーザがトランザクションを実行可能な状態かどうかをチェックするためのリクエストを
表現します。つまり、このクラスは `actions.intent.TRANSACTION_REQUIREMENTS_CHECK` 組み込みインテントを指示します。

`actions.intent.TRANSACTION_REQUIREMENTS_CHECK` インテントを含むレスポンスを受け取った後、Actions on Googleはユーザの状態を
チェックします。

# ユーザが「アクションの決済でトランザクションを試す」と言った時

![]({{ "/images/2018/04/t_api_ja_7.webp" | prepend: site.baseurl }})

「アクションの決済でトランザクションを試す」というユーザフレーズは、"transaction_check_action" という名前のインテントに
登録されています。

![]({{ "/images/2018/04/t_api_ja_8.webp" | prepend: site.baseurl }})

前のインテントと同様に、フルフィルメントが呼ばれます。そのコードは、以下です。

```js
app.intent('transaction_check_action', (conv) => {
  conv.ask(new TransactionRequirements({
    orderOptions: {
      requestDeliveryAddress: false,
    },
    paymentOptions: {
      actionProvidedOptions: {
        displayName: 'VISA-1234',
        paymentType: 'PAYMENT_CARD',
      },
    },
  }));
});
```

このコードはまた、 `TransactionRequirements` クラスを使って、 `actions.intent.TRANSACTION_REQUIREMENTS_CHECK` ビルトイン
インテントを要求します。しかし、このハンドラコードにおいては、このアプリでの自身の決済を指示するために、いくつかの
設定がセットされています。設定のオプションは、 [`GoogleActionsV2TransactionRequirementsCheckSpec`](https://actions-on-google.github.io/actions-on-google-nodejs/interfaces/actionssdk_api_v2.googleactionsv2transactionrequirementscheckspec.html) として
定義されています。具体的には、2つのプロパティがあります。

* `orderOptions` - [`GoogleActionsV2OrdersOrderOptions`](https://actions-on-google.github.io/actions-on-google-nodejs/interfaces/actionssdk_api_v2.googleactionsv2ordersorderoptions.html) クラスで表現されます。開発者は、カスタマー情報と、配送先住所を要求するかどうか
のフラグを指定することができます。
* `paymentOptions` - [`GoogleActionsV2OrdersPaymentOptions`](https://actions-on-google.github.io/actions-on-google-nodejs/interfaces/actionssdk_api_v2.googleactionsv2orderspaymentoptions.html) クラスによって表現されます。

このケースでは、ユーザがアクションの決済を選択しているので、 `actionProvidedOptions` プロパティが指定されています。
開発者は、 [`GoogleActionsV2OrdersActionProvidedPaymentOptions`](https://actions-on-google.github.io/actions-on-google-nodejs/interfaces/actionssdk_api_v2.googleactionsv2ordersactionprovidedpaymentoptions.html) クラスで定義されている
 `displayName` と `paymentType` の2つの値を提供する必要があります。

Actions on Googleは、このレスポンスに従って、ユーザの状態をチェックします。

# ユーザが「Googleの決済でトランザクションを試す」と言った時

![]({{ "/images/2018/04/t_api_ja_9.webp" | prepend: site.baseurl }})

最後のユーザフレーズは、「Googleの決済でトランザクションを試す」です。このフレーズは、"transaction_check_google" インテント
で定義されています。

![]({{ "/images/2018/04/t_api_ja_10.webp" | prepend: site.baseurl }})

このインテントは、上の他のインテントとほとんど同じです。もちろん、このインテントはフルフィルメントを使います。そして、
フルフィルメントのコードは、以下となります。

```js
app.intent('transaction_check_google', (conv) => {
  conv.ask(new TransactionRequirements({
    orderOptions: {
      requestDeliveryAddress: false,
    },
    paymentOptions: {
      googleProvidedOptions: {
        prepaidCardDisallowed: false,
        supportedCardNetworks: ['VISA', 'AMEX'],
        // These will be provided by payment processor,
        // like Stripe, Braintree, or Vantiv.
        tokenizationParameters: {},
      },
    },
  }));
});
```

コードのコメント通り、このハンドラコードは完全ではありません。Googleが提供する決済手段を使うためには、
[`GoogleActionsV2OrdersGoogleProvidedPaymentOptions`](https://actions-on-google.github.io/actions-on-google-nodejs/interfaces/actionssdk_api_v2.googleactionsv2ordersgoogleprovidedpaymentoptions.html) クラスで定義された `googleProvidedOptions` プロパティを
指定する必要があります。上記のコードでは、 `tokenizationParameters` 値が指定されていません。実際には、あなたは
そのプロパティに何らかの値を指定する必要があります。そのプロパティは、以下のようにドキュメントで説明されています。

> These tokenization parameters will be used for generating payment token for use in transaction. The app should get these parameters from their payment gateway.

これは、Stripe, Braintree, またはVanityのような何らかの決済ゲートウェイから決済トークンを発行して入手する必要がある
ことを意味しています。その際に、本番用とテスト用の2つを得るかもしれません。もしサンドボックスにてトランザクションの実施を
試す場合は、テストのための決済トークンを指定する必要があります。

`tokenizationParameters` 値が無指定であり、そしてユーザがGoogle決済を使用しようとする時は、Googleアシスタントは成功の
レスポンスを返します。しかし、トランザクションを確認する際に、このトランザクションは失敗するでしょう。

# ユーザの状況のチェック後

![]({{ "/images/2018/04/transactions_api_11.webp" | prepend: site.baseurl }})

Actions on Googleは、ユーザがトランザクションを実行可能かどうかをチェックします。チェック後に、Actions on Googleは
`actions.intent.TRANSACTION_REQUIREMENTS_CHECK` イベントをDialogflowに送信します。このサンプルでは、このイベントは
"transaction_check_complete" インテントによって受け取られます。

![]({{ "/images/2018/04/transactions_api_12.webp" | prepend: site.baseurl }})

このインテントは、Training Phraseを持ちません。その代わり、`actions.intent.TRANSACTION_REQUIREMENTS_CHECK` イベント名が
セットされています。

前のインテントの呼び出しでは、Dialogflowは `actions.intent.TRANSACTION_REQUIREMENTS_CHECK` ビルトインインテントを含む
レスポンスを返しました。Actions on Googleによってそのレスポンスが受け取られた後、Actions on Googleはユーザの状態をチェックし、
その後 `actions.intent.TRANSACTION_REQUIREMENTS_CHECK` イベントが Actions on Google から Dialogflow に送信されます。
これは、フローの間にユーザのインタラクションがないことを意味しています。これは "サイレントインテント" と呼ばれています。

イベントの送信時に、リクエストはトランザクションのチェック結果を持っています。フルフィルメントのハンドラコードにおいて、
あなたは以下のようにして結果を知ることができます。

```js
app.intent('transaction_check_complete', (conv) => {
  const arg = conv.arguments.get('TRANSACTION_REQUIREMENTS_CHECK_RESULT');
  if (arg && arg.resultType ==='OK') {
    // Normally take the user through cart building flow
    conv.ask(`Looks like you're good to go! ` +
      `Try saying "Get Delivery Address".`);
  } else {
    conv.close('Transaction failed.');
  }
});
```

`TRANSACTION_REQUIREMENTS_CHECK_RESULT` 引数の値が `OK` の場合は、ユーザの状態は問題ないということです。そうではない
場合は、ユーザはトランザクションを実行することができません。上のサンプルにおいて、ユーザの状態がOKではない際には、
`conv.close()` を使って会話が閉じられます。

ユーザはアプリから以下のようなレスポンスメッセージを見るでしょう。

![]({{ "/images/2018/04/t_api_ja_13.webp" | prepend: site.baseurl }})

# ユーザが「配送先住所を得る」と言った際

![]({{ "/images/2018/04/t_api_ja_14.webp" | prepend: site.baseurl }})

前のメッセージにおいて、ユーザはトランザクション向けの配送先住所を決めることを言うように誘導されます。ユーザがその
フレーズを言った時、"delivery_address" という名前のインテントが呼び出されます。

![]({{ "/images/2018/04/t_api_ja_15.webp" | prepend: site.baseurl }})

基本的に、このインテントは、フルフィルメントを呼び出すだけのとてもシンプルなものです。このサンプルのフルフィルメントの
ハンドラ関数は、以下となります。

```js
app.intent('delivery_address', (conv) => {
  conv.ask(new DeliveryAddress({
    addressOptions: {
      reason: 'To know where to send the order',
    },
  }));
});
```

このハンドラ関数は、いかなるレスポンステキストも送信しません。その代わり、
[`DeliveryAddress`](https://actions-on-google.github.io/actions-on-google-nodejs/classes/conversation_question.deliveryaddress.html)
クラスのインスタンスを `conv.ask()` 関数に渡します。インスタンスを渡すと、Dialogflowは Actions on Googleに
`actions.intent.DELIVERY_ADDRESS` ビルトインインテントを含むレスポンスを返します。

`actions.intent.DELIVERY_ADDRESS` インテントを受け取った後、Actions on Googleはユーザが注文を配送したい住所をユーザに尋ねます。
つまり、ユーザは以下のようなメッセージをGoogleアシスタントから受け取ります。

![]({{ "/images/2018/04/t_api_ja_16.webp" | prepend: site.baseurl }})

Androidデバイス上のGoogleアシスタントにおいて、ユーザが登録された住所はリッチカードメッセージとして表示されます。
ユーザは配送したい住所のカードをタップします。

# ユーザがカードをタップした時

![]({{ "/images/2018/04/transactions_api_17.webp" | prepend: site.baseurl }})

カードをタップすることで、配送先住所が決定します。その後、ユーザが配送先住所を決定するために、Dialogflowに
`actions.intent.DELIVERY_ADDRESS` という名前のイベントをActions on Googleが送信します。もちろん、このサンプルでは
そのイベントを受け取るためのインテントを提供しています。その名前は、"delivery_address_complete" です。

![]({{ "/images/2018/04/transactions_api_18.webp" | prepend: site.baseurl }})

このインテントのタスクは、他のインテントと同じように、フルフィルメントを呼び出すことだけです。このサンプルのハンドラ関数は、
以下となります。

```js
app.intent('delivery_address_complete', (conv) => {
  const arg = conv.arguments.get('DELIVERY_ADDRESS_VALUE');
  if (arg.userDecision ==='ACCEPTED') {
    console.log('DELIVERY ADDRESS: ' +
    arg.location.postalAddress.addressLines[0]);
    conv.data.deliveryAddress = arg.location;
    conv.ask('Great, got your address! Now say "confirm transaction".');
  } else {
    conv.close('I failed to get your delivery address.');
  }
});
``` 

`actions.intent.DELIVERY_ADDRESS` イベントのリクエストは、ユーザが決定した配送先住所の情報を持っています。
あなたは、 `DELIVERY_ADDRESS_VALUE` 引数から、その情報を得ることができます。もしユーザがアプリに配送先住所を渡すことに
同意しなかった場合は、 `arg.userDecision` の値は `ACCEPTED` にはなりませんので、このサンプルでは、アプリは `conv.close()`
を使って会話を終えています。

ユーザの同意後、注文を作成する時に使うため、取得された配送先住所を `conv.data` に保存しています。最後に、このアプリは
レスポンスメッセージによって次の手順に誘導します。

![]({{ "/images/2018/04/t_api_ja_19.webp" | prepend: site.baseurl }})

# 「トランザクションを確認する」とユーザが言った時

![]({{ "/images/2018/04/t_api_ja_20.webp" | prepend: site.baseurl }})

ユーザの配送先住所を得た後、ユーザは「トランザクションを確認する」と言った際には、"transaction_decision"という名前の
インテントが呼ばれます。

![]({{ "/images/2018/04/t_api_ja_21.webp" | prepend: site.baseurl }})

ここでの重要なポイントはインテントではなく、フルフィルメントのコードです。インテントを処理する関数は、ユーザに対象の
注文のサマリーを提示し、そしてユーザがトランザクションを実行したいかどうかを確認する責務があります。
注文情報を作成するために、コードサイズは長くなる傾向があるでしょう。

では、フルフィルメントのコードを見てみましょう。最初に、以下のコードは注文情報を作成するためのものです。
数カ所を省略していることに注意してください。

```js
app.intent('transaction_decision_action', (conv) => {
  const order = {
    id: UNIQUE_ORDER_ID,
    cart: {
      merchant: {
        id: 'book_store_1',
        name: 'Book Store',
      },
      lineItems: [
        {
          name: 'My Memoirs',
          id: 'memoirs_1',
          price: {
            amount: {
              currencyCode: 'USD',
              nanos: 990000000,
              units: 3,
            },
            type: 'ACTUAL',
          },
          quantity: 1,
          subLines: [
            {
              note: 'Note from the author',
            },
          ],
          type: 'REGULAR',
        },
        ...
      ],
      notes: 'The Memoir collection',
      otherItems: [
        {
          name: 'Subtotal',
          id: 'subtotal',
          price: {
            amount: {
              currencyCode: 'USD',
              nanos: 220000000,
              units: 32,
            },
            type: 'ESTIMATE',
          },
          type: 'SUBTOTAL',
        },
        {
          name: 'Tax',
          id: 'tax',
          price: {
            amount: {
              currencyCode: 'USD',
              nanos: 780000000,
              units: 2,
            },
            type: 'ESTIMATE',
          },
          type: 'TAX',
        },
      ],
    },
    otherItems: [],
    totalPrice: {
      amount: {
        currencyCode: 'USD',
        nanos: 0,
        units: 35,
      },
      type: 'ESTIMATE',
    },
  };
```

注文情報は、[`GoogleActionsV2OrdersProposedOrder`](https://actions-on-google.github.io/actions-on-google-nodejs/interfaces/actionssdk_api_v2.googleactionsv2ordersproposedorder.html) クラスによって表現されます。これはいくつかの部分で構成されます。具体的には、
そのオブジェクトは、このサンプル内で以下のようなプロパティを持ちます。

* `id` - 注文のユニークなID。
* `cart` - ユーザが注文する商品。
* `totalPrice` - 注文の総合計額。

さらに、 [`GoogleActionsV2OrdersCart`](https://actions-on-google.github.io/actions-on-google-nodejs/interfaces/actionssdk_api_v2.googleactionsv2orderscart.html) クラスによって表現される `cart` は、以下のプロパティを持ちます。

* `merchant` - この注文のマーチャントの情報。
* `lineItems` - ユーザが注文している商品やサービス。
* `otherItems` - このサンプルでは、このプロパティは小計と税の情報を持ちます。

このサンプルでは、ユーザは配送先住所を決定することができます。しかし、ユーザはそれを省略することもできます。代わりに、ユーザは
「配送先住所を得る」と言わずに「トランザクションを確認する」と言うことができます。もしユーザが配送先住所の指定を省略した場合は、
`conv.data` セッションオブジェクトは配送先住所の情報を持っていません。一方、もし `conv.data` にその情報が存在する場合は、注文情報
の中に配送先住所を含めることができます。以下のコードは、その処理を行っています。

```js
  if (conv.data.deliveryAddress) {
    order.extension = {
      '@type': GENERIC_EXTENSION_TYPE,
      'locations': [
        {
          type: 'DELIVERY',
          location: {
            postalAddress: conv.data.deliveryAddress.postalAddress,
          },
        },
      ],
    };
  }
```

はい、あなたは完全な注文情報オブジェクトを得ました。今、あなたは注文を確認してトランザクションを実行するかどうかをユーザに尋ねることが
必要です。これを行うために、あなたは [`TransactionDecision`](https://actions-on-google.github.io/actions-on-google-nodejs/classes/conversation_question.transactiondecision.html) クラスを使います。このサンプルでは、
`TransactionDecision` インスタンスが `conv.ask()` 関数に以下のように渡されています。

```js
  // To test payment w/ sample,
  // uncheck the 'Testing in Sandbox Mode' box in the
  // Actions console simulator
  conv.ask(new TransactionDecision({
    orderOptions: {
      requestDeliveryAddress: true,
    },
    paymentOptions: {
      actionProvidedOptions: {
        paymentType: 'PAYMENT_CARD',
        displayName: 'VISA-1234',
      },
    },
    proposedOrder: order,
  }));
``` 

上記のコードは、アクションによって提供される決済方法のためのものです。つまり、 `actionProvidedOptions` 値が指定されています。
もし決済が必要なければ、 `paymentOptions` の値は省略することができます。一方、アプリが Googleによって提供される決済方法を使う場合は、
あなたは上記のコードの代わりに、このサンプル内の以下のコードを有効にする必要があります。

```js
  // If using Google provided payment instrument instead
  conv.ask(new TransactionDecision({
    orderOptions: {
      requestDeliveryAddress: false,
    },
    paymentOptions: {
      googleProvidedOptions: {
        prepaidCardDisallowed: false,
        supportedCardNetworks: ['VISA', 'AMEX'],
        // These will be provided by payment processor,
        // like Stripe, Braintree, or Vantiv.
        tokenizationParameters: {},
      },
    },
    proposedOrder: order,
  }));
```
このコードは、 `googleProvidedOptions` プロパティを、 `actionProvidedOptions` の代わりに使います。もちろん、あなたは
`tokenizationParameters` 値を、あなたが使用する決済プロバイダ向けに指定しなければなりません。

` TransactionDecision` インスタンスを渡すことで、 `actions.intent.TRANSACTION_DECISION` と言う名前のイベントが Actions on Google
から返されます。

Actions on Googleが `actions.intent.TRANSACTION_DECISION` イベントを受け取った際に、Googleアシスタントはユーザにレスポンスメッセージ
として注文のサマリーを以下のように表示します。

![]({{ "/images/2018/04/t_api_ja_22.webp" | prepend: site.baseurl }})

続きは以下です。

![]({{ "/images/2018/04/t_api_ja_23.webp" | prepend: site.baseurl }})

ユーザが「注文を確定」と言った際には、Googleアシスタントは以下のような決済UIを表示します。

![]({{ "/images/2018/04/t_api_ja_24.webp" | prepend: site.baseurl }})

トランザクションを確定するために、ユーザはパスワードを入力し、"APPROVE"ボタンをクリックする必要があります。このとき、注文の状態は `PENDING` です。

![]({{ "/images/2018/04/t_api_ja_28.webp" | prepend: site.baseurl }})

# ユーザが決済を承認したとき

![]({{ "/images/2018/04/transactions_api_26.webp" | prepend: site.baseurl }})

もしユーザが決済を承認すれば、Actions on Googleは `actions.intent.TRANSACTION_DECISION` イベントをDialogflowに送信します。
このサンプルでは、そのイベントは "transaction_decision_complete" と言う名前のインテントで受け取られます。

![]({{ "/images/2018/04/transactions_api_25.webp" | prepend: site.baseurl }})

このイベントを処理するためのハンドラコードは、以下のようになります。最初に、あなたはユーザの決定をチェックします。これを行うために、
あなたは `conv.arguments.get('TRANSACTION_DECISION_VALUE')` 引数の `userDecision` 値をチェックすることができます。

```js
app.intent('transaction_decision_complete', (conv) => {
  console.log('Transaction decision complete');
  const arg = conv.arguments.get('TRANSACTION_DECISION_VALUE');
  if (arg && arg.userDecision ==='ORDER_ACCEPTED') {
    ...
```

もし userDecision 値が `ORDER_ACCEPTED` だった場合、これはユーザが決済を承認したことを意味しています。このとき、あなたは
注文の状態を `CREATED` に更新することが必要です。 [`OrderUpdate`](https://actions-on-google.github.io/actions-on-google-nodejs/classes/conversation_response.orderupdate.html) クラスがその更新に使われます。

```js
    ...
    const finalOrderId = arg.order.finalOrder.id;

    // Confirm order and make any charges in order processing backend
    // If using Google provided payment instrument:
    // const paymentDisplayName = arg.order.paymentInfo.displayName;
    conv.ask(new OrderUpdate({
      actionOrderId: finalOrderId,
      orderState: {
        label: 'Order created',
        state: 'CREATED',
      },
      lineItemUpdates: {},
      updateTime: new Date().toISOString(),
      receipt: {
        confirmedActionOrderId: UNIQUE_ORDER_ID,
      },
      // Replace the URL with your own customer service page
      orderManagementActions: [
        {
          button: {
            openUrlAction: {
              url: 'http://example.com/customer-service',
            },
            title: 'Customer Service',
          },
          type: 'CUSTOMER_SERVICE',
        },
      ],
      userNotification: {
        text: 'Notification text.',
        title: 'Notification Title',
      },
    }));
    ...
```

このサンプルの上記のコードでは、以下のプロパティがセットされます。

* `orderState` - `CREATED` という新しい状態を指定します。
* `receipt` - レシートに関連付けるための注文IDを指定します。
* `orderManagementActions` - カスタマーサポート情報を指定します。
* `userNotification` - 通知のテキストやタイトルです。

最後に、ユーザにトランザクションの完了を知らせるために、 `conv.ask()` 関数を使ってレスポンスメッセージを送信します。 

```js
    ...
    conv.ask(`Transaction completed! You're all set!`);
  } else if (arg && arg.userDecision === 'DELIVERY_ADDRESS_UPDATED') {
    conv.ask(new DeliveryAddress({
      addressOptions: {
        reason: 'To know where to send the order',
      },
    }));
  } else {
    conv.close('Transaction failed.');
  }
});
```

もし `userDecision` 値が `DELIVERY_ADDRESS_UPDATED` の場合は、これはユーザが配送先住所を更新したいことを意味しています。
この場合は、前のセクションで説明した、 `DeliveryAdderss` クラスを使って配送先住所を決定するようにユーザを誘導する必要があります。

上記のコードの実行後、Googleアシスタントはユーザに以下のようなレスポンスメッセージを送信します。

![]({{ "/images/2018/04/t_api_ja_27.webp" | prepend: site.baseurl }})

Googleアシスタントの注文履歴ページでは、ユーザは最新状態である `ORDER_CREATED` を見ることができます。 

![]({{ "/images/2018/04/t_api_ja_29.webp" | prepend: site.baseurl }})

# まとめ

このエントリでは、Actions on GoogleのTransactions APIの振る舞いについて説明しました。これを読むことで、
処理手順や各コード、アーキテクチャについて理解が深まると嬉しいです。
