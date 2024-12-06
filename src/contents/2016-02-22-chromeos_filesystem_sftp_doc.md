---
layout: post
title: ChromeOS向けSFTP File Systemの構造
categories:
- Chrome Apps
---
もう1年前の話になりますが、ChromebookやChromboxからSFTP経由でファイルを直接やり取りするためのアプリ「[SFTP File System](https://chrome.google.com/webstore/detail/sftp-file-system/gbheifiifcfekkamhepkeogobihicgmn)」を開発、公開して、現在に至っています。ChromeOSはWindowsのエクスプローラやmacのfinderのようなファイラである「Files.app」を持っています。そして、Chrome AppsやChromeウェブブラウザ上のウェブアプリからは、[HTML5 FileSystem API](http://www.html5rocks.com/ja/tutorials/file/filesystem/)や[chrome.fileSystem API](https://developer.chrome.com/apps/fileSystem)を経由して、ディレクトリやファイルにアクセスすることが可能になっています。

![sftp_1.png]({{ "/images/2016/02/sftp_1.png" | prepend: site.baseurl }})

SFTP File Systemアプリは、[chrome.fileSystemProvider API](https://developer.chrome.com/apps/fileSystemProvider)の実装を提供し、HTML5 File System APIやchrome.fileSystem APIに接続されます。つまり、Files.appアプリや、数多くのChrome Appsから、SFTPプロトコルを使ったリモートファイルアクセスが可能になるのです。

このSFTP File Systemアプリの構造を、頑張ってドキュメントに書き起こしてみました。

[Code Structure - SFTP File System](https://github.com/yoichiro/chromeos-filesystem-sftp/blob/master/docs/code_structure.md) - GitHub

SFTP、つまりSSH2プロトコルを最初自分で実装しようと思ったのですが、さすがにそれは辛く（JavaScriptで素数計算するのがきつかった）、libssh2というC言語で書かれたライブラリを使うことにしました。SFTP File SystemはChrome Appsであり、その中ではCやC++で書かれたコード（からビルドされたネイティブコード）を[Native Client Library](https://developer.chrome.com/native-client)というネイティブモジュールを使うことができます。JavaScriptレイヤーとNaClモジュールとの通信は、Pepper APIで可能になります。

![code_structure_1.png](https://raw.githubusercontent.com/yoichiro/chromeos-filesystem-sftp/master/docs/code_structure_1.png)

NaClモジュールの中では、libssh2を使ってSFTP Serverとの通信処理を行うのですが、通信に使われるソケットは自分で開く必要があります。NaClでは、nacl_ioというPOSIX準拠のSocket APIの実装が提供されているので、普通にsocket(2)やselect(2)などが利用可能です。Pepper APIで呼び出されたメインスレッドではnacl_ioで通信できない、などの制約があるのですが、それもpthreadを使って回避しています。

![code_structure_1.png](https://raw.githubusercontent.com/yoichiro/chromeos-filesystem-sftp/master/docs/code_structure_2.png)

そのほかにも、各レイヤー、各クラスがどのような責務を担ってて、それらがどう連携して動作しているかわかるようにしてみたつもりです。ぜひ一度ざっと眺めてみて「あー、Chrome AppsってC++と組み合わせてこんなことできるんだー、へー」って思ってみてください。
