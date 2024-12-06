---
layout: post
status: publish
published: true
title: OmniAuth-mixiとjpmobileを組み合わせてガラケー対応する方法
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1970
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1970
date: '2012-11-24 23:13:29 +0900'
date_gmt: '2012-11-24 14:13:29 +0900'
categories:
- Ruby on Rails
---

RubyのWebアプリでmixiの認証機能を手軽に組み込める「
[OmniAuth-mixi](http://www.eisbahn.jp/yoichiro/2012/11/omniauth-mixi.html)」ですが、標準の機能でもdisplayクエリパラメータを指定することで、スマフォ対応することが可能です。

```
redirect '/auth/mixi?display=touch'
```

でも、ガラケー端末向けには、ちょっと工夫が必要になります。これは、mixiのOAuth2対応では、PC＆スマフォの認可画面と、ガラケー向けの認可画面とで、URLが異なるという事情から来ています。例えばjpmobileを使っている場合であれば、jpmobileの機能を使ってガラケーからのリクエストかどうかを判断して、認可画面の飛び先を変えるというテクニックを使うことで対応できます。

```
Rails.application.config.middleware.use OmniAuth::Builder do
  provider :mixi, ENV['CLIENT_ID'], ENV['CLIENT_SECRET'],
           :setup => lambda {|env|
             request = Rack::Request.new(env)
             env['omniauth.strategy'].options[:client_options][:authorize_url] =
               'http://m.mixi.jp/connect_authorize.pl' if request.mobile?
           }
end
```

もしjpmobileを使ってガラケーにも対応したいとお考えの方は、ぜひ上記を試してください。
