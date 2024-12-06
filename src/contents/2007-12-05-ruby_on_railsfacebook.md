---
layout: post
status: publish
published: true
title: Ruby on Railsでもfacebookアプリは作れます
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 477
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=477
date: '2007-12-05 10:48:59 +0900'
date_gmt: '2007-12-05 01:48:59 +0900'
categories:
- facebook
---

[facebook](http://www.facebook.com/)が「ソーシャルOS」と呼ばれる所以は、facebook上にアプリケーションを自作して登録し、facebookユーザに使ってもらうことができる、ということにある。自作アプリケーションは、当然facebookが持つ様々な機能と連携することが可能である。例えば、アプリケーションで起きたことをMini-Feedに追加したり、プロフィール画面にアプリケーション固有の区画を追加したり、あるいはアプリケーションを自分の友達に勧めたり、といったことが、比較的簡単に実現できる。とても面白い。
では、facebookアプリケーションを実際に開発するためにはどうしたらいいか。これ、非常に敷居が高いと言わざるを得ない。いや、それは僕がPHPをメイン言語にしていないから、かもしれない。日本語のドキュメントはゼロ。ググっても日本語の情報は皆無。そしてPHP以外のサンプルは公開されていない。これだけでも、尻込みしてしまう人は多いだろう。
facebookチームが正式に公開しているライブラリは、PHPとJavaのみである。これは、「
[Facebook Developers | Resources](http://developers.facebook.com/resources.php)」(このURLからわかる通り、facebook自体がPHPでできている)というページで見つけることができる。僕は長らくJavaをやってきたが、Ruby on Railsの敷居の低さ(= 環境構築の容易さ)に魅せられ、facebookアプリケーションもやはりRailsで作ってみたい。
幸いなことに、facebook unofficialではあるが、ASP.NET、ASP(VBScript)、ColdFusion、C++、C#、D、Emacs Lisp、Lisp、Perl、Python、VB.NET、Windows Mobileといった言語・環境向けのクライアントライブラリを、「
[Facebook Developers Wiki](http://wiki.developers.facebook.com/index.php/Main_Page)」ページから辿ることにより入手可能である。そして、もちろんRails向けにも、クライアントライブラリは存在する。それが、
「
[RFacebook](http://rfacebook.rubyforge.org/) - now with RAILS plugin」
である。
このRFacebookは、Railsのプラグインとして提供されている。何をしてくれるかというと、

* facebookとの認証処理をやってくれる。

* facebook Web APIを叩いてくれる。
という主に2点のみ。ま、この2点が非常にでかく、facebookアプリケーションの全てと言ってもいいくらい。僕は最近JRuby & Glassfishという組み合わせが大好きなのだが、もちろんRFacebookを使ったRailsアプリも、warにしてGlassfish上で稼働させることは可能である(実際に今やってる)。
RFacebookに関するチュートリアルは、以下のURLが非常に参考になる。もちろん上記URLの内容を素直にやっていけば、期待通りにfacebookに自作アプリケーションの最初の画面をFBMLを使ったりして表示させることができる。

* [RFacebook Quick Start Guide for Rails](http://rfacebook.rubyforge.org/quickstart.html)

* [Using Ruby on Rails with Facebook Platform](http://wiki.developers.facebook.com/index.php/Ruby_on_Rails) - Facebook Developers Wiki
しかし、非常に残念なのだが、上記URLではRFacebookはちゃんと動いてくれない。
[RFacebookのForum](http://jypsie.com/RFacebook/forums/RFacebook-Help)で「facebook APIの脆(もろ)さは、PHP、ASP、そしてJavaコミュニティに感じる脆さなんだよ」といった過激な発言がでるくらいの原因で不具合が出てしまうのだが、これの解決策はあるのでご安心を。
そんなわけで、facebookアプリケーションをRailsで作るための具体的な手順は、後日としよう。そろそろ電車が新宿に到着するので。。。
