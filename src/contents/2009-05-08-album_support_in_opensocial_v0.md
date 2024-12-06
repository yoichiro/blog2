---
layout: post
status: publish
published: true
title: Album support (0.9)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 639
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=639
date: '2009-05-08 21:27:50 +0900'
date_gmt: '2009-05-08 12:27:50 +0900'
categories:
- OpenSocial
---

OpenSocial v0.9から追加になったAlbum APIについて、そのドラフトから日本語訳を起こしてみました。

---

[Album support in OpenSocial]

OpenSocial v0.9では、複数のMediaItem（写真、動画、そして音声クリップ）により構成されるAlbumをサポートするための機能が追加されました。これは「Albums API」と呼ばれています。この仕様には、JavaScript API、RESTful API、そしてJSON-RPC APIのそれぞれに対して定義が行われました。

Albums APIでは、以下の操作が規定されています。

* Albumの作成、更新、削除

* あるAlbumへのMediaItemの追加、更新、削除

■ JavaScript API

Albumサポートは、新しく追加されたopensocial.Albumクラスによって提供されます。Albumは、本質的に、複数のMediaItem（イメージ、動画、音声トラック）の集合です。あるAlbumに対して、opensocial.Album.getField()関数やopensocial.Album.setField()関数を呼び出すことで、各フィールドにアクセスすることが可能です。それらのフィールドは、opensocial.Album.Fieldクラスに定数として定義されています。string型のIDフィールドは、Albumを特定するための文字列であり、必須項目です。その他に、オプションとして以下のフィールドが定義されています。

THUMBNAIL_URL - string型です。Albumのカバー画像のURLです。
CAPTION - string型です。Albumのタイトル文字列です。
DESCRIPTION - string型です。Albumの説明文です。
LOCATION - opensocial.Address型です。Albumに対応する位置情報です。
OWNER_ID - string型です。Albumの所有者のIDです。
MEDIA_TYPE - MediaItem.TYPEの配列です。Album内のMediaItem群の種別です。
MEDIA_ITEM_COUNT - integer型です。Album内のアイテム数です。

あるAlbumは、opensocial.MediaItemクラスのオブジェクトを複数保持することができます。このクラスは従来から提供されていましたが、opensocial.MediaItem.Fieldに定義されるフィールドがいくつか追加されています。string型のIDフィールドは、MediaItemを特定するための文字列であり、必須項目です。その他に、オプションとして以下のフィールドが定義されています。

CAPTION - string型です。MediaItemの説明文です。
CREATED - string型です。MediaItemの作成日時です。UTCでコンテナにより与えられます。
LAST_UPDATED - string型です。MediaItemの更新日時です。UTCでコンテナにより与えられます。
THUMBNAIL_URL - string型です。MediaItemのサムネイル画像のURLです。
DESCRIPTION - string型です。MediaItemの説明文です。
DURATION - integer型です。音声または動画クリップのためのフィールドであり、再生時間の長さを秒で示します。もし長さが不明の場合は、-1がセットされます。
LOCATION - opensocial.Address型です。MediaItemに関連づけられた位置情報です。
LANGUAGE - string型です。ISO 639-3フォーマットで表現される、MediaItemに関連づけられた言語情報です。
ALBUM_ID - string型です。MediaItemが所属するAlbumです。
FILE_SIZE - long型です。バイト数を示します（もしバイト数が不明の場合は-1がセットされます）。
START_TIME - string型です。ストリームまたはライブコンテンツのためのフィールドであり、コンテンツが利用可能な時間を表します。
RATING - integer型です。0〜10のスケールで表現される、MediaItemの平均レートです。
NUM_VOTES - integer型です。投票を受けた投票数です。
NUM_COMMENTS - integer型です。写真へのコメント数です。
NUM_VIEWS - integer型です。MediaItemの閲覧数です。

以下のフィールドも引き続き存在します。

TYPE - MediaItem.TYPE型です。MediaItemの種別です（AUDIO/VIDEO/IMAGE）。
MIME_TYPE - string型です。MediaItemのmime-typeです。
URL - string型です。MediaItemのURLです。

AlbumおよびMediaItemは、以下の関数で生成されます。

opensocial.newAlbum() - opensocial.Albumオブジェクトを生成します。
opensocial.newMediaItem() - opensocial.MediaItemオブジェクトを生成します。

■■ APIs

AlbumやMediaItemを扱うためのAPIは、DataRequestクラスに追加されています。

---

opensocial.DataRequest.newFetchAlbumsRequest(idSpec, opt_params)

newFetchAlbumsRequest()関数は、複数のAlbum情報を要求するためのDataRequestオブジェクトを生成します。opt_params引数には、以下の情報を指定することが可能です。

