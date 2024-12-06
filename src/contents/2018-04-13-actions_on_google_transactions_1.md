---
layout: actions_on_google
title: Transactions "Overview"の日本語訳です
categories:
- actions on google
---
Googleアシスタントは、もはや多くのユーザの側にいます。Android端末をお持ちであれば、Googleアシスタントが毎日手の中にいることになりますし、Google Homeを家に置いている方々についても、常に家族の一員としてGoogleアシスタントが側にいます。そんな相棒が、商品の購入や予約をしたいと思った際にも、自然な形でサポートしてくれたら、それは素敵な未来だと思いませんか？でも、未来ではなく、もう既にやってきました。日本語環境においても、Actions on Googleが提供するTransactions APIを使うことで、Googleアシスタント向けアプリにそんな体験を組み込むことができるようになりました。

[Actions on GoogleのTransactions APIが日本語環境でも利用できるようになりました](https://developers-jp.googleblog.com/2018/04/actions-on-google-transactions-api.html)

Actions on Googleのドキュメントにある
[Transactions - Overview](https://developers.google.com/actions/transactions/)にて、
Transactions APIに関する概要が説明されています。以下はその日本語訳です。

---
# トランザクション

トランザクションは、**購入 (purchases)** （例: ピックアップ、配送、チケット）、 **場所の予約 (reservations)** や **人との約束 (appointments)** （例: レストラン、ヘアカット、フィットネスクラスなど）をアプリで処理できます。

トランザクションをあなたのアプリに組み込むことで、自然で会話的な注文体験を通じて、ウェブサイトやモバイルアプリよりもシームレスな注文体験を作ることができます。

シューズストアアプリの例を以下に示します。

![](https://developers.google.com/actions/images/sekai1.svg){:width="385px"}

ユーザーが「カート」を構築している間、価格やサービスが利用できるかといったことを決定するために、配送情報や出荷情報を収集する必要があります。配送住所をあなたのアプリに共有するために、あなたのアプリはGoogleに対してユーザーの許可を要求することができます。

チェックアウトの準備が整ったら、構造化された注文データをTransactions APIに提供することで、アプリは注文確認の体験をトリガーすることができます。その後ユーザは、購入を承認し、注文を受諾または拒否することができます。Googleは、ユーザーが支払設定や配達先住所などを変更できるようにするための、予め組み込まれた体験を提供します。

![](https://developers.google.com/actions/images/sekai2.svg){:width="385px"}


ユーザーが購入を承認した場合、アプリは注文がアクティブであることを確認することで、すぐに対応すべきです。これを行うには、Transactions APIに構造化されたレシートを渡します。これは、あなたのアプリとのやりとりの一環として、リッチなレシートの"カード"としてユーザに提示されます。

その後、ユーザーとのダイアログ（対話）を続行できます。この機会にて、あなたのアプリにてアカウントを作成してもらってユーザがリワードを獲得したり、お気に入りのアイテムを再注文してもらうことができるでしょう。

![](https://developers.google.com/actions/images/sekai3.svg){:width="385px"}

レシートの詳細なバージョンが、ユーザーのアシスタントの注文履歴に表示されます。後に、この領収書に関連付けられた注文IDを使用して、構造化された注文の更新を送信することができます。重要な更新は、プッシュ通知としてユーザーに送信されます。

## Transactions APIの組み込み

次の手順を行って、Transactions APIを組み込みます。

1. Actions Consoleでプロジェクトを作成し、Transactionsをオプトインします。
1. Dialogflowや他のツールを使って、アプリを構築します。
   1. 注文を構築するためにユーザーを誘導するための "カートアセンブリ" 体験を作成します。
   1. 支払方法を準備します。できることは、以下です。
      * ユーザーのGoogleアカウントをあなたのウェブサービスにリンクして、ユーザーがあなたに提供する支払方法にアクセスする
      * Stripe、Braintree、Vantivを使用して、ユーザーがGoogleに保存しているクレジットカードにチャージする
      * 「履行 (on fulfillment)」（例 : 代金引換）の支払いを受けるか、まったく支払いを行わない（一部の予約など）
   1. `actions.intent.TRANSACTION_DECISION` インテントを使用して、注文をユーザーに提案します。
   1. ユーザーが承認したら、構造化された領収書を送信します。
1. サンドボックスモードとプロダクションモードの両方をサポートし、テストします。Actions Web Simulatorではなく、Android M+Nのデバイスのみで、Transactions APIをテストします。
1. Conversation Send APIを使用して、注文のライフサイクルを通じて、構造化された注文更新通知を提供します。
1. Actions Console経由で、アプリを提出します。

[今から構築を開始する](https://developers.google.com/actions/transactions/dev-guide)

## 事前条件

アプリをユーザーに配布する方法については、[ポリシーとガイドライン](https://developers.google.com/actions/transactions/policies-guidelines)のドキュメントを参照してください。

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/transactions/)
