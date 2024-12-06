---
layout: post
status: publish
published: true
title: Chrome extensionからOAuth対応APIを使うための工夫
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1095
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1095
date: '2011-10-31 09:36:52 +0900'
date_gmt: '2011-10-31 00:36:52 +0900'
categories:
- Google
---

あるAPIがユーザ認証と認可を必要としている場合、主要なサービスプロバイダはOAuth 2.0をサポートしていることが当たり前になってきました。Chrome extensionからも様々なOAuth対応APIを利用してマッシュアップしたいところですが、残念ながら現状では簡単なことではありません。ここでは、Google+ APIを題材として、実現方法の一つを紹介したいと思います。

問題となるのは、OAuthにて必要となる「元のアプリケーションへのリダイレクト先が制限されるサービスプロバイダが存在する」という点にあります。

通常のWebサービスであれば、httpもしくはhttpsで始まるURLにリダイレクトされれば、それがすなわちアプリケーションの世界に戻ってきたことを意味します。しかし、Chrome extensionは"chrome-extension://"で始まるURLで実行されています。リダイレクト先は、Client IDを発行する際に予めサービスプロバイダに登録しておくものとなるのですが、その際にhttpもしくはhttpsでしかリダイレクト先の入力が許可されていない場合、直接Chrome extensionに戻ってくることができません。例えばGoogle APIs Consoleでは、以下の画面において、chrome-extensionプロトコルで始まるURIの入力は、バリデーションで弾かれます。

