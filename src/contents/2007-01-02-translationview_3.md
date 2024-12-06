---
layout: post
status: publish
published: true
title: TranslationViewプラグインをバージョンアップしました
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 303
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=303
date: '2007-01-02 10:44:11 +0900'
date_gmt: '2007-01-02 01:44:11 +0900'
categories:
- Eclipse
---

Eclipse上で翻訳を行うプラグインである「
[TranslationView](http://yoichiro.cocolog-nifty.com/eclipse/2004/11/post.html)」プラグインを1.1.3にバージョンアップした。今回は，

* 翻訳結果で「全て選択」機能（Ctrl+A）を追加

* 翻訳要求時のパラメータの文字コード指定を追加
の２点を機能追加した（
[ここ](http://eclipsewiki.net/eclipse/index.php?TranslationView%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3)に投稿された依頼より）。
個人的には，
[Exciteの翻訳サイト](http://www.excite.co.jp/world/)でしか動作確認していないのだが，Yahoo!翻訳などではMS932ではなく，UTF-8でパラメータを送らないといけないらしい。そこで，設定画面にて文字コードを指定可能とした。

![translationview-encoding.jpg](http://www.eisbahn.jp/yoichiro/images/translationview-encoding.jpg)
インストールやアップグレードは，以下のURLの更新サイトから行って欲しい。

* http://www.eisbahn.jp/update-site/
ぜひお試しあれ。機能追加要望や不具合報告などは，
[Eclipse-Wikiのページ](http://eclipsewiki.net/eclipse/index.php?TranslationView%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3)やこのブログまでコメントを寄せて欲しい。
