---
layout: post
status: publish
published: true
title: Chrome Socket APIのsetPaused()関数の動作
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2728
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2728
date: '2014-07-04 09:25:11 +0900'
date_gmt: '2014-07-04 00:25:11 +0900'
categories:
- Chrome Apps
---

Chromeアプリからソケット通信を行うためのSocket APIがあるのですが、古いAPIから新しいAPIセットに変更になり、利用可能な関数も結構変わりました。特に、受信がイベント駆動方式に変更になったため、受信したデータを自分で取りに行くのではなく、イベントリスナを登録してコールバックによって受け取るようにコーディングしなければなりません。これは全般的にかなり苦痛なコーディングを強いられるので個人的には元に戻して欲しいと思ってるのですが、イベント駆動方式を使いこなすために必要となる関数がいくつか存在します。

その中の一つに、setPaused()関数があります。TCPを扱うためのSocket APIは、クライアント用（chrome.sockets.tcp）とサーバ用（chrome.sockets.tcpServer）の2種類があるのですが、その両方にsetPaused()関数があります。名前から推測するに「一時停止させるかどうかセットする」ための関数だと思うのですが、使い方が良くわかりません。

いろいろ調べていくうちに、Stack overflowにsetPaused()関数に関する質問を見つけました。以下はその翻訳です。

# Chrome app sockets.tcpServer does not receive data

[http://stackoverflow.com/questions/22545574/chrome-app-sockets-tcpserver-does-not-receive-data](http://stackoverflow.com/questions/22545574/chrome-app-sockets-tcpserver-does-not-receive-data)

私はローカルアプリケーションと通信するためにChromeのsockets.tcpServer APIを使おうとしています。ここに私のコードへのリンクがあります: http://pastebin.com/1mWXeZKr.

サーバが機能しているかどうかテストするために、私は"nc 127.0.0.1 7000"を使っています。しかしながら、そのサーバに接続でき、そのサーバからのメッセージを受信できるのですが、私がクライアント(nc)からそれらを送っても、そのサーバは受信しません。私は正しくAPIを使っているかどうか自信がありません。誰かそのコードにおいて何らかのエラーを指摘してもらえませんか？

ここにコードの断片があります。

```
function onAccept(info) {
  if (info.socketId != serverSocketId)
    return;
  console.log("Client connected.");
  // Start receiving data.
  _stringToArrayBuffer("connected.\n", function(buf) {
    chrome.sockets.tcp.send(info.clientSocketId, buf, function(info) {
      if (info.resultCode != 0)
        console.log("Send failed.");
    });
  });
  chrome.sockets.tcp.onReceive.addListener(onReceive);
}
function onReceive(info) {
  console.log("Data received.");
  _arrayBufferToString(info.data, function(str) {
    console.log(str);
  });
}
```

更新:

OK、私は解決策を見つけました。以下の回答をご覧ください。

回答

私は解決策を見つけました。clientSocketIdソケットは、それが作成された時に、初期動作として停止されます。onAccept関数において、onReceiveリスナを追加した後に、単純に chrome.sockets.tcp.setPaused(info.clientSocketId, false); を追加することで、問題は解決します。

```
function onAccept(info) {
  if (info.socketId != serverSocketId)
    return;
  chrome.sockets.tcp.onReceive.addListener(onReceive);
  chrome.sockets.tcp.onReceiveError.addListener(function(info) {
    console.log("Error: ", info);
  });
  chrome.sockets.tcp.setPaused(info.clientSocketId, false);
  buf = str2ab("Connected.\n");
  chrome.sockets.tcp.send(info.clientSocketId, buf, function(info) {
    if (info.resultCode != 0)
      console.log("Send failed.");
  });
}
```

****


上記の質問は、Chromeアプリ内でサーバを立てた際に、「Chromeアプリ内のサーバ→外部のクライアント」に通信はできるけど、「外部のクライアント→Chromeアプリ内のサーバ」に通信できないどういうこと？という内容です。答えとしては、Acceptされた際の対象クライアントとのソケットは、初期状態でPause状態なので、クライアントから何か送信されたとしても、onReceiveイベントは発火しませんよ、ということのようです。つまり、onReceiveイベントにリスナを登録してデータの受信準備を整えた後にPause状態を解除しないといけないので、setPaused(clientSocketId, false) を呼んであげればonReceiveイベントが発火するようになります、ということですね。

このことは、
[chrome.sockets.tcpServerのAPI Reference](https://developer.chrome.com/apps/sockets_tcpServer#event-onAccept)に、かなり「しれっと」書いてあります。

>Note the client socket is initially paused and must be explictly un-paused by the application to start receiving data.
（クライアントソケットは初期状態として停止されていますので、データの受信を開始するためには、明示的にアプリケーションによって停止状態を解除しなければなりません）


つまり、クライアントからの接続が確立されたということは、クライアントから即データが送信されるかもしれないことを意味しています。onReceiveイベントリスナが登録されていなければ、アプリケーションはそのデータを受信できないわけで、リスナ登録作業を開発者に確実に行わせる猶予を作るために、Pause状態が初期値になってるのかな、と推測できます。

Chromeアプリ内でサーバを立てるときのsetPaused()関数の使い方はわかりました。でも、僕が本当に知りたいのは、クライアント側にあるsetPaused()関数の役目なんですよね。。。
