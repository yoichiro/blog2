---
layout: post
status: publish
published: true
title: Align Portable Contacts and REST (0.9)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 642
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=642
date: '2009-05-11 20:52:26 +0900'
date_gmt: '2009-05-11 11:52:26 +0900'
categories:
- OpenSocial
---

OpenSocial v0.9において追加された仕様「Align Portable Contacts and REST」について紹介しましょう。この仕様は、OpenSocialとPortable Contactsの互換性について語られたものです。
---
[Align Portable Contacts and REST]
Portable Contactsは、アドレス帳やフレンドリストなどにアクセスするための安全な方法をユーザに提供することを開発者が容易に行えるように、とデザインされた仕様です。特に、いかなるサイトで提供可能な、共通のアクセスパターンやコンタクトスキーマ、よく知られた認証やアクセスルール、いかなるサイトでも対応可能な標準ライブラリ、そして絶対的な最小複雑性などを、最も軽量な形で可能とするツールチェインを開発者に要求する形でもたらします。
OpenSocial v0.9では、Portable Contacts仕様とOpenSocial RESTful API仕様において、それらの互換性を確立するための調整が行われました。本仕様追加において、RESTful APIに以下の章が追加されます。これと同時に、Portable Contacts仕様側でも、OpenSocialとの互換性に関する記述が追加されています。
---
Compatibility with Portable Contacts
This version of the OpenSocial RESTful protocol specification is currently wire-compatible with the 
[Portable Contacts version 1.0](http://portablecontacts.net/draft-spec.html) overlapping portion of OpenSocial. Specifically, any compliant OpenSocial 0.9 People Provider is also a compliante Portable Contacts 1.0 Provider, because they are specified to use the same Authorization methods (OAuth), Additional Path Information, Query Parameters, and Contact Schema. The OpenSocial and Portable Contacts communities chose to wire-align our respective specs in order to maximize widespread adoption of a single API for accessing people data. It is our intention to maintain this compatibility going forward, so long as it is feasible, and so long as the changes required are compatible with the Goals and Approach of this spec. Although Portable Contacts is an independent spec, with a more limited scope than OpenSocial, any proposed changes to either this OpenSocial RESTful Protocol spec or the Portable Contacts spec should be considered in the context of both communities, and we should strive not to break compatibility unless it is truly necessary, e.g. if the goals of the two communities diverge significantly in the future.
---
Portale Contactsとの互換性
OpenSocial RESTful protocol仕様のこのバージョンでは、現在OpenSocialの一部分をオーバーラッピングする形で
[Portable Contacts version 1.0](http://portablecontacts.net/draft-spec.html)と硬い互換性があります。特に、いかなるOpenSocial 0.9に準拠したPeople Providerは、Portable Contacts 1.o Providerにもなり得ます。なぜなら、それらは同じ認証メソッド(OAuth)、追加パス情報、クエリパラメータ、そしてコンタクトスキーマを使うように指定されているからです。OpenSocialとPortable Contactsコミュニティは、Peopleデータにアクセスするための単一のAPIが最大限の広さで採用されるために、私たちのそれぞれの仕様を硬く調整することを選択しました。それは、将来的にこれらの仕様をとても長く実現可能にし、そしてこの仕様のゴールやアプローチについて要求される変更を互換とするための、この互換性を維持していくことについての我々の意志です。Portable Contactsは独立した仕様であり、OpenSocialよりもより限定されたスコープではありますが、このOpenSocial RESTful Protocol仕様やPortable Contacts仕様のどちらにいかなる変更の提案がされたとしても、両方のコミュニティにおけるコンテキストにおいて考慮されるべきであり、そして我々は、例えば将来有意に2つのコミュニティのゴールがそれぞれ枝分かれするなどのことが真に必要とされるまでは、互換性が壊されないように努力すべきです。
---
Portable Contacts仕様との互換性を図るために調整された結果のXML Schema文書は、「Resolve schema with Portable Contacts」という別の仕様にて定義されます。
