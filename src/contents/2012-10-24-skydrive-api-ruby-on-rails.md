---
layout: post
status: publish
published: true
title: SkyDrive APIをRuby on Railsから叩く方法
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1732
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1732
date: '2012-10-24 09:34:40 +0900'
date_gmt: '2012-10-24 00:34:40 +0900'
categories:
- Ruby on Rails
---

Image Collector extensionでは、Webページにある画像を一気にSkyDriveにアップロードする機能が備わっています。ただし、Chromeから直接SkyDriveにアップロードすることはできないので、僕の自宅で稼働しているRuby on Railsサーバアプリケーションがその役目を担っています。つまり、Chrome上で動作しているImage Collector extensionは「このURLの画像をSkyDriveにアップロードして！」と指示だけをサーバアプリケーションに投げていて、そのURLの画像の取得とSkyDriveへのアップロードはRubyコードによって行われているのです。

前のエントリでDropboxの場合を書きましたが、SkyDriveの場合、それはそれは面倒でした。公式なSDKが配られているわけでもなく、自分でプロトコルを話さなければなりません。認可はOAuth2.0なので慣れたものだったのですが、画像のアップロードは「恐怖のマルチパートリクエスト」を実装しなければなりませんでした。丸一日試行錯誤を繰り返した記憶があります。これを読んでいる人がそんなことをやらなくてもいいように、このエントリで僕が書いたコードを紹介しておきましょう。

SkyDrive APIはOAuth2.0ベースなので、まずはClient IDとClient secretを発行するところから始めます。そのために、まずは以下のサイトを訪れます。

