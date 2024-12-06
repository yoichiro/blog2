---
layout: post
status: publish
published: true
title: Content Upload Support (0.9)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 646
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=646
date: '2009-05-15 14:18:59 +0900'
date_gmt: '2009-05-15 05:18:59 +0900'
categories:
- OpenSocial
---

OpenSocial v0.9で追加になった「Content Upload Support」仕様について紹介します。OpenSocialアプリケーションでは、画像や動画などが良く使われますし、Album APIも追加になったので、このアップロードの仕様追加は比較的重要なものかと思います。

---

[Content Upload Support]

Content Upload Supportは、OpenSocialコンテナに対して、JSON/XML/AtomPub RESTfulやJSON-RPCを使った呼び出しを通じてファイルをアップロードすることを可能にします。このメカニズムは、以下のような局面で使用することができます: (もちろん他の用途も考えられます)

* ユーザのプロフィール/サムネイル画像のアップロード

* アップロードされたメディアアイテムコンテンツを持つアクティビティストリームエントリの作成

* ユーザのアルバムに含めるための、コンテナに対して音声/動画/画像といったコンテンツのアップロード

背景:

v0.8.1までの(Activity, Person, AppDataなどの)リソースの取得、更新、作成、または削除のためのOpenSocialコンテナへのRESTful呼び出しは、標準的なHTTPメソッド(GET, PUT, POST, DELETE)を使って行われます。特に、PUTやPOSTはリソースを作成あるいは更新するために使われます。HTTPリクエストのPOST Body部には、リソースを作成あるいは更新するためのJSON/XML/AtomPubリクエストが含まれます。しかしながら、OpenSocialにおいて、コンテンツをアップロードすることを可能とする標準的なメカニズムはありませんでした。

OpenSocialコンテナへのJSON/XML-RPC呼び出しにおいて、POST Body部はリクエストのコンテンツを運びます([Appendix B]を参照)。JSON-RPCまたはXML-RPCサービスによるコンテンツアップロードを可能とする標準的なメカニズムは存在していません。

OpenSocialコンテナへのコンテンツのアップロードを可能とすることが必要なことは明白です。本仕様は、ユーザにプロフィール写真をアップロードすることや、アクティビティストリームにメディアアイテムを追加することや、彼らのオンラインアルバムに写真を追加することを可能とするでしょう。

解説:

RESTful呼び出しを使ったアップロード:

レギュラーアップロード:

