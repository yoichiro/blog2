---
layout: post
status: publish
published: true
title: PNaClでOpenSSLの機能を利用するChrome appsの作り方
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 3044
wordpress_url: https://www.eisbahn.jp/yoichiro/?p=3044
date: '2014-11-03 22:00:20 +0900'
date_gmt: '2014-11-03 13:00:20 +0900'
categories:
- Chrome Native Client
---

ここのところ、PNaClについていろいろと調べてきました。なんで調べていたかというと、自分で作ってるChrome appsの中で、JavaScriptで書くのは非常にしんどい処理を既存のNative codeを呼び出すことで利用して楽したい、というのがあります。特に暗号系の処理は、扱う値の桁数も非常に大きく、もしOpenSSLにある関数で事足りるのであれば、それを利用したいところですよね。こういったことが、PNaClで可能なはず。それを自分でできるように習得したかったということです。まぁ、C++のコードはあまり書いたことがなく、単にC++で何か作ってみたかった、という興味もありますが。

このエントリでは、OpenSSLにあるMD5関数（もちろんMD5によるダイジェストを計算してくれる関数）をPNaClで呼び出して結果を表示してくれるChrome appsの作り方を紹介してみたいと思います。今まで調べてきた内容の集大成的な感じです。たぶんこのエントリ内で紹介している各手順について、以下のエントリ群を先に読んでおいた方が、すんなり理解しやすいと思いますので、ぜひ一読ください。長いですけど。

