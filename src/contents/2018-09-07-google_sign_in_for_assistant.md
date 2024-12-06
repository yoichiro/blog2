---
layout: post
title: Google Sign-In for the Assistantが日本語でも利用可能になりました
categories:
- actions on google
---

Googleアシスタントの利用用途は多岐にわたります。その中には、誰が利用しているのか、それをはっきりさせることで、高度なパーソナライズやサービスの提供をすることができるようになることも多いかと思います。ネットサービスのほとんどが、ログイン、つまりユーザ認証を求めてくることは誰しもが実体験していることですが、Googleアシスタントにおいても、アクションを提供する上でユーザ認証は非常に重要です。そして、そのユーザ認証のためにユーザが行う手順が簡単であればあるほど、ユーザ認証してくれる可能性は高まります。また、Google Homeといったスマートスピーカーなど「音声のみ」を扱うデバイスでもそれが可能であれば、言うことなしです。

Google Sign-In for the Assistantは、Googleアカウントを使ってアクション内で簡単にユーザ認証を行うための機能です。アクションのユーザにとっても、アクションの開発者にとっても、とても簡単にその機能を利用することができます。このエントリは、2018年9月6日に書いているのですが、先週くらいまでは Developer Preview 扱いだった機能であり、en-US ロケールでは機能するも、ja-JP ロケールでは使うことができませんでした。しかし、公式ドキュメントも更新され、正式にリリースされた模様です。もちろん、ja-JP ロケールでも利用可能になりました。

