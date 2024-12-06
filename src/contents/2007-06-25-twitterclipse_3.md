---
layout: post
status: publish
published: true
title: Twitterclipseをバージョンアップしました
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 387
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=387
date: '2007-06-25 22:13:28 +0900'
date_gmt: '2007-06-25 13:13:28 +0900'
categories:
- Eclipse
---

変更点は以下の通り。

* twitterサーバが返答したJSON内に含まれる日付のフォーマットが変更されたことへの対応。

* 「ぬるったー」「うまくいったー」「しくったー」機能の実装。
2つ目の機能追加は，Eclipse特有のものである。「ぬるったー」機能とは，何らかの理由でコンソールビューに「NullPointerException」が表示された際に，自分のステータスを自動的に「ぬるったー」に更新する機能である。「うまくいったー」「しくったー」機能は，JUnitテストケースの実行がうまくいった時あるいは失敗した時に「うまくいったー」「しくったー」と自分のステータスを自動的に更新する機能である。
上記機能はもちろんデフォルトでONにしてある。もしOFFにしたいときには，設定画面からOFFにすることが可能である。
Eclipseのかなり面白い機能を使っているが，機能自体は賛否両論だろう。この機能についての一切の責任は負いかねるのでご了承を，ということで。
ぜひバージョンアップされたし。
