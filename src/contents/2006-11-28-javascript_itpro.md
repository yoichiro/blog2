---
layout: post
status: publish
published: true
title: JavaScript流オブジェクト指向プログラミング - ITpro
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 272
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=272
date: '2006-11-28 09:38:10 +0900'
date_gmt: '2006-11-28 00:38:10 +0900'
categories:
- Java
---

JavaやC#などのオブジェクト指向言語とは異なるアプローチでオブジェクト指向を実現できるJavaScript。なかなか丁寧な解説が見つからないのだが，
「
[JavaScript流オブジェクト指向プログラミング](http://itpro.nikkeibp.co.jp/article/lecture/20061114/253669/)」 - ITpro
は非常にわかりやすかった。プロトタイプ型のオブジェクト指向言語は，スクリプト言語という特性（＝サクサク感＆柔軟性）にぴったりだ。
ただし，

>そのオブジェクトの内部のみからアクセス可能なプロパティ（C++やJavaでいうところのプライベート変数）はJavaScriptでは実現できません。

は，
「
[Javascript でカプセル化する方法](http://d.hatena.ne.jp/nirvash/20050902/1125667539)」 - nirvashの日記
で説明されている通り，varの使い方や宣言箇所を工夫することで実現可能である。
DojoやYUIなどを使うと，どうしても既存のオブジェクトの振る舞いを変えたくなったりするので，JavaScriptでのオブジェクト指向実現のための知識は今後必須となるだろう。そのための第一歩として，ITproの記事は非常に有効だと思う。
