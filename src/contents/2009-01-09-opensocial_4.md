---
layout: post
status: publish
published: true
title: OpenSocialはオープンです
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 610
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=610
date: '2009-01-09 11:26:36 +0900'
date_gmt: '2009-01-09 02:26:36 +0900'
categories:
- OpenSocial
---

「
[OpenSocialアプリを開発する上での落とし穴](http://jmblog.jp/archives/240)」というエントリがあったので、ちょっとだけ僕なりの考えを書いてみます。
まず「落とし穴1 まずは「ガジェット」から理解するべし」と「落とし穴2 iGoogle で Gadget API が使えない？！」は、上記エントリのご指摘どおりです。今度の24日に「
[OpenSocial入門](http://www.amazon.co.jp/exec/obidos/ASIN/4774137480/eclipseplugin-22/ref=nosim)」本が出版されますので、これからOpenSocialアプリケーションを開発することになる方々のバイブルになればいいな、と思っています。
さて、「[落とし穴3] 仕様変更の可能性」に関して、

>現段階では、OpenSocial API のバージョンは 0.8.1 であり、正式バージョン1.0 までにはまだまだ仕様が変更される可能性があります。従って、実験的にアプリを作るのはいいですが、企業などで本格的にアプリを開発してしまうのは時期尚早かと思います。

に関しては、v0.6からv0.7への変更で非互換から生じた混乱が教訓になっていて、v0.8以降は仕様検討について互換性は最新の注意が払われています。また、v0.8.1にて基本的な仕様は全て揃った、という認識が仕様検討チームで持たれています。多くのSNSがこのv0.8.1対応となっていますので、v1.0を待つことなく、アプリケーションの開発をスタートさせても良いと思います。つまり、バージョン番号が若いから・・・というのは、OpenSocialに関しては今後気にすることはないかと。
次の「[落とし穴4] ブラウザのような・・・」です。

>つまり、Google は仕様を決めるだけ、実行環境（プラットフォーム）はそれぞれのサイト側で用意しているのです。

OpenSocialの仕様はGoogleだけが決めているわけではありません。主要コンテナベンダーの人々や開発者などが、自由に提案し、自由に議論しながら仕様は決定されています。「Google OpenSocial」ではなく単に「OpenSocial」と呼ばれているのは、つまりそういうことです。日本からも、仕様策定に参加している開発者（私とか）やSNSベンダーの方もいらっしゃいます。
[OpenSocial and Gadgets Specification Discussion]

[http://groups.google.com/group/opensocial-and-gadgets-spec/topics](http://groups.google.com/group/opensocial-and-gadgets-spec/topics)
[OpenSocial Community Wiki]

[http://wiki.opensocial.org/index.php?title=Main_Page](http://wiki.opensocial.org/index.php?title=Main_Page)
そして、OpenSocial対応をSNSが迅速に行い、さらに対応レベルを均一に保つために、OpenSocialコンテナの参照実装として
[Apache Shindig](http://incubator.apache.org/shindig/)が開発されています。事実、OpenSocialに対応したほとんどのSNSは、このApache Shindigを採用しています（使ってないのはMySpaceくらい）。日本における各SNSのOpenSocial対応も、このApache Shindigを使ったコンテナになると予想できます。
OpenSocialの仕様検討のプロセスには、単にAPIを取り決めるだけでなく、参照実装であるApache Shindigに対して新しい仕様に関する実装コードを提供しなければならない、というハードルが次のバージョンから採用される予定となっています。つまり、新しい仕様は実現可能性がちゃんと確保され、しかも多くのSNSがShindigという共通のコンテナ実装を採用することによって、できるだけ環境依存が起きないようにされているわけです。
この辺の話は、以下のエントリに貼ってあるプレゼンを見て頂けるとよろしいかと思います。
[OpenSocialに見るGoogleのオープン戦略]

[http://www.eisbahn.jp/yoichiro/2008/09/seasar_conference_2008_autumn.html](http://www.eisbahn.jp/yoichiro/2008/09/seasar_conference_2008_autumn.html)
というわけで、将来のバージョンアップや環境依存といった懸念については、安心して頂けたらと思います。
