---
layout: post
status: publish
published: true
title: Cocoa Emacsのビルド
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1179
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1179
date: '2011-11-28 09:16:07 +0900'
date_gmt: '2011-11-28 00:16:07 +0900'
categories:
- My PC environment
---

ずっとCarbon Emacsを使ってきたんだけど、22がベースだし、そろそろ新しいものに移行しようとCocoa Emacsのビルドにチャレンジ。結論から言うと「成功」。でも、23.3bはどうしてもビルドできなかった。。。

チャレンジ前にいろいろネットで情報をあさっていると、とりあえず普通にソースを落としてきてmakeしても、lionだとビルド失敗しちゃうみたい。そのビルドを通すためには、いくつかパッチを当てる必要があるっぽい。「
[Emacs23をMac OS X Lionでビルドする](http://henry.animeo.jp/wp/?p=1510)」とか見ながらあれこれやってみたものの、やっぱりNG。

[新しいFormula](https://raw.github.com/gist/1397354/36c7a3ab0ec8255e361dae11e0803cdf5cd09157/emacs.rb)を作って以下のコマンドを試すも、状況は一緒。

```
brew install https://raw.github.com/gist/1397354/36c7a3ab0ec8255e361dae11e0803cdf5cd09157/emacs.rb --cocoa --lion
```

MacPortsからインストールをやってみたが、これも失敗。どうしろと？

結局、最新のコードに期待するしかないな、と思い、チャレンジ。結果は大成功。

```
brew install https://raw.github.com/mxcl/homebrew/8e6afb58cd9ac48e9f1074243e4f07f31a663400/Library/Formula/autoconf.rb
brew install https://raw.github.com/dch/homebrew/64aa34a6cd2ec0d1590c716c17db269c115d09c9/Library/Formula/automake.rb
git clone git://repo.or.cz/emacs.git
cd emacs
./autogen.sh
./configure --with-ns --without-x
make
make install
```

./nextstepにあるEmacs.appをダブルクリック！わーい！Emacs24だー。

![](http://www.eisbahn.jp/yoichiro/images/2011/11/emacs24.png)

新しいものは、ワクワクしますね！さっそく.emacsをいろいろいじらなくちゃ。

(追記 2011/12/01) Xcode3だったのがビルド失敗の原因だったみたい。Xcode4にしたら、無事ビルドできました。最初のbrewを使って、いとも簡単にEmacs.appをゲットできました。
