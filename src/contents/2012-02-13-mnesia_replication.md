---
layout: post
status: publish
published: true
title: mnesiaのレプリケーション
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1302
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1302
date: '2012-02-13 16:45:01 +0900'
date_gmt: '2012-02-13 07:45:01 +0900'
categories:
- Erlang
---

最近またerlangでコードを書き始めた。こんなやつ。

[mixi_page crawler]

[https://github.com/yoichiro/mixi_page_crawler](https://github.com/yoichiro/mixi_page_crawler)

まさにうちの運用に怒られそうなコードです。いわゆるクローラですね。mixiページの情報を引っ張れるAPIがないので、とりあえずスクレイピングしまくってます。指定時間間隔で各ページに訪れて、新着情報のページを解析し各フィードの情報をそれぞれ抜き出します。それをerlang標準搭載のmnesiaデータベースにせっせと書き込んでいきます。

1回のクロールで何ページを並列いくつのプロセスで処理するか、というコントロールができるようになってます。1回のクロールはgen_serverのhandle_cast()1回呼び出しで表現されていて、timer:send_interval()でhandle_cast()を呼び出す処理をキックするメッセージを一定間隔で送信する、という感じです。こういうサーバを書くのに、ホントerlangは適してると痛感してます。エラーハンドリングとかはまだまだだけど。

さて、せっかくmnesiaつかってるので、レプリケーションとか試したくなります。そこで、2つのマシンそれぞれでkvm上にerlangノードを作って実践。1台のマシン内でやらずに、わざわざ物理的に分けてます。まぁ1台の中でkvmで2つのOS入れて・・・でも一緒なんだけど、そこはせっかく自宅に3台もサーバ機があるので。

![](http://www.eisbahn.jp/yoichiro/images/2012/02/my_servers.jpg)

同じCookieを".erlang.cookie"ファイルに書いて、以下のようにして各ノードを起動。

>[yoichiro@apollo]$ erl -name apollo1
[yoichiro@soyuz]$ erl -name soyuz1


これだけだと、なぜかお互いのノードを自動的に認識してくれません。「おまえいるかー？」とわざわざ聞くことで、認識されます。ここはUDPでマルチキャストするなどして自動判別されるといいなぁ、と思いつつ。

>(apollo1@apollo.eisbahn.jp)1> net_adm:ping('soyuz1@soyuz.eisbahn.jp').
(apollo1@apollo.eisbahn.jp)2> nodes().
['soyuz1@soyuz.eisbahn.jp']


次に、最初のノードにてmnesiaデータベースを作成。

>(apollo1@apollo.eisbahn.jp)3> db:setup().


db:setup()のコードは
[こちら](https://github.com/yoichiro/mixi_page_crawler/blob/master/db.erl#L15)。mnesia:create_schema(), mnesia:start(), mnesia:create_table() をしています。これでうまくいくはずなんだけど、なぜか失敗します。予めmnesia:create_schema()しておいてから、再度db:setup()するとうまくいく。なんでだろう。。。

このあと、apollo1ノードにあるmnesiaデータベースにガンガンレコードをwriteしていきます。

さて、次にsoyuz1ノードでもmnesiaデータベースを起動します。

>(soyuz1@soyuz.eisbahn.jp)1> mnesia:start().


これだけでは関連がまったくないので、apollo1ノードにあるmnesiaデータベースの設定を変更して、soyuz1ノードにあるmnesiaデータベースを参加させます。

>(apollo1@apollo.eisbahn.jp)4> mnesia:change_config(extra_db_nodes, ['soyuz1@soyuz.eisbahn.jp']).


これを実行することで、2つのmnesiaデータベースが関連されます。mnesia:system_info()でそれを確認。

>(apollo1@apollo.eisbahn.jp)5> mnesia:system_info().
...
running db nodes   = ['soyuz1@soyuz.eisbahn.jp','apollo1@apollo.eisbahn.jp']
...


ついでにsoyuz1ノード側でも確認。いい感じ。

>(soyuz1@soyuz.eisbahn.jp)2> mnesia:system_info().
...
running db nodes   = ['apollo1@apollo.eisbahn.jp','soyuz1@soyuz.eisbahn.jp']
...


ここまででデータベースの関連はできましたが、soyuz1ノードのmnesiaデータベースはメモリ上で保持されるram_copiesモードになってます。これをdisc_copiesモードに変えます。schemaテーブルは既にsoyuz1ノードにあってモードを変えるだけでいいので、mnesia:change_table_copy_type()を使っています。他にも今回のdb:setup()の例だと、pageテーブルおよびfeedテーブルがあります。これらはsoyuz1ノードからはremoteで見えているだけなので、これらをmnesia:add_table_copy()してレプリケーションします。

>(apollo1@apollo.eisbahn.jp)6> mnesia:change_table_copy_type(schema, soyuz1@soyuz.eisbahn.jp, disc_copies).
(apollo1@apollo.eisbahn.jp)7> mnesia:add_table_copy(page, soyuz1@soyuz.eisbahn.jp, disc_copies).
(apollo1@apollo.eisbahn.jp)8> mnesia:add_table_copy(feed, soyuz1@soyuz.eisbahn.jp, disc_copies).


こうしてあげることで、ディスクに永続化される形でレプリケーション環境が2台で構築できました。

>(soyuz1@soyuz.eisbahn.jp)3> mnesia:system_info().
...
disc_copies        = [feed,page,schema]
...


追加したノード側でmnesia:info()すると、各テーブルに入ってるレコード数が元のノードの値と一緒であることが確認できるでしょう。

ちなみに、追加したノードを削除したいときは、以下のようにすると実現できました。つまり、ノードを外すというよりは、レプリケーションをテーブルごとに解除したあとにノードを停止させる、っていう手順になります。除外したノードは、稼働ノード上で中途半端にstopped db nodesとしてmnesia:system_info()したときに表示され続けますが、まぁ実害はないと思います。

>(apollo1@apollo.eisbahn.jp)9> mnesia:del_table_copy(feed, soyuz1@soyuz.eisbahn.jp).
(apollo1@apollo.eisbahn.jp)10> mnesia:del_table_copy(page, soyuz1@soyuz.eisbahn.jp).

(soyuz1@soyuz.eisbahn.jp)4> mnesia:stop().


それでも、すっごく表示されていることが気に食わないときには、上記のあとに以下を実行すると消えました。これが正しい手順かどうかは、僕にはわかりませんが。。。

>(apollo1@apollo.eisbahn.jp)11> mnesia:del_table_copy(schema, soyuz1@soyuz.eisbahn.jp).


さて、レプリケーションかけた状態で一晩レコードを定期的に追加していったのですが、朝見たら以下のようなエラーメッセージが出ていて、レプリケーションが止まってました。

>=ERROR REPORT==== ...
... ** ERROR ** mnesia_event got {inconsistent_database, ...


元のapollo1ノードにはちゃんとレコードが追加されていっていたようなので、soyuz1ノードのmnesiaデータベースをmnesia:stop(), mnesia:start()して再起動したところ、データの同期がちゃんと行われました。「一貫性がない」状態とか怖いこと言ってるけど、なんだったんだろう。。。

とりあえず、いろいろ把握できました。面白いですね！
