---
layout: post
status: publish
published: true
title: Dropbox APIをRuby on Railsから叩く方法
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1708
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1708
date: '2012-10-23 09:35:02 +0900'
date_gmt: '2012-10-23 00:35:02 +0900'
categories:
- Ruby on Rails
---

Image Collector extensionを使うことで、Webページ上にある画像をDropboxにアップロードすることができます。この際、Chrome extensionから直接Dropboxに上げることはできませんので、僕の自宅サーバで動いているRuby on Railsアプリがその処理を代行するようになっています。つまり、Chrome extensionからは「このURLとこのURLの画像を取得して、それをDropboxにアップロードしておいて」とお願いするだけです。

その処理の作り方はとっても簡単です。なぜなら、DropboxがSDKを提供してくれているからです。ここでは、その使い方をちょっと紹介してみます。

まず、Ruby on Railsアプリを新規に作り、Gemfileに以下のように記述します。

```
gem 'dropbox-sdk'
```

その後"bundle install"することで、DropboxのAPIをRubyで叩くことができるようになります。

Dropbox APIを利用するためには、DropboxのDevelopersサイトにてApp keyおよびApp secretと呼ばれる値を入手する必要があります。Dropbox APIはOAuth1が採用されていて、これらはClient IDおよびClient secretに相当します。まず、Dropbox Developersサイトにいきます。

[https://www.dropbox.com/developers/apps](https://www.dropbox.com/developers/apps)

次に、[Create an app]ボタンをクリックします。

![](http://www.eisbahn.jp/yoichiro/images/2012/10/c1.png)

次に、アプリケーション名およびその説明文を入れます。これは実際にAPIアクセスに関する認可をユーザに求める際に、その画面に表示されるものになります。

![](http://www.eisbahn.jp/yoichiro/images/2012/10/c2.png)

Access levelは重要です。App folderを選択した場合は、あなたのアプリケーションはユーザのAppsフォルダ下しかアクセスできません。Image Collector extensionではこれで十分です。もしあなたがDropboxで共有される全てのファイルに対してアクセスしたいアプリケーションを作る場合には、Full Dropboxを選択すべきでしょう。

作成後、App keyとApp secretが発行されます。

![](http://www.eisbahn.jp/yoichiro/images/2012/10/c3.png)

ここで、App statusがDevelopmentになってますが、実際にこのApp keyを使った認可を広く使えるようにするためには、Dropboxの審査を通過する必要があります。僕がImage Collector extensionで申請した時には、確か2、3日で結果がきました。もしかしたらFull Dropboxの場合はもう少し日数がかかるかもしれません。

では、Rubyのコードです。まずはユーザを認可画面に誘導しなければなりません。その際のコントローラクラスは以下のようになるでしょう。

```
require 'dropbox_sdk'

class DropboxController < ApplicationController
  APP_KEY = 'App key文字列'
  APP_SECRET = 'App secret文字列'

def index
    dbsession = DropboxSession.new(APP_KEY, APP_SECRET)
    dbsession.get_request_token()
    session[:token] = params[:token]
    session[:dropbox_session] = dbsession.serialize()
    url = dbsession.get_authorize_url(url_for(action: 'callback'))
    redirect_to url
  end
```

Dropbox SDKはDropboxSessionを使うところから始まります。これのインスタンスをApp keyとApp secretを使って生成し、まずはリクエストトークンを取得します。ユーザに表示する認可画面のURLはget_authorize_url()メソッドを使って得られますので、このURLにリダイレクトします。このget_authorize_url()メソッドには、認可後に戻ってきたいURLを指定します。

この際、元々アクセスしてきたユーザは「Image Collector extensionとして誰か」をDropboxの認可処理を超えて継続してあげなければなりません。上記では、sessionの機構を使ってそれを実現しています。特徴的なのは、DropboxSessionのインスタンスのserialize()メソッドを使っているところでしょう。これによって、リクエストトークンを得た時点での情報を文字列化してあげて、sessionの機構に乗せてあげています。これはとても楽です。

リダイレクトされたあと、ユーザに認可画面が表示されます。場合によっては、ログイン画面から表示されます。

![](http://www.eisbahn.jp/yoichiro/images/2012/10/c4.png)

ユーザが同意した場合、get_authorize_url()メソッドにて指定したURLにDropbox側からリダイレクトしてくれます。それを待ち受けるコードは以下のようになります。

```
  def callback
    dbsession = DropboxSession.deserialize(session[:dropbox_session])
    dbsession.get_access_token()
    Session.set_dropbox_session(session[:token], dbsession.serialize())
    ...
  end
```

まず、シリアライズしてsessionに入れておいたDropboxSessionのインスタンスをdeserialize()メソッドにより取り戻します。そして、get_access_token()メソッドを呼び出して、APIアクセスに必要なアクセストークンを取得します。とても簡単ですね。上記では、自作したSessionクラスのset_dropbox_session()メソッドに対して、トークン値とDropboxSessionのシリアライズ結果を渡しています。これはDropboxへのアクセスに必要な情報をデータベースに入れている処理となります。

ここまで来たら、あとは使いたいAPIを叩けます。

まず、APIにアクセスするためのDropboxClientのインスタンスを生成します。これはDropboxSessionを元に作ることができます。

```
dbsession = DropboxSession.deserialize(...)
client = DropboxClient.new(dbsession)
```

そして、フォルダを作成したい場合は、file_create_folder()メソッドにパスを渡します。

```
path = 'foo/bar'
client.file_create_folder(path)
```

ファイルをアップロードしたい場合は、put_file()メソッドを使います。

```
file = ...
name = 'foo/bar/hoge.txt'
client.put_file(name, file, true)
```

第1引数はアップロードするファイルの名前とアップロード場所を指定します。第2引数はアップロードするファイルの実体。ローカルにある何らかのファイルをオープンして、それを指定すればよろしくそのファイルをアップロードしてくれます。第3引数は同名のファイルが既にあったときに上書きするかどうかです。trueだと上書き、falseだとかぶらないようにうまく違うファイル名をつけてくれます。

これらの操作の時に、DropboxAuthError例外やDropboxError例外が発生することもありますので、それぞれ対処するコードを記述しておくのを忘れずに。

一般的にファイルのアップロードに関するAPIは、大抵multipart requestを作らなければならないなど、すごーーく面倒な印象があると思いますが、Dropbox SDKはそれらを完全に隠蔽してくれていて、使いやすいインタフェースを提供してくれています。Dropbox SDKのAPIリファレンスは以下にて公開されています。クラス名、メソッド名を見れば、だいたいどんなことができるのかわかると思います。

[https://www.dropbox.com/static/developers/dropbox-ruby-sdk-1.5.1-docs/index.html](https://www.dropbox.com/static/developers/dropbox-ruby-sdk-1.5.1-docs/index.html)

ぜひあなたもDropboxをインテグレートしたサービス、アプリケーションを作ってみてください。
