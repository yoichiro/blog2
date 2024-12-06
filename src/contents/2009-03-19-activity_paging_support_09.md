---
layout: post
status: publish
published: true
title: Activity Paging Support (0.9)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 632
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=632
date: '2009-03-19 09:39:31 +0900'
date_gmt: '2009-03-19 00:39:31 +0900'
categories:
- OpenSocial
---

People & Friends APIを使って会員情報や友達情報などを取得する際に、条件に一致する人数が多くなることを想定し、ページングを指定することができるようになっています。例えば、JavaScript APIであれば、PeopleRequestFields.MAXとPeopleRequestFields.FIRSTを使って、1リクエストに含める件数と開始インデックスを指定することができるようになっています。

しかし、Activities APIにおいては、残念ながらOpenSocial 0.8.1においてもページングが考慮されていませんでした。そのため、多量のActivityがコンテナとクライアント間でやり取りされてしまう問題が発生します。特に、Apache ShindigにおけるActivityServiceインタフェースにおいても、ページングに関する引数が定義されていませんでした。

Activity Paging Supportは、Activityの取得に関してもページングをサポートするようにしましょう、という提案になります。

[Activity Paging]

[http://wiki.opensocial.org/index.php?title=Activity_Paging](http://wiki.opensocial.org/index.php?title=Activity_Paging)

[Proposal for Activity Paging Support in OpenSocial]

[http://docs.google.com/View?docid=dg2cfnzw_45ff6wcccn](http://docs.google.com/View?docid=dg2cfnzw_45ff6wcccn)

RESTful ProtocolおよびRPC Protocolでは、OpenSocial 0.8.1にて既にstartIndex、countパラメータが横断的に定義されていたため、OpenSocial 0.9の仕様上の変更はありません。

JavaScript APIに関しては、Activityのページングに関する条件を指定するために、ActivityRequestFieldsクラスが新規に定義されます。

opensocial.DataRequest.ActivityRequestFields

　　
object FIRST
　　　　ページングを行う際に、取得するActivityの集合の開始インデックスを数値で指定します。
　　
object MAX
　　　　取得するActivityの集合の最大件数を数値で指定します。初期値は20です。

ActivityRequestFieldsクラスは、newFetchActivitiesRequest()関数にて使用します。具体例としては、以下のようなコードになるでしょう。

var p1 = {};
p1[opensocial.DataRequest.ActivityRequestFields.FIRST] = 20;
p1[opensocial.DataRequest.ActivityRequestFields.MAX] = 10;
var p2 = {};
p2[opensocial.IdSpec.Field.USER_ID] = opensocial.IdSpec.PersonId.VIEWER;
p2[opensocial.IdSpec.Field.GROUP_ID] = "FRIENDS";
var idSpec = opensocial.newIdSpec(p2);
var req = opensocial.newDataRequest();
req.add(req.newFetchActivitiesRequest(idSpec, p1), "activities");
req.send(...);

Activityは今後のソーシャルWebのキーワードとなるものです。上記のコードを開発者が書く機会も増えるのではないでしょうか。
