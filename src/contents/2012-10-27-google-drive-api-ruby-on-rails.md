---
layout: post
status: publish
published: true
title: Google Drive APIをRuby on Railsから叩く方法
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1745
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1745
date: '2012-10-27 11:56:29 +0900'
date_gmt: '2012-10-27 02:56:29 +0900'
categories:
- Ruby on Rails
- Google Drive API/SDK
---

DropboxとSkyDriveの各APIを叩いてRuby on Railsアプリからファイルのアップロードする方法を紹介してきましたが、最後はGoogle Driveを紹介してみます。Image Collector extensionでサポートしているGoogle Driveへの画像のアップロードですが、実は連携数はNo.1です。Dropboxが人気なのかな、と勝手に思ってたんですが、やっぱりみんなGoogle大好き！ってことなのでしょうか。

今までGoogle Driveにファイルをアップロードするための方法といえば、Google Documents List APIを使うことでした。これはGData APIの一種です。つまり、Atom文書がベースとなっているAPIであり、ホントに苦労させられます。なかなかValidなXMLを作れず、サーバから返却されるエラーメッセージが貧弱なこともあり、「うまくいかねーよ！そろそろ夜が明けちまうじゃないか！！！」という思いをした方は大勢いるのではないでしょうか？僕は夜がめっぽう弱いので夜明けを迎えるとかそういうのはないのですが、確か最初にGoogle Documents List APIでファイルアップロードに成功するまで、1日では終わらなかったと記憶しています。ホント、試行錯誤の連続を経過しないと、うまく受け付けてくれないんです。

