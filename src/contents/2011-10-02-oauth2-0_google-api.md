---
layout: post
status: publish
published: true
title: OAuth2.0によるGoogle+ APIのアクセス方法
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1006
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1006
date: '2011-10-02 13:10:18 +0900'
date_gmt: '2011-10-02 04:10:18 +0900'
categories:
- Google
---

Google+のAPIが先日公開されました。API Keyを使ってシンプルにAPIアクセスを試すことができるのですが、本来であればOAuth2.0にてアクセストークンを取得しAPIアクセスする方式でなければ今後公開されるであろうAPI群は利用できないはずですので、ここでOAuth2.0によるGoogle+ APIのアクセス手順を紹介しておきましょう。全手順において、プログラミングをすることなく試すことができます。

1. Client IDの発行

まずはClient IDやClient secretなどを発行しましょう。まずは、Google APIs ConsoleにWebブラウザでアクセスします。

[https://code.google.com/apis/console/](https://code.google.com/apis/console/)

まだプロジェクトを作成していないのであれば、Other projects→Create...にて新規にプロジェクト作成を行います。その後、Servicesタブを選択し、その中にあるGoogle+ APIをONにします。

![](http://www.eisbahn.jp/yoichiro/images/2011/10/api_on.jpg)

次に、API Accessタブを選択します。Create an OAuth 2.0 client ID...というボタンがあるので、それを押します。

![](http://www.eisbahn.jp/yoichiro/images/2011/10/oauth.jpg)

Create Client IDダイアログで最低限必要な入力項目はProduct nameのみです。何らかプロダクト名を入力し、Nextボタンを押します。次のページでは、Application typeとしてWeb applicationを選択し、Your site or hostnameの箇所ではhttp://localhostとなるように入力します。その後Create client IDボタンを押すことで、Client IDなどAPIアクセスに必要な情報が発行されます。

![](http://www.eisbahn.jp/yoichiro/images/2011/10/oauth_info.jpg)

2. ユーザの認可

次に、ユーザにAPIアクセスの認可をしてもらい、その結果が反映されたAuthorization codeを入手します。これは、Webブラウザを利用し、https://accounts.google.com/o/oauth2/auth にアクセスします。その際、以下のクエリパラメータが必要になります。

* client_id : Google APIs Consoleで発行したClient ID

* redirect_uri : Google APIs Consoleで発行したRedirect URIs

* scope : "https://www.googleapis.com/auth/plus.me"

* response_type : "code"

この際、redirect_uriとscopeの値はURIエンコードされている必要があります。全体としては以下のようになるでしょう。

>https://accounts.google.com/o/oauth2/auth ?client_id=723478066735.apps.googleusercontent.com &redirect_uri=http%3A%2F%2Flocalhost%2Foauth2callback &scope=https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fplus.me &response_type=code


このURLにWebブラウザでアクセスすると、以下のように認可ページが表示されます。「アクセスを許可」ボタンを押すことで、Redirect URIにて指定したURLにリダイレクトされます。その際、codeクエリパラメータが付与されます。このcodeパラメータ値が、Authorization code値となります。

![](http://www.eisbahn.jp/yoichiro/images/2011/10/authz.jpg)

アクセストークンの取得

Authorization code値から、APIにアクセスするためのアクセストークンを取得します。これにはWebブラウザは使わず、ここではcurlコマンドを使用します。アクセストークンの取得は、https://accounts.google.com/o/oauth2/token にPOSTメソッドでHTTPリクエストを送ります。その際、以下のパラメータ値の指定が必要になります。

* client_id : Google APIs Consoleで発行したClient ID

* client_secret : Google APIs Consoleで発行されたClient Secret

* redirect_uri : Google APIs Consoleで発行したRedirect URIs

* grant_type : "authorization_code"

* code : Authorization code値

この際、redirect_uri値はURIエンコードされている必要があります。全体としては以下のようになるでしょう。

>curl -d client_id=723478066735.apps.googleusercontent.com -d client_secret=[YOUR Client Secret] -d redirect_uri=http%3A%2F%2Flocalhost%2Foauth2callback -d grant_type=authorization_code -d code=[YOUR Authorization code] https://accounts.google.com/o/oauth2/token


この実行結果として、以下のようなJSON形式の文字列が返却されます。

>{"access_token":"アクセストークン値","token_type":"Bearer","expires_in":3600,"refresh_token":"リフレッシュトークン値"}


access_tokenプロパティの値が、APIにアクセスするためのアクセストークン値となります。

APIアクセス

アクセストークンを得ることができれば、あとは各APIを利用することができます。アクセストークンは、Authorizationリクエストヘッダに指定します。例えば、認可ユーザ自身のユーザ情報を取得するには、以下のようになるでしょう。

>curl -H "Authorization: OAuth [YOUR Access token]" https://www.googleapis.com/plus/v1/people/me


この実行結果として、PortableContacts仕様に則ったレスポンスが返却されます。また、認可ユーザの一般公開のフィード一覧を得たい場合は、以下のようにします。

>curl -H "Authorization: OAuth [YOUR Access token]" https://www.googleapis.com/plus/v1/people/me/activities/public


この実行結果として、ActivityStreams仕様に則ったレスポンスが返却されます。

アクセストークンの再発行

アクセストークンが失効した場合は、リフレッシュトークンを使ってアクセストークンを再発行します。そのためには、https://accounts.google.com/o/oauth2/token にPOSTメソッドでHTTPリクエストを送ります。その際、以下のパラメータが必要となります。

* client_id : Google APIs Consoleで発行したClient ID

* client_secret : Google APIs Consoleで発行されたClient Secret

* refresh_token : リフレッシュトークン値

* grant_type : "refresh_token"

全体としては以下のようになるでしょう。

>curl -d client_id=723478066735.apps.googleusercontent.com -d client_secret=[YOUR Client Secret] -d refresh_token=[YOUR Refresh token] -d grant_type=refresh_token https://accounts.google.com/o/oauth2/token


この実行結果として、アクセストークン取得の時と同じ内容のJSON文字列が返却されます。その中のaccess_tokenプロパティ値が、新しく発行されたアクセストークン値となります。
