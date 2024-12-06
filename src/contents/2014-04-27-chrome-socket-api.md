---
layout: post
status: publish
published: true
title: Chrome Socket APIを古くから使っている人に新しいAPIのススメ
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2618
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2618
date: '2014-04-27 12:02:38 +0900'
date_gmt: '2014-04-27 03:02:38 +0900'
categories:
- Chrome Apps
---

Chrome Webブラウザは、Web標準技術を使って、OS Nativeなアプリと遜色がないGUIアプリケーション（Chrome apps、昔Packaged appsと呼んでたもの）を実行する環境が搭載されています。OS Nativeなアプリに負けないためには、「できること」が多くなければなりません。すなわち、APIが充実していればしているほど、きっとOS Nativeなアプリとの競争力も強くなっていくことでしょう。

そのAPI群の中に、ソケットを扱うためのAPIがあります。Packaged appsが作れるようになったかなり初期の頃からSocket APIは提供されていました。具体的には、Chrome 24からあります。そのSocket APIの名前空間は、"chrome.socket"です。このSocket APIにより、TCPやUDPのクライアントコードを書くことはもちろん、サーバ側のコードも記述することができます。

* [chrome.socket](https://developer.chrome.com/apps/socket)

そんなSocket API、Chrome 33からなんとDeprecated扱いになってしまいました。その代わりに、新しいSocket APIが登場しています。新しいSocket APIは、その用途ごとに名前空間がわけられています。

* [chrome.scokets.tcp](https://developer.chrome.com/apps/sockets_tcp)

* [chrome.sockets.tcpServer](https://developer.chrome.com/apps/sockets_tcpServer)

* [chrome.sockets.udp](https://developer.chrome.com/apps/sockets_udp)

僕はMySQL Serverに接続してごにょごにょするChrome appsを作っていますが、もちろんDeprecated扱いになったSocket APIを使って開発を進めてきました。Deprecatedってことは、いつかはなくなってしまいます。それは、自分の作ったChrome appsも動作しなくなることを意味しています。非常にまずいですね。新しいAPIに移行しなければなりません。

では、その移行は簡単なのか？って話なのですが、実際にやってみて、とても苦労しました。といっても2,3時間で終わってはいますが、新旧APIでやはり差があり、それを吸収しないといけない事態となりました。

具体的に、chrome.socketのTCP向けAPIと、chrome.sockets.tcpのAPIとで、どのような差があったかというと、データを送信する方は、関数名がwriteからsendになっただけなので、シンプルです。問題は、データを受信する方でした。以下の差があります。

* 旧API: read関数によって、指定バイト数だけ受信した結果のArrayBufferを欲しい時に受信することができる。

* 新API: データを受信した際にonReceiveイベントが発生し、予め設定しておいたコールバック関数に「受信した分だけ」のArrayBufferが引数で渡される。

つまり、データの受信方法が上記のように大きく変わっています。本当にこれは大きな差です。簡単に言うと、今まで「欲しい分だけ自分のタイミングで取れた」ものが、「ある時突然データが未知の塊で飛んでくる」という感じですね。APIとして、全くの別物に感じます。

ソケット通信は、その上で流れるデータの送受信手順はプロトコルによって様々ですが、基本的には「何かを送信する、何かを受信する、何かを送信する、何かを受信する、何かを・・・」の繰り返しです。この際、特に受信においては、プロトコルの仕様に基づいて、ある程度固定長で扱うことがほとんどでしょう。MySQL Protoclの場合も、まず3バイトで渡ってくるMySQL Packetの長さを受信し、次に1バイト分読んでPacket IDとし、その後MySQL Packet長分だけ実際のデータを受信する、っていう感じの受信方法になります。

つまり、旧APIのread関数が、まさにそれを実現してくれるドンピシャなAPIだったわけです。コード的には、以下のようになります（いろいろ問題あるコードですがまあ置いといて）。

```
var buffer = ...; // 送信するArrayBuffer
chrome.socket.write(socketId, buffer, function(writeInfo) {
  chrome.socket.read(socketId, 3, function(readInfo) {
    var buffer = readInfo.data; // 3バイト分のArrayBuffer
    var packetLength = ...; // bufferから長さを取得
    chrome.socket.read(socketId, 1, function(readInfo) {
      var buffer = readInfo.data; // 1バイト分のArrayBuffer
      var packetId = ...; // bufferからPacket IDを取得
      chrome.socket.read(socketId, packetLength, function(readInfo) {
        var buffer = readInfo.data; // 実際のデータ
        // Do something...
      });
    });
  });
});
```

具体的には、以下のコードを見てみてください。MySQL Serverとやり取りをするJavaScriptライブラリのChrome依存コードです。

[mysql_js_driver/src/chrome_socket.js](https://github.com/yoichiro/mysql_js_driver/blob/master/src/chrome_socket.js)

writeやread関数を作っていますが、どれも基本的にChromeの古いSocket APIをそのまま呼んでいるだけなことがわかると思います。

では、新しいAPIだとどうなるでしょうか？onReceiveイベントを扱う簡単なコードをまずは見てみましょう。

```
var buffer = ...; // 送信するArrayBuffer
chrome.sockets.tcp.send(socketId, buffer, function(writeInfo) {
  // Do something...
});
chrome.sockets.tcp.onReceive.addListener({}, function(readInfo) {
  var buffer = readInfo.data; // 受信したバイト分のArrayBuffer
  // Do something...
});
```

データの受信はonReceiveイベントのイベントハンドラで受け取らなければならないため、send関数のコールバック関数と直接関係を結ぶことはできません。送信と受信処理が、新しいAPIではかなりの距離を感じてしまいます。つまり、「次にデータを受信したら、このコールバック関数を呼び出してください」という古いAPIでできていたコールバック関数指定の自由さはなくなりました。

さらに、「○○バイト分読み込む」という都合の良いこともできません。そしてもっと最悪なことに、例えば通信相手のサーバから1000バイトが送信されたと仮定しましょう。その際、onReceiveイベントが発火し、コールバック関数には1000バイト分のArrayBufferが渡される、と思いますよね？確かに1000バイト分がonReceiveイベント1回分で得られることもありますが、多くの場合、onReceiveイベントは複数回に渡って発火します。しかも、1回で渡されるデータの長さは、Chromeの気まぐれで決まります。onReceiveイベントによって得られた受信データから希望するバイト数分のデータが欲しいとしても、その時点では十分なデータ量を受信できていないかもしれないのです。その場合、次のonReceiveイベントを待つしかありませんし、それでも足りないかも知れません。

ソケット通信のためのAPIとして、これはきついです。新APIをそのまま使える気が全くしません。「改悪」って言われても仕方ないと個人的には思います。

とはいえ、旧APIはそのうち使えなくなるわけで、新APIをこれからは使っていかなければなりません。既に旧APIを使って開発されたアプリ側をonReceiveイベントを直接扱うように修正するのは至難の業なので、新APIを旧APIのI/Fで使えるようにするのが現実的でしょう。そのために必要となることは、以下の3点です。

* 旧APIと同じread関数を作る。そのread関数に渡された「読み込みたいバイト数」と「データを受け取りたいコールバック関数」のセットを1つのオブジェクトとし、キューに入れていく。

* onReceiveイベント発火時に受け取った（細切れの）データを一時的に格納しておくバッファを作る。onReceiveイベント発火の度に、そのバッファにデータを次々と追加していく。

* 上記のonReceiveイベント発火時のデータ追加処理が終わった後、もしキューにオブジェクトが1つ以上あれば、1つ目のオブジェクトを取り出して、バッファから指定バイト数分データを取り出し、そのデータを引数に指定してコールバック関数を呼ぶ。取り出したバイト列をバッファから消す。キューが0個になる、もしくは指定バイト数分のデータをバッファかた取り出せなくなるまで、これを繰り返す。

文章で書くとわかりにくいですね。擬似コードで書くと以下のようになります。

```
var buffer = new ArrayBuffer(0);
var queue = [];
var read = function(socketId, length, callback) {
  var object = {
    length: length, // 読み込みたいバイト数
    callback: callback // 読み込んだバイト列を受け取るコールバック関数
  };
  queue.push(object);
};
chrome.sockets.tcp.onReceive.addListener(function(info) {
  var data = info.data;
  appendToBuffer(data); // 読み込んだデータをバッファに追加
  fetch();
});
var fetch = function() {
  if (queue.length > 0) {
    var object = queue[0];
    var length = object.length;
    if (バッファにあるバイト数 >= length) {
      var data = readFromBuffer(length); // バッファから読み込み
      deleteDataFromByffer(length); // バッファから読み込んだ分を削除
      deleteObjectFromQueue(); // キューからオブジェクトを削除
      object.callback({ // コールバック関数を呼び出す
        resultCode: 0,
        data: data
      });
      fetch(); // 再帰呼び出し
    }
  }
};
```

このように実装することで、新APIを内部で使った旧APIと同じような使い方ができるようになります。実際に動作しているコードは、以下です。

[mysql_js_driver/src/chrome_socket_2.js](https://github.com/yoichiro/mysql_js_driver/blob/master/src/chrome_socket_2.js)

上記の実装コードについて1つ心配があるとすれば、onReceiveイベントのイベントハンドラ関数が複数のスレッドから同時に呼び出された場合に、バッファへの追加処理が競合する可能性があります。Socket IDのチェックも上記実装ではやってないのも問題ですね。macosxのChrome 34.0.1847.131で動作させた限りでは、onReceiveイベントはシーケンシャルに発火している模様です。もしそれが保証されないのであれば、バッファへの登録処理もQueue対応する必要があるでしょう。

今回新APIのWrapperを書いてみたわけですが、実は旧APIはChromeの内部で同じような処理を行っていたのではないか、と想像しています。ただ、僕が書いたWrapperで新APIに切り替えて実際に処理を行わせた時に、新API版の方が処理速度が体感できるほど速くなりました。体感できるほどなので、本当に大きな差です。これだけでも結果的には新APIに移行するモチベーションになるほど、と言って良いでしょう。

Chrome Socket APIを使っている開発者がどれだけいるかは疑問ですが、もし何かの参考になれば幸いです。おそらくnode.jsのソケット通信もイベント駆動型のI/Fだったと思うので、同じような実装ができるかもしれませんので、参考になればと思います。
