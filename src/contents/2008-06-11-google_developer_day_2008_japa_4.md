---
layout: post
status: publish
published: true
title: Google Developer Day 2008 Japan [OpenSocial]
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 552
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=552
date: '2008-06-11 19:11:28 +0900'
date_gmt: '2008-06-11 10:11:28 +0900'
categories:
- OpenSocial
---

OpenSocialに関するセッションは、Googleエンジニアの向井さんによる発表。まずはデモンストレーションとして、脳内メーカー for OpenSocial(デベロッパー交流会第５回で発表されたもの)を取り上げ、会員情報が使われるというOpenSocialの特徴をデモで見せるところから始まった。
OpenSocialは既にGoogleの手から離れていて、仕様策定はOpenSocial.orgによりオープンに行われているとのこと。実際に、この前発表されたVersion 0.8の仕様は、Google Codeのサイトではなく、OpenSocial.orgのサイトに先に掲載された。このことからも、オープン性を実際に見ることができる。
OpenSocialの重要性として、
・開発者：2億人以上の潜在的なユーザの存在
・コンテナ提供者：新しいSNSアプリの移植性
・アプリ利用者：より多くのアプリを利用可能
というアクター毎の利点が説明された。そして、HTML + JavaScript + REST + OAuthという標準規格ベースでアプリケーションが構成される点も重要。
OpenSocialの基本は、People&Friends、Activities、そしてPersistenceの３つ。それぞれのコードサンプルを示しながら、3つの取り扱い方法が示された。
OpenSocialアプリケーションは、Version 0.7まではJavaScript + HTMLという組み合わせだったが、Version 0.8になってRESTが仕様に追加された。これはかなり大きな話で、OpenSocialコンテナ内でのみ動作可能だったVersion 0.7に比べて、Version 0.8からはRESTを使ったサーバサイドアプリケーションも開発可能になる。つまり、JavaScriptが動かない携帯向けアプリケーションも、サーバサイドでRESTによりOpenSocialコンテナと会話して情報を取得するなどして開発が可能になるということ。
REST APIに関しては、Shindigを使ったデモンストレーションが行われた。URLを見る限り、クリスの個人的なサイトの模様。でも、Shindigを使って、今でもRESTを試すことができるというのが示されたのは大きい。
OpenSocialの基本概念と、RESTに関する説明が丁寧に行われた本セッションは、OpenSocialを知らなかった人もきっとどんなものなのかを把握できたのではないだろうか。
ここから実際にやり取りされたQ&Aを紹介してみよう。
REST APIはサーバ上で呼び出すことと紹介されていたが、クライアント(=Webブラウザ)から呼び出すことは可能か？仕様上問題ないのか？
　　→問題ない。
OpenSocialで作ったアプリケーションは、他のサーバと通信ができるのか？
　　→できる。
他のサーバと通信ができるとした場合、友達情報などがSNS外部に漏れるのは問題ないのか？
　　→OAuthで取得範囲をユーザが決定することになるので問題ないはず。
OAuthで認可が行われる粒度(何が取得できて何が取得できないのか)はどうなるのか？
　　→仕様としてはそこまで踏み込んでいないので、コンテナ依存。
REST APIとJavaScript APIは等価なのか？
　　→現在は等価だし、今後も等価になるだろう。
Apache Shindigを作った理由と利用用途は？
　　→Googleが作ったものではないので、Shindig作者に聞いて欲しい。利用用途としてはOpenSocialコンテナを手軽に提供するための実装と考える。
OpenIDとOAuthは親和性が高い。同時に使うことができるのか？使うためにはどうしたらいいか？
　　→OpenSocial自体は、OpenIDに依存する箇所はない。
REST APIに{guid}という記述があったが、OpenIDの場合IDがURLになるため、URLが{guid}に入っても大丈夫なのか？
　　→{guid}の部分には、コンテナ依存の文字列が入る。
