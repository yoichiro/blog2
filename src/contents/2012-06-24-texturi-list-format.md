---
layout: post
status: publish
published: true
title: text/uri-listリソースの書式
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1497
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1497
date: '2012-06-24 23:26:53 +0900'
date_gmt: '2012-06-24 14:26:53 +0900'
categories:
- Social web
---

普段よく使っているMIME Typeとして、例えばtext/plain、text/html、application/json、image/jpegといったものは見たことがあると思います。HTTPで情報のやり取りをする際にはこういったものが良く使われると思いますが、Web Intentsとなるともう少し細かく、そしてバリエーションが効いたMIME Typeが使われるようになると予想されます。

例えば、
[webintents.org](http://webintents.org/)のページでは、text/uri-listという見慣れないMIME Typeが良く出てきます。Drag&Dropを実装したことのある人は、もしかしたら知っているかもしれません。このtext/uri-listの仕様は、
[RFC 2483 - URI Resolution Services Necessary for URN Resolution](http://tools.ietf.org/html/rfc2483)のセクション5にて説明されています。text/uri-listは、その名の通り、複数のURIのリストを定義したものです。

書式のポイントは3点あります。

* '#'文字で始まる行はコメント行であり、処理中に無視されます。URIは'#'文字を含むこともありますが、行の最初に'#'文字が来た時だけコメント文字として扱われます。

* 上記以外のコメントではない行は、URLまたはURN仕様(
[RFC2141](http://tools.ietf.org/html/rfc2141), 
[RFC1738](http://tools.ietf.org/html/rfc1738), 
[RFC2396](http://tools.ietf.org/html/rfc2396))に従ってエンコードされたURIであるべきです。各URIは、1行に1つ記述されます。とても長いURIであっても、text/uri-list書式内では壊れることはありません。Content-transfer-encodingは、行の長さ制限を実施するために使われるかもしれません。

* 全てのtext/*書式では、行はCRLFの組が終端となります。

簡単に言うならば「URIを改行区切りで複数指定可能」ということです。例としては以下のようになるでしょう。

---
# urn:isbn:0-201-08372-8
http://www.huh.org/books/foo.html
http://www.huh.org/books/foo.pdf
ftp://ftp.foo.org/books/foo.txt
---

Web Intentsなどに代表される細かな情報のやり取りについては、今後こういった書式が頻繁に使われるようになるのではないか、と思います。
