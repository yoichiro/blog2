---
layout: post
status: publish
published: true
title: "昨日の退職エントリへのインパクト"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2236
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2236
date: '2013-02-19 11:59:26 +0900'
date_gmt: '2013-02-19 02:59:26 +0900'
categories:
- Other
---

昨日の朝に、「
[ミクシィ社を退職しました](http://www.eisbahn.jp/yoichiro/2013/02/retire-from-mixi.html)」っていうエントリを出しました。その反響はすごく、Twitter、Facebook、Google+などで多くの方からいろいろなコメントをいただくことができました。今後も精進せなあかんな、と思った次第です。皆さま、ありがとうございます。

このブログは、僕の自宅にあるサーバで運用しています。最近
[munin](http://munin-monitoring.org/)というサーバの稼働状況をグラフでモニタリングできるソフトウェアを入れて、各サーバ、各VMの状況を見れるようにしていました。昨日のエントリに対する状況ももちろん記録されていましたので、ここでいくつか紹介してみたいと思います。記念に記録しておくという意味も込めて。

まずは、WordPressを動かしているVMのグラフ。apacheのアクセス量は以下のような感じになりました。エントリポストと同時に一気に跳ね上がってます。普段アクセスがほとんど来ないブログでしたが、昨日は一日中結構な人数アクセスしに誰かが来ていたって結果でした。

![](http://www.eisbahn.jp/yoichiro/images/2013/02/apache_accesses-day.png)

そのVMのeth0、つまりネットワークに関する転送量のグラフです。apacheのアクセス量と傾向は一緒です。当たり前だけど。

![](http://www.eisbahn.jp/yoichiro/images/2013/02/if_eth0-day.png)

そのVMのCPU使用率のグラフ。跳ね上がってるけど、それほどでもないかな。

![](http://www.eisbahn.jp/yoichiro/images/2013/02/cpu-day.png)

そのVMのCPU Loadのグラフ。CPUは2つ割り当ててあるので、実際はフルパワーの50%ほどでピーク時も捌けてたって感じでしょうか。

![](http://www.eisbahn.jp/yoichiro/images/2013/02/load-day.png)

そのVMのメモリの状況のグラフ。「なんだなんだ？なにが起きたんだ？」って感じで揺れてます。

![](http://www.eisbahn.jp/yoichiro/images/2013/02/memory-day.png)

次は、mysqlを動かしているVMの状況です。まずはmysqlへのクエリーの量のグラフから。そりゃーselectがすんごい飛んでます。

![](http://www.eisbahn.jp/yoichiro/images/2013/02/mysql_queries-day.png)

でもslow queryは出ず。そりゃそうですよね、皆さん全く同じエントリを見に来てるわけで。

![](http://www.eisbahn.jp/yoichiro/images/2013/02/mysql_slowqueries-day.png)

mysqlのVMのCPU使用率は、ほとんど揺れず。

![](http://www.eisbahn.jp/yoichiro/images/2013/02/cpu-day-1.png)

そのVMのCPU Loadもほとんど変化なしです。ブログのデータベースなんで、頭は使わずひたすら出し入れですね。

![](http://www.eisbahn.jp/yoichiro/images/2013/02/load-day-1.png)

そのVMのメモリ使用量も安定してました。

![](http://www.eisbahn.jp/yoichiro/images/2013/02/memory-day-1.png)

ちなみに、僕がTwitterなどに流した時の短縮URLのクリック数は、これを書いている今は3098です。こんなにクリックされたURLを流したのは初めてですね。goo.glのグラフを見てみると、ブラウザの統計はChromeが最も多く、なんと2番目はMobileでした。

![](http://www.eisbahn.jp/yoichiro/images/2013/02/browser.png)

プラットフォームの統計を見ると、iPhoneが多かったのがわかります。エントリをポストしたのが午前10時前だったので、みんな通勤時間にTwitterやFacebookなどでフィードを見て、すぐにそのリンクをクリックしてエントリを見に来ていた、ってことかなと思います。

![](http://www.eisbahn.jp/yoichiro/images/2013/02/platform.png)

僕はブロガーってわけじゃないと思ってるのですが、アルファブロガーな方々はエントリの度にこんなグラフを得てるってことなんですよね。すごいなぁ。とりあえず、自宅のサーバが落ちなくて良かったです。
