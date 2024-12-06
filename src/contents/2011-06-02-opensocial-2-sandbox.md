---
layout: post
status: publish
published: true
title: "サンドボックスで遊びましょう！"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 997
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=997
date: '2011-06-02 09:27:46 +0900'
date_gmt: '2011-06-02 00:27:46 +0900'
categories:
- OpenSocial
---

これは、OpenSocial Blogに投稿された「
[Go play in the sandbox!](http://goo.gl/h0gWE)」の和訳です。

---

OpenSocial 2.0 Containerが全ての人にオープンされました!

5月12日に行われたOpenSocial State of the Unionにおいて、私たち(Andrew Davis(IBM)、Ryan Baxter(IBM))は、アプリケーション開発者に対してOpenSocial 2.0 仕様に対するアプリケーションをテストするためサンドボックス環境（
[http://opensocial2.org:8080/collabapp/index.html](http://opensocial2.org:8080/collabapp/index.html)）をビルドした、とアナウンスしました。

私たちは、activity stream、inbox、そして任意のガジェットをレンダリングするためのエリアを含むサンプルのコラボレーションアプリケーションを構築しました。そのサンドボックスは、OpenSocial 2.0の参照実装であるShindig 3.0のデイリービルドがベースとなっています。OpenSocial 2.0コンテナ上で動作するアプリケーションを開発することに興味を持っているガジェット開発者は、そのサンドボックスに行ってテストしたい任意のアプリケーションを追加することが可能です。

将来のプラン

私たちはそのサンドボックスに磨きをかけ、さらに完成されたものにするために仕事を続けることを予定しています。コラボレーションアプリケーションの中のソーシャルデータはとても豊富とは言えませんし、開発者が彼らのアプリケーションをよりテスト可能とするために、より完成されたセットを含みたいと考えています。もしアイディアがあれば、ぜひ私たちに知らせてください（
[https://groups.google.com/group/opensocial-and-gadgets-spec?hl=en](https://groups.google.com/group/opensocial-and-gadgets-spec?hl=en)）。あなたが貢献できることは多くあるのです。

inboxやactivity streamの中のデータは静的であり、その方法は依然として未実装です。しかし、開発者が彼ら自身のアクティビティや電子メールを与えられたセッションに追加できるようにするために、彼らは機能を追加することを計画しています。これはEmbedded experiencesを使ったOpenSocialアプリケーションをテストするためのキーとなるでしょう。また、コンテナに実装されていないOpenSocial仕様の一部がいくつかまだあります。たとえば、Gadget preferences、Pubsub2、そしてgadget.window.setTitleのようなガジェットコンテナAPIです。

数週間、数ヶ月と、サンドボックスをより完成されたものにするために、私たちは仕様から未実装な部分を実装する作業に従事することでしょう。また、私たちは、Shindigのデイリービルドがそのサーバに自動的にデプロイされるように、Shindigコミュニティに手を差し伸べました。これは、アプリケーション開発者に、最新のOpenSocialプロポーサルに対するガジェットを開発することを可能にします。いくつかのポイントにおいて、そのアプリケーションはShindigの一部分になり、仕様の進化のために、サンドボックスの強化を続けることをコミュニティに可能とするでしょう。

私たちは、あなたからのフィードバックを聞きたいと考えています。ぜひあなたのアイディアをSpecification's Googleグループ（
[https://groups.google.com/group/opensocial-and-gadgets-spec?hl=en](https://groups.google.com/group/opensocial-and-gadgets-spec?hl=en)）にポストしてください。
