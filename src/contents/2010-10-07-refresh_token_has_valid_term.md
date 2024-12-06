---
layout: post
status: publish
published: true
title: "リフレッシュトークンにも有効期間があるんです"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 846
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=846
date: '2010-10-07 09:17:18 +0900'
date_gmt: '2010-10-07 00:17:18 +0900'
categories:
- Social web
---

mixi Graph APIでは、OAuth 2.0 Draft 10が採用されています。具体的には、OAuth 2.0のWeb server profileが採用されています。

[1.4.1.  Web Server]

[http://tools.ietf.org/html/draft-ietf-oauth-v2-10#section-1.4.1](http://tools.ietf.org/html/draft-ietf-oauth-v2-10#section-1.4.1)

特にmixi Graph APIでは、仕様上ではOptionalですが、アクセストークンだけでなく、リフレッシュトークンも実装を行いました。アクセストークンの有効期間は短く（現状約15分）、アクセストークンが失効した場合には、リフレッシュトークンを使って、アクセストークンを再発行する手順が必要となります。

・・・と、ここまでは多くの開発者はすんなりと理解できていると思います。OAuth 1.0aでも、アクセストークンの有効期間があり、失効した場合は再認可が必要、という意識をOAuthを使ったことのある開発者は持っていると思います。

しかし、OAuth 2.0において「リフレッシュトークンにも有効期間がある」ということを意識していない開発者は多いかもしれません。実際には、ユーザが認可画面で同意した内容に影響を受けるのは、アクセストークンの有効期間ではなく、リフレッシュトークンの有効期間です。つまり、開発者はアクセストークンの有効期間切れを意識するだけでなく、リフレッシュトークンの有効期限切れに関しても意識をする必要があるということです。

mixi Graph APIを利用する場合、ユーザの認可画面には常に同意するかどうかを指定するためのチェックボックスがあります。このチェック状態に応じて、リフレッシュトークンの有効期間が決定します。

[http://developer.mixi.co.jp/connect/mixi_graph_api/api_auth#toc-1](http://developer.mixi.co.jp/connect/mixi_graph_api/api_auth#toc-1)

つまり、リフレッシュトークンを使ってアクセストークンを再発行する要求は、常に成功するのではなく、上記のリフレッシュトークンの有効期間によっては「失敗することもある」という前提でクライアントプログラムは実装される必要があります。具体的には、アクセストークンの再発行時点で以下の項で書かれたエラーが返却された場合は、これに該当します。

[http://developer.mixi.co.jp/connect/mixi_graph_api/api_auth#toc-3](http://developer.mixi.co.jp/connect/mixi_graph_api/api_auth#toc-3)

この場合は、リフレッシュトークンを再発行しなければなりません。そのためには、ユーザに再認可をしてもらう必要があります。つまり、Authorization codeの取得からやり直しとなります。クライアントプログラムは、上記のリフレッシュトークンの有効期間が切れたエラーを受け取った時点で、ユーザに再認可を行わせるように実装することが求められるのです。

[http://developer.mixi.co.jp/connect/mixi_graph_api/api_auth#toc-authorization-code](http://developer.mixi.co.jp/connect/mixi_graph_api/api_auth#toc-authorization-code)

上記の対処を忘れている開発者が結構いらっしゃるようでしたので、まとめてみました。ぜひmixi Graph APIを利用して素敵なアプリケーション、サービスを作ってください！
