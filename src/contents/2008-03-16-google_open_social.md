---
layout: post
status: publish
published: true
title: Googleデベロッパー交流会 第５回「Open Social」に参加してきました
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 523
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=523
date: '2008-03-16 12:22:11 +0900'
date_gmt: '2008-03-16 03:22:11 +0900'
categories:
- OpenSocial
---

先週の3月14日、
[Googleデベロッパー交流会](http://www.google.co.jp/developer/prg/event.html) 
[第５回「Open Social」](http://googlejapan.blogspot.com/2008/02/open-social.html)に参加させていただいた。今回は、パネルディスカッションのパネラーの一人として、Open Socialについて他のパネラーの方々と共に語らせていただいた。
何だかんだ言って、結局僕が一番話をしちゃってたかもしれない。うーん、良かったのか、悪かったのか。。。
Open Socialというと、やはりSNSアプリケーションの開発に関する話が中心になると思っていたのだが、僕にとって非常に意外だったのは、SNSを運用している人、あるいはSNSのサーバを作っている人が会場に多く来ていたということだ。特に、地域SNSとそれらのスケールアップを課題に持っている人が多く、その解決策としてOpen Socialに期待している、という話を伺うことができた。
Open SocialでのSNSアプリのポータビリティ性を重要と考えたため、今回僕がデモをした「こみゅすけ Open Social Edition」も、
[orkut](http://www.orkut.com/)上で開発＆テストしたものが、そのまま1文字も変えることなく、
[hi5](http://www.hi5.com/)でも動作することを紹介した。さらに、自前のデータベースを持つことで、異なるSNSの情報も一緒に扱うことができるという点も取り上げた。僕個人的にも、まさか何にも変える事なく動くとは思っていなかったので、会場に来ていただいた方々も、かなりOpen Socialを身近に感じてもらえたと思う。
先ほどの地域SNSに関しても、複数のSNSコンテナ上でのソーシャルグラフの構築やアプリケーションの展開をしていくことによって、現実のものになると思う。しかし、現状すぐに！というわけにはいかず、残念ながらOpen Socialコンテナをまともに実装したSNSサーバソフトウェアが登場するまではお預け状態というのが正直な現状である。OpenPNEやOpenSNPなどの今後に期待したい。
アプリが動くかどうかという点の他にも、Open Socialアプリ都合で自前データベースを設置したときに、

* どこまで情報を保持して良いのか

* 悪意を持つアプリについてどう対策するか

* Open Socialコンテナの正当性をどう保証するか
などなど、まだまだOpen Socialにおける課題があることが見えたことも、今回のデベロッパー交流会での収穫ではないかと思う。
もし「Open Socialアプリを試してみたい！」と思っていただいた方には、下記のサイトを参考にすると、手順を追ってアプリの作成法を身に付けられる。個人的には、
[orkut](http://code.google.com/apis/orkut/)あるいは
[hi5](http://developer.hi5.com/)がお勧め。

[http://code.google.com/apis/opensocial/gettingstarted.html](http://code.google.com/apis/opensocial/gettingstarted.html)
orkutには、「
[OpenSocial Japan](http://www.orkut.com/Community.aspx?cmm=47213793)」というコミュニティがあって、今回のパネリストがメンバーとして参加している。もしアプリケーションの作成や実行方法など疑問点などあれば、ぜひフォーラムに投稿して欲しい。
今回のレポートや動画は
[Google Japan Blog](http://googlejapan.blogspot.com/)にて今後掲載されるとのことなので、会場に来れなかった方もぜひチェックして欲しい。
