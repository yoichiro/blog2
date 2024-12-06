---
layout: post
status: publish
published: true
title: Align JS API and REST (0.9)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 641
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=641
date: '2009-05-10 20:24:17 +0900'
date_gmt: '2009-05-10 11:24:17 +0900'
categories:
- OpenSocial
---

OpenSocial v0.9において追加された仕様「Align JS API and REST」について紹介しましょう。
---
[Align JS API and REST]
OpenSocial JavaScript APIでは、会員情報を表すためのいくつかのクラスが規定されています。その中で、住所を表すクラスとして、opensocial.Addressクラスがあります。Addressクラスには、緯度経度を表すフィールドや、住所の種別（自宅、勤務先など）のフィールドが定義されています。
opensocial.Address.Field.LATITUDE - 緯度(number型)
opensocial.Address.Field.LONGITUDE - 経度(number型)
opensocial.Address.Field.TYPE - 種別(string型)
JavaScript APIで規定されているフィールドは、基本的にRESTful APIやJSON-RPC APIにおいても同様に定義されている必要があります。しかし、v0.8.1でのRESTful APIには、上記3つのフィールドが規定されていませんでした。v0.8.1にてJavaScript APIは、Portable Contactsへの準拠に伴って調整されましたが、v0.9では本仕様において、上記3つのフィールドを追加します。
REST Specification: 更新内容
11.1.4 address element
追加:
* LATITUDE
* LONGITUDE
* TYPE
これにより、v0.9ではRESTful APIにおいても、住所に関して緯度経度や種別を扱えるようになります。