* [Chrome Native Client Tutorial ダウンロード編](https://www.eisbahn.jp/yoichiro/2014/10/chrome-native-client-tutorial-download.html)

* [Chrome Native Client Tutorial サンプル実行編](https://www.eisbahn.jp/yoichiro/2014/10/chrome-native-client-tutorial-sample.html)

* [Chrome Native Client Tutorial C++ Getting Started編 (Part 1)](https://www.eisbahn.jp/yoichiro/2014/10/chrome-native-client-tutorial-c-getting-started-part-1.html)

* [Chrome Native Client Tutorial C++ Getting Started編 (Part 2)](https://www.eisbahn.jp/yoichiro/2014/10/chrome-native-client-tutorial-c-getting-started-part-2.html)

* [naclportsがmacでmakeできなかった話](https://www.eisbahn.jp/yoichiro/2014/10/naclports_couldnt_be_made_on_mac.html)

では、早速作業に取りかかりましょう。

# YeomanによるChrome appsの自動作成

最初に、Chrome appsのひな形をYeoman使って自動作成します。Chrome appsの開発においては、どう考えても自分で環境整備するよりも、このYeomanで自動生成した方が、リッチな開発環境を整えられます。node.jsはインストールされているとして、以下の手順でYeomanのインストールからChrome appsのひな形作成まで、サクッとやっつけましょう。

```
$ sudo npm install -g yo
$ sudo npm install -g generator-chromeapp
$ mkdir simple_md5_app
$ cd simple_md5_app
$ yo chromeapp
$ ls
```

yo chromeappのところでいくつか質問が出てきますが、ここでは全て単にENTERキーを押していけば良いです。最終的に、gruntやbowerを利用する開発環境が整います。さらに、livereloadによる、ファイルの更新を監視してChrome appを自動的に再起動してくれるタスクも入っていたりするので、本当に便利です。上記を実行後、以下のようにファイルが生成されているはずです。

![スクリーンショット 2014-11-01 7.27.41](https://www.eisbahn.jp/yoichiro/images/2014/11/55a082f43960744395f7de3a40a1123e.png)

以降、simple_md5_appディレクトリの場所を ${APP_ROOT} と表現します。

# Chrome appの動作確認

Yeomanで出力されたコード群は、生成直後のこの状態でも実行して確認することができます。最初に動くものを入手して実行できることを確認する作業は、やる気の面で非常に重要ですね。Chromeに登録して実行させてみましょう。

* Chromeのアドレスバーに chrome://extensions/ と打ち込んで、拡張機能のページを表示する。

* 「デベロッパーモード」にチェックを入れる。

* 「パッケージ化されていない拡張機能を読み込む...」ボタンを押す。

* 表示されたディレクトリ選択ダイアログにて、${APP_ROOT}/app ディレクトリを選択する。

${APP_ROOT}ではなく、${APP_ROOT}/appを選択する必要があることに注意しましょう。この操作により、simple_md5_appアプリケーションが登録され、拡張機能一覧に追加表示されます。

![スクリーンショット 2014-11-01 7.39.03](https://www.eisbahn.jp/yoichiro/images/2014/11/cb9693052f7a4ee88a84638746c81459.png)

「起動」リンクをクリックしてみましょう。以下のようなウィンドウが表示されれば、成功です。

![スクリーンショット 2014-11-01 7.41.26](https://www.eisbahn.jp/yoichiro/images/2014/11/ecb6a39ab624603ce0f049cc71a1a308.png)

# UIの追加

今回作るものは、入力された文字列のMD5ダイジェスト値を表示する、というものです。そのために必要となるUIを、ここで追加してしまいましょう。

出力されたファイルの中に、${APP_ROOT}/app/index.html ファイルがあります。このファイルのbody要素内に、以下のコードを追加します。h1要素が一つあるので、その下にでも追加しておきましょう。

```
...
    <body>
<h1>'Allo</h1>
        <input id="source" type="text" />
        <button id="btn">Calculate MD5</button>
<div id="result"></div>
        <!-- build:js scripts/vendor.js -->
...
```

では、アプリケーションを再実行しましょう。アプリケーションウィンドウ内でコンテキストメニューを表示すると、「アプリの再読み込み」という項目があります。これをクリックすることで、修正が反映された状態のアプリケーションを実行することが可能です。

![スクリーンショット 2014-11-01 8.17.00](https://www.eisbahn.jp/yoichiro/images/2014/11/84cde56b00bf138c0d005594508015d4.png)

以下のようにUIが追加されたはずです。

![スクリーンショット 2014-11-01 8.18.04](https://www.eisbahn.jp/yoichiro/images/2014/11/8a72be18cbe716b39374961125a8a0d7.png)

# ボタンを押された時のイベントハンドリングの追加

先ほど配置したボタンが押された時のイベントハンドラを追加しましょう。index.htmlに動作を与えるために、既にindex.jsファイルが${APP_ROOT}/app/scriptsディレクトリ内に作成されています。このファイルの末尾に、以下のコードを追加します。

```
(function() {
  var MD5Calc = function() {
    this.assignEventListener();
  };
  MD5Calc.prototype.assignEventListener = function() {
    var btn = document.querySelector('#btn');
    btn.addEventListener('click', function() {
      this.btnClicked();
    }.bind(this));
  };
  MD5Calc.prototype.btnClicked = function() {
    var source = document.querySelector('#source').value;
    console.log('btnClicked: ' + source);
  };
  window.addEventListener('DOMContentLoaded', function() {
    new MD5Calc();
  });
})();
```

先ほどと同じようにアプリケーションを再実行します。何か入力してボタンを押したときに、DevToolsのConsoleに出力が行われてイベントハンドラが成城に呼び出されていることを確認します。DevToolsは、アプリケーションのコンテキストメニューから「要素の検証」を選ぶことで表示できます。

![スクリーンショット 2014-11-01 11.29.49](https://www.eisbahn.jp/yoichiro/images/2014/11/3b8ad2a69eaded00f97a76fd5f59e84b.png)

# NaCl SDKのインストール

HTMLやJavaScriptからは少し離れて、ここからはNaClモジュールを開発するための環境整備をしていきます。まずは、NaClモジュールをビルドするために必要となるSDKをインストールします。

NaCl SDKを以下の場所からダウンロードします。おそらく、nacl_sdk.zipというファイル名です。

[Download the Native Client SDK](https://developer.chrome.com/native-client/sdk/download)

これを任意の場所に展開し、安定版バンドルを入手します。SDKを利用するための事前準備などは、「
[Chrome Native Client Tutorial ダウンロード編](https://www.eisbahn.jp/yoichiro/2014/10/chrome-native-client-tutorial-download.html)」をご覧ください。

```
$ unzip nacl_sdk.zip
$ cd nacl_sdk
$ ./naclsdk update
```

このエントリを執筆している時点では、pepper_37という安定版バンドルがインストールされました。基本的に、その時の安定版バンドルが自動的に選択されます。インストール後、nacl_sdkディレクトリの中に、pepper_37ディレクトリが作成されているのがわかると思います。

![スクリーンショット 2014-11-01 11.41.21](https://www.eisbahn.jp/yoichiro/images/2014/11/5ba73534a5a135a64ed577c910dcb669.png)

# 環境変数の設定

実際にNaClモジュールをビルドする際に、SDKがインストールされているディレクトリを環境変数に設定しておくと、何かと便利です。SDKに含まれる各種サポートファイルは、NACL_SDK_ROOTという名前の環境変数にSDKのインストール場所が設定されていることを期待しているので、それを.bashrcや.zshenvなどに記述して有効にしておきます。

```
export NACL_SDK_ROOT=<nacl_sdkディレクトリ>/pepper_37
```

この際、nacl_sdkディレクトリ内にインストールされたpepper_37ディレクトリを指定することがポイントとなります。

# depot_toolsのインストール

NaCl SDKが準備できた後、次はnaclportsをインストールすることになるのですが、そのnaclportsをインストールするために、depot_toolsが必要になります。これは、ChromiumやChromium OSの開発で使われているスクリプト集です。ここでは、naclportsをダウンロードするためにdepot_toolsを利用します。

以下の手順で、depot_toolsをインストールします。

```
$ cd <depot_toolsをインストールしたい場所>
$ git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git
```

gitでcloneするだけです。そして、環境変数PATHに、cloneして作成されたdepot_toolsディレクトリを追加しておきます。.bashrcや.zshenvに追加して、有効にしておきましょう。

```
export PATH=<depot_toolsディレクトリ>:$PATH
```

# naclportsのインストール

depot_toolsに含まれるgclientコマンドを使って、naclportsをインストールします。以下の手順でインストールしましょう。

```
$ cd <naclportsをインストールしたい場所>
$ mkdir naclports
$ cd naclports
$ gclient config --name=src  https://chromium.googlesource.com/external/naclports.git
$ gclient sync
```

gclient syncを実行している間は、少し時間がかかるかもしれません。完了するまでゆっくり待ちましょう。もしmacで試している場合は、この後エラーが出て先に進めなくなっていたのですが、既に
[その問題は解決](https://code.google.com/p/naclports/issues/detail?id=158)しているので、大丈夫なはずです。

これで、NaClモジュールを開発するための環境が整いました。

# OpenSSLのビルド

もし目の前にある開発途中のChrome appの中でJavaScriptでは困難な処理に出くわした場合、以下の3つの選択肢があります。

* それでもJavaScriptで頑張る。

* CやC++でやりたい処理を全部自分で書いて、NaClモジュールとしてJavaScriptから利用する。

* naclportsにある既存ライブラリを使うNaClモジュールを作って、それをJavaScriptから利用する。

今回のMD5ダイジェスト値計算はOpenSSLにあることがわかっていて、naclportsにOpenSSLが含まれているので、もちろんそれを利用しない手はないでしょう。naclportsにあるライブラリを利用したい場合は、以下の手順となります。

* 使いたいライブラリをnaclportsからビルドし、NaCl SDKにインストールする。

* NaCl SDKにインストールされたヘッダファイルを使って、NaClモジュール内から使いたい関数を呼び出して利用する。

* NaCl SDKを使って、NaClモジュールをビルドする。

つまり、naclportsをインストールしただけでは利用できず、使いたいライブラリを事前にビルドしてNaCl SDKに配置しておくことが必要になります。naclportsで提供される全てのライブラリを一気にビルドしてNaCl SDKに配置することも可能ですが、個人的には必要なものだけを都度ビルドしていく方が好みです。

では、OpenSSLをビルドしましょう。今回はアーキテクチャ非依存のPortable NaCl（=PNaCl）モジュールを作りたいので、アーキテクチャにpnaclを指定してOpenSSLをビルドします。

```
$ cd <naclportsをインストールしたディレクトリ>
$ NACL_ARCH=pnacl make openssl
```

OpenSSLなので、ビルドにはかなりの時間がかかります。気長に待ちましょう。基本的には問題なく終了するはずです。ビルドが完了した後、例えば$NACL_SDK_ROOTディレクトリ内で以下のようにOpenSSL関連のファイルが配置されていることがわかると思います。

![スクリーンショット 2014-11-01 22.49.58](https://www.eisbahn.jp/yoichiro/images/2014/11/a5535952a1076b905b55691d147a55ed.png)

# MD5計算をするC++コードの作成

やっと、C++でのコーディングの時間です。環境構築は何かと手間がかかる作業なので、致し方ないですね。

NaClモジュールとしてC++でコードを作成する際に、実は最初の一歩は、決まり切ったひな形でccファイルを作成することになります。そのひな形は、以下のようになるでしょう。ここでは、appディレクトリ内にmd5_calc.ccというファイル名で作成します。

```
#include "ppapi/cpp/instance.h"
#include "ppapi/cpp/module.h"
#include "ppapi/cpp/var.h"
class MD5CalcInstance : public pp::Instance {
public:
  explicit MD5CalcInstance(PP_Instance instance) : pp::Instance(instance) {
  }
  virtual ~MD5CalcInstance() {
  }
  virtual void HandleMessage(const pp::Var& var_message) {
    if (!var_message.is_string()) {
      return;
    }
    // Do something...
    pp::Var var_reply(...);
    PostMessage(var_reply);
  }
};
class MD5CalcModule : public pp::Module {
public:
  MD5CalcModule() : pp::Module() {
  }
  virtual ~MD5CalcModule() {
  }
  virtual pp::Instance* CreateInstance(PP_Instance instance) {
    return new MD5CalcInstance(instance);
  }
};
namespace pp {
  Module* CreateModule() {
    return new MD5CalcModule();
  }
}
```

MD5CalcModule, MD5CalcInstanceという2つのクラスが宣言されています。このMD5Calcという部分は、作りたいNaClモジュールに合わせて変更してください。今回はMD5ダイジェスト値計算が目的なので、MD5Calc〜という命名をしました。

NaClモジュールは、Pepper APIと呼ばれるJavaScriptとNaClモジュール間のメッセージパッシングの機構を使って処理の連携を行います。上記のコードの中で重要な箇所は、HandleMessage()関数です。JavaScriptからメッセージが送信されると、このHandleMessage()関数が呼び出され、受信したメッセージが引数に渡されます。このメッセージの内容によって何か処理を行って、必要であればPostMessage()関数を使ってメッセージをJavaScript側に送る、という流れになります。

ちなみに、"handleMessage"と書いてしまうと、コンパイルは通って実行時もエラーは何も出ないのに、メッセージを受け取れないという事態になります。僕はこれで30分ほど人生の貴重な時間を失いました。気をつけましょう。

では、MD5ダイジェスト値計算を追加していきましょう。まず、MD5()関数が定義されたmd5.hヘッダファイルをインクルードします。先ほどビルドしたOpenSSLがNaCl SDKに配置されているので、下記のようにしてインクルードすることができるようになりました。

```
#include <openssl/md5.h>
```

次に、MD5CalcInstanceクラスのプライベートメンバ関数として、MD5ダイジェスト値計算を行う処理を追加します。

```
private:
  std::unique_ptr<unsigned char[]> GenerateMD5(std::string message) {
    std::unique_ptr<unsigned char[]> result(new unsigned char[16]);
    auto c_message = message.c_str();
    MD5((const unsigned char*)c_message, strlen(c_message), result.get());
    return result;
  }
```

JavaScriptからPepper APIで渡されたC++文字列を引数に受け取り、MD5ダイジェスト値をMD5()関数によって計算し、その結果返しています。C++11で利用可能になったunique_ptrやautoを使っています。MD5ダイジェスト値は必ず16バイトとなるので、上記のコードでも16とハードコードしています。

さらにプライベートな関数として、MD5ダイジェスト値の計算結果を16進数の文字列として変換する関数を追加します。

```
std::unique_ptr<char[]> ToHexString(const unsigned char* source) {
    std::unique_ptr<char[]> result(new char[33]);
    for (int i = 0; i < 16; ++i) {
      sprintf(&result[i * 2], "%02x", (unsigned int)source[i]);
    }
    return result;
  }
```

あとは、この2つのプライベート関数を使って、HandleMessage()関数を以下のように仕上げます。

```
virtual void HandleMessage(const pp::Var& var_message) {
    if (!var_message.is_string()) {
      return;
    }
    std::string message = var_message.AsString();
    auto md5_result = GenerateMD5(message);
    auto md5_string = ToHexString(md5_result.get());
    pp::Var var_reply(md5_string.get());
    PostMessage(var_reply);
  }
```

C++11構文のおかげで、とてもすっきり書けますね。

これでMaClモジュールが完成です。最終的なmd5_calc.ccファイルは以下のようになります。

```
#include "ppapi/cpp/instance.h"
#include "ppapi/cpp/module.h"
#include "ppapi/cpp/var.h"
#include <openssl/md5.h>
class MD5CalcInstance : public pp::Instance {
public:
  explicit MD5CalcInstance(PP_Instance instance) : pp::Instance(instance) {
  }
  virtual ~MD5CalcInstance() {
  }
  virtual void HandleMessage(const pp::Var& var_message) {
    if (!var_message.is_string()) {
      return;
    }
    std::string message = var_message.AsString();
    auto md5_result = GenerateMD5(message);
    auto md5_string = ToHexString(md5_result.get());
    pp::Var var_reply(md5_string.get());
    PostMessage(var_reply);
  }
private:
  std::unique_ptr<unsigned char[]> GenerateMD5(std::string message) {
    std::unique_ptr<unsigned char[]> result(new unsigned char[16]);
    auto c_message = message.c_str();
    MD5((const unsigned char*)c_message, strlen(c_message), result.get());
    return result;
  }
  std::unique_ptr<char[]> ToHexString(const unsigned char* source) {
    std::unique_ptr<char[]> result(new char[33]);
    for (int i = 0; i < 16; ++i) {
      sprintf(&result[i * 2], "%02x", (unsigned int)source[i]);
    }
    return result;
  }
};
class MD5CalcModule : public pp::Module {
public:
  MD5CalcModule() : pp::Module() {
  }
  virtual ~MD5CalcModule() {
  }
  virtual pp::Instance* CreateInstance(PP_Instance instance) {
    return new MD5CalcInstance(instance);
  }
};
namespace pp {
  Module* CreateModule() {
    return new MD5CalcModule();
  }
}
```

# NaClモジュールをビルドするMakefileの作成

先ほど作成したmd5_calc.ccファイルをビルドするためのMakefileファイルを作成します。これは、md5_calc.ccファイルをコンパイル＆リンクし、最終的にPNaClモジュールの実体であるpexeファイルやそれを利用するためのnmfファイルを生成することが目的になります。

このMakefileを全部自分で書くのはかなりしんどいので、NaCl SDKに付いてくるチュートリアルのMakefileをコピーして使いましょう。以下のようにして、Makefileをまずはコピーします。

```
$ cp $NACL_SDK_ROOT/getting_started/part2/Makefile app
```

そして、以下のように変更します。

* VALID_TOOLCHAINSをpnaclのみにする。

```
VALID_TOOLCHAINS := pnacl
```

* TARGETを自分のモジュール名にする。

```
TARGET = md5_calc
```

* LIBSにcryptoを追加する。

```
LIBS = ppapi_cpp ppapi pthread crypto
```

* CFLAGSにC++11有効化オプションを追加する。

```
CFLAGS = -Wall -std=c++11
```

* SOURCESに先ほど作ったccファイル名を指定する。

```
SOURCES = md5_calc.cpp
```

MD5()関数はlibcrypto.aファイル内に定義されているので、LIBSオプションにcryptoを指定しています。また、C++11の構文を使ってccファイルを書いたので、コンパイルできるようにstd=c++11も追加しています。これらの修正が行われた後のMakefileは、以下のようになるでしょう（コメント文は省略しています）。

```
VALID_TOOLCHAINS := pnacl
NACL_SDK_ROOT ?= $(abspath $(CURDIR)/../..)
include $(NACL_SDK_ROOT)/tools/common.mk
TARGET = md5_calc
LIBS = ppapi_cpp ppapi pthread crypto
CFLAGS = -Wall -std=c++11
SOURCES = md5_calc.cc
$(foreach src,$(SOURCES),$(eval $(call COMPILE_RULE,$(src),$(CFLAGS))))
ifneq (,$(or $(findstring pnacl,$(TOOLCHAIN)),$(findstring Release,$(CONFIG))))
$(eval $(call LINK_RULE,$(TARGET)_unstripped,$(SOURCES),$(LIBS),$(DEPS)))
$(eval $(call STRIP_RULE,$(TARGET),$(TARGET)_unstripped))
else
$(eval $(call LINK_RULE,$(TARGET),$(SOURCES),$(LIBS),$(DEPS)))
endif
$(eval $(call NMF_RULE,$(TARGET),))
```

かなり短くまとまっていますが、これはNaCl SDKのcommon.mkファイルが提供してくれる各種マクロによる効果です。

# gruntからmakeコマンドを実行できるようにする

Makefileを作ったので、md5_calc.ccファイルをビルドできるようにはなったのですが、makeコマンドを自分で叩くのは、ここではナンセンスです。なぜなら、Yeomanで生成されたこのChrome appプロジェクトは、gruntによってビルドできるように環境整備されているからです。gruntコマンドを実行した際に、makeも走るようにして、ビルド作業を統合しましょう。

makeコマンドをgruntから起動するために、まずはgrunt-shellをインストールします。

```
$ cd ${APP_ROOT}
$ npm install --save-dev grunt-shell
```

次に、Gruntfile.jsファイルの中で、grunt.initconfig([...})にshellという定義を追加します。そのshell定義の中で、appディレクトリに移動してmakeコマンドを実行する記述を行います。initiconfig()関数に渡されているオブジェクトには数多くのプロパティが定義されていますが、その最後にあるcompress定義の後にでも追加すると良いでしょう。

```
...
  grunt.initconfig({
    ...
    compress: {
      ...
    },
    shell: {
      make: {
        command: [
          'cd app',
          'NACL_ARCH=pnacl make',
          'cd ..'
        ].join(';')
      }
    }
...
```

makeコマンドの実行は、NACL_ARCH=pnaclという指定を伴って行われています。これにより、makeコマンドの実行結果としてPNaClモジュールをビルドするように指示をしています。

このshell定義を実際に実行するmakeタスクも追記しておきます。Gruntfile.jsファイルの最後の"}"行の手前に追記します。

```
grunt.registerTask('make', [
    'shell:make'
  ]);
```

ここで、makeタスクを実行して、md5_calc.ccファイルをコンパイルすることができるか確認しておきましょう。以下を実行します。

```
$ cd ${APP_ROOT}
$ grunt make
```

正常に実行されれば、以下のような出力を見ることができるでしょう。

![スクリーンショット 2014-11-02 19.35.21](https://www.eisbahn.jp/yoichiro/images/2014/11/b57672e80facda1b5b0b40dc59c7d538.png)

${APP_ROOT}/app/pnacl/Releaseディレクトリ内に、ビルド結果のファイルが生成されていることがわかります。その中で実際に必要となるのは、md5_calc.pexeとmd5_calc.nmfファイルの2つのみです。

# md5_calc.cc編集時の自動make実行と、Chrome appビルド時のコピー処理追加

もう少しGruntfile.jsファイルに追記が必要なので、このタイミングで記述をしてしまいましょう。

Yeomanで出力されたGruntfile.jsファイルには、livereloadによるアプリの自動再起動機構が含まれています。これは、開発中に何かソースコードに編集を加えて保存したことを検知して、自動的にChrome appを再起動してくれる機能です。いちいち変更を反映するためにChrome appの再起動を手動でやるのは非常に苦痛なので、一度この環境を体験してしまうと、2度と手放せなくなってしまう機能になることでしょう。

このファイル編集検知に、md5_calc.ccファイルも追加します。md5_calc.ccファイルの変更後はビルドすることも必要になるため、先ほど追加したmakeタスクを自動的に実行することも併せて定義します。grunt.initconfig()関数に渡されたオブジェクトに、watch定義があります。そのwatch定義の先頭に、ccファイルの監視定義を追記します。

```
...
    watch: {
      cc: {
        files: ['<%= config.app %>/*.cc'],
        tasks: ['make'],
        options: {
          livereload: true
        }
      },
      bower: {
...
```

この追記を行った後に、grunt debugと実行しておき、Chrome appを起動した後にmd5_calc.ccファイルを何か更新してみてください。自動的にmakeが実行され、Chrome appも再起動するのがわかると思います。

${APP_ROOT}/appディレクトリ内で開発中は動作確認を行うことになりますが、最終的にはgruntでビルド作業を行い、Chrome WebStoreにそのままアップロード可能なzipファイルを作成します。その際、一旦distディレクトリに必要なファイルがコピーされ、それがzipファイルにまとめられます。

現状のままだと、PNaClモジュール用のファイルがコピー対象になっていないので、pexeファイルやnmfファイルが対象となるようにGruntfile.jsファイルに追記します。grunt.initconfig()関数に渡されているオブジェクト内のcopy定義に、以下の追記をします。

```
...
    copy: {
      dist: {
        files: [{
          ...
          src: [
            ...
            'pnacl/Release/*.{pexe,nmf}'
          ]
...
```

これで、grunt build（単にgruntを実行したときも一緒）を実行したときに、PNaClモジュールも一緒に入るようになります。ただし、grunt buildした際にmakeタスクがこのままだと実行されないので、buildタスクの登録処理の中にmakeタスクを追加しておきましょう。

```
...
  grunt.registerTask('build', [
    'clean:dist',
    'make',
    'chromeManifest:dist',
...
```

# HTMLへのNaClモジュールの埋め込み

NaClモジュールの準備が整いましたので、HTML側に戻りましょう。NaClモジュールを利用するには、HTML内にembed要素を使って埋め込みを行う必要があります。${APP_ROOT}/app/index.htmlファイル内のh1要素の直下に、以下のコードを追記します。

```
<div id="listener">
  <embed id="md5_calc"
               width=0 height=0
               src="pnacl/Release/md5_calc.nmf"
               type="application/x-pnacl" />
</div>
```

div要素で囲っている理由は、embedで埋め込んだNaClモジュールのイベント処理は、その親要素を経由して行うことになります。つまり、イベントハンドラの登録は、embed要素に直接ではなく、その親要素に対して行うことになります。そのため、div要素で囲っているということになります。

今回のNaClモジュールは、単にMD5ダイジェスト値計算のみ行うものであり、見た目に関するものではありません。そのため、width属性とheight属性に0を指定して非表示状態としています。src属性には、先ほどmakeして生成されたnmfファイルを指定します。type属性に"application/x-pnacl"を指定することで、src属性値のnmfファイルが読み込まれ、そしてNaClモジュールが活性化します。

# JavaScriptからNaClモジュールへのメッセージ送信

最初に配置した"Calculate MD5"ボタンのクリックイベントハンドラを変更して、入力された文字列をNaClモジュールに送信する処理を書きます。NaClモジュールへのメッセージ送信は、postMessage()関数を利用します。${APP_ROOT}/app/scripts/index.jsファイルの中にあるbtnClicked()関数を以下のように書き換えてください。

```
MD5Calc.prototype.btnClicked = function(evt) {
  var md5CalcModule = document.querySelector('#md5_calc');
  var source = document.querySelector('#source');
  md5CalcModule.postMessage(source.value);
};
```

NaClモジュールは、embed要素をquerySelector()関数にid値を指定して取得できます。それに対して、postMessage()関数を呼び出すことで、NaClモジュールにメッセージを送信することが可能です。その結果、md5_calc.ccファイルで定義したMD5CalcInstanceクラスのHandleMessage()関数が呼び出されます。

# NaClモジュールから送信されたメッセージの受け取りと画面表示の記述

コーディングの最後は、NaClモジュールから送信されたメッセージをJavaScript側で受け取り、その結果を表示する処理です。NaClモジュールからPostMessage()関数でメッセージが送信されると、embed要素の親要素に対して、messageイベントが発火します。このmessageイベントを受け取るイベントリスナの登録と、受け取ったメッセージをそのまま画面表示する処理は、以下のようになります。${APP_ROOT}/app/scripts/index.jsファイルのassignEventHandlers()関数の中にイベントハンドラ登録処理を追加し、handleMessage()関数を新規に追加して、その中で画面表示処理を行いましょう。

```
...
  MD5Calc.prototype.assignEventListener = function() {
    ...
    var listener = document.querySelector('#listener');
    listener.addEventListener("message", function(evt) {
      this.handleMessage(evt);
    }.bind(this), true);
  };
...
  MD5Calc.prototype.handleMessage = function(evt) {
    var result = document.querySelector('#result');
    result.textContent = evt.data;
  };
...
```

# 動作確認する

これで全てのコーディングが完了しました。実際に動作させてみましょう。

まず、デバッグ用にgruntコマンドを以下のようにして起動します。

```
$ cd ${APP_ROOT}
$ grunt debug
```

その後、chrome:extensionsページ、もしくはアプリランチャーからsimple_md5_calcアプリケーションを起動します。

![スクリーンショット 2014-11-03 19.48.37](https://www.eisbahn.jp/yoichiro/images/2014/11/6df5443816d11704ebbe7eb79a638732.png)

そして、テキストフィールドに、例えば"Hello"と入力します。

![スクリーンショット 2014-11-03 19.49.59](https://www.eisbahn.jp/yoichiro/images/2014/11/6e8af83a879c5baa2506f202904140f1.png)

そして、"Calculate MD5"ボタンを押してみてください。以下のように"Hello"のMD5値が表示されれば成功です。

![スクリーンショット 2014-11-03 19.51.11](https://www.eisbahn.jp/yoichiro/images/2014/11/5d4f1dada4bdfd1682825656d60b56cb.png)

計算結果が本当に正しいかどうか不安ですよね？md5コマンドをシェルで実行してみましょう。

![スクリーンショット 2014-11-03 19.52.34](https://www.eisbahn.jp/yoichiro/images/2014/11/0020817c9d2ae8a04c777b69079abad3.png)

大丈夫そうですね。

もし計算結果が表示されないなど不具合があれば、md5_calc.ccファイルやindex.jsファイルなどの内容を確認してください。それらのファイルを変更するだけで、アプリケーションが再起動されて変更が反映されていることも確認できると思います。

# Chrome WebStoreにアップロードするzipファイルの作成

Chrome appsの開発は、zipファイルを作ってChrome WebStoreにアップロードすることで完了します。既にzipファイルを作成することができるだけのGruntfile.jsファイルの修正は終わっています。実際にzipファイルを作成してみましょう。以下のコマンドを実行してください。

```
$ cd ${APP_ROOT}
$ grunt
```

正常にビルドが完了すると、${APP_ROOT}/paclageディレクトリの中にzipファイルが生成されています。これをChome WebStoreにアップロードすることで、Chromeブラウザを使っているユーザがあなたのアプリケーションを入手することができるようになります。

ファイルの編集の仕方によっては、gruntによるビルド作業中にエラーが表示されることもあるでしょう。動作上は問題なくても、jshintによる文法チェックなども実行されるので、表示される内容に合わせて変更を行っていってください。

# まとめ

このエントリでは、OpenSSLを利用したPNaClモジュールを作成し、それをChrome appから利用する例を紹介しました。環境構築が手順の中に含まれていたため、とても作業量が多く感じたと思いますが、一旦環境ができてしまえば、ソースコード自体は単純なものだと思います。また、単にPNaClモジュールの作成だけでなく、OpenSSLなどの既存ライブラリの利用方法を知ることで、開発可能性がより大きく高まると考えています。

ぜひこのエントリの内容を参考にして、NaClモジュールを作ってみてください。