RESTfulは各リソースの作成、更新、取得や削除のためのエンドポイントを提供します。これらの考えと同じ連鎖に従うとすると、新しく関連づけられるリソースの作成は、含まれるエンティティのURLへのリソースのコンテンツをポストすることによって行われるでしょう。そのレスポンスは、IDや他の関連づけられる属性を含みます。(これは
[RFC 5023 Section 9.6](http://tools.ietf.org/html/rfc5023#section-9.6)に似ています)。コンテンツをアップロードするためには、Content-Typeヘッダはアップロードされるファイルの種別を指定しなければなりませんし、それらがエンティティのアップロードのリクエストとして扱われるようにするために、application/xml、application/atom+xmlやapplication/jsonとしてはいけないことに注意してください。その"Accept-Type"ヘッダは、期待するレスポンスの書式を示すために使われます。

ショートカットアップロード:

ショートカットメカニズムも、コンテナエンティティにおいて、新しいコンテナエンティティの作成の2つのステップのプロセスを簡単に避けることを可能とし、コンテンツをアップロードできる提案です。各REST仕様として、コンテナエンティティはatom/xml/json属性を持つ特定のURLの新しいPOSTによって生成されます。そのURLパラメータ(オプション)におけるその属性を持つURLへのPOSTや、POST Bodyにおけるコンテンツのアップロードは、エンティティを作成し関連づけられたアップロードをするためのショートカットメソッドになるでしょう。このためのContent-Typeは、アップロードされているコンテントタイプのセットとなることに注意してください。

[Appendix A]は、レギュラーアップロードとショートカットアップロードの例を示します。

JSON/XML-RPCを使ったアップロード:

JSON/XML-RPCの既存のメカニズムにおいて、POST HTTPリクエストは、POST Body内の要求オペレーションの詳細として置かれます。このメカニズムは、サポートされ続けるでしょう。加えて、マルチパートフォームデータリクエストメカニズム("Content-Type: multipart/form-data")は、コンテンツのアップロードを可能とするでしょう。そのようなリクエストは、名前によって識別される各フィールドを伴ういくつかのセクション(fields)によって分割されたPOST Bodyを持ちます。そのフィールド名"request"は予約されていて、要求する処理の詳細を含んでいます(普通のPOSTオペレーションにおけるPOST Bodyのコンテンツと同じ)。

これをイメージするための良い例が、[Appendix B]として提供されます。

レスポンス:

アップロードが成功したら、そのリクエストに対するレスポンスコードは、リソースのアクション要求のレスポンスコードとして関連づけられるでしょう。しかしながら、ファイルサイズの制限違反のために失敗した場合、413("Request Entity Too Large")のエラーコードが返されるべきです。そのファイルアップロードに関連づけられてリクエストされたアクションはまた、実行されないでしょう。

Appendix A: REST

このセクションでは、アルバム内にメディアアイテムを作成することを依頼する例を使います。アルバムのための仕様はここです: 
[http://docs.google.com/Doc?id=dpbz5zc_5gdbp3dgd](http://docs.google.com/Doc?id=dpbz5zc_5gdbp3dgd)。本質的に、その例はコンテナにコンテンツをアップロードするための2つの方式(レギュラー、ショートカット)で記述します。

レギュラーアップロード:

ほとんどのシナリオにおいて、アップデートされたコンテンツは、エンティティの作成と関連があります。エンティティの作成は、日常的にコンテンツのアップロードから生じます。例えば、新しいメディアアイテムの作成のリクエストは、写真のアップロードの手順を生みます。そのリクエストはこのようなものです:

Step 1: イメージのプレースホルダーとなるメディアアイテムを作成する。
Step 2: 写真をアップロードする。

その2つのステップのリクエストは、以下のような内容となります。

Step 1: POSTリクエストによるアルバム(id:112233)へメディアアイテム(イメージのプレースホルダー)を作成する

リクエスト

POST /album/112233/mediaitem/ HTTP/1.1

Content-Type: application/atom+xml
Content-Length: length

Lena

IMAGE



2008-10-24T20:30:40.500Z

レスポンス

urn:guid:example.org:33445577722



100

1

10

223344

Lena

IMAGE



2008-10-24T20:30:40.500Z

そのレスポンスはメディアアイテムが作成され、メディアアイテムのIDが223344であることを示しています。

Step 2: メディアアイテムプレースホルダーの中にイメージをアップロードする

リクエスト

POST /album/112233/mediaitem/223344 HTTP/1.1

Accept-Type: application/xml
Content-Type: image/gif
Content-Length: length
GIF89a....
レスポンス

1

1

1

...

アップロードメソッドのショートカット

上記で示したこの2つのステップのプロセスは、アップロードするアイテムにセットするコンテントタイプを伴う単一のPOSTリクエストによって一つにマージすることが可能です。そのPOST URLは、オプション的に属性を渡すことができます。そのAccept-typeは、期待されるレスポンスの書式を指定します。

リクエスト

POST /album/112233/mediaitem/?caption=Lena&media_type=IMAGE HTTP/1.1

Accept-Type: application/json
Content-Type: image/gif
Content-Length: length
GIF89a....

レスポンス

{
"startIndex" : 1,
"itemsPerPage" : 1,
"totalResults" : 1,
"entry" : {
"id" : "223344",
"caption" : "Lena",
"media_type" : "IMAGE",
}
}

Appendix B: RPC

このセクションでは、JSON/RPCリクエストを使ってメディアアイテムを伴うアクティビティを作成するための例を示します。XMLリクエストもこれに類似しています。

既存のRPCメカニズム

メディアアイテムを伴うアクティビティの作成のための既存のJSON/RPCリクエストは、このようになるでしょう:
(URLパラメータは外部サーバにホストされたコンテンツへポイントしていることに注意してください)

POST /social/rpc?st=
HTTP/1.1

Content-Type: application/json
[{
"method":"activities.create",
"params": {
"userId":["@viewer"],
"groupId":"@self",
"appId":"@app",
"activity": {
"title": "hello world!",
"mediaItems": [ {
"mimeType":"image",
"url":"http://robotics.eecs.berkeley.edu/~sastry/ee20/images/lena.gif"
} ]
}
},
"id":"key"
}]

アップロードを伴うRPC

コンテンツアップロードを伴うリクエストも先のリクエストと似ていて、以下のようになります:
(現在のURLパラメータは"@field:image1"ですので注意してください。また、フィールドが"request"や"image1"であることも注意ください。)

Content-type: multipart/form-data; boundary=------------abcdef012345xyZ
Content-length: 
------------abcdef012345xyZ
Content-Disposition: form-data; name="request"
[{
"method":"activities.create",
"params": {
"userId":["@viewer"],
"groupId":"@self",
"appId":"@app",
"activity": {
"title": "hello world!",
"mediaItems": [ {
"mimeType":"image",
"url":"@field:image1"
} ]
}
},
"id":"key"
}]
------------abcdef012345xyZ
Content-Disposition: form-data; name="image1"
GIF89....
------------abcdef012345xyZ
