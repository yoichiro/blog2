---
layout: post
status: publish
published: true
title: Java PetStore 2.0ea1のインストール
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 230
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=230
date: '2006-07-17 18:20:19 +0900'
date_gmt: '2006-07-17 09:20:19 +0900'
categories:
- PetStore
---

前回の「
[Java PetStore 2.0ea1のための環境準備](http://www.eisbahn.jp/yoichiro/2006/07/java_petstore_20ea1_1.html)」エントリの内容で，NetBeansとGlassFishの準備は整っている。さて，いよいよJava PetStoreのインストールを行う。Java PetStoreは，NetBeansのプロジェクトとして作成されているので，子の環境であれば簡単に動作確認までもっていくことができる。
まずはJava PetStoreの入手から。
[PetStore](https://blueprints.dev.java.net/petstore/)のページから
[ダウンロードページ](https://blueprints.dev.java.net/servlets/ProjectDocumentList?folderID=5315&expandFolder=5315&folderID=0)に遷移する。ここにある「javapetstore-2.0-ea1-installer.jar」ファイルをダウンロードする。
このファイルは実行可能jarファイルなので，そのまま「 javapetstore-2.0-ea1-installer.jar」アイコンをダブルクリックし，ライセンス同意ダイアログで[Accept]ボタンを押すことで，javapetstore-2.0-ea1ディレクトリが作成される。このディレクトリを，適当な場所に配置する。
ではさっそくNetBeansに読み込ませてみる。NetBeansを起動し，GlassFishもスタートさせておく。そして，[File]-[Open Project]メニューを選択する。

![OpenProject.gif](http://www.eisbahn.jp/yoichiro/images/OpenProject.gif)
Open Projectダイアログで，javapetstore-2.0-ea1ディレクトリを選択し，[Open Project Folder]ボタンを押下する。

![SelectProject.gif](http://www.eisbahn.jp/yoichiro/images/SelectProject.gif)
読み込み処理後，Projectsウィンドウにpetstoreノードが出現する。このノードを展開すると，Web Pagesなどのいくつかのフォルダがあるのがわかるだろう。

![PetStoreProject.gif](http://www.eisbahn.jp/yoichiro/images/PetStoreProject.gif)
実行する前に，PetStore用のデータベースを作成する必要がある。データベース作成用のantビルドファイルが同梱されているので，手軽に作成することが可能だ。Filesウィンドウを開き，petstore/setup/setup.xmlファイルの上で右クリックを行い，Run Targetメニューからsetupターゲットを実行する。この際，GlassFishが起動していないと失敗してしまうので注意。

![PetStoreDBSetup.gif](http://www.eisbahn.jp/yoichiro/images/PetStoreDBSetup.gif)
正常に作成されれば，GlassFishのJDBCリソースとしてPetstoreDBが登録される。

![PetstoreDB.gif](http://www.eisbahn.jp/yoichiro/images/PetstoreDB.gif)
ここまで来れば，PetStoreを実行することができる。Projectsウィンドウのpetstoreノード上で右クリックし，Run Projectメニューを選択する。

![PetStoreStarting.gif](http://www.eisbahn.jp/yoichiro/images/PetStoreStarting.gif)
ビルドとデプロイが実行され，自動的にWebブラウザが起動して，PetStoreのトップページが表示される。

![PetStoreTop.gif](http://www.eisbahn.jp/yoichiro/images/PetStoreTop.gif)
派手な画面を期待していたので「あれ？」という感じだが，「Enter the Store」リンクをクリックすれば，期待したPetStoreのトップページが表示される。

![PetStoreTop2.gif](http://www.eisbahn.jp/yoichiro/images/PetStoreTop2.gif)
ペットの種類を左から選んでクリックすると，カタログページが表示されて，具体的にペットを選ぶことができる。かなりインタラクティブな操作感。さすがWeb2.0対応である。
あとは，NetBeans上でソースコードを修正などして，[Run Project]を繰り返すことで，PetStoreの仕組みを追っていくことができる。準備完了だ。