![](http://www.eisbahn.jp/yoichiro/images/2011/10/b1.png)

実際の流れとコードを追っていきながら見ていきましょう。まず、Chrome extension内から認可ページを表示します。認可ページの表示方法は様々な手法が考えられますが、Apache Shindigに含まれている
[popup.jsファイル](http://code.google.com/p/googl-url-shortener/downloads/detail?name=popup.js&can=2&q=)を使うと便利です。これはOAuthの認可ページをポップアップウィンドウで表示し、そのポップアップウィンドウがクローズされたことを監視して所定の関数を呼び出してくれる機能を提供します。このpopup.jsファイルを使って、Googleの認可画面を表示するためのコードが以下となります。これがChrome extensionのJavaScriptコード中にあり、ボタンクリックなど何らかのイベント発生時に呼び出されると考えてください。

```
var host = location.host;
var url = "https://accounts.google.com/o/oauth2/auth?"
        + "client_id=[YOUR Client ID]"
        + "&redirect_uri=http://www.eisbahn.jp/bootcamp/test.html"
        + "&scope=https://www.googleapis.com/auth/plus.me"
        + "&response_type=token"
        + "&state=" + host;
var popup = shindig.oauth.popup({
    destination: url,
    windowOptions: "width=640,height=480",
    onOpen: function() {},
    onClose: function() {
        // ポップアップが閉じたときの処理
    }.bind(this)
});
popup.createOpenerOnClick();
```

client_idやscopeなどは特段変わったところはありません。ここで注目はredirect_uriとstateです。redirect_uriは、chrome-extensionプロトコルではなく、httpです。ここでは、このブログを運営してる私の自宅サーバに置かれたhtmlファイルとしています。そして、stateにはlocation.hostの結果を指定しています。これは、Chrome extensionが動作しているドメイン、つまり「chrome-extension://[Extension ID]/foobar」のExtension IDの部分となります。このIDは後ほど利用することになります。

![](http://www.eisbahn.jp/yoichiro/images/2011/10/p1.png)

Googleの認可ページにてユーザが「Allow access」ボタンを押したとき、先に述べた通りChrome extensionに直接リダイレクトできればいいのですが、それは現状では叶いません。そこで、www.eisbahn.jpにあるページにリダイレクトを行います。

![](http://www.eisbahn.jp/yoichiro/images/2011/10/p2.png)

認可ページの呼び出し時にresponse_type=tokenを指定していますので、リダイレクトの際にAuthrorization codeではなく直接アクセストークンが渡されます。ただし、クエリパラメータとしてではなく、URI Fragmentとして渡されてきます。具体的には以下のような感じです。

>http://www.eisbahn.jp/bootcamp/test.html#state=[state値]&access_token=[アクセストークン値]&token_type=Bearer&expires_in=3600


アクセストークと共に、stateパラメータ値も渡されていることがわかると思います。これらはURI Fragmentですので、www.eisbahn.jp/bootcamp/test.htmlをWebブラウザが読み込む際に、そのリクエストには含まれません。つまり、www.eisbahn.jpのサーバには送られないわけです。発行されたアクセストークンがユーザのWebブラウザ外に送信されることは避けるべきことであり、その工夫がURI Fragmentという形で表現されています。

さて、リダイレクト先での処理ですが、ここからちょっと複雑な仕組みが入ってきます。この時点では、アクセストークンを持つドメインは「www.eisbahn.jp」であり、Chrome extensionのドメインである「chrome-extension://[Extension ID]/」とはもちろん異なっているために、簡単には情報の受け渡しを行うことができません。この壁を越えるために、iframeを使います。

[リダイレクト先のページ](http://www.eisbahn.jp/bootcamp/test.html)は、見た目的には「閉じてください」というメッセージのみを表示した非常にシンプルなページですが、実は動的に見えないiframeが生成されています。そのコードは以下のようになります。

```
認証認可が終わりました。このウィンドウを閉じてください。
```

location.hashを使ってURI Fragmentを取得し、iframeを動的生成する際に、そのsrc属性値として指定するURLのドメインにstate値、つまりChrome extensionのExtension ID値を指定していることに注目です。これにより、www.eisbahn.jpドメインからChrome extensionのドメインに、アクセストークン値を渡すことが可能になります。

![](http://www.eisbahn.jp/yoichiro/images/2011/10/p3.png)

iframe内に読み込むファイルは、Chrome extensionに含まれているbridge.htmlファイルです。このファイルの内容は、以下のようになります。

```

```

bridge.htmlファイルで行っていることはとてもシンプルです。先ほどと同じようにlocation.hashからURI Fragmentにて指定されたアクセストークン値を取得し、それをCookieに格納しています。このCookieのドメインは、もちろんbridge.htmlのドメイン、つまりChrome extensionのドメインです。

![](http://www.eisbahn.jp/yoichiro/images/2011/10/p4.png)

ユーザがポップアップウィンドウを閉じた際に、popup.jsの機能によって、予め指定されたonCloseイベントハンドラ関数が呼び出されます。あとはその関数内にて、Cookieからアクセストークン値を取得すれば良いでしょう。もちろんドメインは同じですので、問題なくCookieからアクセストークン値を得ることができます。

![](http://www.eisbahn.jp/yoichiro/images/2011/10/p5.png)

Cookieからアクセストークン値を取得し、APIアクセスを行うためには、以下のようなコードを書けば良いでしょう。

```
var cookie = document.cookie;
var accessToken = cookie.split("=")[1];
var url = "https://www.googleapis.com/plus/v1/people/me";
new Ajax.Request(url, {
    method: "get",
    requestHeaders: [
        "Authorization", "OAuth " + accessToken
    ],
    onSuccess: function(req) {
        var response = req.responseJSON;
        // do something...
    }
});
```

Authorizationリクエストヘッダにアクセストークン値を指定することにより、具体的なAPIアクセスが成功します。もちろん、以下のようにmanifest.jsonファイルにパーミッションを設定しておくことをお忘れなく。

```
{
  ・・・
  "permissions" : [
    "https://www.googleapis.com/"
  ]
}
```

以上がOAuth 2.0対応のAPIをChrome extensionから利用するための実装手法の例となります。テクニックとしてiframeを使ったやり方は覚えておくと良いでしょう。もちろん「これのみ」というわけではなく、Authorization codeを使った手法でもいいですし、iframeの代わりにpostMessage関数を使うなど、いくつかやり方は考えられると思います。
