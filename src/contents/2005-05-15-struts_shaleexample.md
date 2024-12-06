---
layout: post
status: publish
published: true
title: Struts Shaleに新example登場！
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 65
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=65
date: '2005-05-15 18:54:52 +0900'
date_gmt: '2005-05-15 09:54:52 +0900'
categories:
- Shale
---

次期StrutsのShaleだが，今まではuse-casesというサンプルしかなく，これは非常に簡単なものだった。use-casesからShaleの本質はもちろん見えてきたが，実際にShaleの利用シーンは自分である程度想像するしかなかった。

しかし，というか，やっぱりというか，今日Struts Shaleの開発が行われているSVN Repository（
[http://svn.apache.org/repos/asf/struts/shale/trunk](http://svn.apache.org/repos/asf/struts/shale/trunk)）を覗いたら，mailreaderという新しいサンプルが作成されていた。ソースコードなどを見ると，Yahoo!やhotmailなどのアカウントをメモリ内データベースで持ち，新規アカウントの登録や編集，削除を行うことができるようだ。つまり，Webメールアカウント管理アプリである。メッセージリソースを見ると，アカウントだけでなく，実際にWebメールにアクセスしてメールの一覧などを見れるようになるんだろうな，という想像もできる。

ビルドをやってみようと思ったが，単にantの実行だけではうまくいかなかった。いろいろと調整しなければならなそうなので，とりあえず後回し。

なんといっても驚きなのが，日本語メッセージリソースがすでに存在することだ。日本の開発者を大事にしてくれているのが，とっても嬉しい。あとロシア語のリソースもあるのだが，ロシアってそんなにソフトウェア開発が盛んなのだろうか？

このmailreaderは，ダイアログもAJAXも使用されていないようで，現時点ではJSFとShaleのAbstractViewControllerを使った例のようだ。やはりShaleはダイアログによる画面遷移の管理とcommons-chainを使ったAJAX対応が売りだと思うので，このサンプルの更なる拡張を望むところである。
