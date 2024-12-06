---
layout: post
status: publish
published: true
title: maven2のsiteゴールによる標準Webページの出力
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 320
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=320
date: '2007-02-15 09:05:13 +0900'
date_gmt: '2007-02-15 00:05:13 +0900'
categories:
- Maven
---

ソフトウェアを構成するファイル群やそれらに対するコンパイルやパッケージング，依存関係などを管理してくれるmaven2。プロジェクトに存在するさまざまな情報や処理結果をチームで共有することが開発に必要なのは明らかであり，多くの場合はそのプロジェクト用にWebページを構築して情報をチームメンバーに公開することが行われる。しかし，Webページの更新作業について，手作業ではなかなか難しく，ついつい更新が滞ってしまうことが多くのプロジェクトで見られる。アジャイルやイテレーティブな開発プロセスを採用している場合は，更新が滞ってしまうと致命的なミスを高い確率で招いてしまうだろう。
maven2では，プロジェクトの情報や，単体テスト結果やカバレッジ結果，JavadocのWebページへの公開などについて，自動でWebページを作成するゴールが標準で備わっている。それがsiteゴールだ。
maven2で管理されているプロジェクトであれば，何も考えずにsiteゴールを実行するだけで，標準的なWebページを出力してくれる。

>mvn site

作成されたWebページは，target/siteディレクトリに出力されている。この中のindex.htmlファイルをWebブラウザで表示すれば，以下のようなWebページが表示されるはずである。

![maven-site-std.jpg](http://www.eisbahn.jp/yoichiro/images/maven-site-std.jpg)
使用したpom.xmlファイルの記載がほとんどなされていないため（descriptionタグすら書いていない），非常に殺風景なページである。本来pom.xmlの中に，チームの構成やメーリングリスト，ライセンス関連やソースコードのリポジトリなどの情報が記述されていれば，それらが出力されるWebページに反映される。もちろん，対象のプロジェクトが必要とする依存ライブラリの情報に関しても，しっかりとWebページに反映される。標準で出力してくれるWebページを以下に示しておこう。

* About - プロジェクトの概要。descriptionタグの内容が反映される。

* Continus Integration - 継続的統合に関する情報。

* Dependencies - 依存ライブラリの情報。dependenciesタグの内容が反映される。

* Issue Tracking - 問題解決のための問題管理システムに関する情報。issueManagementタグの内容が反映される。

* Mailing Lists - メーリングリストの情報。mailingListsタグの内容が反映される。

* Project License - ライセンスの情報。licensesタグの内容が反映される。

* Project Summary - プロジェクトの基本的なサマリー情報。プロジェクト名やホームページ，組織の情報やビルド情報など。それぞれname，url，organization，buildタグなどの内容が反映される。

* Project Team - プロジェクトのチームの情報。developersタグなどの情報が反映される。

* Source Repository - ソースコード管理に関する情報。scmタグの内容が反映される。
これだけでも非常に有用な情報である。maven2を使用し，適切にpom.xmlが記述されていれば，プロジェクトの概要を第3者に説明するためのWebページは，いとも簡単に作成することができてしまう。
しかし，これだけではプロジェクトメンバーに十分な情報が共有されるとは思えないだろう。不十分な点として，以下があげられる。

* 単体テスト結果やカバレッジ測定結果，JavadocやCheckstyleなどの実行結果といった各種レポートが含まれていない。

* pom.xmlファイルに記述可能な定型的なものではなく，プロジェクト固有の情報を掲載する自由なページを追加したい。
上記2つの点についても，maven2のsiteゴールはきちんと対応してくれる。しかも，かなり手軽にそれを実現することができる。
次のエントリからは，maven2のsiteゴールを有効に利用するための上記2つの実現方法について紹介していこうと思っている。
