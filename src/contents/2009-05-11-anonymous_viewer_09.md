---
layout: post
status: publish
published: true
title: Anonymous Viewer (0.9)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 643
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=643
date: '2009-05-11 21:12:20 +0900'
date_gmt: '2009-05-11 12:12:20 +0900'
categories:
- OpenSocial
---

OpenSocial v0.9において追加された仕様「Anonymous Viewer」について紹介しましょう。この仕様は、Viewerが特定されない、あるいはViewerの情報を取得する権限がない場合に、OpenSocialアプリケーションがViewer情報をどのように得るべきか、を示したものです。もちろんコンテナによっては、Viewer情報を取得しようとした際に、この仕様の内容ではなく、エラーを返す場合もあります。
---
[Anonymous Viewer]
OpenSocialでは、従来から匿名のViewerをサポートしていました。しかし、特に匿名のViewerのPerson IDについて、標準的なレスポンスはありませんでした。コンテナは匿名ユーザのIDをコンテナ自身の表記で実装していました。
アプリケーションが匿名ユーザであることを知ることや区別することが重要であるとする時に、異なるコンテナにおいて、様々なレスポンスをハンドルする必要があります。もし仕様としてこのレスポンスが標準化されれば、クロスコンテナアプリケーション開発は容易になり、異なるコンテナでの様々な振る舞いを学び、知る必要がなくなります。
標準的なレスポンスのために、いくかの可能性があります。以下はいくつかのコンテナでそのような表記法として既に実装されていたものを考慮した提案です。
匿名のViewerに対するレスポンスを標準化するために:

* コンテナは匿名のViewerのIDとして(-1)を返さなければなりません。

* コンテナはユーザフレンドリーな説明としてNAMEやNICKNAMEを返すことを選択するかもしれません（例: Guest、Anonymous、Unauthenticatedなど）。これはDisplayNameが空になるのを防ぎます。

* コンテナはまた、匿名ユーザのための標準的な代理画像へのポイントをTHUMBNAIL_URLとして返すかもしれません。
という振る舞いをコンテナは実装します。
