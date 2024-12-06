---
layout: post
status: publish
published: true
title: NaClモジュールをgdbでステップ実行や変数値を覗くための手順
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 3188
wordpress_url: https://www.eisbahn.jp/yoichiro/?p=3188
date: '2014-12-29 21:00:43 +0900'
date_gmt: '2014-12-29 12:00:43 +0900'
categories:
- Chrome Native Client
---

Chromeは、NativeClientと呼ばれる「OSが直接理解できるコンパイル済みコードを（サーバ側ではなく）Chromeブラウザ側で実行する仕組み」が備わっています。
[最近はGo言語でもNaClが書ける](https://github.com/golang/go/wiki/NativeClient)みたいですが、基本的にはCやC++でNaClモジュールを書くことになります。もうそれだけで「うわ、難しそう」と思ってしまうことでしょう。実際、CやC++でコードを書いたことがほとんどなければ、かなり難しく感じると思います。少なくともLL系などの超高級言語(?)しか経験のない人にとっては、CやC++を書くことで「コンピュータとはこういうものだったのか！？」と実感することになるはずです。

基本的にNaClモジュールの標準出力や標準エラー出力はChrome自体に接続されているので、ChromeをTerminalなどから直接起動すれば、NaClモジュール内で fprintf(stderr, "%s\n", msg) などすることでデバッグ情報を出力することが可能です。僕なんかはfprintf()埋めまくって何とかデバッグしてしまうのですが、それだけでは不十分な人も多いと思います。自分でデバッグ情報の出力処理をコード中に埋めることなく、変数値の出力やステップ実行ができれば、開発もし易くなることでしょうし、NaClに対する心理的障壁も減るはずですね。

ここでは、作成したNaClモジュールをgdbでデバッグするまでの手順を紹介します。

# Debug設定でビルド

ステップ実行や実行中の変数値を覗くためには、予めDebug設定でmakeしておくことが必要です。
[Chrome Native Client Tutorial C++ Getting Started編 (Part 2)](https://www.eisbahn.jp/yoichiro/2014/10/chrome-native-client-tutorial-c-getting-started-part-2.html)で説明したMakefileであれば、

```
$ make CONFIG=Debug
```

と指定してビルドすることで、Debug情報付きのバイナリを得ることができます。newlibであれば、newlib/Debugというディレクトリの中に、nexeファイルやnmfファイルが生成されます。

# Debug情報付きバイナリの読み込み

NaClモジュールをgdbで覗くためには、もちろん先ほど生成したDebug情報付きのnexeファイルを読み込まなければなりません。つまり、newlibであれば、newlib/Debugディレクトリ内のnmfファイルをWebページにて記述します。

```
<div id="listener">
  <embed id="ssh2_port_forwarding"
         width=0 height=0
         src="newlib/Debug/ssh2_port_forwarding.nmf"
         type="application/x-nacl" />
</div>
```

# デバッグオプション付きでChromeを起動

普通にChromeを起動してもgdbは使えません。デバッグが可能になるようなオプション付きでChromeを起動する必要があります。例えばmacであれば、以下のようにして起動します。

```
$ cd /Applications/Google\ Chrome.app/Contents/MacOS
$ ./Google\ Chrome --enable-nacl --enable-nacl-debug --no-sandbox --disable-hang-monitor
```

起動すると、以下のような警告が表示されますが、気にしません。

![スクリーンショット 2014-12-29 19.38.44](https://www.eisbahn.jp/yoichiro/images/2014/12/940b7615990a19cfaa54d20f78a472d7.png)

# gdbの起動

いよいよgdbを起動するのですが、NaCl SDKに付属されているgdbコマンドを利用します。mac+newlibであれば、以下のように起動します。

```
$ cd $NACL_SDK_ROOT/toolchain/mac_x86_newlib/bin
$ ./x86_64-nacl-gdb
```

もしPNaClやglibcなどの場合は、toolchain下にある各ディレクトリの中に入って、上記のようにgdbを起動してください。

# NaClモジュール設定の読み込みと接続

先ほど起動したgdbからChromeに接続してデバッグを開始します。まず、デバッグしたいNaClモジュールの定義が書かれたnmfファイルの場所をgdbに教えます。

```
(gdb) nacl-manifest <YOUR_NMF_FILE_PATH>
```

次に、デバッグしたいChrome appsを起動します。試したことはないですが、PNaCl＋Webページの場合は、ここでそのWebページを開いておけば良いと思います。起動してNaClモジュールが読み込まれると、Chromeを起動したTerminalには以下のような表示がされます。

```
[5386,2015879936:19:55:25.735683] Native Client module will be loaded at base address 0x000011fa00000000
[5386,2015879936:19:55:25.811067] nacl_debug(138) : Debugging started.
```

ではいよいよ接続します。

```
(gdb) target remote localhost:4014
Remote debugging using localhost:4014
0x000000000fd90120 in ?? ()
```

これで接続できました。Chromeを起動したTerminal上でも、デバッグが開始されたことを示す表示がなされます。

```
[5386,209670144:19:56:01.209643] nacl_debug(86) : Connected, happy debugging!
```

以上で準備が整いました。

# ブレイクポイントの設定とNaClモジュールの実行

では実際にgdbを使ってステップ実行や変数値を参照してみましょう。まず、ブレイクポイントを設定します。Pepper APIでJavaScriptからメッセージを受け取ったときに呼び出される関数が一番扱いやすいと思いますので、そこにブレイクポイントを置いてみます。

```
(gdb) break Ssh2PortForwardingInstance::HandleMessage
Breakpoint 1 at 0x2b9a0: file ssh2_port_forwarding.cc, line 32.
```

C++であれば、クラス名::メンバ関数名で止められます。実際のソースコードの行数が表示されることで、正しくブレイクポイントが設定されたかどうかわかると思います。この時点ではNaClモジュールがまだ実行されていないので、NaClモジュールを実行させます。

```
(gdb) continue
Continuing.
```

もしNaClモジュールのInstanceサブクラスのコンストラクタで何か標準出力に表示していれば、この時点でTerminal側にそれが表示されると思います。

```
Ssh2PortForwardingInstance: Instance created.
```

先ほど起動しておいたChrome appsやWebページ上で何か操作を行って、NaClモジュールにメッセージを送ります。ブレイクポイントを仕掛けた関数が呼び出されれば、gdb側で実行が停止されたことが表示されます。

```
Breakpoint 1, Ssh2PortForwardingInstance::HandleMessage (this=0xfeec0528, var_message=
    ...) at ssh2_port_forwarding.cc:32
32	  Log("Message handled");
```

丁寧にこれから実行されようとしている行のソースコードが表示されます。ここからステップ実行してみましょう。関数の中に入りたい場合はstep、入らない場合はnextを使います。

```
(gdb) next
33	  if (!var_message.is_string()) {
(gdb) next
37	  Json::Value root;
(gdb) next
38	  if (Json::Reader().parse(var_message.AsString(), root) &&
(gdb) next
40	    std::string command = root["command"].asString();
(gdb) next
41	    const Json::Value& args = root["args"];
```

ここでlistとタイプすると、実行対象の行付近のソースコードが表示されます。

```
(gdb) list
36
37	  Json::Value root;
38	  if (Json::Reader().parse(var_message.AsString(), root) &&
39	      root.isObject()) {
40	    std::string command = root["command"].asString();
41	    const Json::Value& args = root["args"];
42	    if (!command.empty() && args.isArray()) {
43	      if (command == "connect") {
44	        std::string server_hostname = args[0].asString();
45	        std::string server_port_string = args[1].asString();
```

上記の例では、40行目まで実行されています。つまり、command変数には何か値が入っているはずです。では、command変数の値を覗いてみましょう。

```
(gdb) print command
$1 = {static npos = 4294967295,
  _M_dataplus = {<std::allocator<char>> = {<__gnu_cxx::new_allocator<char>> = {<No data fields>}, <No data fields>}, _M_p = 0xfeec31fc "connect"}}
```

"connect"という文字列が入っていたことがわかりました。では、ここから先は最後まで実行させてしまいましょう。continueとタイプします。

```
(gdb) continue
Continuing.
```

NaClモジュール側で通常の処理が走り、最終的に何かPostMessage()されていれば、Chrome apps側もしくはWebページ側で何か表示が変わることでしょう。

# ブレイクポイントの一覧表示と解除

NaCl用gdbとは言え、一般的なgdbと同じなのですが、設定されたブレイクポイントの一覧はinfo breakpointsで、ブレイクポイントの解除はdeleteで行います。まず、NaClモジュールの監視中（continueされた状態）であれば、Ctrl+Cでそれを停止します。

```
^C
[Thread 1] #1 stopped.
0x000000000fd98a00 in ?? ()
(gdb)
```

ブレイクポイントの一覧を表示してみます。

```
(gdb) info breakpoints
Num     Type           Disp Enb Address            What
1       breakpoint     keep y   0x000000000002b9a0 in Ssh2PortForwardingInstance::HandleMessage(pp::Var const&) at ssh2_port_forwarding.cc:32
	breakpoint already hit 1 times
```

設定したブレイクポイントに何回ヒットしたかも表示されますね。では解除しましょう。上記のNum値が使えます。

```
(gdb) delete 1
(gdb)  info breakpoints
No breakpoints or watchpoints.
```

解除されたようです。これで再度Chrome appsやWebページから同じように操作した場合も、continueするだけで、処理は止まらずに最後まで一気に実行されるようになります。

****


以上のように、NaCl向けgdbを使うことで、実行中のNaClモジュールについて、どのような処理分岐が行われたのかステップ実行で確認することや、変数にどのような値がセットされたのか実行中に確認することができるようになりました。これを最初から知っていれば、NaClモジュールを開発する際に非常に安心して作業ができると思います。

ぜひ冬休みの宿題として、皆さんもNaClにチャレンジしてみてください。
