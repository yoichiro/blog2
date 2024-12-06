---
layout: post
status: publish
published: true
title: TranslationViewプラグイン1.1.4リリース！
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 329
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=329
date: '2007-02-22 18:11:22 +0900'
date_gmt: '2007-02-22 09:11:22 +0900'
categories:
- Eclipse
---

Eclipse上で翻訳を行うプラグインである「
[TranslationView](http://yoichiro.cocolog-nifty.com/eclipse/2004/11/post.html)」プラグインを1.1.4にバージョンアップした。今回は，

* 翻訳前，翻訳結果のテキストボックス内でCtrl+Vなどをしたときの不具合修正

* 翻訳結果を持つレスポンスの文字コード指定を追加
の２点を機能追加した（
[ここ](http://eclipsewiki.net/eclipse/index.php?TranslationView%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3)に投稿された依頼より）。
Excite翻訳でしか確認していないのだが，どうも他の翻訳サイトを利用したときに，レスポンスの解析に失敗して文字化けしてしまうらしい。文字コードは「JISAutoDetect」で自動判別させていたのだが，JISAutoDetectは怪しい動きをする場合もあるので，レスポンスに関しても文字コードを指定できるようにした。

![TranslationView-After-Encoding.jpg](http://www.eisbahn.jp/yoichiro/images/TranslationView-After-Encoding.jpg)
インストールやアップグレードは，以下のURLの更新サイトから行って欲しい。

* http://www.eisbahn.jp/update-site/
ぜひお試しあれ。機能追加要望や不具合報告などは，
[Eclipse-Wikiのページ](http://eclipsewiki.net/eclipse/index.php?TranslationView%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3)やこのブログまでコメントを寄せて欲しい。
