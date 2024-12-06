---
layout: post
status: publish
published: true
title: Google OAuth2 Web Server Profileでのリフレッシュトークン
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1471
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1471
date: '2012-05-19 11:45:10 +0900'
date_gmt: '2012-05-19 02:45:10 +0900'
categories:
- Social web
---

久々にGoogleのOAuth 2.0のWeb Server Profileを使っていて、あれ？って思ったので、ここでメモ代わりに書いておきます。

基本的には、以下のブログエントリで語られている話です。

[Upcoming changes to OAuth 2.0 endpoint - The official Google Code blog](http://goo.gl/ecVbr)

OAuth 2.0でのWeb Server Profileのセオリーでは、以下の手順が踏まれます。

* Client IDとScopeを指定して認可画面を要求します。

* ユーザは認可画面を見て、許可するか拒否するか選択します。

* redirect_uriにリダイレクトされ、その際にauthorization_codeが渡されます。

* authorization_codeと引き替えに、access_tokenとrefresh_tokenを取得します。

* access_tokenが失効したら、refresh_tokenを使ってaccess_tokenを再発行します。

mixi Platformであれば、上記の流れとなります。これがFacebookだと、APIがHTTPSでしか叩けないことを理由にして、access_tokenの有効期限が十分に長くなっているため、5番目の手順を行う必要がなくなっています。

ではGoogleだとどうなるか。結構面白い挙動をします。

* Client IDとScopeを指定して認可画面を要求します。

* 対象ユーザにとって最初の認可機会だったときは、ユーザに認可画面が表示され、許可するか拒否するか選択します。もし2回目以降だった場合は、認可画面はスキップされます（表示されずに即次のステップへ）。

* redirect_uriにリダイレクトされ、その際にauthorization_codeが渡されます。

* authorization_codeと引き替えに、access_tokenを取得します（ここでrefresh_tokenはGoogleから返ってきません）。

* access_tokenが失効したら、最初のステップからやり直します。

「え、最初からやり直し？」と思うかもしれませんが、ユーザ体験的には上記2つについて何ら変化はありません。なぜこのような挙動になっているかというと、「ほとんどのAPI利用が認可後に1回だけってことが多いでしょ？どうせrefresh_tokenを使ってaccess_tokenを再発行する機会がないなら、もうそこは省いてしまって、その代わりにユーザへの認可画面表示をスキップして自動承認しちゃえばいいよね」って理由となります。かなり現実路線を突き進んでる印象を持つかもしれませんが、裏でrefresh_token使ってaccess_tokenを再発行されていることと、ユーザが意識することなく自動承認されることを比べてみれば、APIアクセスに関するユーザからの権限委譲という点においては特に違いはありません。アプリケーションの作り方が違ってくるだけです。

このGoogleの挙動ですが、必ずWebブラウザ上で認可画面に遷移しなければならない、という制約があります。つまり、Webアプリケーションであればシームレスに上記の挙動を作り込めますが、サーバ側でAPIをユーザのリクエストとは非同期に比較的長く使いたい場合は、この手順ではなく従来のセオリー（つまりrefresh_tokenが欲しくなる）を適用したくなることでしょう。ちゃんとその場合向けのやり方があります。認可画面（https://accounts.google.com/o/oauth2/auth）に遷移する際に、以下のクエリパラメータを付与してあげます。

* approval_prompt=force : 認可画面をスキップさせない。

* access_type=offline : オフラインでAPIを使う（＝refresh_tokenも発行してもらう）。

これにより、従来と同じような挙動（mixi Platformと同等）で認可＆API利用をすることが可能になります。

「あれ？refresh_tokenが返ってこないんですけどー？」と思った方は、上記が参考になると嬉しいです。