opensocial.Album.Field.ID - 取得するAlbumのIDの配列です。もし無指定の場合は全てのAlbumの取得要求になりますが、ページングのために結果数は限定されます。
opensocial.Album.Field.MEDIA_TYPE - 取得するAlbumの種別を指定するためのMediaItem.TYPE値の配列です。

---

opensocial.DataRequest.newFetchMediaItemsRequest(idSpec, albumId, opt_params)

newFetchMediaItemsRequest()関数を使うことで、Album内のMediaItemのリストを取得することができます。opt_params引数には、以下の情報を指定することが可能です。

opensocial.MediaItem.Field.ID - 取得するMediaItemのIDの配列です。もし無指定の場合は全てのMediaItemの取得要求になりますが、ページングのために結果数は限定されます。
opensocial.MediaItem.Field.MEDIA_TYPE - 取得するMediaItemの種別を指定するためのMediaItem.TYPE値の配列です。

FilterやFIRST、MAXを使ったテクニックは、FetchAlbumsやFetchMediaItemsにおいて、ページングのために利用することができます。

---

opensocial.DataRequest.newCreateAlbumRequest(idSpec, album)

新しいAlbumを作成し、結果として作られたAlbumのIDを返します。コンテナは、ViewerがViewer自身のためのみAlbumを作成することが許可される、といった制限を実装します。

---

opensocial.DataRequest.newCreateMediaItemRequest(idSpec, albumId, mediaItem)

新しいMediaItemをAlbum内に作成し、作られたAlbumのIDを返します。コンテナは（newCreateAlbumRequest()関数のような）制限を実装します。

---

opensocial.DataRequest.newUpdateAlbumRequest(idSpec, albumId, fields)

パラメータで指定された項目を更新します。結果はvoidです。以下の項目はセットできません: MEDIA_ITEM_COUNT、OWNER_ID、ID。コンテナは（newCreateAlbumRequest()関数のような）制限を実装します。

---

opensocial.DataRequest.newUpdateMediaItemRequest(idSpec, albumId, mediaItemId, fields)

パラメータで指定された項目を更新します。結果はvoidです。以下の項目はセットできません: ID、CREATED、ALBUM_ID、FILE_SIZE、NUM_COMMENTS。コンテナは（newCreateAlbumRequest()関数のような）制限を実装します。

---

opensocial.DataRequest.newDeleteAlbumRequest(idSpec, albumId)

指定されたAlbumを削除します。結果はvoidです。コンテナは（newCreateAlbumRequest()関数のような）制限を実装します。

---

opensocial.DataRequest.newDeleteMediaItemRequest(idSpec, albumId, mediaItemId)

指定されたAlbumに含まれるMediaItemを削除します。結果はvoidです。コンテナは（newCreateAlbumRequest()関数のような）制限を実装します。

■■ Examples

■■■ ユーザのAlbumの取得

function requestAlbums() {
var idspec_params = {};
idspec_params[opensocial.IdSpec.Field.USER_ID] = opensocial.IdSpec.Field.PersonId.OWNER;
var idspec = opensocial.newIdSpec(idspec_params);
var params = {};
req = new opensocial.newDataRequest();
req.add(req.newFetchAlbumsRequest(idspec, params), 'albums');
req.send(onLoadAlbums);
}
/* list albums items */
function onLoadAlbums(dataResponse) {
var html = 'List of albums: 



';
var albums = dataResponse.get('albums').getData();
albums.each(function(album) {
html += '

* ' + album.getField(opensocial.Album.Field.CAPTION) + '';
});
html += '';
document.getElementById('album_container').innerHTML = html;
};

■■■ Album内のMediaItemの取得

function listMediaItems(album) {
var albumId = album.getField(opensocial.Album.ID);
var idspec_params = {};
idspec_params[opensocial.IdSpec.Field.USER_ID] = opensocial.IdSpec.Field.PersonId.OWNER;
var idspec = opensocial.newIdSpec(idspec_params);
var req = new opensocial.newDataRequest();
var params = {};
req.add(req.newFetchMediaItemsRequest(idspec, albumId, params), 'mediaitems');
req.send(onLoadMediaItems);
}
/** list mediaitem details */
function onLoadMediaItems(dataResponse) {
var html = 'List of MediaItems: 



';
var mediaitems = dataResponse.get('mediaitems').getData();
mediaitems.each(function(mediaitem) {
html += '

* ' + mediaitem.getField(opensocial.MediaItem.Field.CAPTION);
html += '
![](' + mediaitem.getField(opensocial.MediaItem.Field.THUMBNAIL_URL + ')';
});
html += '';
document.getElementById('mediaitem_container').innetHTML = html;
};

■ RESTful API

新しい"album"サービスは、AlbumやMediaItemの要求をハンドルするために追加されました。そのサービスは、ディスカバリXML文書内のエントリを通じて発見されます。

http://ns.opensocial.org/album/0.x

