---
layout: post
status: publish
published: true
title: TranslationViewプラグインバージョンアップ間近！
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 20
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=20
date: '2005-03-20 00:12:45 +0900'
date_gmt: '2005-03-19 15:12:45 +0900'
categories:
- Eclipse
---

「
[Eclipseプラグイン開発](http://yoichiro.cocolog-nifty.com/eclipse/)」で公開している「
[TranslationView Plug-in](http://yoichiro.cocolog-nifty.com/eclipse/2004/11/post.html)」。最初に公開した直後にいくつか改善や機能追加の要望をもらっていた。しかし，約3ヶ月ちょい放置状態。せっかくBlogにコメントもらったし，バージョンアップしましょうということで，先週，今週と作業をやってきた。

とりあえず，機能追加は完了。目玉としては，翻訳作業をやってもらうサイトとのやり取りを変更できるようにしたことかな。これでExciteのインタフェースがかわっても対応できるし，他の翻訳サイトを使うこともたぶん可能だ。

あとは，Ctrl + Spaceで翻訳を実行できるようにしたり，テキストエディタやJavaエディタ上の右クリックメニューに[Translate]メニュー項目を追加したりした。それと，アイコンもちょっとカッコよくして，翻訳中はアイコンをかえるようにしてある。翻訳前＆翻訳後の表示のエディタ部にも，右クリックメニューを追加してある。

![](http://www.eisbahn.jp/yoichiro/images/163857.gif)

これで，格好も動作も，一応まともなプラグインになったかな。

しかし，「/*」「<>」の除外指定の動作がなんとなく怪しいので，明日軽くチェックしてから一般公開しようと思う。もうちょいだ。
