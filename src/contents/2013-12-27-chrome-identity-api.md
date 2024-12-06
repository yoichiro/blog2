---
layout: post
status: publish
published: true
title: chrome.identity APIのリファレンスを和訳してみました
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2569
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2569
date: '2013-12-27 19:26:32 +0900'
date_gmt: '2013-12-27 10:26:32 +0900'
categories:
- Chrome extension
---

Chromeのstable 29から、OAuth2を拡張機能内で扱うためのAPIが搭載されました。APIリファレンスを和訳してみましたので、興味のある方は参考にしてください。

[Original: http://developer.chrome.com/extensions/identity.html](http://developer.chrome.com/extensions/identity.html)

# chrome.identity

説明: OAuth2アクセストークンの取得のためにchrome.identity APIを使います。
利用可能環境: Chrome 29 安定版以降
権限: "identity"
詳しい説明: 
[Identify User](http://developer.chrome.com/extensions/app_identity.html)

# Methods

getAuthToken

```
chrome.identity.getAuthToken(object details, function callback)
```

[manifest.jsonのoauth2セクション](http://developer.chrome.com/extensions/app_identity.html#update_manifest)にて指定されたClient IDおよびスコープを使って、OAuth2アクセストークンを取得します。

Identity APIは、メモリにアクセストークンをキャッシュしますので、トークンが必要になる時はいつでもgetAuthTokenを呼び出して大丈夫です。トークンキャッシュは、失効を自動的に扱います。



* details (optional object): トークンオプション。

* interactive (optional boolean): トークンの取得は、ユーザにChromeでサインインを求めるかもしれません。または、アプリケーションが要求したスコープの同意も求めるかも知れません。もしinteractiveフラグがtrueの場合、getAuthTokenは必要に応じてそれを促すでしょう。フラグがfalseの時や省略された時は、プロンプトの表示が必要になる時はいつでもgetAuthTokenは失敗を返すでしょう。

* callback (function): manifestによって指定された通りのOAuth2アクセストークンを伴って呼び出されます。もしエラーが発生したときには、undefinedが渡されます。



manifestによって指定された通りのOAuth2アクセストークンを伴って呼び出されます。もしエラーが発生したときには、undefinedが渡されます。

callbackパラメータは、以下のような関数として指定されるべきです:

```
function(string token) {...};
```

* token (optional string)

removeCachedAuthToken

```
chrome.identity.removeCachedAuthToken(object details, function callback)
```

Identity APIのトークンキャッシュからOAuth2アクセストークンを削除します。

もしアクセストークンが無効だと判断された場合は、それをキャッシュから削除するためにremoveCachedAuthTokenに渡すべきです。アプリはその後getAuthTokenを使って新しいトークンを取得することができるでしょう。



* details (object): トークン情報。

* token (string): キャッシュから削除されるべき指定されたトークン。

* callback (function): キャッシュからトークンが削除されたときに呼び出されます。



callbackパラメータは、以下のような関数として指定されるべきです:

```
function() {...};
```

launchWebAuthFlow

```
chrome.identity.launchWebAuthFlow(object details, function callback)
```

指定されたURLで認証フローを開始します。

このメソッドは、プロバイダーの認証フローにおけるWeb viewを起動して最初のURLに遷移することで、Googleではないアイデンティティプロバイダーを使った認証フローを可能にします。プロバイダーがhttps://
.chromiumapp.org/*パターンにマッチするURLにリダイレクトした時、ウィンドウは閉じられ、最後のリダイレクトURLがcallback関数に渡されるでしょう。



* details (object): Web認証フローオプション。

* url (string): 認証フローを開始するURL。

* interactive (optional boolean): インタラクティブモードで認証フローを起動するかどうか。いくつかの認証フローでは結果のURLに直ちにリダイレクトするかも知れないので、launchWebAuthFlowは最初の遷移が最終URLにリダイレクトするか、表示されることを意味する読み込み中のページが終了するまで、そのWeb viewを隠します。もしinteractiveフラグがtrueの場合、ページ読み込みが完了したときにウィンドウが表示されるでしょう。もしフラグがfalseや省略された時は、launchWebAuthFlowは初期遷移がフローを完了しなかった場合にエラーを返すでしょう。

* callback (function): あなたのアプリケーションにリダイレクトで戻ってきたときにURLを引数として呼び出されます。



callbackパラメータは、以下のような関数として指定されるべきです:

```
function(string responseUrl) {...};
```

* responseUrl (optional string)

getRedirectURL

```
string chrome.identity.getRedirectURL(string path)
```

launchWebAuthFlowで使われるリダイレクトURLを生成します。

生成されるURLは、https://
.chromiumapp.org/*パターンにマッチします。



* path (optional string): 生成されるURLの最後に追加されるパスです。

# Events

onSignInChanged

ユーザのプロフィールのアカウントに対してサインイン状況が変化したときに発火します。



```
chrome.identity.onSignInChanged.addListener(function callback)
```



* callback (function)



callbackパラメータは、以下のような関数として指定されるべきです:

```
function(object account, boolean signedIn) {...};
```

* account (object)

* id (string): アカウントのユニークな識別子です。このIDはアカウントのライフタイムに対して変化しないでしょう。

* signedIn (boolean)
