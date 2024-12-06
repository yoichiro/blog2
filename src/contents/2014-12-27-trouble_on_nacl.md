---
layout: post
status: publish
published: true
title: NaCl、nacl_ioやるときにハマったこと
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 3173
wordpress_url: https://www.eisbahn.jp/yoichiro/?p=3173
date: '2014-12-27 19:40:44 +0900'
date_gmt: '2014-12-27 10:40:44 +0900'
categories:
- Chrome Native Client
---

Chrome MySQL Adminで「SSH Tunnelingできないから星1つ」とか頭にくるフィードバックする人が結構いて、それができないからって全否定かよ！としばらく怒っていました。その怒りを力に変えて、NaClでlibssh2使ってSSH2 Port forwardingできるように機能追加をしました。

[https://github.com/yoichiro/chrome_mysql_admin](https://github.com/yoichiro/chrome_mysql_admin)

記憶に残っているうちに、ハマリどころを書いておこうと思います。

# NaCl moduleからJavaScript側にメッセージ送ってるのに届かない

これ、数回、数時間悩みました。C++側のコードをいくら見ても良さそうにしか見えない。JavaScript側のコードをいくら見ても良さそうにしか見えない。でも、メッセージを受け取れない。具体的には、C++側からPostMessage()しても、JavaScript側でコールバック関数が呼び出されない。

答えは簡単で、addEventListener()した時に第3引数の指定を忘れると、コールバック関数が呼び出されません。例えば、以下のように書いてたとします。

```
<div id="listener">
  <embed id="ssh2_port_forwarding"
         width=0 height=0
         src="newlib/Release/ssh2_port_forwarding.nmf"
         type="application/x-nacl" />
</div>
```

で、listenerにイベント登録を以下のようにしてると、コールバック関数は呼ばれません。

```
var listener = document.querySelector("#listener");
listener.addEventListener("message", function(evt) {
});
```

以下のようにしないとダメです。

```
var listener = document.querySelector("#listener");
listener.addEventListener("message", function(evt) {
}, true);
```

最後のtrue指定が重要です。useCaptureをtrueにするってことで、めでたくNaCl module側からのメッセージを受け付けることができるようになります。

# そもそもデバッグどーやるの？

JavaScriptであれば、DevToolsのConsole見ればいろいろ表示されるし、console.log()使って自分でもログ出力ができます。でも、単独のプロセスとして動作するNaCl moduleのデバッグって、どうやればいいか、最初は見当もつきませんでした。とにかくPepper API使ってJavaScript側にPostMessage()すれば良いんでしょうけど、それも何か違う気がします。

僕はあまりステップ実行とか変数値をツールで覗いたりせず、どんな言語、環境でも「print文による出力」で切り抜けてきています。JavaであればSystem.out.println()、JavaScriptであればconsole.log()、て感じですね。ではNaClではどうしたか？はい、fprintf()を使いました。

```
void Ssh2PortForwardingInstance::Log(const char *msg)
{
  fprintf(stderr, "Ssh2PortForwardingInstance: %s\n", msg);
}
```

NaCl moduleの標準出力と標準エラー出力は、Chromeアプリ自体のそれらに接続されています。つまり、ChromeをTerminalから起動すれば、そのTerminal内にNaCl moduleからfprintf(stdout, "foobar: %s\n", "baz); とした結果が表示されます。Chrome MySQL Adminを作ってたときは、具体的には以下のような出力となりました。

```
[1226/065655:ERROR:breakpad_mac.mm(235)] Breakpad initializaiton failed
[8816,1947529984:06:56:56.024400] Native Client module will be loaded at base address 0x0000425d00000000
Ssh2PortForwardingInstance: Instance created.
Ssh2PortForwardingInstance: Message handled
Ssh2PortForwardingThread: ConnectAndHandshake
Ssh2PortForwardingThread: Start connection and handshaking
Ssh2PortForwardingThread: Libssh2Init() 0
Ssh2PortForwardingThread: ConnectToSshServer() 0
```

# nacl_ioでSocket APIが動作する条件

NaCl SDKには、sys/socket.hで定義されているソケット通信のための実装が含まれています。nacl_ioと呼ばれているものなのですが、socket.hをインクルードしたとしても、そのままでは通信できません。

Chrome appsでnacl_ioを動作させるには、以下の条件を全て満たす必要があります。

* Socket APIの権限を得ます。Chromeを起動する際に、--allow-nacl-socket-api=ndgn... というように、Socket APIを利用したいChrome appのIDを指定しておくか、manifest.jsonファイルに権限を定義しておきます。注意点としては、Chrome 39の時点では、古い書き方をしなければなりません。

```
"permissions": [
    {
        "socket": [
            "resolve-host",
            "tcp-connect:*:*",
            "tcp-listen::*"
        ]
    },
    ...
],
...
```

* nacl_io_init_ppapi()関数でnacl_ioを初期化しておきます。これはNaCl moduleのメインスレッドでOK。

```
nacl_io_init_ppapi(instance, pp::Module::Get()->get_browser_interface());
```

* Socket APIを使うときは、メインスレッドではない別のスレッドから行う必要があります。メインスレッドでやってしまうと、connect()が正常値を返すくせに、その後recv()とかすると「接続されてません」というエラーが返ってくるので、完全に騙されるというか、別スレッドからでないといけないってことを知らないと数時間ハマります。この制限については、
[The nacl_io Library](https://developer.chrome.com/native-client/devguide/coding/nacl_io)というドキュメントに以下のように書かれています。

>Make sure that file and socket API calls are all made from the background thread. This is because the main Pepper thread does not support the blocking behavior needed by the POSIX I/O operations.

* PNaClだとSocket APIは使えませんでした。newlibではOK。glibcは試していません。

上記の前提条件を知らないと、本当に数日単位でハマります。