http://api.example.org/album/{userId}/{groupId}/{optionalAlbumId}/mediaitem/{optionalMediaItemId}

userId: ユーザID
groupId:
@self: ユーザ(ユーザID)のAlbum
@friends, @all、またはグループID: ユーザのグループ内の全ての人々が所有するAlbum [これのサポートはオプションです]
optionalAlbumId: 特定のAlbumを参照するためのAlbum ID
optionalMediaItemId: Album内の特定のMediaItemを参照するためのMediaItem ID

操作:
GET /album/@me/@self は、Albumの配列を取得します
POST /album/@me/@self は、新しいAlbumを作成します
GET /album/@me/@self/albumId は、特定のAlbumのみを返します
PUT /album/@me/@self/albumId は、Albumを更新します
DELETE /album/@me/@self/albumId は、Albumを削除します

GET /album/@me/@self/albumId/mediaitem は、Album内のMediaItemを返します
POST /album/@me/@self/albumId/mediaitem は、新しいMediaItemの作成に使われます
GET /album/@me/@self/slbumId/mediaitem/mediaItemId は、MediaItemを返します
PUT /album/@me/@self/slbumId/mediaitem/mediaItemId は、MediaItemを更新します
DELETE /album/@me/@self/slbumId/mediaitem/mediaItemId は、MediaItemを削除します

■■ Examples

IDが44332211のAlbumを得るための GET /album/@me/@self/44332211 は、以下をもたらします。

{
"id" : "44332211",
"thumbnail_url" : "http://pages.example.org/albums/4433221-tn.png",
"caption" : "Example Album",
"description" : "This is an example album, and this text is an example description",
"location" : { "latitude": 0, "longitude": 0 },
"owner_id" : "example.org:55443322"
}

IDが11223344のMediaItemを得るための GET /album/@me/@self/44332211/mediaitem/11223344 は、以下をもたらします。

{
"id" : "11223344",
"thumbnail_url" : "http://pages.example.org/images/11223344-tn.png",
"mime_type" : "image/png",
"type" : "image",
"url" : "http://pages.example.org/images/11223344.png",
"album_id" : "44332211"
}

■ RPC API

以下のメソッドは、Albumをサポートします:
album.get、album.update、album.create、そしてalbum.delete
mediaitem.get、mediaitem.update、mediaitem.create、そしてmediaitem.delete

以下の例は、album.* の操作のためのものですが、mediaitem.* に関しても流儀は同じです。userid（@me）やgroupid（@self）の初期値は、指定されなかった場合に適用されます。

■■ album.get

リクエスト

{
"method" : "album.get",
"id" : "get_all_albums",
"params" : {
"userid" : "@me",,
"groupid" : "@self",
"id" : [ ],
}
}

レスポンス

{
"id" : "get_all_albums",
"result" : {
"totalResults" : 1,
"startIndex" : 0,
"itemsPerPage" : 20,
"list" : [
{
"userid" : "@me",
"groupid" : "@self",
"id" : "5544331",
"thumbnail_url" : "http://pages.example.org/albums/4433221-tn.png",
"caption" : "Example Album",
"description" : "This is an example album, and this text is an example description",
"location" : { "latitude": 0, "longitude": 0 },
"owner_id" : "example.org:55443322"
},
{..},
{..},
]
}
}

■■ album.update

Albumを更新するためのリクエストは、更新されたAlbumの情報を指定します。もし成功した場合は、空の結果を返します。

リクエスト

{
"method" : "album.update",
"id" : "my_update_album_request",
"params" : {
"userid" : "@me",
"groupid" : "@self",
"id" : "5544331",
"fields" : {
"caption" : "my holiday pictures"
}
}
}

レスポンス

{
"id" : "my_update_album_request",
"result" : {
}
}

■■ album.create

Owner IDおよびAlbum IDは指定されるべきではありません。Owner IDやAlbum IDは自動的に割り当てられます。Album IDが結果として返却されます。

リクエスト

{
"method" : "album.create",
"id" : "my_album_create_request",
"params": {
"userid" : "@me",
"groupid" : "@self",
"album" : {
"thumbnail_url" : "http://pages.example.org/albums/4433221-tn.png",
"caption" : "Example Album",
"description" : "This is an example album, and this text is an example description",
"location" : { "latitude": 0, "longitude": 0 },
}
}
}

レスポンス

{
"id" : "my_album_create_request",
"result" : {
"id" : "445544577"
}
}

■■ album.delete

削除のリクエストは、Albumを特定するためにAlbum IDを要求します。正常に完了した際には、空の結果が返されます。

リクエスト

{
"method" : "album.delete",
"id" : "my_delete_request",
"params" : {
"id" : "445544577"
}
}

レスポンス

{
"id" : "my_delete_request",
"result" : {
}
}
