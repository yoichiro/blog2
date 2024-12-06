---
layout: actions_on_google
title: Account Linking "Overview"の日本語訳です
categories:
- actions on google
---
Googleアシスタント向けアプリでは、開発者が持つサービス側のアカウントと、アシスタント向けアプリを利用する際のGoogleアカウントを、互いに紐付けることをしたくなるはずです。これにより、パーソナライズがさらに加速すると共に、元々開発者が持っていたサービスをアシスタント向けアプリ経由でユーザが利用することができるようになります。このアカウントの紐付けを「アカウントリンク」と呼び、それには「シームレスなアカウントリンク」と「従来のリンクフローを使ったユーザ体験」の2つがポイントとなります。

Actions on Googleのドキュメントにある
[Account Linking - Overview](https://developers.google.com/actions/identity/)にて、
アカウントリンクの概要が説明されています。以下はその日本語訳です。

---

# Googleを使ったアカウントリンク

アカウントリンクは、ユーザーが自分のGoogleアカウントを、あなたのサービス上の既存のアカウントに接続するのに最適です。これにより、あなたのサービス上のアカウントに既に存在するデータを活用する、より豊かなユーザー体験を構築できます。食べ物の嗜好、既存の支払いアカウント、音楽の好みなど、ユーザーはアカウントをリンクすることでGoogleアシスタントの操作性を向上させることができます。

## シームレスなアカウントリンクの利用

シームレスなアカウントリンクは、ユーザーが既存のアカウントにサインインしたり、新しいアカウントを作成したりすることによって、アカウントをリンクするのに最適な体験を提供します。この体験は、ユーザーからの2回のクリックのみであり、ユーザー名、パスワード、または長い構成手順は必要ありません。これは次のようになります。

![](https://developers.google.com/actions/images/account-linking-1.svg){:width="300px"}
![](https://developers.google.com/actions/images/account-linking-2.svg){:width="300px"}

詳細については、Google Assistantでシームレスなアイデンティティ体験をサポートするためにOAuthサーバーを設定する方法についての[効率的なアイデンティティフロー](https://developers.google.com/actions/identity/oauth2-assertion-flow)をご覧ください。

## 従来のリンクフローを使用したユーザー体験

あなたが合理化されたアイデンティティフローをまだサポートする準備が整っていない場合でも、OAuth 2.0リンクフローを使用して従来のアカウントリンクをサポートできます。たとえば、ユーザが音声でアクティブ化されたスピーカと対話している場合、認証フローは次のようになります。

<i>"It looks like your &lt;invocation name&gt; account is not linked yet. You can link &lt;invocation name&gt; to your Google Account from the Google Home app."</i>

ユーザーは、アカウントをリンクするか、新しいアカウントを作成するために、サービスを認証する必要があります。ユーザーがWebアプリのアカウントリンクフローを完了すると、アシスタントアプリを呼び出すことができ、アシスタントアプリはあなたのサービスへのコールを認証できます。

## アカウントリンクの実装

私たちのアカウントリンク手法の一つを使って始めるには、以下のリンクをクリックしてください。

[GET STARTED](https://developers.google.com/actions/identity/account-linking)

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/identity/)
