---
layout: post
status: publish
published: true
title: mixiへの認証を行ってくれる「OmniAuth-mixi」を作ってみました
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1966
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1966
date: '2012-11-24 22:50:52 +0900'
date_gmt: '2012-11-24 13:50:52 +0900'
categories:
- Ruby on Rails
---

Ruby on RailsやSinatraで作るWebアプリケーションに各種認証機能を手軽に追加できる機構として、「
[OmniAuthシリーズ](https://github.com/intridea/omniauth)」があります。これのmixi版を作ってみました。

[OmniAuth-mixi](http://goo.gl/tMJAG) - Github

OmniAuthは、Strategyという形式で任意の認証プロバイダの実装を追加できるようになっています。OmniAuth-mixiを使うことで、OAuth2にてmixi Graph APIの認証認可を行い、People APIで認可ユーザのプロフィール情報を取得する、という処理を行ってくれます。

使い方は簡単です。Ruby on Railsの場合、まずGemfileに以下を追記します。

```
gem 'omniauth-mixi', git => 'https://github.com/yoichiro/omniauth-mixi.git'
```

次に、config/initializers/omniauth.rbファイルに以下を追記します。

```
Rails.application.config.middleware.use OmniAuth::Builder do
  provider :mixi, 'consumer_key', 'consumer_secret'
end
```

consumer_key、consumer_secretは、もちろんそれぞれmixiから発行されたもので置き換えます。準備はこれで完了です。簡単ですね！

実際のアプリケーション内でmixiのユーザ認証したくなった時には、mixi Graph APIの認証認可画面にリダイレクトさせます。と言っても、直接mixiの画面に飛ばすわけではなく、OmniAuth-mixiが提供してくれているリダイレクト先「/auth/mixi」に飛ばします。

```
redirect '/auth/mixi'
```

mixiの認証認可画面から戻ってくるときには、「/auth/mixi/callback」が呼び出されるようにしておきます。具体的には、mixiへのサービス登録時に、redirect_uriとしてこのパスが呼び出されるように登録しておきます。具体的なコントローラにマッピングするために、routes.rbファイルに以下を記述します。

```
match '/auth/mixi/callback', to: 'sessions#create'
```

コントローラの中から、「request.env['omniauth.auth']」とアクセスすることで、認証結果を得ることができます。例えばsessions#createに対応するコントローラとして、以下のようなコードになるでしょう。

```
class SessionsController < ApplicationController
  def create
    auth_hash = request.env['omniauth.auth']
    user_id = auth_hash['uid']
    nickname = auth_hash['user_info']['nickname']
    image_url = auth_hash['user_info']['image']
    # do something...
  end
end
```

上記のコードはmixiに特化していますが、:providerやauth_hash['provider']を使うことで、mixiだけでなく他の認証プロバイダにも対応することができるようになるでしょう。

今後、rubygemsへの正式登録や、
[omniauth.org](http://www.omniauth.org/)への掲載を目指していきたいと思ってます。

ぜひお試しください。
