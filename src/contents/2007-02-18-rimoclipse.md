---
layout: post
status: publish
published: true
title: Rimoclipseでウキウキプログラミング
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 325
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=325
date: '2007-02-18 23:08:08 +0900'
date_gmt: '2007-02-18 14:08:08 +0900'
categories:
- Eclipse
---

はてなが新しく公開を始めたサービス，それが「
[Rimo](http://rimo.tv/)」である。現在は，
[YouTube](http://www.youtube.com/)の人気動画を集めてテレビ化されている。
特に意味はないのだが，Rimoを見ながらプログラミングができるよう，Eclipseプラグインを作成してみた。
その名も「Rimoclipse」。
eisbahn.jpのアップデートサイト「http://www.eisbahn.jp/update-site/」からインストールすることができる。Eclipseの対象バージョンは3.1以上とした。インストール後，「Window」-「Show View」メニューの「Other」カテゴリに「Rimo」というビューを選んで表示すると，Rimo閲覧用のViewが追加され，さっそく再生が開始される。

![rimoclipse.jpg](http://www.eisbahn.jp/yoichiro/images/rimoclipse.jpg)
再生されている動画上にマウスカーソルを移動させることで，チャンネル変更のリモコンが表示される。ま，BrowserコンポーネントをViewに貼り付けているだけなので，当然の動きである。
1時間ちょいで作ったものなので，なーんの機能もないし，MacBook Pro＋MacOSX10.4＋Eclipse3.2でしか動作確認していないが，たぶん他の環境でも大丈夫だろう。
このプラグインを利用することによって生じる生産性の低下については，一切保証しかねるのでご了承を。自己責任でご利用あれ。
