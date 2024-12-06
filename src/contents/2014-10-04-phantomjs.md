---
layout: post
status: publish
published: true
title: phantomjsを試してみた
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2910
wordpress_url: https://www.eisbahn.jp/yoichiro/?p=2910
date: '2014-10-04 20:12:51 +0900'
date_gmt: '2014-10-04 11:12:51 +0900'
categories:
- Web Technologies
---

GithubでJavaScriptのライブラリを探しているときに、最近「phantomjs」というものを使っているプロジェクトに出くわします。特に、そのJavaScriptライブラリのテストコードを動かすために採用されているっぽいです。例えばそのJavaScriptライブラリに対して何か手を加えてPull requestを出したい場合に、テストコードにも手を入れる必要があり、そのためにはphantomjsの環境も作っておかなければなりません。

そこで、自分のmacにphantomjsを入れて動作確認をしてみました。

まず、HomeBrewを使ってインストールしてみました。結果は「失敗」でした。

```
brew install phantomjs
```

インストール自体は成功したような感じなのですが、例えばバージョン情報を表示してみると、それだけでエラーが表示されます。

```
$ phantomjs --version
dyld: Library not loaded: @@HOMEBREW_PREFIX@@/opt/openssl/lib/libssl.1.0.0.dylib
  Referenced from: /usr/local/bin/phantomjs
  Reason: image not found
[1]    6303 trace trap  phantomjs --version
```

は？って感じですね。sslの動的ライブラリのロードに失敗したようです。このエラーを解決する元気は、僕にはありません。消します。

```
brew remove phantomjs
```

次に、phantomjsのサイトから配布されているアーカイブをダウンロードして、そっちを使ってみます。

[http://phantomjs.org/download.html](http://phantomjs.org/download.html)

今回使ったのは、phantomjs-1.9.7-macosx.zipというファイルです。このファイルを任意の場所に展開します。ここでは、/Applications/phantomjs というディレクトリ下に展開しました。そして、zshenvファイルに以下のように設定します。

```
export PHANTOMJS_HOME=/Applications/phantomjs/phantomjs-1.9.7-macosx
export PATH=$PHANTOMJS_HOME/bin:...
```

rehashした後に、さっきのようにバージョン番号を出してみます。

```
$ phantomjs --version
1.9.7
```

成功です！ちゃんとバージョン番号が表示されました。

では、続けて動作確認をしてみます。セオリー通り、console.log()での出力テストと、Webページのキャプチャ画像を取ってみます。

```
console.log("Hello, PhantomJS!");
var page = require("webpage").create();
var url = "https://www.eisbahn.jp/yoichiro/";
page.open(url, function(status) {
    page.render("blog.png");
    phantom.exit();
});
```

これをtest.jsファイルとして保存し、phantomjsコマンドに渡して実行してみます。

```
phantomjs ./test.js
```

上記を実行したターミナルには「Hello, PhantomJS!」と表示され、ちょっと時間をおいた後に終了します。同じディレクトリにblog.pngファイルが保存されているのがわかると思います。これを開くと、以下のような画像でした。ブラウザで表示したときのページ全体がそのまま画像で保存されていることがわかります。

[![blog](https://www.eisbahn.jp/yoichiro/images/2014/10/blog-72x300.png)](https://www.eisbahn.jp/yoichiro/images/2014/10/blog.png)

phantomjsがうまく動作することを確認できました。
