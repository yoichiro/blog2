---
layout: post
status: publish
published: true
title: Chrome Native Client Tutorial サンプル実行編
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 3018
wordpress_url: https://www.eisbahn.jp/yoichiro/?p=3018
date: '2014-10-20 10:08:14 +0900'
date_gmt: '2014-10-20 01:08:14 +0900'
categories:
- Chrome Native Client
---

[前のエントリ](https://www.eisbahn.jp/yoichiro/2014/10/chrome-native-client-tutorial-download.html)で、Chrome Native Client SDKのインストール方法に関するドキュメントの和訳を掲載しました。このエントリでは、インストールされたSDKに含まれる各種サンプルコードをビルドし実行するための方法が説明されたドキュメントについて、僕が和訳したものを掲載したいと思います。百聞は一見にしかず、とにかくまずは実際に動いているものを見ることが、技術の習得の近道と言えるでしょう。もしNative Clientに興味がある方は、ぜひSDKをインストールして、サンプルを実行してみてください。

原文: 
[Examples](https://developer.chrome.com/native-client/sdk/examples)

****


# サンプル

全てのNative Client SDKバンドルは、サンプルのアプリケーションのフォルダーがあります。各サンプルは、一つあるいは二つのNative Clientプログラミングコンセプトのキーをデモンストレーションします。SDKをダウンロードした後に、サンプルをビルドして実行するために、このページの指示に従ってください。

あなたのChromeのバージョンは、あなたのSDKバンドルのバージョンと同じかそれ以上でなければなりません。例えば、もしpepper_35バンドルを使って開発している場合は、Google Chromeバージョン35あるいはそれ以上を使う必要があります。使っているChromeのバージョンを見つけるには、Chromeのアドレスバーにてabout:chromeあるいはabout:versionをタイプしてください。

# Native Clientの有効化

もしChrome 31もしくはそれ以降を使っている場合は、このセクションをスキップできます。Portable Native Clientアプリケーションを実行するために、ChromeにてNative Clientを特別に有効化する必要があります:

1. Chromeのアドレスバーでabout:flagsをタイプし、"Native Client"にスクロールダウンします。

* もし"Native Client"の下のリンクが"Disable"と書かれている場合は、Native Clientは既に有効化されているため、何もする必要はありません。

* もし"Native Client"の下のリンクが"Enable"と書かれている場合は、"Enable"リンクをクリックしてください。

2. そのページの一番下までスクロールダウンし、"Relaunch Now"をクリックしてください。Chromeを再起動した際は、全てのブラウザのウィンドウがリスタートするでしょう。

# SDKのサンプルのビルド

SDKサンプルのMakefileスクリプトは、3つのSDK Toolchain（newlib, glibc, PNaCl）のどれか、そしてRelease、Debugの両方の設定を使って、サンプルの複数バージョンをビルド可能です。いくつかのサンプル（例えばdlopen）は特定のToolchainでのみビルド可能なことに注意してください。

特定のサンプルのためのMakefile内にあるVALID_TOOLCHAINS変数を探すことで、各サンプルのToolchainsを見つけます。一覧の最初の選択肢は、初期値になります。パラメータなしでサンプルのMakefileを実行したときに、それはビルドされます。例えば、pepper_35のcoreディレクトリでmakeを実行すると、サンプルがnewlib toolchainを使ってビルドします。

```
$ cd pepper_35/examples/api/core
$ make
  CXX  newlib/Release/core_x86_32.o
  LINK newlib/Release/core_unstripped_x86_32.nexe
  VALIDATE newlib/Release/core_unstripped_x86_32.nexe
  CXX  newlib/Release/core_x86_64.o
  LINK newlib/Release/core_unstripped_x86_64.nexe
  VALIDATE newlib/Release/core_unstripped_x86_64.nexe
  CXX  newlib/Release/core_arm.o
  LINK newlib/Release/core_unstripped_arm.nexe
  VALIDATE newlib/Release/core_unstripped_arm.nexe
  STRIP newlib/Release/core_x86_32.nexe
  STRIP newlib/Release/core_x86_64.nexe
  STRIP newlib/Release/core_arm.nexe
  CREATE_NMF newlib/Release/core.nmf
```

見ての通り、これはpepper_35/examples/api/core/Releaseディレクトリ内にいくつかのアーキテクチャ特有のnexeファイルを提供します。makeコマンドのCONFIGパラメータを使うことで、Debugバージョンを作成します。

```
$make CONFIG=Debug
```

これは、pepper_35/examples/api/core/Debugの中に、同じような出力を生成します。

TOOLCHAINパラメータを使うことで、異なるToolchainを選択します。例えば以下のようになります:

```
$ cd pepper_35/examples/api/core
$ make TOOLCHAIN=pnacl CONFIG=Release
  CXX  pnacl/Release/core.o
  LINK pnacl/Release/core_unstripped.bc
  FINALIZE pnacl/Release/core_unstripped.pexe
  CREATE_NMF pnacl/Release/core.nmf
```

初期Toolchainを使って全てのReleaseバージョンをビルドするために、TOOLCHAINにallをセットすることも可能です。

```
$ cd pepper_35/examples/api/core
$ make TOOLCHAIN=all
make TOOLCHAIN=newlib
make[1]: Entering directory 'pepper_35/examples/api/core'
  CXX  newlib/Release/core_x86_32.o
  LINK newlib/Release/core_unstripped_x86_32.nexe
  VALIDATE newlib/Release/core_unstripped_x86_32.nexe
  CXX  newlib/Release/core_x86_64.o
  LINK newlib/Release/core_unstripped_x86_64.nexe
  VALIDATE newlib/Release/core_unstripped_x86_64.nexe
  CXX  newlib/Release/core_arm.o
  LINK newlib/Release/core_unstripped_arm.nexe
  VALIDATE newlib/Release/core_unstripped_arm.nexe
  STRIP newlib/Release/core_x86_32.nexe
  STRIP newlib/Release/core_x86_64.nexe
  STRIP newlib/Release/core_arm.nexe
  CREATE_NMF newlib/Release/core.nmf
make[1]: Leaving directory 'pepper_35/examples/api/core'
make TOOLCHAIN=glibc
make[1]: Entering directory 'pepper_35/examples/api/core'
  CXX  glibc/Release/core_x86_32.o
  LINK glibc/Release/core_unstripped_x86_32.nexe
  VALIDATE glibc/Release/core_unstripped_x86_32.nexe
  CXX  glibc/Release/core_x86_64.o
  LINK glibc/Release/core_unstripped_x86_64.nexe
  VALIDATE glibc/Release/core_unstripped_x86_64.nexe
  ...
  (content excerpted)
  ...
```

ビルド結果

makeの実行後、サンプルのディレクトリは以下に示した一つ以上のサブディレクトリを含むでしょう。これは、実行されたMakefileに依存します:

* DebugおよびReleaseサブディレクトリを持つnewlib

* DebugおよびReleaseサブディレクトリを持つglibc

* DebugおよびReleaseサブディレクトリを持つpnacl

newlibやglibc Toolchainのために、DebugおよびReleaseサブディレクトリは、対象のアーキテクチャ全てに対して.nexeファイルを含みます。PNaCl Toolchainの場合は、一つの.pexeファイルを含みます。PNaCl debugは、デバッグを簡単にするために、事前に変換された.nexeファイルも提供されます。全てのDebugおよびReleaseディレクトリは、関連される.nexeや.pexeファイルを参照するマニフェスト(.nmf)ファイルを含みます。Native Clientマニフェストファイルについての情報は、
[Technical Overview](https://developer.chrome.com/native-client/overview.html)を参照ください。

makeの使い方の詳細は、
[GNU 'make' Manual](http://www.gnu.org/software/make/manual/make.html)をご覧ください。SDK Toolchain自体の使い方の詳細は、
[Building Native Client Modules](https://developer.chrome.com/native-client/devguide/devcycle/building.html)を参照ください。

# SDKサンプルの実行

Chromeキャッシュの無効化

Chromeの気の利いたキャッシュ機構は、リソースをアグレッシブにキャッシュします。Native Clientアプリケーションの開発の際は、最新のバージョンをChromeが読み込むことを確実にするために、キャッシュ機構を無効化すべきです。インテリジェントキャッシュは、Developer Toolsを開いている間だけ、無効になります。そうでなければ、アグレッシブなキャッシュは継続します。

* メニューアイコン
![](https://developer.chrome.com/native-client/images/menu-icon.png)をクリックしてChromeのDeveloper Toolsを開き、Tools>Developer toolsを選択します。

* Chromeウィンドウの右下のコーナーにあるギアのアイコン
![](https://developer.chrome.com/native-client/images/gear-icon.png)をクリックします。

* "General"設定の下で、"Disable cache"の隣のチェックボックスにチェックを入れます。

サンプルの実行

SDKのサンプルを実行するために、make runコマンドを使います:

```
$ cd pepper_35/examples/api/core
$ make run
```

これは、サンプルを動かすためのローカルHTTPサーバを起動します。その後、このサーバのアドレスを持つChromeを起動します。通常、そのアドレスはhttp://localhost:5103です。Chromeを閉じた後、ローカルHTTPサーバも自動的に終了します。

このコマンドは、PATH環境変数の中からgoogle-chromeという名前の実行形式を見つけようとします。もしそれができない場合は、このようなエラーメッセージを得るでしょう:

```
pepper_35/tools/common.mk:415: No valid Chrome found at CHROME_PATH=
pepper_35/tools/common.mk:415: *** Set CHROME_PATH via an environment variable, or command-line..  Stop.
```

Chromeのための環境変数の追加

Chrome実行形式の場所を指すCHROME_PATH環境変数をセットします。

・Windows:

Chromeのインストール場所の初期値は、Chrome安定版ではC:\Program Files (x86)\Google\Chrome\Application\chrome.exe、Chrome CanaryではC:\Users\
\AppData\Local\Google\Chrome SxS\Application\chrome.exeです。最初にそれらのディレクトリを見つけてください:

```
> set CHROME_PATH=<Path to chrome.exe>
```

・Linux:

```
$ export CHROME_PATH=<Path to google-chrome>
```

・Mac:

Chromeのインストール場所の初期値は、Chrome安定版では/Applications/Google Chrome.app/Contents/MacOS/Google Chrome、Chrome Canaryでは/Applications/Google Chrome Canary.app/Contents/MacOS/Google Chromeです。トップレベルの.appディレクトリではなく、アプリケーションバンドルの内部にある実行形式を参照しなければならないことに注意してください。

```
$ export CHROME_PATH=<Path to Google Chrome>
```

# Packaged appsとしてSDKのサンプルを実行する

各サンプルは、Packagedアプリケーションとしても起動することができます。Packagedアプリケーションは、Chromeウェブストアでホストされる特別なZipファイル(.crx拡張子を持ちます)です。このファイルは、Chromeウェブストアマニフェストファイル(manifest.json)、アイコン、通常のNative Clientアプリケーションファイルの全て、といったアプリケーションの部品の全てを含みます。Packagedアプリケーションの作成についてのより詳しい情報は、
[What are Chrome Apps](https://developer.chrome.com/apps/about_apps)を参照してください。

TCP/UDPソケットアクセスといったいくつかのPepper機能は、Packagedアプリケーションの中でのみ許可されます。これらの機能を使うサンプルは、以下のコマンドを使うことで、Packagedアプリケーションとして実行される必要があります。

```
$ make run_package
```

異なるToolchainや設定を使って実行するために、上記で説明されたTOOL_CHAINやCONFIGパラメータを使うことが可能です。

# SDKサンプルのデバッグ

NaCl SDKは、Native Clientコードをデバッグするために
[GDB](https://www.gnu.org/software/gdb/)を使います。SDKは、NaClコードと互換のあるGDBの事前ビルドバージョンを含みます。それを使うために、サンプルディレクトリからmake debugコマンドを実行してください:

```
$ make debug
```

これは、--enable-nacl-debugフラグがセットされたChromeを起動します。このフラグは、NaClモジュールが最初に読み込まれる時にChromeを一時停止して、gdbからの接続を待ちます。make debugコマンドは、同時にGDCも起動し、そのNEXEのシンボルを読み込みます。GDBをChromeに接続するために、GDBコンソールの中で、以下をタイプします:

```
(gdb) target remote :4014
```

これは、Chromeがリッスンしているポートであるlocalhost:4014のTCPポートに接続することをGDBに伝えます。GDBは以下の返事をするでしょう:

```
Remote debugging using :4014
0x000000000fa00080 in ?? ()
```

この時点で、NaClモジュールをデバッグするための標準的なGDBコマンドを使うことが可能です。あなたがデバッグで使うことになる最も共通的なコマンドは、continue, step, next, break, そしてbacktraceでしょう。NaCl Clientアプリケーションのデバッグについてのより詳しい情報は、
[Debugging](https://developer.chrome.com/native-client/devguide/devcycle/debugging.html)をご覧ください。