そんなGoogle Documents List APIは、すでにDeprecated扱いになっていて、つまり
[廃止が決まっています](http://googleappsdeveloper.blogspot.jp/2012/09/retiring-google-documents-list-api-v3.html)。Google Driveへの任意のアプリからのファイルアップロードなど更新系の処理について、Google Documents List APIの廃止後にどうなってしまうのだろうか、Google Drive SDK経由、つまりGoogle Drive UIと統合されるアプリでないとできなくなってしまうのだろうか、と危惧していました。しかし、そんな心配は無用でした。Google Drive SDKとは別に、Google Drive APIと呼ばれる任意のアプリから利用可能なAPIセットがちゃんとリリースされています。このAPIを使って、Google Driveにファイルをアップロードすることが可能になっています。

しかも、Google Documents List API時代に「もう死ねばいいのに」と思わされたAtom文書の取り扱いを、もうやる必要はありません。
[Google API Ruby Client](http://code.google.com/p/google-api-ruby-client/)を使って、自分でプロトコルを話さなくてもAPIを利用することができるようになっています。もちろん、Google Drive APIもその対象です。

では、Ruby on RailsからGoogle Drive APIを叩くための手順を紹介していきましょう。まず、
[Google API Console](https://code.google.com/apis/console/)にアプリ登録をしてClient IDと Client secretを入手するところから始めます。もちろん認可方式として、みんな大好きOAuth 2.0を使います。All Servicesの中にDrive APIを見つけることができるはずです。これをONにします。

![](http://www.eisbahn.jp/yoichiro/images/2012/10/c5.png)

次に、API Accessの中で、OAuth 2.0クライアント登録を行います。ここでは、for Web applicationを選択しています。使用するのは、Client ID、Client secret、Redirect URIです。

![](http://www.eisbahn.jp/yoichiro/images/2012/10/c6.png)

これで必要なものは入手しました。Rubyコードの出番です。まず、Gemfileに追記して、Google Ruby API Clientを利用可能にしましょう。bundle installを忘れずに。

```
# To use Google API Client library
gem 'google-api-client'
```

次に、ユーザを認可画面に誘導しなければなりません。あ、ここから先のコードは、あえてわかりやすいように冗長に書いてます。

```
require 'google/api_client'

class GdriveController < ApplicationController
  CLIENT_ID = 'Client ID文字列'
  CLIENT_SECRET = 'Client secret文字列'
  REDIRECT_URI = 'リダイレクトURI文字列'
  OAUTH_SCOPE = 'https://www.googleapis.com/auth/drive'

def index
    session[:token] = params[:token]

client = Google::APIClient.new
    drive = client.discovered_api('drive', 'v2')
    client.authorization.client_id = CLIENT_ID
    client.authorization.client_secret = CLIENT_SECRET
    client.authorization.scope = OAUTH_SCOPE
    client.authorization.redirect_uri = REDIRECT_URI

uri = client.authorization.authorization_uri
    redirect_to uri.to_s
  end
```

Image Collector extension側のセッションを維持するために、セッショントークンをsessionの機構に乗せています。GoogleのOAuth 2.0での認可画面に背にするためのURLは、Google Ruby API Clientを早速使うことで入手できます。OAuth 2.0のGrant typeとしてAuthorization codeを使ったフローに必要となるパラメータをセットして、client.authorization.authorization_uriを呼び出すことでURLを得られます。ここにリダイレクトさせて、ユーザに認可画面を表示します。

![](http://www.eisbahn.jp/yoichiro/images/2012/10/c7.png)

ちなみに、'https://www.googleapis.com/auth/drive'がDrive APIのスコープになります。

認可画面でユーザが同意すると、Google側でリダイレクトURLにその名の通りリダイレクトされ、アプリケーションに戻ってきます。戻ってきた後の処理が以下です。

```
  def callback
    client = Google::APIClient.new
    client.authorization.client_id = CLIENT_ID
    client.authorization.client_secret = CLIENT_SECRET
    client.authorization.redirect_uri = REDIRECT_URI
    client.authorization.code = params[:code]
    token_info = client.authorization.fetch_access_token!
    token_info['issue_timestamp'] = Time.now
    Session.set_gdrive_session(session[:token], token_info)
```

先ほどと同じようにGoogle Ruby API Clientのインスタンスを作って、それに受け取ったAuthorization codeを渡します。そしてclient.authorization.fetch_access_token!()メソッドを呼び出して、リフレッシュトークンやアクセストークンをGoogleのサーバから取得します。簡単ですね。上記ではfetch_access_token()という「！」がつかないメソッドでも構わない状況です。アクセストークンを取得してそのままAPIのアクセスを継続する場合には「！」付きにします。

ここで、なぜclient.authorizationとなっているか気になった方がいるかもしれません。client.authorizationは、実は
[oauth-signet](http://code.google.com/p/oauth-signet/)ライブラリに含まれるSignet::OAuth2::Clientというクラスのインスタンスになります。Google Ruby API Clientは、OAuth関連の処理を入れ替えられるようになっていて、generate_authenticated_requestメッセージを受け取れるようにしてあれば、自作してそれに入れ替えることも可能です。実装上これは大きなメリットなのですが、Google Ruby API Clientのリファレンスを見てもclient.authorizationに対してできることが見えてこないのが最初きついです。OAuth系の処理をしたいときにはoauth-signetライブラリ側のリファレンスを見る、これを覚えておけばいいでしょう。

fetch_access_token!()メソッドの戻り値は、各種トークンを持つGoogleサーバからのレスポンスであるJSON文字列をRubyのハッシュにパースしたものになります。発行日時をそのパース済みJSONに追加しておいて、あとでトークンの失効を判断できるようにします。Session#set_gdrive_session()メソッドは、Image Collector extension側のセッションと各種トークンを持つハッシュを対応させてDBに保存するメソッドです。

APIを叩くためのアクセストークンは有効期間が短いため、リフレッシュトークンを使ってアクセストークンを再発行することが求められます。そのためのコードは以下のようになるでしょう。

```
token_info = Session.get_gdrive_session(token)
raise 'Not authorized' unless token_info
if Time.now > (token_info['issue_timestamp'] + token_info['expires_in'])
  client = Google::APIClient.new
  client.authorization.client_id = CLIENT_ID
  client.authorization.client_secret = CLIENT_SECRET
  client.authorization.update_token!(token_info)
  client.authorization.grant_type = 'refresh_token'
  token_info = client.authorization.fetch_access_token!
  token_info['issue_timestamp'] = Time.now
  Session.set_gdrive_session(token, token_info)
end
token_info
```

Session.get_gdrive_session()メソッドを使って、前回使った各種トークンの情報をデータベースから取得します。そして、記録しておいたトークンの発行日時とexpires_in値を足して、それが現在日時よりも小さかったら、つまりアクセストークンの有効期限が切れていたら、リフレッシュトークンを使ってアクセストークンを再発行します。これも、Google Ruby API Clientがサポートしてくれます。

前回fetch_access_token!()メソッドで取得した結果について、client.authorization.update!()メソッドにそれを渡すだけで、Google Ruby API Clientがそれを元にして機能するようになります。その後再度fetch_access_token!()メソッドを呼び出せばいいのですが、その前にgrant_typeを'refresh_token'に変更しておくことを忘れてはなりません。既にリフレッシュトークンを得ていて、それを使ってアクセストークンを再発行する場合にこれが必要になります。

やっと具体的なAPIへのアクセスです。まずはGoogle Driveにフォルダを作ってみましょう。

```
token_info = ... # さっき取得したやつ
client = Google::APIClient.new
client.authorization.update_token!(token_info)
drive = client.discovered_api('drive', 'v2')

folder =drive.files.insert.request_schema.new({
  'title' => 'フォルダのタイトル文字列',
  'description' => 'フォルダの説明文',
  'mimeType' => 'application/vnd.google-apps.folder'
})
result = client.execute(
  :api_method => drive.files.insert,
  :body_object => folder
)
parent_id = result.data.to_hash['id']
```

最新のアクセストークンを持つtoken_infoをclient.update_token!()メソッドに渡してGoogle Ruby API Clientをセットアップするのは先ほどと同じです。その後、Drive API特有の処理を行います。client.discovered_api()メソッドにAPI名とバージョンを渡して、APIの仕様を読み込みます。その結果を使って、フォルダを作るためのリクエスト内容を作っていきます。Google DriveのフォルダのMIME Typeは'application/vnd.google-apps.folder'です。これとフォルダ名などを元にして、drive.files.insertというAPIを叩きます。上記の例では、Google Driveのルート下にフォルダを作っています。

フォルダの作成に成功すると、その結果として作成されたフォルダの情報が返ってきます。その中にあるid属性値は、そのフォルダを指し示すためのIDになります。そのフォルダ内にファイルをアップロードする時に必要となりますので、取り出しておきます。

では、APIのたぐいで最も面倒な処理、ファイルアップロードをGoogle Driveに対して行ってみましょう。Image Collector extensionを例にして、画像をアップロードするためのコードが以下となります。

```
token_info = ... # さっき取得したやつ
client = Google::APIClient.new
client.authorization.update_token!(token_info)
drive = client.discovered_api('drive', 'v2')

file = drive.files.insert.request_schema.new({
  'title' => file_name,
  'description' => '',
  'parents' => [{
    'kind' => 'drive#fileLink',
    'id' => parent_id
  }]
})
media = Google::APIClient::UploadIO.new(file, 'image/*')
client.execute(:api_method => drive.files.insert,
                     :body_object => file,
                     :media => media,
                     :parameters => {
                       'uploadType' => 'multipart',
                       'alt' => 'json'
                     }
)
```

フォルダを作成するための処理と似ていますが、以下の違いがあります。

* リクエストの内容にparents属性を指定していること。

* Google::APIClient::UploadIOクラスを使っていること。

parents属性のidとして先ほど取得したparent_idを指定することで、ファイルアップロード先として特定のフォルダとすることができます。parentsを省略すると、Google Driveのルートにファイルが作られます。

Drive APIのファイルアップロードのプロトコルは、やはりmultipartなリクエストです。Google Ruby API Clientでは、アップロードしたいファイルの実体とその属性情報を同時に指定するために、実体はmedia、属性情報はbody_objectにそれぞれ指定します。mediaは、UploadIOクラスのインスタンスとなります。UploadIOクラスのコンストラクタに、オープン済みのアップロードしたいファイルのハンドルオブジェクトと、MIME Typeを指定します。あとは、parametersにmultipartであることを指示してclient.execute()メソッドを呼び出すことで、Google Driveにファイルがアップロードされます。自分でmultipartなリクエストを構築しなくて良い分、かなり楽です。

他のファイルの場合にはMIME Typeを返るなどするだけで、上記のコードは使い回せるはずです。ここまでくれば、以下のAPIリファレンスを見ながら、やりたいことをコーディングできると思います。

[API Reference - Google Drive SDK](https://developers.google.com/drive/v2/reference/)

Dropboxと違って、Drive APIを使うことに対する審査はありませんが、叩ける数に上限があります。もし人気が出てLimitに達してしまう場合は、Google API Consoleから上限アップをリクエストできますので、試してみてください。
