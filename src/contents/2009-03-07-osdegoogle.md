---
layout: post
status: publish
published: true
title: OSDEプロジェクトをGoogleの各種サービスに移行しました
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 629
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=629
date: '2009-03-07 21:54:34 +0900'
date_gmt: '2009-03-07 12:54:34 +0900'
categories:
- OpenSocial
---

自宅サーバで環境を構築して開発を進めてきたOSDE(OpenSocial Development Environment)。このほど、Googleが提供する各種サービスに移行を行いました。
[移行前の環境]
・プロジェクトサイト(Wiki、Issue管理含む): 自宅サーバのTrac
・ソースコード: 自宅サーバのSubversion
・配布のためのUpdate-site: 自宅サーバのApache2
・コミュニティ: Google Groups
[移行後の環境]
・プロジェクトサイト(Wiki、Issue管理含む): Google Project Hosting
・ソースコード: Google Project Hosting
・配布のためのUpdate-site: Google Project Hosting
・コミュニティ: Google Groups
・機能要望: Google Moderator
Google Project Hosting

[http://code.google.com/p/opensocial-development-environment/](http://code.google.com/p/opensocial-development-environment/)
Google Groups

[http://groups.google.com/group/opensocial-development-environment](http://groups.google.com/group/opensocial-development-environment)
Google Moderator

[http://moderator.appspot.com/#16/e=29cfd](http://moderator.appspot.com/#16/e=29cfd)
ソースコードリポジトリの移行は、svnsyncを使って簡単に移行できました。管理していたIssueは、未解決の数が少なかったため、手動で登録。既存のTracはもう使用しないので、Apache2の設定に追記してProject Hostingのサイトにリダイレクトするようにしてあります。
そして、今回初めて使い始めたサービスが、Google Moderator。アイディアや質問などを簡単なフォームを使って登録でき、それに対して投票ができるというサービスです。OSDEの新規アイディアを受け付けてVoteをしてもらうには、うってつけのサービスです。どうしてもProject HostingのIssueでは、入力が大変になってしまうので。
ちょっと既存のOSDEユーザに申し訳ないのが、OSDEを配布するためのEclipse Update-siteを移行してしまったこと。今後はProject Hostingで配布することになるため、以下のURLで示されるUpdate-siteの登録を追加してもらうことが必要になります。

[http://opensocial-development-environment.googlecode.com/svn/update-site/site.xml](http://opensocial-development-environment.googlecode.com/svn/update-site/site.xml)
というわけで、これからもOSDEをよろしくお願いいたします。
