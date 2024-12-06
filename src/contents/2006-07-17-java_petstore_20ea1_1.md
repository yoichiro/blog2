---
layout: post
status: publish
published: true
title: Java PetStore 2.0ea1のための環境準備
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 229
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=229
date: '2006-07-17 09:04:31 +0900'
date_gmt: '2006-07-17 00:04:31 +0900'
categories:
- PetStore
---

最近Java PetStoreの2.0ea1を探り始めている。これはSunの今後のアプリケーション構築方針の具体例であり，J2EEアプリケーション開発従事者であれば，抑えておかなければならないコードだろう。完全に時代の最先端に乗り遅れている感が否めないので，その近道としてWeb2.0対応PetStoreをしばらくは見ていく。
ここでは，PetStoreを追っていくための環境の構築について，手順を示してみようと思う。ちなみに，Mac前提だ。
PetStoreはSun純正なので，やっぱりNetBeansで追っていくのが最適だろう。NetBeans5.5のβ版とGlassFishという組み合わせを今回は選択した。NetBeansはここの
[ダウンロードページ](http://www.netbeans.info/downloads/download.php?type=5.5b)から，MacOS Xと言語(英語のみだけど)を選んでダウンロードする。GlassFishはここの
[ダウンロードページ](https://glassfish.dev.java.net/public/downloadsindex.html)から「
[V1 UR1 Build 03 30-June-06](https://glassfish.dev.java.net/downloads/v1_ur1-b03.html)」をそれぞれダウンロードする。
NetBeansのインストールは簡単だ。tar ballを展開して，NetBeans.appファイルを得るだけ。NetBeans.appアイコンをダブルクリックすれば，NetBeansが起動する。
次にGlassFishだ。GlassFishのインストール方法は，さっきの
[ダウンロードページ](https://glassfish.dev.java.net/public/downloadsindex.html)に記述されている。実行可能jarファイルなのだが，普通に起動してしまうとheapメモリが不足してしまうため，「java -Xmx256m -jar glassfish-installer-v1_ur1-b03.jar」というようにjavaコマンドを手動で実行する。ライセンス同意ダイアログで[Accetp]ボタンを押すとglassfishディレクトリが生成されるので，適当な場所に移動させる(ここでは/Applications/java/に配置したことにする)。さらにシェルからglassfishディレクトリに移動して「ant -f setup.xml」というようにantを実行すれば，domain1ドメインが生成されてGlassFishが起動可能状態になる。
では，NetBeansからGlassFishを使えるようにしてみよう。NetBeansを起動し，RuntimeタブにあるServersノード上で右クリック。「Add Server...」を選択する。

![AddServers.gif](http://www.eisbahn.jp/yoichiro/images/AddServers.gif)
Add Server Instanceダイアログが表示されるので，まずは以下のように「Sun Java System Application Server」「GlassFish」と入力し，[Next]ボタンを押す。

![AddServerInstance1.gif](http://www.eisbahn.jp/yoichiro/images/AddServerInstance1.gif)
次のパネルでは，以下のように「/Application/java/glassfish」(GlassFishディレクトリ)「Register Local Default Domain」「・・・/domains/domain1」を入力し，[Next]ボタンを押す。

[![AddServerInstance2.gif](http://www.eisbahn.jp/yoichiro/images/AddServerInstance2.gif)](http://www.eisbahn.jp/yoichiro/images/AddServerInstance2.gif)
管理者用認証情報を入力する次のパネルでは，以下のように「admin」「adminadmin」(GlassFishの初期値)と入力し，[Finish]ボタンを押す。

![AddServerInstance3.gif](http://www.eisbahn.jp/yoichiro/images/AddServerInstance3.gif)
すると，GlassFishがNetBeansに登録され，Serversノードに追加される。

![AddedGlassFish.gif](http://www.eisbahn.jp/yoichiro/images/AddedGlassFish.gif)
では，GlassFishをNetBeansから起動してみる。Serversノードに登録されたGlassFishノードの上で右クリックし，Startメニューを選択する。

![StartingGlassFish.gif](http://www.eisbahn.jp/yoichiro/images/StartingGlassFish.gif)
Outputウィンドウ内に，「Application server startup complete.」と表示されれば起動完了。同じように，Stopメニューを選択すれば，GlassFishを停止させることができる。
これで，PetStoreのための準備は完了である。
