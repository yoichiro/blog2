---
layout: post
status: publish
published: true
title: "丸レクでREST on Railsについて話をしてきました"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 490
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=490
date: '2007-12-18 23:26:32 +0900'
date_gmt: '2007-12-18 14:26:32 +0900'
categories:
- Ruby on Rails
---

本日、「
[丸山先生レクチャーシリーズ RESTfulサービス技術の台頭](http://www.c-sq.com/modules/article/article131.html)」で話をしてきた。僕の題目はというと、「Ruby on Rails2.0におけるREST対応」。使用したスライドは下のものである。

[![SlideShare](http://static.slideshare.net/swf/logo_embd.png)](http://www.slideshare.net/?src=embed) | 
[View](http://www.slideshare.net/yoichiro/20072008-206661) | 
[Upload your own](http://www.slideshare.net/upload)
PDFは
[こちら](http://www.c-sq.com/modules/article/article131.html)
個人的には、やっぱり練習をしていくべきだったかなぁ、と、話し方に関して反省である。何度もRESTとRailsを言い間違えたし、日本語として正しくないよなぁ、と思ってしまうほど文法上酷い言い方をしてしまった部分もあった。しかも、かなりの早口になっていたはずで、45枚あるスライドで、しかも途中でデモもやっているのに、時間が余るって何だよ、ってことになってしまった。まだまだスキルが僕は足りないな、と痛感。
しかし、会場に来ていただいた方々からのアンケート結果は、意外にも高評価。「わかりやすかった」「RailsでのREST対応があんなに簡単だとは思ってなかった」という嬉しい意見を頂くことができた。「聞き手側にわかりやすく説明していた」というのが、昨日の僕にとって最高の褒め言葉だった。とりあえず、役目は果たせたのかな、と。
講演が終わって、僕が「あっさりとActionWebServiceをRails2.0になって捨てました」と言ってしまったのが気になったのか、「Railsは3.0になってもそういう姿勢なのか？」という質問を頂いた。正直に言うと、僕はRails3.0なんてどうなってるかわからないけど、今後も時代の流れに従って、標準として搭載するものははっきりとした方向性を出していくのではないか、と答えた。そして、もちろんActionWebServiceがなくなってしまったわけではなく、別途インストールは可能である、とも付け加えた。
丸レク終了後、個別に質問を頂いた。ここに情報共有してみる。
---
Q. 〜.xmlとか〜.atomとするのがRailsなのか？これはRESTに関して一般的なのか？
標準規約がそうなだけで、これを無視することもできるし、routes.rb次第で如何様にもなる。現に、僕は〜.xmlが(理由を聞かれると困るけど)好きではないので、単に「/employees/1」というリクエストにはHTMLではなくXMLで返すように実装してしまう、と答えた。
Q. DELETE /employees/1.xml を受け取ったとき、すでにそのリソースが存在しなかった時に返却する値は、404ではなく、204(No Content)では？
一般的にGETによる対象リソースがなかった場合は404を返す。ネットや書籍でいろいろ調べた結果、DELETEについても実装例で最も多かったのが404だったため、そのように説明した。しかし、確かに204を返して、20xシリーズ(=成功レスポンス)を使うのが好ましいかもしれない。ま、DELETEしようとして既にリソースがない場合は、それをエラーとして扱ったところで何もすることはないため、200でも構わないかもね、と話をした。
---
Rails2.0.2になり、MySQLが標準ではなくなるなど、さらに進化が続いている。Project ZeroやJava API for RESTful Web Servicesの動向も気になるところだが、やはり実践という点では、Rails2.0に軍配が上がっているのが現状だと感じる。
会場にいらしていただいた方々、ありがとうございました。今後ともREST on Railsを注目してみてください。
