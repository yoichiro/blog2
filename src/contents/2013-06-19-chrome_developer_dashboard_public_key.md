---
layout: post
status: publish
published: true
title: Chromeデベロッパーダッシュボードで公開鍵を確認できるけど何のため？
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2428
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2428
date: '2013-06-19 23:40:34 +0900'
date_gmt: '2013-06-19 14:40:34 +0900'
categories:
- Chrome extension
---

ちょっと前に僕がリリースしている拡張機能をバージョンアップしようとして、Chromeデベロッパーダッシュボードを開きました。そこには見慣れないリンク「詳細」がありました。

![pub_key_1](http://www.eisbahn.jp/yoichiro/images/2013/06/pub_key_1.png)

押してみると、アイテムIDや公開鍵が表示されました。

![pub_key_2](http://www.eisbahn.jp/yoichiro/images/2013/06/pub_key_2.png)

ここで「おぉ、なんと便利な！」と思った方は、下を読まなくてもいいです。僕は最初「え、公開鍵なんて表示されても、何に使うんだ？利用用途ないだろおい」と思ってしまいました。でも、理由なく単に「特に意味ないけど出してみました、てへっ」なんてことはないと思うので、G+のコミュニティにて聞いてみました。

「What is this?」 
[http://goo.gl/z7aAW](http://goo.gl/z7aAW)

そこでの返事を要約すると、以下のような感じになります。

>アプリや拡張機能を開発してるときは、パッケージ化してない状態でローカルにあるコードを使って開発するよね？でも、例えばPush messagingといった機能を使うには、通常Chromeウェブストアにて公開された後に決定されるApp IDや公開鍵が必要になる。パッケージ化されたものを覗くことなくApp IDや公開鍵を知ることができれば、とても便利だよ。ローカルにあるコードのmanifest.json内で、"key":"公開鍵"と書いてあげれば、それに対応するApp IDが付与されるので、Push messagingとかの機能をローカルで試すことができるようになるんだよ。わかったかい？


Push messagingは、Google Cloud Messaging for Chrome（開発者のサーバからアプリや拡張機能に情報をプッシュする技術）のことを指しています。これを試すためには、通常Chromeウェブストアにて公開されたパッケージをChromeにインストールすることが求められます。ただ、それだと開発やデバッグが大変です。コード修正、Chromeウェブストアに公開、テスト、コード修正・・・という繰り返しは、苦行以外の何物でもないでしょう。公開鍵を手軽に入手できることで、この苦行を行わなくても済みます。なんて素敵なんでしょう！

アプリや拡張機能では、今後Push messagingを活用する機会は増えてくることは確実です。Chromeデベロッパーダッシュボードで公開鍵を見れるようになったことは、僕が思っていた以上に僕らの開発を楽なものにしてくれるでしょう。