[Live Connect デベロッパーセンター](http://msdn.microsoft.com/ja-jp/live/)

上部にある「マイ アプリ」リンクをクリックして、次のページにある「アプリケーションの作成」リンクをクリックします。

![](http://www.eisbahn.jp/yoichiro/images/2012/10/c11.png)

入力項目は「アプリケーション名」と「言語」のみ。シンプルです。

![](http://www.eisbahn.jp/yoichiro/images/2012/10/c21.png)

規約に同意すると、さくっとClient IDとClient secretが発行されます。

![](http://www.eisbahn.jp/yoichiro/images/2012/10/c31.png)

これだけでは実は足りません。リダイレクトURIを予め登録しておく必要があります。ここには、SkyDrive側で行われるユーザへの認可処理が終わった後に戻ってきて欲しいアプリケーション、サービスのURLを記述しておきます。

![](http://www.eisbahn.jp/yoichiro/images/2012/10/c41.png)

これで登録作業は終わりです。コードの紹介に移ります。まずはユーザを認可画面に誘導しなければなりません。

```
require 'cgi'
require 'uri'
require 'net/http'

class SdriveController < ApplicationController
  CLIENT_ID = 'Client ID文字列'
  CLIENT_SECRET = 'Client secret文字列'
  REDIRECT_URI = 'リダイレクトURI文字列'
  SCOPES = CGI::escape('wl.skydrive_update wl.offline_access')
  AUTHORIZATION_URL =
    "http://oauth.live.com/authorize?client_id=#{CLIENT_ID}&scope=#{SCOPES}&response_type=code&redirect_uri=#{REDIRECT_URI}"

def index
    session[:token] = params[:token]
    redirect_to AUTHORIZATION_URL
```

AUTHORIZATION_URLを見てわかる通り、OAuth 2.0です。スコープとして、wl.skydrive_updateとwl.offline_accessの2つを指定しています。前者はSkyDriveに関する更新系のAPIを使いたいため、後者はリフレッシュトークンを発行してもらうため、です。Image Collector extensionでのセッションを認可後にも維持したいため、ここではsessionの機構を使ってセッショントークンを引き継ぐようにしています。

ユーザの認可後にアプリケーションに戻ってきた時の処理は以下となります。ここから泥臭くなってくるのがすぐにわかるでしょう。エラー処理は省いてあります。また、ここではあえてわかりやすく冗長に書いてます。もちろん本当にImage Collector extensionのバックエンドで動いているコードはちゃんと最適化してありますので、ご指摘なく。

```
  def callback
    authorization_code = params[:code]
    uri = URI('https://oauth.live.com/token')
    https = Net::HTTP.new(uri.host, 443)
    https.use_ssl = true
    https.verify_mode = OpenSSL::SSL::VERIFY_PEER
    params = {
      'client_id' => CLIENT_ID,
      'client_secret' => CLIENT_SECRET,
      'redirect_uri' => REDIRECT_URI,
      'grant_type' => 'authorization_code',
      'code' => authorization_code
    }
    data = params.map {|k, v| "#{CGI::escape(k)}=#{CGI::escape(v)}"}.join('&')
    response = https.post(uri.path, data)
    token_info = ActiveSupport::JSON.decode(response.body)
    token_info['issue_timestamp'] = Time.now
    Session.set_sdrive_session(session[:token], token_info)
    ...
  end
```

認可画面からリダイレクトされる際に、authorization codeと呼ばれる値がクエリパラメータで渡されます。これとClient ID、Client secret、そしてリダイレクトURIを使って、アクセストークンやリフレッシュトークンをSkyDrive側で発行してもらいます。https://oauth.live.com/tokenに対してPOSTリクエストを送る際に、各種パラメータをリクエストボディに乗せます。正しく受理されれば、アクセストークンやリフレッシュトークン、有効期限などが書かれたJSON文字列が返ってきます。発行日時をそのパース済みJSONに追加しておいて、あとでトークンの失効を判断できるようにします。Session#set_sdrive_session()メソッドは、Image Collector extension側のセッションとJSON文字列を対応させてDBに保存するメソッドです。

ここまで来れば、あとはAPIを叩くのみなはずですが、もう一つ実装しておかなければならないことがあります。それは、アクセストークンが失効していた場合の再発行処理です。

```
  def self.get_access_token(token)
    token_info = Session.get_sdrive_session(token)
    raise 'Not authorized' unless token_info
    if Time.now > (token_info['issue_timestamp'] + token_info['expires_in'])
      token_info = self.refresh_access_token(token, token_info)
    end
    token_info['access_token']
  end

def self.refresh_access_token(token, token_info)
    uri = URI('https://oauth.live.com/token')
    https = Net::HTTP.new(uri.host, 443)
    https.use_ssl = true
    https.verify_mode = OpenSSL::SSL::VERIFY_PEER
    params = {
      'client_id' => CLIENT_ID,
      'client_secret' => CLIENT_SECRET,
      'refresh_token' => token_info['refresh_token'],
      'grant_type' => 'refresh_token'
    }
    data = params.map {|k, v| "#{CGI::escape(k)}=#{CGI::escape(v)}"}.join('&')
    response = https.post(uri.path, data)
    new_token_info = ActiveSupport::JSON.decode(response.body)
    new_token_info['issue_timestamp'] = Time.now
    new_token_info['refresh_token'] = token_info['refresh_token']
    Session.set_sdrive_session(token, new_token_info)
    new_token_info
  end
```

get_access_token()メソッド内では、引数で渡されたImage Collector extension側のセッションに割り当てられたSkyDriveのトークン情報をDBから取り出して、アクセストークンが失効していないかどうかを確認しています。先ほど追加しておいたissue_timestamp日時とexpires_in日時との比較を行っています。もしもう失効しちゃってるとなった場合は、refresh_access_token()メソッドを呼び出します。

refresh_access_token()メソッドで行われている処理は、ほとんど認可後に最初に行われるトークン発行処理と同じです。grant_typeパラメータが違っている点、authorization codeではなくリフレッシュトークンを渡している点、くらいが異なる箇所です。

APIのアクセスの度に、直前に上記の処理を行って、有効なアクセストークンを得られるようにしているというわけです。

ではやっとAPIを叩くことができます。まずはSkyDriveにフォルダを作成するための処理です。

```
  def self.create_folder(token, title)
    access_token = self.get_access_token(token)
    uri = URI('https://apis.live.net/v5.0/me/skydrive')
    https = Net::HTTP.new(uri.host, 443)
    https.use_ssl = true
    https.verify_mode = OpenSSL::SSL::VERIFY_PEER
    headers = {
      'Authorization' => "Bearer #{access_token}",
      'Content-Type' => 'application/json'
    }
    params = "{name:\"#{title}\"}"
    response = https.post(uri.path, params, headers)
    result = ActiveSupport::JSON.decode(response.body)
    parent_url = result['upload_location']
```

https://apis.live.net/v5.0/me/skydriveがSkyDrive APIのエンドポイントです。これに対して、{"name":"名称"}というJSON文字列をリクエストボディに持つPOSTリクエストを送信することで、フォルダが生成されます。レスポンスもJSON形式ですが、その中にupload_locationというプロパティ値があって、作成したフォルダにファイルをアップロードしたい場合は、そのプロパティ値がAPIのエンドポイントとなります。フォルダの登録はできてしまうと意外と簡単でした。

次のファイルアップロードがくせ者です。まずはコードを見てみましょう。引数のparent_urlは、先ほど取得したupload_location値、fileはオープン済みのアップロードしたいファイルのハンドルオブジェクト、nameはアップロードしたいファイルにつける名前です。

```
  def self.fetch_and_save(token, parent_url, file, name)
    access_token = self.get_access_token(token)
    target_url = "#{parent_url}?access_token=#{access_token}"
    uri = URI(target_url)
    https = Net::HTTP.new(uri.host, 443)
    https.use_ssl = true
    https.verify_mode = OpenSSL::SSL::VERIFY_PEER
    headers = {
      'Content-Type' => 'multipart/form-data; boundary=imagecollectorserver12345'
    }
    content = "--imagecollectorserver12345"
    content += "\r\n"
    content += "Content-Disposition: form-data; name=\"file\"; filename=\"#{name}\""
    content += "\r\n"
    content += "Content-Type: application/octet-stream"
    content += "\r\n\r\n"
    content += file.read
    content += "\r\n"
    content += "--imagecollectorserver12345--"
    https.post(uri.request_uri, content, headers)
```

泥臭い。うぇってなるほど泥臭い。いわゆるマルチパートなリクエストです。といっても、エントリは一つだけで、ファイルの実体をapplication/octet-streamで投げているだけです。ただし、ファイル名の指定がContent-Dispositionで書かれています。

実は、PUTによるもっとシンプルなアップロードのやり方も存在します。その場合は、ファイル名はエンドポイントのURLに含んでしまって、リクエストボディはファイルの実体のみを流し込めば良く、上記のやり方よりもコード量も少なくて済むはずです。では何故僕はわざわざ難しい方を選んだのか、今となってはさっぱり思い出せません。技術者のチャレンジ魂が火を噴いた結果だ、とでもしておきましょう。

Image Collector extensionでやっていることは以上となりますが、SkyDrive API自体はもっと多くの機能を提供してくれています。

[SkyDrive API](http://msdn.microsoft.com/ja-jp/library/live/hh826521.aspx)

Dropboxと違い、SkyDriveに関しては審査などの手順はありません。上記のコードを参考にして、ぜひあなたのサービス、アプリケーションとSkyDriveを連携してみてください。
