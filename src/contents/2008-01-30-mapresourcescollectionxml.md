---
layout: post
status: publish
published: true
title: map.resourcesでcollectionを指定すると.xmlを受け付けない、なんてことはなかった
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 512
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=512
date: '2008-01-30 21:30:45 +0900'
date_gmt: '2008-01-30 12:30:45 +0900'
categories:
- Ruby on Rails
---

RESTful APIをRuby on Railsで実装するための重要な機構に、map.resourcesがある。これはconfig/routes.rbファイルで記述され、URIに関してRESTfulなプロトコルに対応した処理のマッピングを自動的に設定してくれる仕組みだ。例えば、

map.resources :articles

としてあげれば、「GET /articles/1.xml」というリクエストに対してArticleController#showメソッドが、「DELETE /articles/1.xml」というリクエストに対してArticleController#destroyメソッドが呼び出される、といった具合になる。たった１文でこんな設定が実現できるのは、非常に嬉しい。

さて、上記の例では、URIの最後に「.xml」という拡張子をつけている。これは「レスポンスはXML形式で返してね」という指示を意味する。URIに拡張子を付けなくても、リクエストヘッダのAcceptフィールドでレスポンスの形式を指定することもできる。つまり、上記の設定は、「GET /articles/1」でも問題なくArticleController#showメソッドが呼び出される。

Railsで想定されるRESTful APIは、上記のような「/リソース名/ID」という指定が基本となる。しかし、例えば「GET /articles/rss」というように、ID値指定ではなく、意味のある単語でURIを構成したいこともあるだろう。そのような場合には、map.resourcesでの記述は、

map.resources :article, :collection => { :rss => :get }

というようにすれば良い。これにより、「GET /article/rss」というリクエストに応じて、ArticleController#rssメソッドが呼び出されるようになる。

しかしRails 1.2.6では、:collectionを指定した際に、「GET /article/rss.xml」というように拡張子を付与した形でリクエストを投げても、認識されずに404となってしまう。もちろん「GET /article/rss」でAcceptリクエストヘッダを指定すれば、レスポンスの形式を指定した上でArticleController#rssメソッドがちゃんと呼ばれる。しかし、「GET /article/rss.xml」とか「GET /article/rss.json」とか「GET /article/rss.atom」とか指定しても、404。

これはさすがにバグな予感がするので、Rails Tracにチケットを発行して登録してみた。

[Ticket #10960](http://dev.rubyonrails.org/ticket/10960) 「:collection of map.resources doesn't recognize the extension of URI」- Rails Trac

この問題、別にAcceptリクエストヘッダを使えばいいだけの話なのだが、現状のActiveResourceは、内部で生成されるURIに必ず「.xml」や「.json」などの拡張子が自動的に付いてしまうため、「GET /article/rss」を呼べないのだ。つまり、Railsで作ったRESTful APIなのに、Railsに含まれているActiveResourceでアクセスできない、ということになってしまう。これは余りにもダサい。。。

Rails 2.0でどうなのかは試してないので、もしかしたら上記の問題は発生しない、かも。あとで試してみることにする。

追記：2008年1月31日

どうも僕のmap.resourcesの書き方が間違っていたみたいで、シンプルなアプリを作って検証したら、Rails1.2.6でもRails 2.0.2でもちゃんと拡張子付きで処理された。よって、上記チケットもclose。自分の確認力のなさに絶望。。。orz
