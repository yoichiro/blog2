---
layout: post
status: publish
published: true
title: Twitterclipse公開中！
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 359
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=359
date: '2007-04-22 08:45:50 +0900'
date_gmt: '2007-04-21 23:45:50 +0900'
categories:
- Eclipse
---

自分が何をしているのか？友達や知り合いが何しているのか？こんなことを共有して楽しもうというサービスが，
[twitter](http://twitter.com/)である。そんなtwitterを，例のごとくEclipseから利用できるように，プラグインを開発した。その名も，twitterclipse。

![twitterclipse.jpg](http://www.eisbahn.jp/yoichiro/images/twitterclipse.jpg)
eisbahn.jpのアップデートサイト（http://www.eisbahn.jp/update-site/）にtwitterclipseを配置しているので，Eclipse上でインストールを行うことができる。
機能としては，

* 予め登録されているFriendのステータスメッセージを表示（最新のみ）する。

* 自分のステータスメッセージを更新する。
を搭載した。使用を開始する場合は，[Window]-[Preference]-[Twitterclipse]メニューで表示される設定画面で，自分のユーザIDとパスワードを入れる必要がある（これがミスっている場合は401エラーが表示される）。自分のステータスメッセージを更新するには，下のテキストフィールドにメッセージを入れて[Enter]キーを押せば良い。
TranslationView，Rimoclipse，Lingrclipse，Twitterclipseと作ってきたが，そろそろお遊びパースペクティブを定義して公開するのも面白いかも。
ぜひ試してみて欲しい。感想や意見，不具合などのコメントをどんどんくれると嬉しい限りである。
