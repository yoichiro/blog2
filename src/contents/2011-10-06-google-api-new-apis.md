---
layout: post
status: publish
published: true
title: Google+ APIにいくつか新機能が追加されました
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1046
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1046
date: '2011-10-06 21:29:19 +0900'
date_gmt: '2011-10-06 12:29:19 +0900'
categories:
- Google
---

Google+ APIに以下の新しい機能が追加されました。

[Google+ APIs: Now With Search and More!](http://goo.gl/iuqvp) - The Google+ Platform Blog

相変わらず一般公開された情報のみのサポートですが、取得可能な情報が徐々に増えてきましたね。

* 検索条件を指定したパブリックなアクティビティ一覧の取得

* 検索条件を指定したユーザのプロフィール一覧の取得

* あるアクティビティを再共有したユーザ一覧の取得

* あるアクティビティに+1をしたユーザ一覧の取得

* あるアクティビティに投稿されたコメント一覧の取得

* アクティビティに投稿されたあるコメント情報の取得

一つずつ見ていきましょう。

検索条件を指定したパブリックなアクティビティ一覧の取得

これは、GET /plus/v1/activities リクエストに対して、queryクエリパラメータが指定可能になった、という新機能になります。具体的には、以下のような指定となります。

>GET https://www.googleapis.com/plus/v1/activities?query=cookie%20recipes


上記の例の通り、空白区切りで複数のキーワード文字列が指定可能になっています。このキーワードは、アクティビティの本文およびそのアクティビティに投稿されたコメント群に対しての検索指定となります。取得結果は、GET /plus/v1/activities リクエストを普通に使った場合と同じ、ActivityStreams仕様に則ったJSON文字列となります。

検索条件を指定したユーザのプロフィール一覧の取得

アクティビティと同様に、ユーザについてもキーワード指定をして検索が可能になりました。具体的には、以下のようにAPIにアクセスします。

>GET https://www.googleapis.com/plus/v1/people?query=vic%20gundotra


特定のユーザの情報を取得する場合は、GET /plus/v1/people/{userId} というようにユーザIDを指定する必要があるのですが、その代わりにqueryクエリパラメータを指定することで、条件にあったユーザの集合を得ることが可能です。アクティビティの時と同じように、キーワード指定は空白区切りで複数指定が可能です。このキーワードは、プロフィール情報の氏名(name)、バイオグラフィー(bio)、今いる場所(location)、タグ(tag line)、そして紹介文(description)が検索対象となります。取得結果は、以下のようなJSONになります。複数ユーザのプロフィール情報が結果として得られますので、itemsプロパティは配列値を持つことになります。一人一人のプロフィール情報の表現形式は、もちろんPortableContactsです。

>{
　"kind": "plus#peopleFeed",
　"selfLink": {string},
　"title": {string},
　"nextPageToken": {string},
　"items": [
　　
people Resource
　]
}


あるアクティビティを再共有したユーザ一覧の取得

ユーザが投稿した何らかのアクティビティは、他のユーザによって再共有されることがあります。投稿主からすると、誰が再共有したかを知りたくなることでしょう。あるアクティビティについて、再共有したユーザの一覧をAPIにて取得できるようになりました。具体的には以下のようなリクエストを送ります。

>GET https://www.googleapis.com/plus/v1/activities/{activityId}/people/resharers


GET /plus/v1/activities/{activityId} に対して、/people/resharers を追加することで、再共有したユーザのプロフィール情報の一覧を得ることが可能です。取得結果は、先ほどのユーザ検索の結果のJSON文字列と同様となります。つまり、itemsプロパティ値として、再共有したユーザのプロフィール情報の配列が結果として得られることになります。

あるアクティビティに+1をしたユーザ一覧の取得

あるアクティビティを再共有したユーザ一覧の取得と同様に、あるアクティビティを+1したユーザ一覧も取得することが可能です。これには、具体的に以下のリクエストを送ります。

>GET https://www.googleapis.com/plus/v1/activities/{activityId}/people/plusoners


resharersではなく、plusonersにすることで、+1したユーザ一覧の取得指定となります。取得結果についてはresharersの場合と同じです。

あるアクティビティに投稿されたコメント一覧の取得

あるアクティビティに投稿されたコメントの一覧をAPIにて取得できるようになりました。リクエスト自体は、以下のように/commentsをつけるだけであり、シンプルです。

>GET https://www.googleapis.com/plus/v1/activities/{activityId}/comments


コメント一覧を得るためには、アクティビティのIDをactivityIdとして指定する必要があります。この取得結果は、以下のようになります。

>{
　"kind": "plus#commentFeed",
　"nextPageToken": {string},
　"nextLink": {string},
　"title": {string},
　"updated": {datetime},
　"id": {string},
　"items": [
　　
comments Resource
　]
}


itemsプロパティ値は、コメントの情報を持つオブジェクトの配列となります。各コメントの情報は、"plus#comment"種別でオブジェクトタイプが"comment"なActivityStreams仕様のJSON文字列となります。

アクティビティに投稿されたあるコメント情報の取得

先ほどはコメント一覧でしたが、あるコメントの情報のみを取得することもできます。そのために、コメントのIDをリクエストに指定することが求められます。

>GET https://www.googleapis.com/plus/v1/comments/{commentId}


あるコメントの情報を得る際には、それが属するアクティビティのIDを指定する必要はありません。直接commentリソースの取得要求として指定することができます。取得結果は、"plus#comment"種別でオブジェクトタイプが"comment"なActivityStreams仕様のJSON文字列のみとなります。
