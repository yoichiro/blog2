---
layout: post
status: publish
published: true
title: mountain lionにNode.jsをインストール
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1951
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1951
date: '2012-11-22 09:38:58 +0900'
date_gmt: '2012-11-22 00:38:58 +0900'
categories:
- Node.js
---

ちょっと理由があって自分のmac(mountain lion)にNode.jsをインストールしました。その手順をここに残しておきます。

# 入手

以下のサイトにWebブラウザでまずは行きます。

[http://nodejs.org/](http://nodejs.org/)

ここの[INSTALL]って緑のところをクリックすると、macでNode.jsをインストールするためのパッケージのダウンロードが自動的に始まります。現時点では「node-v0.8.14.pkg」がダウンロードされました。

[![](http://www.eisbahn.jp/yoichiro/images/2012/11/node1.png)](http://nodejs.org/)

# インストール

落としてきたパッケージファイルをダブルクリックして、インストールを行います。次へ次へしてれば、インストールは終わります。なんて簡単なんでしょう。

![](http://www.eisbahn.jp/yoichiro/images/2012/11/node2.png)

![](http://www.eisbahn.jp/yoichiro/images/2012/11/node3.png)

![](http://www.eisbahn.jp/yoichiro/images/2012/11/node4.png)

![](http://www.eisbahn.jp/yoichiro/images/2012/11/node5.png)

![](http://www.eisbahn.jp/yoichiro/images/2012/11/node6.png)

/usr/local/bin下にnodeとnpmの実行ファイル(npmはシンボリックリンク)が配置されるので、/usr/local/binにPATHを通すように促されています。まぁ、普通はすでに通していることでしょう。

# 動作確認

さっそく動作確認してみたくなりますよね。Terminalを開いて、おもむろに「node RET」と叩いてみます。

```
$ node
>
```

良い子はいきなりこんなことしてはいけません。きっとこのあと途方に暮れます。僕は暮れました。ググラビリティを試されます。終了の仕方がわからないのです。quit、exit、Ctrl+C、q().、quit()、exit()、全てはね飛ばされます。

正解は、以下です。絶対知らないとわからないです。

```
> process.exit();
$
```

良い子は、node -vとしてバージョン番号を表示してみましょう。以下のように出れば、インストールは成功してます。

```
$ node -v
v0.8.14
```

ようこそ、Node.jsの世界へ！
