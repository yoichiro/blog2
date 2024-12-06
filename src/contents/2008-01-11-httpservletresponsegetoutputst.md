---
layout: post
status: publish
published: true
title: HttpServletResponse#getOutputStream()したストリームは自分でclose()するのか？
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 499
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=499
date: '2008-01-11 09:01:21 +0900'
date_gmt: '2008-01-11 00:01:21 +0900'
categories:
- Java
---

久々にJavaな話題。昨日社内で、
「Servletの中でresponse#getOutputStream()や#getWriter()したストリームは、自分でclose()する必要があるの？それともclose()せずに放置が正解なの？」
という質問を受けた。僕は「自分で獲得したリソースは自分で明示的に解放すべき」という考えが基本にあり、思い返すと、doGet()やdoPost()メソッド内でちゃんと自分でclose()するコーディングを長年に渡って行ってきた。flush()に関してもclose()したタイミングで行われるだろうし、初期のアホコンテナに対しては、ちゃんと後処理を行ってくれないんじゃないか、という不信感があったため、いつも自分でclose()を明示的に呼んでいた。
でも、昨日質問を受けて、果たしてclose()を自分でする必要があるのか？コンテナにclose()は任せる必要があるんじゃないか、という疑問が沸々と湧いてきた。「getしたものは自分でリソースを作ったわけじゃないので、解放の責務は自分じゃないのでは？」という意見も出て、ますます自分でclose()してきたことは間違いなのではないか、と思い始めた。もちろん自分でclose()しなくてもコンテナが後処理をよろしくやってくれることは知っているけど、実際にclose()するという行為がServlet APIの仕様上いかがなものなのか？ということを確かめたくなった。
そこで、Servlet APIの仕様書に何か書いていないかどうかを見てみた。すると、Servlet API 2.4の仕様の中に、以下の記述を発見。

>SRV.5.5 レスポンスオブジェクトのクローズ

SRV.5.5 Closure of Response Object

レスポンスが閉じられる時、コンテナはレスポンスバッファに残っている全てのコンテンツをクライアントへ直ちにフラッシュしなければならない。

When a response is closed, the container must immediately flush all remaining
content in the response buffer to the client.
サーブレットがリクエストを満たし、レスポンスオブジェクトが閉じられることを示すのは、以下のイベントです:

The following events indicate that the
servlet has satisfied the request and that the response object is to be closed:

* サーブレットのserviceメソッドが終了したとき。

The termination of theservice method of the servlet.

* レスポンスのsetContentLengthメソッドで指定されたコンテンツの量がレスポンスに書かれたとき。

The amount of content speciﬁed in thesetContentLength method of the re-
sponse has been written to the response.

* sendErrorメソッドが呼ばれたとき。

ThesendError method is called.

* sendRedirectメソッドが呼ばれたとき。

ThesendRedirect method is called.

つまり、コンテナがレスポンスオブジェクトをcloseするタイミングが規定されているわけで、アプリケーション作者はcloseの処理をコンテナに任せて良い、ということが言える。「自分で閉じちゃいけない」という記述は見つけられないけど、ちゃんと決まったタイミングでコンテナが閉じますよ、という記述はあるということ。
よって結論としては、「自分ではclose()メソッドを使わない（＝閉じない）」ということで良いと思う。うーん、今までの僕は仕様も読まずに単純な思想でclose()していたことになる。深く反省。orz