[Account linking with Google Sign-In](https://developers.google.com/actions/identity/google-sign-in) - Actions on Google

さっそく、組み込み方を紹介してみましょう。

# Actions on Google Consoleでの設定

まず、コードを書く前に、Actions on Googleのコンソールにて、Google Sign-In for the Assistantを利用するための設定を行います。これをしておかないと、コードからGoogle Sign-In for the Assistantを使おうとした際にエラーが発生してしまいますので、注意が必要です。

Actions on Googleに作成されたプロジェクトにて、Actions on Google Consoleの左メニューに "Account Linking" というメニュー項目がありますので、まずはこれをクリックします。

![]({{ "/images/2018/09/gsi4a_01.webp" | prepend: site.baseurl }})

ページが切り替わった後、右側で "Yes, allow users to sign up for new accounts via voice" を選択して、NEXT ボタンを押します。

![]({{ "/images/2018/09/gsi4a_02.webp" | prepend: site.baseurl }})

次に、Linking type として、"Google Sign In" を選択して、NEXT ボタンを押します。

![]({{ "/images/2018/09/gsi4a_03.webp" | prepend: site.baseurl }})

ここまで来ると、Client ID が発行されて表示されます。グレーになっていますが、テキストフィールド内の Client ID 文字列をメモしておきます。この Client ID 文字列は、あとでコードに記載することになります。メモしたら、右下の SAVE ボタンを押します。

![]({{ "/images/2018/09/gsi4a_04.webp" | prepend: site.baseurl }})

以上で、Actions on Google Console 側での設定は完了です。

# コードからのGoogle Sign-In for the Assistantの呼び出し

では、コードから Google Sign-In for the Assistant を利用してみましょう。 [actions-on-google-nodejs](https://github.com/actions-on-google/actions-on-google-nodejs) というSDKを使っている場合には、Google Sign-In for the Assistant を利用することはとても簡単です。

## Client IDの設定

先ほどの Actions on Google Console の設定の結果、手元に Client ID 値があると思います。SDK の利用を始める際に、メモしておいた Client ID を渡します。

Dialogflow を使っている場合は、以下のようなコードを書きます。

```js
const {
  dialogflow
} = require("actions-on-google");

const app = dialogflow({
  clientId: <YOUR_CLIENT_ID>
});
```

Actions SDK を使っている場合は、以下のようなコードになります。

```js
const {
  actionssdk
} = require("actions-on-google");

const app = actionssdk({
  clientId: <YOUR_CLIENT_ID>
});
```

## サインインの要求を出す

ユーザとの会話の中で、ユーザ認証を行いたくなったタイミングがあったとします。その時に、SDK が提供してくれている [`SignIn`](https://actions-on-google.github.io/actions-on-google-nodejs/classes/conversation_question.signin.html) クラスを使うことで、Google Sign-In for the Assistant でユーザ認証したいことを Actions on Google 側に伝えることができます。

Dialogflow を使っている場合は、以下のようなコードを書きます。

```js
const {
  dialogflow,
  SignIn
} = require("actions-on-google");

...

app.intent("Start Sign-in", conv => {
  conv.ask(new SignIn("パーソナライズするために"));
});
```

Actions SDK を使っている場合は、以下のようなコードになります。Dialogflow の場合と、ほとんど同じです。

```js
const {
  actionssdk,
  SignIn
} = require("actions-on-google");

...

app.intent("actions.intent.TEXT", conv => {
  conv.ask(new SignIn("パーソナライズするために"));
});
```

これにより、Googleアシスタントはユーザにサインインを求められていることを伝えます。Actions Simulator で試してみた様子が以下です。ここでは、アクション名は「あれこれアクション」という名前にしています。「サインインしたい」というフレーズに反応する Intent を作ってあって、その Intent を処理する Fulfillment コードから SignIn オブジェクトを返した、という流れです。

![]({{ "/images/2018/09/gsi4a_05.webp" | prepend: site.baseurl }})

文面のテンプレートは、日本語では以下のようになっているっぽいですね。

```txt
<CONTEXT_STRING>、<ACTION_NAME> のアカウントが必要です。 アカウント作成のために、氏名、メールアドレス、プロフィール写真を Google から取得してもよろしいですか。 「はい」か「いいえ」で答えてください。 詳しく知りたい場合は、「詳細を確認」と言ってください。
```

`<CONTEXT_STRING>` は、 `SignIn` クラスのコンストラクタに渡した文字列です。「詳細を確認」と返事をすると、Googleアシスタントから以下のように返ってきます。

![]({{ "/images/2018/09/gsi4a_06.webp" | prepend: site.baseurl }})

ユーザは「はい」か「いいえ」で許可するかどうか答えます。

## サインイン結果の取得

Googleアシスタントからのユーザ認証要求に対して、ユーザは「はい」か「いいえ」で答えます。その後、Actions on Google はユーザ認証結果をアクションに届けるために、 `actions_intent_SIGN_IN` イベントを送信してきます。アクションの開発者は、このイベントを処理する Fulfillment を準備することになります。

Dialogflow を使っている場合は、 `actions_intent_SIGN_IN` イベントを受け取るための Intent を作ります。ここではその Intent の名前を "Get Signin" としましょう。そして、その Intent を処理する Fulfillment コードを以下のようにします。

```js
app.intent("Get signin", (conv, params, signin) => {
  if (signin.status === "OK") {
    conv.ask("サインインしました。");
  } else {
    conv.ask("パーソナライズするためには、サインインしてください。");
  }
});
```

Actions SDK を使っている場合は、以下のようになります。これも、Dialogflow とほとんど一緒です。

```js
app.intent("actions_intent_SIGN_IN", (conv, params, signin) => {
  if (signin.status === "OK") {
    conv.ask("サインインしました。");
  } else {
    conv.ask("パーソナライズするためには、サインインしてください。");
  }
});
```

ユーザ認証結果は、 `app.intent()` 関数に渡すコールバック関数の第3引数に渡されます。この第3引数は、 [`SignInValue`](https://developers.google.com/actions/reference/rest/Shared.Types/SignInValue) で定義されていて、その `status` 値としては、 [`SignInStatus`](https://developers.google.com/actions/reference/rest/Shared.Types/SignInStatus) で定義された値のどれかが渡されてきます。基本的には、 "OK" が来ればユーザ認証にユーザが同意した、それ以外だったらユーザ認証にユーザが同意しなかった、と見なして処理をすると良いでしょう。

もしユーザが「いいえ」と言った際に、上記のコード例では「パーソナライズするためには、サインインしてください。」という返事を返しています。ユーザにこの文章だけが送信されるかというと、違うようです。実際には、以下のような返事となります。

![]({{ "/images/2018/09/gsi4a_07.webp" | prepend: site.baseurl }})

サインインしないと機能制限されるかも、という強めの表現になっていますが、その文章が固定的に付加されます。

ユーザが「はい」と言うと、その時点でサインインが確定するのではなく、もう一度Googleアシスタントは以下のようにユーザに問いかけてきます。

![]({{ "/images/2018/09/gsi4a_08.webp" | prepend: site.baseurl }})

日本語として、変ですね。「または、」は必要ないように思えます。ここでユーザは再度「いいえ」と言ってユーザ認証を拒否することができます。その際には、先ほどと同じように `signin.status` 値は "OK" にはなりません。

Googleアカウントからの再度の問い合わせに「はい」とユーザが答えた場合には、サインインが確定し、 `signin.status` 値として "OK" が渡されてきます。上記のコード例では、アクションからの返事として「サインインしました。」と返していますが、先ほどと同じようにGoogleアシスタントは文章を付け加えます。

![]({{ "/images/2018/09/gsi4a_09.webp" | prepend: site.baseurl }})

これとほぼ同時に、以下のようなメールが届きます。

![]({{ "/images/2018/09/gsi4a_10.webp" | prepend: site.baseurl }})

このメールには、ユーザ認証を取り消すための画面へのURLなどが記載されています。

## ユーザ情報の取得

Google Sign-In for the Assistant によってユーザ認証された後、アクションはそのユーザに関するプロフィール情報を得ることができます。具体的には、ユーザ認証の後、ユーザがそのアクションに何か言う度に、Actions on Google はアクションへのリクエストに JWT による ID Token を含めます。actions-on-google-nodejs SDK には、この ID Token の妥当性を検証し、扱いやすい形でユーザ情報を Fulfillment に提供してくれる機能があります。

ユーザ情報は、 `conv.user.profile.payload` で取得できます。逆に、その値が取得できなければ、まだサインインされていない、というように判断することもできます。新規に Intent を作成して、ユーザ情報を取得して BasicCard を使ってユーザ情報を表示するコードは、以下のようになります。

```js
const {
  ...
  BasicCard,
  Image
} = require("actions-on-google");

app.intent("Show User Profile", conv => {
  const payload = conv.user.profile.payload;
  if (payload) {
  
    const userId = payload.aud;
    const name = payload.name;
    const givenName = payload.given_name;
    const familyName = payload.family_name;
    const email = payload.email;
    const emailVerified = payload.email_verified;
    const picture = payload.picture;
    
    conv.ask("これがあなたのプロフィール情報です。");
    conv.ask(new BasicCard({
      text: `ID:${userId}  
      Name:${name}  
      Given name:${givenName}  
      Family name:${familyName}  
      Email:${email}  
      Email verified:${emailVerified}`,
      image: new Image({
        url: picture,
        alt: "Profile Image"
      })
    }));
  } else {
    conv.ask("まだサインインしていません。");
    conv.ask(new Suggestion("サインインしたい"));
  }
});
```

サインイン後に上記コードが実行された結果、Googleアシスタントはユーザに以下のような返事をします。Actions Simulator で試しに行ってみた結果です。

![]({{ "/images/2018/09/gsi4a_11.webp" | prepend: site.baseurl }})

Android の Google アシスタントでは、以下のように表示されました。ほとんど一緒ですが。

![]({{ "/images/2018/09/gsi4a_12.webp" | prepend: site.baseurl }})

手元で動作確認したところ、上記のコード例のように、以下のユーザ情報を取得することができるっぽいです。

* ユーザID: `conv.user.profile.payload.aud`
* 氏名: `conv.user.profile.payload.name`
* 氏: `conv.user.profile.payload.family_name`
* 名: `conv.user.profile.payload.given_name`
* プロフィール画像のURL: `conv.user.profile.payload.picture`
* メールアドレス: `conv.user.profile.payload.email`
* メールアドレスが検証済みか: `conv.user.profile.payload.email_verified`

# Google Sign-In for the Assistantではできないこと

Google Sign-In for the Assistantは、あくまで「Googleアカウントを使ったユーザ認証」のための機能です。そのため、以下のことは Google Sign-In for the Assistant だけではできませんので、ご注意ください。

* Actions on Google からアクションに渡されるものは、ID Token です。Access Token は渡されません。
* 取得可能なユーザ情報は、上記で述べた項目のみです。「追加で○○も欲しい！」というような指定を行うことは、現状ではできません。

# まとめ

OAuth による Account Linking では、Google Sign-In は対象外となっていて、Actions on Google の規約上も Google アカウントによるユーザ認証をしたければ Google Sign-In for the Assistant を使わなければならない、という決まりになっています。しかし、今まで Google Sign-In for the Assistant は Developer Preview であり、日本語ロケールでは使えませんでした。結構多くの方々が、Google Sign-In for the Assistant を待っていたのではないでしょうか？

ちょっと日本語が変な箇所もありますが、皆さんが開発されているアクションに Google Sign-In for the Assistant を組み込むことができますので、ぜひチャレンジしてみてください。このユーザ認証機能によって、アクションがさらにリッチに、そして更に価値あるアクションになることを期待しています！
