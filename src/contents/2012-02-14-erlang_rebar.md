---
layout: post
status: publish
published: true
title: rebar、それはErlangのビルドツール
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1316
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1316
date: '2012-02-14 22:53:59 +0900'
date_gmt: '2012-02-14 13:53:59 +0900'
categories:
- Erlang
---

Erlangのコンパイラと言えばerlcですが、Erlang専用のビルドツールが存在します。それがrebarです。

まず、入手から。githubから落としてきます。

>git clone git://github.com/basho/rebar.git


そして、rebarをビルドします。もちろんErlang OTPはインストール済みな状態からやりましょう。

>cd rebar
./bootstrap


これでrebarコマンドができあがります。さっそく実行してみましょう。rebarがサポートするコマンド一覧を出力してみます。

>./rebar -c


いろいろ出てくれば成功です。

新規にプロジェクトを始めるときには、以下のようにして自動生成させます。

>mkdir myproject
cd myproject
cp ../rebar/rebar .
./rebar create-app appid=myproject


プロジェクトをコンパイルするには、以下のようにします。

>./rebar compile


すると、ebinディレクトリができて、その中にbeamファイルが生成されます。

さて、既に作られているErlangコードがあれば、それをrebar対応したくなります。以下の手順で移行することができます。

* rebarファイルをプロジェクトディレクトリのトップに置く。

* srcディレクトリを作成し、erlファイルを全てsrcディレクトリ内に移動する。

* アプリケーションソースファイル(app.srcファイル)をsrcファイル内に作成する。

このアプリケーションソースファイルは、いわばプロジェクトファイルになります。そのファイル名は、[プロジェクト名].app.srcとなります。myprojectというプロジェクト名としたければ、myproject.app.srcファイルとします。その内容は、以下のような感じです。

```
{application, myproject,
 [
  {description, ""},
  {vsn, "1"},
  {registered, []},
  {applications, [
                  kernel,
                  stdlib
                 ]},
  {mod, { myproject_main, []}},
  {env, []}
 ]}.
```

myproject_mainは、プロジェクトで中心となるモジュールの名前を書いておけば良さそうです。正確にはまだよくわからないけど。。。

ここまで来れば、rebar compileでコンパイルできるはずです。いくつかerlファイルがあれば、それらがebinディレクトリにbeamファイルとしてコンパイルされていることがわかると思います。
