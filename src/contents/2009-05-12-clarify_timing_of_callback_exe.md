---
layout: post
status: publish
published: true
title: Clarify timing of callback execution in JS API (0.9)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 644
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=644
date: '2009-05-12 21:03:30 +0900'
date_gmt: '2009-05-12 12:03:30 +0900'
categories:
- OpenSocial
---

OpenSocial v0.9にて追加となった「Clarify timing of callback execution in JS API」仕様について紹介します。コールバック関数の呼び出しに関する規定についてですが、このようなちょっとしたことでも、OpenSocialアプリケーション開発者にとっては非常に重要です。
---
[Clarify timing of callback execution in JS API]
今までのOpenSocial APIでは、コールバック関数が呼び出されるタイミングについて、実装がはっきりしないという問題がありました。
例えば、requestPermission(permissions, reason, opt_callback)関数のケースを考えてみましょう。この関数の実装をサポートしないコンテナは、'not implemented'という結果でopt_callback関数を同期的に呼び出します。一方、この関数を解決するためにXHRを使って実装が行われる場合、そのコールバック関数は非同期的に呼び出されるでしょう。これは、コールバック関数の実行のタイミングにおいて、ガジェットはコンテナの実装依存を気にしなければならないことを意味しています。全てのコールバック関数の呼び出しが非同期的に行われることを保証することで、開発者はクロスコンテナでの振る舞いの違いを意識する必要がなくなるでしょう。
本仕様では、コールバック関数の呼び出しを伴う全てのAPIについて、以下の文章を追加することで、コールバック関数が非同期的に呼び出されることを保証します。
The callback function will not be called until after the existing callstack has completed execution.
(このコールバック関数は、既存のコールスタックが完全に実行された後までは呼び出されることはないでしょう。)
本仕様に関しては、window.setTimeout(
, 0); を使うことで、手間をかけることなく達成することができるでしょう。
