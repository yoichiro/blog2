---
layout: post
status: publish
published: true
title: Ruby on Railsでもfacebookアプリは作れます(2)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 478
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=478
date: '2007-12-06 21:34:00 +0900'
date_gmt: '2007-12-06 12:34:00 +0900'
categories:
- facebook
---

[前のエントリ](http://www.eisbahn.jp/yoichiro/2007/12/ruby_on_railsfacebook.html)で、facebookアプリがRuby on railsでも開発することができるということを紹介した。数回にわけて、実際にアプリを作るための手順を紹介していこうと思う。もちろん、facebookにアカウントを登録してサインインしていることが前提条件。
まず、アプリケーション開発を行うためのアプリケーション「Developer」を、自分の登録アプリとしなければならない。Developerアプリを使用することで、新規アプリの登録が行えるようになる。Developerアプリの登録は、以下のページにある「Add Facebook Developer Application」リンクをクリックすることで実施することができる。

[Facebook Developers | Get Started](http://developers.facebook.com/get_started.php)
で、さっそくアプリを登録したいところなのだが、その前にfacebookアプリの仕組みについて把握しておかなければならない。まず、下の図を見て欲しい。

![facebook3.jpg](http://www.eisbahn.jp/yoichiro/images/facebook3.jpg)
Webブラウザに表示されるのは、あくまでfacebookのWebページである。そのページの一部分を、自作アプリケーションの出力結果で埋めることができる、という仕組み。なので、あくまでWebブラウザからのリクエストは、必ずfacebookサーバを中継して、その後にあなたのアプリケーションサーバに届く。そして、処理結果としてFBML(HTMLのサブセット)を返し、それがfacebookサーバ内で解釈され、解釈結果が埋め込まれたfacebookページとしてWebブラウザに返される、という流れだ。実際には、rfacebookプラグインがfacebookサーバと認証処理を行ったりするので、2.から3.の間でやり取りが発生するのだが、それはrfacebookプラグインが隠蔽してくれるので、気にしなくて良い。
なんでこんな根本的なフローを説明したかというと、Developerアプリで自作アプリを登録する際に、前もって決めておかなければならない情報が出てくるから。具体的には、あなたのWebアプリケーションをfacebookが呼び出しに行く際のURLを決めておかなければならい。例えば、www.eisbahn.jpドメインで3000番ポートでrailsアプリケーションを起動し、facebookサーバからのリクエストをcanvasコントローラで受け付ける、とするなら、
http://www.eisbahn.jp:3000/canvas/ ・・・(1)
というURLを決めておく、ということである。
では、アプリの登録を始めてみる。Developerアプリを左のナビメニューから実行する。

![facebook1.jpg](http://www.eisbahn.jp/yoichiro/images/facebook1.jpg)
すると、右上に「Set Up New Application」というボタンが出るはず。それをクリックすると、登録フォームが表示される。

![facebook2.jpg](http://www.eisbahn.jp/yoichiro/images/facebook2.jpg)
入力しなければならない項目は、以下の通り。

* Application Name: アプリ名(任意の文字列)。

* Check here to...angree to the term...: ライセンス同意(チェックを入れる)。

* Optional Fields: 展開する。

* Callback Url: あなたのアプリのURL(上記の(1)のURL)。

* Canvas Page URL: アプリを呼び出すためのURL(任意の文字列)。

* Use FBML: にチェック。

* Can you app...added on Facebook?: Yesにチェック。

* Who can...Facebook account?: Usersにチェック。

* Post-Add URL: アプリが追加された後に呼び出されるURL(Canvas Page URLをそのまま入力)。

* Developer Mode: チェックを入れる(自分しかそのアプリは使えない状態)。

* Side Nav URL: 画面左のナビに登録するアプリのURL(Canvas Page URLをそのまま入力)。
あとは[Submit]ボタンを押せば、アプリケーションの登録は完了である。Canvas Page URLにブラウザでアクセスすることにより、アプリケーションをCanvasにて起動することができるようになる。
ここでのハマりポイントとしては、

* Callback Urlの最後に「/」を打ち忘れると、例えば「
[...」とリンクを作っても「http://〜/canvashoge」となってしまう。](hoge)

* 誰かにアプリを試してもらいたくても、Developer Modeの場合は「そんなアプリはないよ」となってしまう。

* Use FBMLとUse iframeを間違えると、facebookとあなたのアプリ間でのやり取りが全く違ってくるために、わけわかめになる。
といったことがあげられる。特に1つ目のハマりポイントは、やってしまいがちなのでご注意を。
これでfacebook側の準備は完了。実際にCanvas Page URLにアクセスすると、あなたのサーバにfacebookから要求が飛んでいることがわかるはずである。以下のようなページや、明らかに出るな的なエラーメッセージが表示されれば、成功である。

![facebook4.jpg](http://www.eisbahn.jp/yoichiro/images/facebook4.jpg)
残念ながら、このエントリも長くなってしまったので、rfacebookプラグインを使ったRailsアプリの作り方は、またもや次回に持ち越すこととしよう。
