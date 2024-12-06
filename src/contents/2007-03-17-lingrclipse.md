---
layout: post
status: publish
published: true
title: Lingrclipseの公開を始めました
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 341
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=341
date: '2007-03-17 23:51:43 +0900'
date_gmt: '2007-03-17 14:51:43 +0900'
categories:
- Eclipse
---

AjaxとCometによるリアルタイムなチャットサービスが，
[Lingr](http://www.lingr.com/)である。そのEclipseプラグインを開発してみた。その名も「Lingrclipse」。eisbahn.jpのアップデートサイトにてLingrclipseの公開を先ほどから開始した。
eisbahn.jp アップデートサイト

>http://www.eisbahn.jp/update-site/

上記アップデートサイトから，下記のプラグインをインストールすると，Lingrclipseを使用することが可能になる。

* lingrclipse-core

* lingrclipse-view
対象プラットフォームは，JDK5.0以上，Eclipse
3.13.2以上が理論的な環境。実際には，JDK5.0＋Eclipse3.2のWinとMac環境で確認した。
インストールしたあと，[Window]-[Show View]-[Other]メニューから，[Other]-[Lingrclipse View]を選択することで，LingrclipseのViewが表示される。

![lingrclipse1.jpg](http://www.eisbahn.jp/yoichiro/images/lingrclipse1.jpg)
LingrclipseのViewは，Lingrルームへの参加者の一覧表示と，やり取りされるメッセージの表示部の２つがそれぞれ左右に配置されている。

![lingrclipse2.jpg](http://www.eisbahn.jp/yoichiro/images/lingrclipse2.jpg)
左下にある「Enter room」リンクをクリックすると，入室したいルームの名前（例えば"java-ja"）と自分のニックネームを入力するためのダイアログが表示される。

![lingrclipse3.jpg](http://www.eisbahn.jp/yoichiro/images/lingrclipse3.jpg)
[OK]ボタンを押すと，既に入室している参加者の一覧と，やり取りされているメッセージが表示される。

![lingrclipse4.jpg](http://www.eisbahn.jp/yoichiro/images/lingrclipse4.jpg)
あとは，Cometパターンによって，参加者の発言が次々と表示され，右下のテキストフィールドに発言したいメッセージを入力して[Enter]キーを押すか[Say!]ボタンを押せば，メッセージが送信される。退室したいときは，左下の[Exit room]リンクを押すことで退室できる。
Lingrのサイトでチャットを実際に行ったことがある人にとっては，Lingrclipseは全く機能不足に感じることだろう。メッセージの表示についても，「字がデカイ」「URLがリンクとして表示されない」などについて自分でも不便に思っている。今後次々とバージョンアップを行うつもりでいるので，しばらくは我慢してもらうか，我慢できなければLingrの本家サイトでチャットをして欲しい。
とはいえ，ぜひLingrclipseを使って頂いて，不具合などの突っ込みをコメントしてくれると非常に嬉しい。Lingrclipseは
[Google Code](http://code.google.com/p/lingrclipse/)にてソースも公開しているので，恥ずかしい限りだが，コードも見て突っ込みを頂きたい。
そして，Lingrclipseを使って，
[Lingrのjava-jaルーム](http://www.lingr.com/room/java-ja)に是非来て欲しい。その先には素晴らしい世界が広がっているはずだ。
