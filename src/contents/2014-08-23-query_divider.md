---
layout: post
status: publish
published: true
title: "複数のSQL文を含む文字列を区切り文字列判断して分割するパーサーを書いてみた話"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2891
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2891
date: '2014-08-23 22:54:33 +0900'
date_gmt: '2014-08-23 13:54:33 +0900'
categories:
- Chrome Apps
---

MySQLに標準で付属してくるコマンドライン版のClientであるmysqlコマンドには、例えばCREATE PROCEDUREをする際に一つのSQL文の中で複数のSQL文を含めるために、通常文末を示すセミコロン記号を文末と評価しないこと、つまり文末記号を別の記号に置き換えることができるようになってます。delimiterっていう命令がそれで、これはSQLの予約語ではなく、mysqlコマンドが提供してるものです。MySQL  Serverに"delimiter"って送ってもエラーになります。

[sql]
delimiter //
select a from b// select c from d//
delimiter ;
[/sql]

こんな風に書けます。実際にmysqlコマンドがMySQL Serverに投げるのは、"select a from b"と"select c from d"の2つの文です。mysqlコマンドは、事前に入力された文字列をパースして、実行すべき文を取り出してるって動作になってます。

このdelimiter命令は、mysqlコマンドだけでなく、phpMyAdminやSequel Proでも使えるらしい、ということを知りました。しかし、現状の
[ChromeMyAdmin](https://chrome.google.com/webstore/detail/chromemyadmin/ndgnpnpakfcdjmpgmcaknimfgcldechn)ではもちろんサポートしていません。なぜなら、僕がdelimiter命令を知らなかったから、です。

そこで、ChromeMyAdminでもdelimiter命令をサポートしたいと思い、文の区切りを検出するコードを書くことにしました。ポイントは以下です。

* 基本的にはdelimiter命令を探して、そこで宣言された区切り文字列を使って、文字列全体を分割していけば良い。

* しかし、delimiter命令は1つの文字列で複数回出現する可能性がある。

* さらに、"delimiter"はSQLの予約語ではないため、普通にSQL文中に列名とかで出現するかもしれないし、コメント文の中にも書かれるかもしれない。

* そんなことを気にし出すと、ある程度ちゃんとパースしないといけなさそう。

というわけで、パーサーを書きたいわけですが、それにもいくつかの方法が考えられます。

* ステートチャートに基づく処理を全て自前で書く。

* BNF使って定義し、何らかのパーサジェネレータ使ってパーサーを自動作成する。

* まずコメント文の位置を正規表現で頑張って求め、コメント文以外の文字列内でdelimiter命令とそれに続く区切り文字列を正規表現で頑張って求め、あとはコメント分以外の箇所で出現する区切り文字列で全体の文字列を分割していく。

もちろんプログラマたるもの、パーサーはサクッと自分の手で書けるようになりたいものです。世の中便利なもので、たぶん2番目や3番目の方法を使えば短時間で目的のことはできるんでしょうけど、全部自前でパーサーを書いてみることにしました。

出来上がったコードは、以下です。

QueryDivider - A parser to divide SQL queries with a delimiter string which is decided by the "delimiter" command.

[https://gist.github.com/yoichiro/a192cf77abac52cf519a](https://gist.github.com/yoichiro/a192cf77abac52cf519a)

全体の文字列を1文字ずつ評価していき、現在どの状態なのかを判断して遷移させてます。状態数は結局ちょっと多くなりました。

```
query: this.query.bind(this),
lineStart: this.lineStart.bind(this),
escapedQuery: this.escapedQuery.bind(this),
sharpComment: this.sharpComment.bind(this),
maybeDashComment: this.maybeDashComment.bind(this),
dashComment: this.dashComment.bind(this),
maybeInlineCommentStart: this.maybeInlineCommentStart.bind(this),
inlineComment: this.inlineComment.bind(this),
maybeInlineCommentEnd: this.maybeInlineCommentEnd.bind(this),
maybeDelimiterDef: this.maybeDelimiterDef.bind(this),
delimiterDef: this.delimiterDef.bind(this),
delimiterDefEnd: this.delimiterDefEnd.bind(this),
maybeDelimiter: this.maybeDelimiter.bind(this)
```

先読みはせず、基本的に1文字ずつ評価しています。そのため、例えば"-- "という3文字のマーカーで始まりが決定するコメントなどは、「ひょっとしたらコメント文かも」という未確定状態（maybeDashComment）を作ってます。たぶん先読みをして、確定したらその文字数分だけposを増やしてしまえば、このような未確定状態は全て消せるかもしれません。

また、ちょっと面白かった点としては、例えば以下のような文字列が入力されたとします。

[sql]
delimiter /**
select /* a */ b from c/**
delimiter ;
[/sql]

2行目のインラインコメントの始まりを示すスラッシュ記号が来た時点では、1行目で指定された区切り文字列なのか、インラインコメントなのか、判断がつきません。僕が書いた上記のパーサーでは、まず区切り文字列である可能性があることを優先して、状態をmaybeDelimiterDefに遷移させています。その後、アスタリスクが2つ続かなかったと判断され、区切り文字列でないことが決定します。ここで、区切り文字列ではないと判断された/*という2文字を、SQL文に含まれる文字列としてbuffer配列に追加してしまうのは簡単です。しかし、この場合はインラインコメントである可能性も残されているわけです。

つまり、基本的に1文字ずつ評価をしていけばいいのですが、上記の場合は「評価した文字列を別のルールで再評価する」ということが必要になります。ここで注意点としては、状態をqueryにして評価位置を戻して再評価するのですが、単にそのままだと「戻る→再評価開始→区切り文字かも→違った→戻る→・・・」を延々と繰り返してしまいます。そこで、戻る際に「次のquery状態での評価時は、区切り文字列でないことはすでにわかっているので、区切り文字列評価をしない」という指定ができるようにしています。これによって、永久ループすることなく、インラインコメントであるかどうかの評価がされることになります。

delimiter命令を大文字小文字区別なく評価できるようにまだしていませんが、とりあえず正しく動作するように書けた模様です。

たまにはパーサーを自力で書くと、普段使わない頭を使っている気がして、ボケ防止には良さそうですね。
