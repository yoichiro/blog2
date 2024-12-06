---
layout: actions_on_google
title: Account Linking "Implicit Flows"の日本語訳です
categories:
- actions on google
---
Googleアシスタント向けアプリにおいて、ユーザ認証は非常に重要な機能となります。特に、アシスタント向けアプリの開発者が持つサービスのアカウントとのリンクについてが重要であり、これはアカウントリンクという仕組みで実現されます。それにはOAuth 2.0のフローが適用されるため、もしOAuth 2.0についての知見があれば、スムーズにその仕組みを理解し実装することができると思います。

Actions on Googleのドキュメントにある
[Account Linking - Implicit Flows](https://developers.google.com/actions/identity/oauth2-implicit-flow)にて、
アカウントリンクにOAuth 2.0 Implicit Flowを適用するための方法が説明されています。以下はその日本語訳です。

---

# OAuth 2.0 Implicit Flowを使ったGoogleとの統合

このドキュメントでは、あなたのサービスをGoogleと統合するのに十分な、 _implicit flow_ をサポートする最小のOAuth 2.0サーバーを実装する方法について説明します。Implicit flowは実装が簡単ですが、サポートされている他のOAuth 2.0フローと比較すると、いくつかのトレードオフがあります。

## 概要

OAuth 2.0 implicit flow をサポートするために、あなたのサービスはHTTPSによって利用可能な認可エンドポイントを作成します。このエンドポイントは、データアクセスのためにユーザーを認証し、同意を得る責任があります。認可エンドポイントは、まだサインインされていないユーザーにサインインUIを提示し、要求されたアクセスへの同意を記録します。

Googleがあなたのサービスの認可APIの1つを呼び出す必要がある場合、Googleはこのエンドポイントを使用して、ユーザーに変わってサービスがAPIを呼び出す権限を取得します。Googleによって開始された典型的なOAuth 2.0 implicit flowセッションは、次のような流れになります。

1. まず、Googleはユーザーのブラウザに認可エンドポイントを開きます。既にサインインしていない場合はユーザーがサインインし、アクセス許可をまだ与えていない場合は、APIでデータにアクセスする許可をGoogleに付与します。
1. その後、サービスは _アクセストークン_ を作成し、アクセストークンをリクエストに添付してユーザーのブラウザをGoogleにリダイレクトして、アクセストークンをGoogleに返します。
1. 最後に、GoogleはサービスのAPIを呼び出し、リクエストごとにアクセストークンを添付します。サービスは、アクセストークンがAPIにアクセスするためのGoogle認可を許可していることを確認し、API呼び出しを完了します。

## 始める前に

OAuth 2.0認可をサービスに追加する前に、次のことを行います。

1. あなたの認可エンドポイントのURLを決定します。例: `https://myservice.example.com/auth` <br>認証エンドポイントは、HTTPS経由でのみアクセス可能でなければなりません。暗号化されていないHTTP要求を受け入れてはいけません。多くの場合、既存のサインインページをこの目的に適用することができます。
1. あなたの選択したクライアントIDをGoogleに割り当てます。これは、Googleがリクエストを発信したことを示す許可エンドポイントに渡します。クライアントIDには、URLで安全な任意の文字列を使用できます。例えば、それは単に `google` かもしれません。
1. Google API Consoleの[Settings page](https://console.developers.google.com/iam-admin/settings/project?project=_)で、あなたのプロジェクトIDを探します。
1. [Enabling Account Linking](https://developers.google.com/actions/develop/identity/account-linking#enabling_account_linking)の手順に従って、上記の情報を登録します。

## 認可リクエストの処理

Googleがサービスのリソースにアクセスする必要がある場合、Googleはユーザーを次の形式のURLで認可エンドポイントに送信します。

```text
GET https://myservice.example.com/auth?client_id=GOOGLE_CLIENT_ID&redirect_uri=REDIRECT_URI&state=STATE_STRING&response_type=token
```

サインインリクエストは、以下のパラメータを含んでいます。

<table style="background-color: #78909c;">
<tr style="color: white;">
<td style="padding: 5px;" colspan="2">認可エンドポイントパラメータ</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.87); padding: 5px;"><b>client_id</b></td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">あなたがGoogleに割り当てたClient IDです。</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.87); padding: 5px;"><b>redirect_uri</b></td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">このリクエストに対してレスポンスを送信するためのURLです。</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.87); padding: 5px;"><b>state</b></td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">リダイレクトURIで変更されずにGoogleに返される検証のための値です。</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.87); padding: 5px;"><b>response_type</b></td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">レスポンスにおいて返される値の種別です。OAuth 2.0 implicit flowにおいては、response typeは常に <b>token</b> です。</td>
</tr>
</table>

あなたの認可エンドポイントは、以下の手順でサインインリクエストを処理します。

