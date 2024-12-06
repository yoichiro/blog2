---
layout: post
status: publish
published: true
title: Chrome Native Client Tutorial ダウンロード編
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 3005
wordpress_url: https://www.eisbahn.jp/yoichiro/?p=3005
date: '2014-10-18 20:26:41 +0900'
date_gmt: '2014-10-18 11:26:41 +0900'
categories:
- Chrome Native Client
---

もし、Webアプリケーションの開発の中で「どーしてもJavaScriptではきつい箇所」に直面した時、Chromeであれば「Native Client」を使って開発するという手があります。Googleより公開されているSDKを使ってCやC++でモジュールを作成し、JavaScriptとそのモジュール間で通信することが可能です。従来からあるアーキテクチャごとにモジュールの実行形式を作って提供するNaClと、LLVMの中間形式の形でモジュールを作って提供するPNaClがあります。どちらも、同じSDKで開発が可能です。

Googleから公開されているSDKのインストール方法について、和訳を行ってみました。もしNaClやPNaClについて気になってる方は、ぜひSDKを入手して見てください。

原文: 
[Download the Native Client SDK](https://developer.chrome.com/native-client/sdk/download)

****


# Native Client SDKのダウンロード

このページは、Native Client SDKの概要を提供し、そしてSDKのダウンロードとインストールを紹介します。

# 概要

[Download SDK Zip File](http://storage.googleapis.com/nativeclient-mirror/nacl/nacl_sdk/nacl_sdk.zip)

Native Client SDKは、以下を含んでいます:

* 複数のPepperバージョンのサポート Chromeの指定最小バージョンでコンパイルする。

* 更新ユーティリティ 新しいバンドルをダウンロードし、既存のバンドルを更新する。

* Toolchains Portable Native Client (PNaCl), 従来のNative Client (NaCl), そしてglibcによる特定のアーキテクチャ向けのNative Clientアプリケーションをコンパイルする。

* 例 NaClやPepperの使い方を把握できるCやC++ソースファイルやヘッダファイル、各Toolchainを使った例をビルドするためのMakefileを含む。

* ツール Native Clientモジュールの検証や、コマンドラインからモジュールを実行する。

以下の手順に従って、Native Client SDKのダウンロードとインストールを行ってください。

# 準備

Python 2.7

Python実行形式がPATH変数に含まれているかどうか確認してください。Python 3.xはまだサポートされていません。

* MacやLinuxでは、Pythonはプリインストールされているでしょう。端末ウィンドウ内でpython -Vコマンドを実行し、2.6.xまたは2.7.xバージョンを持っているかどうか確認してください。

* WIndowsでは、Pythonをインストールする必要があります。
[http://www.python.org/download/](http://www.python.org/download/)に行き、最新の2.xバージョンを選んでください。加えて、Pythonディレクトリ（例えば、C:¥pyhthon27）がPATH環境変数内にあることを確認してください。PATH変数が正しく設定されていることを確認するために、python -Vをコマンドラインから実行してください。

Make

* Macでは、SDK内の例をビルドおよび実行する前に、システムにmakeがインストールされていることが必要です。makeを入手するための簡単な方法は、いくつかの他の便利なツールと共に、
[Xcode Developer Tools](https://developer.apple.com/technologies/tools/)をインストールすることです。Xocdeのインストール後、Xcodeメニューに行き、設定ダイアログボックスを開き、ダウンロードとコンポーネントを選択します。Command Line Toolsがインストールされていることを確認してください。もしXcodeをインストールしたくない場合は、makeの
[オープンソースバージョン](http://mac.softpedia.com/dyn-postdownload.php?p=44632&t=4&i=1)をダウンロードしビルドすることが可能です。プログラムをビルドするために、
[gcc](https://github.com/kennethreitz/osx-gcc-installer)もダウンロードしインストールすることが必要になるでしょう。

* Windowsでは、Native Client SDKはGNU Makeのコピーを含んでいます。

# プラットフォーム

Native Clientは、Windows, Linux, OSX, そしてChromeOSを含むいくつかのオペレーティングシステムをサポートします。そして、x86-32, x86-64, ARM, そしてMIPSを含むいくつかのアーキテクチャをサポートしています。

# バージョン

Chromeは6週間のサイクルでリリースされていて、Chromeの開発者バージョンは各リリースの3週間前に公開ベータチャンネルにプッシュされます。他のソフトウェアと同様に、Chromeの各リリースでは、既存のアプリケーションに更新が必要となるかもしれないNative ClientやPepperの変更を含んでいるかも知れません。しかしながら、Pepper/Chromeのあるバージョン向けにコンパイルされたモジュールは、Pepper/Chromeのその後のバージョンを使って動作するはずです。SDKは、開発者がAPI変更の調整を行い、そして新しい機能を活用するために、Pepper APIの複数のバージョン（stable, beta, dev）を含んでいます。

# SDKのインストール

ダウンロードおよびZIPの解凍

1. 
[SDK更新zipファイル](http://storage.googleapis.com/nativeclient-mirror/nacl/nacl_sdk/nacl_sdk.zip)をダウンロードします。
2. Zipファイルを解凍します:

* Mac/Linuxでは、端末ウィンドウ内でunzip nacl_sdk.zipコマンドを実行する。

* Windowsでは、.zipファイルの上で右クリックし、"Extract All..."を選択する。ダイアログボックスが開き、場所を入力して"Extract"をクリックする。
以下のファイルやディレクトリを持つnacl_sdkと呼ばれるディレクトリが作成されます。

* naclsdk (Windowsではnaclsdk.batも） - バンドルをダウンロードし更新するコマンドである更新ユーティリティ

* sdk_cache - 既にダウンロードされたバンドルが一覧されたマニフェストファイルを持つディレクトリ。

* sdk_tools - naclsdkコマンドによって実行されるコード。

# 安定版バンドルのインストール

1. ダウンロードが可能なSDKバンドルを見るために、nacl_sdkディレクトリに行き、listを伴うnaclsdkコマンドを実行してください。SDKはChrome/Pepperの各バージョンで分離されたバンドルを持ちます。

Mac/Linux:

```
$ cd nacl_sdk
$ ./naclsdk list
```

Windows:

```
> cd nacl_sdk
> naclsdk list
```

このような出力を見るはずです:

```
Bundles:
 I: installed
 *: update available
  I  sdk_tools (stable)
     vs_addin (dev)
     pepper_31 (post_stable)
     pepper_32 (post_stable)
     pepper_33 (post_stable)
     pepper_34 (post_stable)
     pepper_35 (stable)
     pepper_36 (beta)
     pepper_37 (dev)
     pepper_canary (canary)
     bionic_canary (canary)
```

上の出力サンプルでは、いくつかのバンドルがダウンロード可能であり、zipファイルの中に含まれていたsdk_toolsバンドルの最新リビジョンが既にインストールされていることを示しています。あなたはsdk_toolsバンドルを更新する必要は全くありません。それは、naclsdkを実行した際に（必要であれば）自動的に更新されます。

バンドルは、post-stable, stable, beta, dev, またはcanaryとラベル付けされます。これらのラベルは通常Chromeの現在のバージョンと一致します。私たちは、"stable"バンドルに対して開発することを勧めます。なぜなら、そのようなバンドルは全ての現在のChromeユーザによって利用可能だからです。Native Clientは、後方互換になるようにデザインされました。例えば、pepper_31バンドルを使って開発されたアプリケーションは、Chrome31, Chrome32, その他で実行可能です。

2. 現在の"stable"バンドルを含む推奨されたバンドルをダウンロードするために、updateを指定したnaclsdkコマンドを実行します。

Mac/Linux:

```
$ ./naclsdk update
```

Windows:

```
> nacksdk update
```

初期設定により、naclsdkは一般的に"stable"である推奨されたバンドルのみをダウンロードします。例えば、もし現在の"stable"バンドルがpepper_35の場合、その時のupdateはそのバンドルをダウンロードします。pepper_36バンドルをダウンロードするには、それを明示的に指定しなければなりません:

```
$ ./naclsdk update pepper_36
```

# バンドルの更新

1. listを指定したnaclsdkコマンドを実行します。これにより利用可能なバンドルの一覧が表示され、インストールされているバンドルの検証が行われます。

Mac/Linux:

```
$ ./naclsdk list
```

Windows:

```
> naclsdk list
```

バンドルの隣のアスタリスク(*)は、利用可能な更新があることを示しています。例です:

```
Bundles:
 I: installed
 *: update available
  I  sdk_tools (stable)
     vs_addin (dev)
     pepper_31 (post_stable)
     pepper_32 (post_stable)
     pepper_33 (post_stable)
     pepper_34 (post_stable)
  I* pepper_35 (stable)
     pepper_36 (beta)
     pepper_37 (dev)
     pepper_canary (canary)
     bionic_canary (canary)
```

もし今naclsdk updateを実行した場合、このようなメッセージが表示されます:

```
WARNING: pepper_35 already exists, but has an update available. Run update
with the --force option to overwrite the existing directory. Warning: This
will overwrite any modifications you have made within this directory.
```

2. 新しいバンドルをダウンロード、インストールするために、以下を実行します:

Mac/Linux

```
$ ./naclsdk update --force
```

Windows:

```
> naclsdk update --force
```

# naclsdkユーティリティのヘルプ

1. naclsdkユーティリティについてのより詳しい情報は、以下を実行します:

Mac/Linux:

```
$ ./naclsdk help
```

Windows:

```
> naclsdk help
```

# 次のステップ

* SDKや新しいバンドルについての重要な情報を
[Release Notes](https://developer.chrome.com/native-client/sdk/release-notes)で閲覧してください。

* もしあなたがちょうどNative Clientを始めたとしたら、私たちは
[Technical Overview](https://developer.chrome.com/native-client/overview)を読むこと、そして
[Getting Started Tutorial](https://developer.chrome.com/native-client/sdk/devguide/tutorial/tutorial-part1)を行うことをお勧めします。

* むしろ飛び込みたい場合は、
[Building Native Client Modules](https://developer.chrome.com/native-client/sdk/devguide/devcycle/building)を見てください。
