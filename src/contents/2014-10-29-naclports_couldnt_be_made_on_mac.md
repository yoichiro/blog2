---
layout: post
status: publish
published: true
title: naclportsがmacでmakeできなかった話
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 3039
wordpress_url: https://www.eisbahn.jp/yoichiro/?p=3039
date: '2014-10-29 19:32:24 +0900'
date_gmt: '2014-10-29 10:32:24 +0900'
categories:
- Chrome Native Client
---

NaClやPNaClをいろいろ試していく中で、自分で書いたコードだけでなく、他のコードを使いたいときが出てきます。例えば、opensslやその他便利なライブラリが、それに該当するでしょう。これらのライブラリは、NaCl SDKに付属するビルドツールでmakeされて初めて、自分のNaClコードから利用可能になります（と思ってます）。

NaCl向けに各種Patchがあてられた状態になっているライブラリ集が、naclportsです。この中に含まれる使いたいライブラリをビルドすることで、自分のNaClモジュールに取り込むことができるようになる、はずです。

[naclports - Ports of open-source projects to Native Client](https://code.google.com/p/naclports/)

naclportsを落としてきて、あとは使いたいものをビルドしていけばいいって感じになってます。ざっくり手順を紹介すると、以下のような感じです。

* [depot_tools](http://dev.chromium.org/developers/how-tos/install-depot-tools)をインストールする。

* naclportsを入れるディレクトリを作成する。
  
```
$ mkdir naclports
  $ cd naclports
```

* gclientコマンドを使って、naclportsの設定ファイルを作成する。
  
```
$ gclient config --name=src  https://chromium.googlesource.com/external/naclports.git
```

* 実際のnaclportsファイル群をダウンロードする。
  
```
$ gclient sync
```

そんなに難しい手順ではないですね。その後、srcディレクトリに入っておきます。あ、環境変数として、以下を設定するのを忘れずに。

```
export NACL_SDK_ROOT=
<pepper_[ver]ディレクトリへのPath>
```

これで準備完了です。あとは、make_all.shスクリプトファイルを使って、ビルドするだけです！試しに、glibc-compatをビルドしてみます。

```
$ ./make_all.sh glibc-compat
+ set -e
+ TARGETS=glibc-compat
+ TARGETS=glibc-compat
+ BUILD_FLAGS=--ignore-disabled
+ export TOOLCHAIN
+ export NACL_ARCH
+ export BUILD_FLAGS
+ NACL_ARCH=x86_64
+ TOOLCHAIN=glibc
+ make glibc-compat
bin/naclports install glibc-compat --ignore-disabled
Installing 'glibc-compat' [x86_64/glibc/release]
naclports: invalid file in package: ._pkg_info
make: *** [glibc-compat] Error 1
```

本当に世の中うまくいかないもんですねー。makeでこけてしまいました。。。

出力をよく見てみると、"naclports: invalid file in package: ._pkg_info"と言ってます。"._pkg_info"というファイルが無効だそうです。わけわかりません。実際そのファイルを検索してみても存在しません。ググっても、それらしい症例がヒットしません。困りました。

macだからいけないのかもしれない。Linuxでやればうまくいったのかも知れません。ただ、これだけのためにLinuxインストールするのは手間ですし、身近にあったLinuxで試そうと思ったらpythonのバージョンが低くて対応できませんでした。むー、本当に困りました。ソースコードをいろいろgrepするも、よーわかりません。

困ったときは、Bug報告あるのみです。naclportsの本家にIssue登録してみました。

[Issue 158: All ports build failed on OSX with "invalid file in package: ._pkg_info"](https://code.google.com/p/naclports/issues/detail?id=158)

返事来るかなー、来なかったらしばらくNaClは忘れようかな、と思ってましたが、意外なことに(失礼)すぐに返事をもらうことができました。いくつかやり取りしましたが、この問題を回避するコードを追加してもらえました。

[Issue 687823002: Don't package OSX's magic ._foo files (Closed)](https://codereview.chromium.org/687823002/)

その後、./make_all.sh clean を実行してglibc-compatを再ビルドしてみましたが、同じエラーが出ました。うーん、cleanできれいになってないのかな、と思って、naclportsを落としてくる最初のところからやってみたら、今度はビルドに成功しました！

・・・つまり、naclportsをmacでmakeした人って、今までいなかったってことですか？？？

既にgclientで落としてくるコードの中に今回の差分は入ってますので、今後macでnaclportsを使いたい人は、無事ビルドできると思います。

良かった良かった、って話でした。