1. `client_id` および `redirect_uri` 値を検証します。
   * あなたがGoogleに割り当てたClient IDと `client_id` が一致するか確認します。
   * `redirect_uri` によって指定されたURLが、以下の様式であるかを確認します。
     ```text
     https://oauth-redirect.googleusercontent.com/r/YOUR_PROJECT_ID
     ```
     **YOUR_PROJECT_ID** は、Google API Consoleの [Setting page](https://console.developers.google.com/iam-admin/settings/project?project=_)で見つけることができます。これは、あなたがGoogleに登録したものです。

   これらのチェックは、意図しないクライアントアプリや誤った構成のクライアントアプリへのアクセスを許可しないようにするために重要です。複数のOAuth 2.0フローをサポートする場合は、 `response_type` が `token` であることも確認してください。
1. ユーザーがサービスにログインしているかどうかを確認します。ユーザーがログインしていない場合は、サービスのサインインまたはサインアップのフローを完了します。
1. GoogleがAPIにアクセスするために使用するアクセストークンを生成します。アクセストークンは任意の文字列値にすることができますが、使用するユーザーとクライアントをトークンが一意に表す必要があり、推測できないようにしなければなりません。アクセストークンを実装する一般的な方法は、128ビット（16バイト）のエントロピーで生成され、暗号化されたセキュアな番号で索引付けされたトークンごとにデータベースレコードを作成し、base64でエンコードされたインデックス番号をアクセストークンとして使用することです。たとえば、次のようなAuthorizationsテーブルがあるとします。

    <table style="background-color: #78909c;">
    <tr style="color: white;">
    <td style="padding: 5px;">AccessToken</td>
    <td style="padding: 5px;">UserID</td>
    <td style="padding: 5px;">ClientID</td>
    </tr>
    <tr>
    <td style="background-color: rgba(255,255,255,.95); padding: 5px;">YWJjZGVmZ2hpamtsbW5vcA==</td>
    <td style="background-color: rgba(255,255,255,.95); padding: 5px;">1234</td>
    <td style="background-color: rgba(255,255,255,.95); padding: 5px;">google</td>
    </tr>
    </table>

    アクセストークンを実装する別の方法は、Googleに割り当てたUser IDとClient IDを含むJSONオブジェクトを作成することです。次に、AESなどの対称暗号化アルゴリズムを使用してJSONオブジェクトを暗号化し、その結果の文字列をアクセストークンとして使用します。

    > **注:** Googleでは、implicit flowを使用して発行されたアクセストークンが期限切れにならないようにするので、他のOAuth 2.0フローと同様に、アクセストークンの許可時間を記録する必要はありません。

1. ユーザーのブラウザを `redirect_uri` パラメータで指定されたURLにリダイレクトするHTTPレスポンスを送信します。URLフラグメントに次のパラメータをすべて含めます。

    * `access_token` : あなたが今作成したアクセストークンです。
    * `token_type` : `bearer` 文字列です。
    * `state` : 元のリクエストから未変更のstate値です。

    たとえば、次のようにユーザーをURLにリダイレクトします。

    ```text
    https://oauth-redirect.googleusercontent.com/r/YOUR_PROJECT_ID#access_token=ACCESS_TOKEN&token_type=bearer&state=STATE_STRING
    ```

GoogleのOAuth 2.0リダイレクトハンドラはアクセストークンを受け取り、 `state` 値が変更されていないことを確認します。Googleがサービスのアクセストークンを取得した後、GoogleはサービスのAPIへのその後の呼び出しにトークンを添付します。

## データアクセスリクエストの処理

あなたのサービスのAPIエンドポイントは、アクセストークンを使用して、GoogleがAPI呼び出しを行っているユーザーを識別し、Googleがユーザーの代わりにエンドポイントにアクセスする権限を持っていることを確認します。

サービスがアクセストークンを要求に添付する一般的な方法は、 `Authorization` ヘッダーを使用する方法です。たとえば、Googleがあなたのサービスに対して次のようなリクエストを行う場合があります。

```text
GET /mycontent HTTP/1.1
Host: myservice.example.com
Content-Type: application/x-www-form-urlencoded
Authorization: Bearer ACCESS_TOKEN
```

アクセストークンをリクエストに添付する具体的な方法は、あなたのAPIを呼び出すGoogleサービスによって異なります。詳細については、サービスのデベロッパードキュメントを参照してください。

アクセストークンの送信方法に関係なく、エンドポイントは次の操作を行います。

1. アクセストークンをデータベースのインデックスとして実装した場合は、インデックスが存在することを確認して、認可レコードを取得します。アクセストークンを暗号化オブジェクトとして実装した場合は、選択した暗号化方式を使用してアクセストークンをデコードします。
1. アクセストークンのユーザーIDを使用して、リクエストに応答します。

あなたのOAuth 2.0の実装は、これでGoogleサービスとの統合に十分になります。

## あなたの実装の検証

OAuth 2.0 Playgroundツールを使用して、あなたの実装を検証できます。ツールで、次の手順を実行します。

1. ギアのアイコンをクリックして、OAuth 2.0 設定ウィンドウを開きます。
1. OAuthフローフィールドで、 **Client-side** を選択します。
1. OAuth Endpointsフィールドで、 **Custom** を選択します。
1. 対応するフィールドにOAuth 2.0エンドポイントとGoogleに割り当てたClient IDを指定します。
1. Step 1 セクションにて、何のGoogle scopeを選択しなかった場合は、代わりに、あなたのサーバで有効な scope を入力して、 Authorize APIs をクリックします。
1. Step 2, Step 3 セクションにて、OAuth 2.0フローの手順を実施し、各手順が期待通りに動作するかどうか確認します。

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/identity/oauth2-implicit-flow)
