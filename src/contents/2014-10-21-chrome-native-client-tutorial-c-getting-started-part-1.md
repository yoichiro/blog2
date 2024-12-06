---
layout: post
status: publish
published: true
title: Chrome Native Client Tutorial C++ Getting Started編 (Part 1)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 3022
wordpress_url: https://www.eisbahn.jp/yoichiro/?p=3022
date: '2014-10-21 09:24:15 +0900'
date_gmt: '2014-10-21 00:24:15 +0900'
categories:
- Chrome Native Client
---

[前のエントリ]()では、Native Client SDKに含まれる各種サンプルコードのビルドと実行方法について紹介したドキュメントを和訳して掲載しました。このエントリでは、更に進んで、C++によるシンプルなNative Clientモジュールを作成するチュートリアルについて和訳してみましたので、掲載してみたいと思います。このチュートリアル自体は本当に簡単であり、しかし仕組みが良くわかる内容になっています。試すこと自体はあっという間にできるので、ぜひ試してみてください。

原文: 
[C++ Tutorial: Getting Started (Part 1)](https://developer.chrome.com/native-client/devguide/tutorial/tutorial-part1)

****


# C++ Tutorial: Getting Started (Part 1)

# 概要

このチュートリアルは、Portable Native Client(PNaCl)を使ったWebアプリケーションをビルドし実行するための方法を紹介します。これは、HTML、JavaScript、そしてC++で書かれたNative Clientモジュールを使うクライアント側のアプリケーションです。PNaCl Toolchainは、WebページからダイレクトにNative Clientモジュールを実行することを可能にするために使われます。

このチュートリアルを進めていく前に、
[Native Client Technical Overview](https://developer.chrome.com/native-client/overview.html)を読むことを勧めます。

このチュートリアルでのアプリケーションがすること

このチュートリアルでのアプリケーションは、Webページ内にNative Clientモジュールを読み込む方法、そしてJavaScriptとNative Clientモジュール間でメッセージを送信する方法を紹介します。このサンプルアプリケーションにおいて、JavaScriptはNative Clientモジュールに'hello'メッセージを送信します。Native Clientモジュールがメッセージを受信した時、そのメッセージが'hello'文字列と一致するかどうかをチェックします。もし一致すれば、Native Clientモジュールは'hello from NaCl'というメッセージを返します。JavaScriptアラートパネルは、Native Clientモジュールから受け取ったメッセージを表示します。

JavaScriptとNative Clientモジュール間の通信

Native Clientのプログラミングモデルは、JavaScriptとNative Clientモジュール間で双方向の通信をサポートします。両方の側から、メッセージを送信し受信することが可能です。全ての場合において、その通信は非同期です; 呼び出し側(JavaScriptまたはNative Clientモジュール)はメッセージを送信しますが、呼び出し側は返事を待たず、期待もしません。この振る舞いは、Webのクライアント/サーバ通信に類似します。つまり、クライアントはメッセージをサーバに投稿し、直ちに復帰します。Native Clientメッセージングシステムは、Pepper APIの一部であり、
[Developer's Guide: Messaging System](https://developer.chrome.com/native-client/devguide/coding/message-system.html)にて詳しく説明されています。それはまた、JavaScriptにおいてメインの文書と相互に関係する
[web workers](http://en.wikipedia.org/wiki/Web_worker)の方法に似ています。

# Step 1: Native Client SDKのダウンロードとインストール

Native Client SDKをダウンロードしインストールするために、
[Download](https://developer.chrome.com/native-client/sdk/download.html)ページの指示に従ってください。

# Step 2: ローカルサーバの開始

プロダクション環境をシミュレートするために、SDKはlocalhostにてアプリケーションを稼働可能にするシンプルなWebサーバを提供します。serveと呼ばれる便利なMakefileルールを以下の方法で簡単に呼び出せます:

```
$ cd pepper_$(VERSION)/getting_started
$ make serve
```

>SDKは、Chrome/Pepperバージョンごとに存在する(
[versioning information](https://developer.chrome.com/native-client/version.html)をご覧ください)いくつかの"バンドル"で構成されています。上記のサンプル呼び出しにおいて、pepper_$(VERSION)はあなたが使いたい特定のバージョン(例えば、pepper_31)を参照します。もし必要とするバージョンを知らない場合は、naclsdk listコマンドによって(stable)とラベル付けされたものを使ってください。より詳しい情報は、
[Download the Native Client SDK](https://developer.chrome.com/native-client/sdk/download.html)をご覧ください。


もしポート番号が指定されなかった場合は、サーバはポート5103を初期値とします。つまり、http://localhost:5103でアクセス可能です。

任意のサーバは、開発を目的として利用することが可能です。SDKと共に提供されるものは便利ですが、必須ではありません。

# Step 3: Chromeブラウザのセットアップ

PNaClは、Chromeバージョン31およびそれ以降で最初から有効化されています。このチュートリアルを勧めるために、適したバージョンを持っているかどうか確認してください。Native Clientモジュールをビルドするために使われるSDKバンドルがChromeバージョンと同じ、もしくはそれよりも新しいバージョンを使うことが重要です。

>Chromeのバージョンを見つけるために、アドレスバー内でabout:chromeとタイプしてください。


より良い開発体験のために、Chromeキャッシュ機構を無効化することをお勧めします。Chromeは、リソースをアグレッシブにキャッシュしますので、キャッシュ機構の無効化はNative Clientモジュールの最新バージョンが開発中に読み込まれることを確実にしてくれます。

* メニューアイコン
![](https://developer.chrome.com/native-client/images/menu-icon.png)をクリックしてChromeのDeveloper Toolsを開き、Tools>Developer toolsを選択します。

* Chromeウィンドウの右下のコーナーにあるギアのアイコン
![](https://developer.chrome.com/native-client/images/gear-icon.png)をクリックします。

* "General"設定の下で、"Disable cache (while DevTools is open)"の隣のチェックボックスにチェックを入れます。

* Native Clientアプリケーションを開発している間は、Developer Toolsのパネルを開いたままにします。

# チュートリアルのスタブコード

チュートリアルのスタブコードは、SDK内(pepper_$(VERSION)/getting_started/part1)で利用可能です。それは以下のファイルを含んでいます:

* index.html: ページのHTMLレイアウトや、Native Clientモジュールと相互作用するJavaScriptコードを含みます。Native Clientモジュールは、マニフェストファイルを指し示すページ内の
タグを含みます。

* hello_tutorial.nmf: HTMLにNative Clientモジュールを指し示すために利用され、またChromeブラウザの一部であるPNaCl変換機能に追加のコマンドを任意で提供するマニフェストファイルです。

* hello_tutorial.cc: シンプルなNative ClientモジュールのC++コード。

* Makefile: hello_tutorial.cc内のC++コードからpexe(portable executable)をビルドするためのコンパイルコマンドです。

今これらのファイルを見てみることは良いアイディアです。それらは、構造や内容を説明するための助けになるとても多くのコメントを含んでいます。典型的なNative Clientモジュールの構造についての詳細は、
[Application Structure](https://developer.chrome.com/native-client/devguide/coding/application-structure.html)をご覧ください。

スタブコードは、わざと非常に小さくしています。自身を正しく初期化すること以外、C++コードは何もしません。JavaScriptコードはNative Clientモジュールが読み込まれることを待っていて、それを受けてWebページ上でステータステキストを変更します。

# Step 5: Native Clientモジュールのコンパイルとスタブアプリケーションの実行

Native Clientモジュールをコンパイルするために、makeを実行します:

```
$ cd pepper_$(VERSION)/getting_started/part1
$ make
```

サンプルはSDKツリーの中に配置されているので、MakefileはPNaCl Toolchainを自動的に見つけるための方法を知っていて、モジュールのビルドのためにそれを使います。もしNaCl SDKツリーの外でアプリケーションをビルドする場合は、$NACL_SDK_ROOT環境変数をセットすべきでしょう。詳細は、
[Building Native Client Modules](https://developer.chrome.com/native-client/devguide/devcycle/building.html)をご覧ください。

Step 2の指示に従ってローカルサーバが開始されていたと仮定すると、あなたは今http://localhost:5103/part1(訳注: たぶん"/part1"は必要ないです)にChromeをポイントすることで、サンプルを読み込むことができます。ChromeはNative Clientモジュールの読み込みに成功し、ステータステキストは"LOADING..."から"SUCCESS"に変更されるはずです。もし問題が発生した場合は、以下の
[Troubleshooting section](https://developer.chrome.com/native-client/devguide/tutorial/tutorial-part1#tutorial-troubleshooting)を確認してください。

# Step 6: Native Clientモジュールにメッセージを送信するためのJavaScriptコードの修正

このステップでは、ページがモジュールを読み込んだ後に、Native Clientモジュールにメッセージを送信するためのWebページ(index.html)の修正を行います。

JavaScript関数のmoduleDidLoad()を探してください。そして、モジュールに'hello'メッセージを送るための新しいコードを追加してください。新しい関数は、以下のようになるはずです:

```
function moduleDidLoad() {
  HelloTutorialModule = document.getElementById('hello_tutorial');
  updateStatus('SUCCESS');
  // Send a message to the Native Client module
  HelloTutorialModule.postMessage('hello');
}
```

# Step 7: Native Clientモジュール内にメッセージハンドラを実装

このステップでは、アプリケーションのJavaScriptコードから受信されるメッセージに応答するためのNative Clientモジュール(hello_tutorial.cc)の修正を行います。特に、以下になります:

* モジュールインスタンスのHandleMessage()メンバ関数を実装します。

* モジュールからJavaScriptコードにメッセージを送信するために、PostMessage()メンバ関数を使用します。

最初に、Native Clientモジュールによって使われる変数を定義するためにコードを追加します(JavaScriptから受け取ることを期待している'hello'文字列と、返信としてJavaScriptに返却したい返事の文字列)。hello_tutorial.ccファイルの中で、#include文の後にこのコードを追加します:

```
namespace {
// The expected string sent by the browser.
const char* const kHelloString = "hello";
// The string sent back to the browser upon receipt of a message
// containing "hello".
const char* const kReplyString = "hello from NaCl";
} // namespace
```

ここで、kHelloStringをチェックしてkReplyStringを返却するために、HandleMessage()メンバ関数を実装します。以下の行を探してください。

```
// TODO(sdk_user): 1. Make this function handle the incoming message.
```

以下のようにして、メンバ関数をコードで満たしてください:

```
virtual void HandleMessage(const pp::Var& var_message) {
  if (!var_message.is_string())
    return;
  std::string message = var_message.AsString();
  pp::Var var_reply;
  if (message == kHelloString) {
    var_reply = pp::Var(kReplyString);
    PostMessage(var_reply);
  }
}
```

[pp::Instance.HandleMessage](https://developer.chrome.com/native-client/pepper_stable/cpp/classpp_1_1_instance.html#a5dce8c8b36b1df7cfcc12e42397a35e8)および
[pp::Instance.PostMessage](https://developer.chrome.com/native-client/pepper_stable/cpp/classpp_1_1_instance.html#a67e888a4e4e23effe7a09625e73ecae9)メンバ関数についての追加の情報は、Pepper APIドキュメントをご覧ください。

# Step 8: Native Clientモジュールのコンパイルとアプリケーションの再実行

* makeコマンドを再び実行して、Native Clientモジュールをコンパイルします。

* make server(訳注: たぶんserveの間違いです)を実行して、SDK Webサーバを開始します。

* Chromeにてhttp://localhost:5103/part1(訳注: たぶん"/part1"は必要ないです)をリロードして、アプリケーションを再実行します。

ChromeがNative Clientモジュールを読み込んだ後、モジュールから送られたメッセージを見ることができるはずです。

# トラブルシューティング

もしアプリケーションが動作しない場合は、Chromeブラウザとローカルサーバの両方について、正しくあなたの環境がセットアップされたかどうか検証するために、上記の
[Step 3](https://developer.chrome.com/native-client/devguide/tutorial/tutorial-part1#tutorial-step-3)をご覧ください。あなたが使っているSDKバンドルのバージョンと同じかそれ以降の、Chromeの正しいバージョンで実行しているかどうか確認してください。

もう一つの便利なデバッグの助けとして、Chrome JavaScriptコンソール(ChromeのToolsメニュー経由で利用可能)があります。それを使って、何が悪かったかの原因を調べます。例えば、もし"NaCl module crashed"と書かれたメッセージがあった場合は、Native Clientモジュールにバグがある可能性があります。つまり、
[debugging](https://developer.chrome.com/native-client/devguide/devcycle/debugging.html)が必要でしょう。

ドキュメントの中にトラブルシューティングについての追加情報があります:

* [FAQ Troubleshooting](https://developer.chrome.com/native-client/faq.html#faq-troubleshooting).

* [Progress Events](https://developer.chrome.com/native-client/devguide/coding/progress-events.html)ドキュメントは、エラーイベントのハンドリングについてのいくつか便利な情報を含んでいます。

# 次のステップ

* Native Clientモジュールがどのような構造なのかについての情報を、Developer's Guideの中の
[Application Structure](https://developer.chrome.com/native-client/devguide/coding/application-structure.html)セクションでご覧ください。

* Pepper APIsの使い方についての詳細は、
[C++ Reference](https://developer.chrome.com/native-client/pepper_stable/cpp)をチェックしてください。

* Native Clientアプリケーションの書き方と、Pepper APIsの利用についての
  追加のテクニックを学ぶために、SDKサンプル(examplesディレクトリ内)のソースコードを閲覧してください。

* Native Clientアプリケーションのビルド、実行、そしてデバッグの仕方についての情報は、
[Building](https://developer.chrome.com/native-client/devguide/devcycle/building.html), 
[Running](https://developer.chrome.com/native-client/devguide/devcycle/running.html), そして
[Debugging pages](https://developer.chrome.com/native-client/devguide/devcycle/debugging.html)をご覧ください。

* Native Clientと共に使用されるライブラリとして何が移植されているかを知るために、
[naclports](http://code.google.com/p/naclports/)プロジェクトをチェックしてください。もしあなた自身が使うためにオープンソースライブラリを移植した場合は、ぜひnaclportsに追加をお願いします(
[How to check code into naclports](http://code.google.com/p/naclports/wiki/HowTo_Checkin)をご覧ください)。
