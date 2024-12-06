---
layout: post
status: publish
published: true
title: ShindigのPHP実装コードを追うときに使ってる機能
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 589
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=589
date: '2008-10-12 13:35:15 +0900'
date_gmt: '2008-10-12 04:35:15 +0900'
categories:
- OpenSocial
---

Apache Shindigのコード、特にPHP実装のコードを対象にいろいろやっていると、「あれ？動かないぞ？」という状況に度々出会うことになる。もちろん現在まさに進化している最中のコードセットなので、動かない箇所があるのは当たり前。
もし自分の手で直せるものなら直してしまいたいので、PHPのコードについてデバッグしたいわけだが、あまりにもPHPに無知な自分であるため、一筋縄ではいかない。デバッガなどの統合開発環境を知っているわけでもない（使いたがらないというのが正確）。
最初に試したことで、しかも今も多用しているのが、echo。とにかくechoで確認したい値を表示させて目視するという手法は、最も単純でわかりやすい。しかし、もちろんこれだけでは限界がある。
今日Shindigのコードを追うために、以下の機能を見つけて使ってみた。これらはとても便利だ。

* debug_print_backtrace - バックトレースを表示する（JavaのStack Traceのようなものが表示される）

* print_r - 引数で渡した変数の情報を表示する

* error_log - error_logに文字列を出力する
特にprint_rに配列を渡すと、その内容が人が見やすい形式で表示されるため、なかなか親切。
「PHPなんて」とずっと思ってたけど、ちょっといろいろ知ってくると、これはこれでなかなかの環境だ。もちろん、ShindigのPHP実装がJavaコードに似せてわざと書かれていることも、PHPメインの人々には文句ブーブーみたいだけど、Javaメインな僕にとっては願ったり叶ったり。
食わず嫌いはいけないな、と思った。ちなみに僕の乳製品嫌いは、食わず嫌いではありません。
