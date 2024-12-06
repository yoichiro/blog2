---
layout: post
status: publish
published: true
title: Lingrclipseに認証機能を追加しました
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 342
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=342
date: '2007-03-19 16:44:16 +0900'
date_gmt: '2007-03-19 07:44:16 +0900'
categories:
- Eclipse
---

先日公開したLingrclipseだが，さっそくバージョンアップを行ってみた。
追加機能は，認証機能である。Lingrにアカウント持っている人は，予め設定（Preference）画面にてメルアドとパスワードを入力しておけば，入室時にLingrにログインされる。もちろん，退室時にはログアウトも行われる。それだけ。

![lingrclipse-login.jpg](http://www.eisbahn.jp/yoichiro/images/lingrclipse-login.jpg)
この認証機能，Lingrのヘビーユーザは真っ先に欲しいと思う機能のようだ（僕も思ってたけど）。びっくりしたことに，
「
[スゲーっすよ Eclipse の lingr プラグイン](http://yoshiori.org/blog/2007/03/_eclipse_lingr.php)」- Yoshioriの日記

>感動ついでにちょこっとソース弄って
アカウントでログインできるようにしちゃいました♪

と，認証機能を自作してしまうハカーが出現。とてもすばらしいことである。作って頂いたパッチを採用したいところなのだが，認証機能は暗黙的に行われた方がいいかな，と思い，機能追加をすることにした。申し訳ない気持ちでいっぱいである。
CoreとView共々1.1.0が最新バージョンだ。興味のある方は，是非インストールしてみて欲しい。
