---
layout: post
status: publish
published: true
title: ErlangでQRコードを生成する方法
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1373
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1373
date: '2012-03-14 09:22:01 +0900'
date_gmt: '2012-03-14 00:22:01 +0900'
categories:
- Erlang
---

ErlangでQRコードの画像を生成するのは、とても簡単です。rebarでプロジェクト管理をしていれば、なおさらです。

まず、rebar.configファイルに以下のように記述します。

[erlang]
{deps, [
    {qrcode, ".*",
       {git, "https://github.com/coolchevy/qrcode.git",
              "master"}},
    ...
[/erlang]

これでqrcodeモジュールが使えるようになります。makeを走らせておくと、githubから自動的にqrcodeモジュール一式がdepsディレクトリ下に配置されます。

次に、QRコードの生成処理を呼び出したい自分のモジュール内で、qrcode.hrlをインクルードします。

[erlang]
-include_lib("deps/qrcode/include/qrcode.hrl").
[/erlang]

例えば何らかのURL文字列をQRコードにしたければ、まずURL文字列をバイナリにして、それをqrcodeモジュールのencode/1関数に渡します。

[erlang]
Url = "http://www.eisbahn.jp/yoichiro/",
QRCode = qrcode:encode(list_to_binary(Url)),
[/erlang]

これでqrcodeレコードのdataにQRコードの情報がセットされて返されるのですが、これを何らかの画像形式に変換しないといけません。今回使ったqrcodeプロジェクトの中には、PNG形式に変換するサンプルコードも含まれていました。これを基本的にそのまま使ってあげれば、めでたくWebブラウザで表示可能なQRコードのバイナリを得ることができます。

[https://github.com/coolchevy/qrcode/blob/master/src/qrcode_demo.erl#L65](https://github.com/coolchevy/qrcode/blob/master/src/qrcode_demo.erl#L65)

これをfile:write関数でファイルに出力するもよし、MochiWebを使っているならmochiweb_request:respond関数でWebブラウザに返却するもよし、です。ちなみに、上記で作られる画像は256x256というちょっと大きめのサイズなので、表示する際に小さくするとか、そもそも生成時に小さくするとか、工夫すると良いでしょう。
