---
layout: post
status: publish
published: true
title: Solaris10でのbindが遅かったこととその改善策
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 587
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=587
date: '2008-09-27 09:49:59 +0900'
date_gmt: '2008-09-27 00:49:59 +0900'
categories:
- My PC environment
---

新居に引っ越して、もうすぐで1ヶ月。外構も昨日終わり、ほぼ完成。今まで実家に置いてあったサーバも新居に持ってきたため、自宅でのネットワーク環境もそのサーバが提供するサービスが基盤となった。アパートでは、ルータのDNS機能を使っていたが、今は自作サーバのbindが自宅内ネットワークのDNSを担当している。
しかし、何故か名前解決が遅い。5秒程度かかっている模様。完全に体感できる遅さで、だんだんとストレスが溜まってきた。試しにルータのDNSに切り替えてみると、瞬時に結果が得られる。これは自分で構築したbindが何かいけないことになってるはずだ。
ということでいろいろ検索して原因を探すと、ipv6が遅さの原因らしい。Solaris10付属のbindは、ipv6に対応したコンフィグレーションとなっていて、ipv4よりも前にipv6が優先されて参照され、毎度毎度ipv6のタイムアウトを待っている状態だった。しかも、bindのipv6対応の無効化は、再makeが必要とのこと。面倒なことになりそうだけど、ネット環境は精神衛生上大切なので、チャレンジすることにした。
最新のbindを落としてきて、--enable-ipv6=noを付けてconfigure&make。「そう言えばbind8からbind9へのバージョンアップだな」とか思いながら、コンパイル＆インストールは終了。設定ファイルはそのまま引き継ぐので手を加えず、svcのマニフェストファイルに手を入れて新しくインストールしたbindが使われるようにして、サービスを再起動。
期待通り、namedが起動しない。「unknown key ***」。設定ファイルいじってないのに。。。
どうもbind9では、named.confファイルからrndc.keyファイルをinclueしないといけなくなったらしい。そのための文をnamed.confファイルに追記して再度サービスを起動したら、ちゃんとnamedが常駐するようになった。
速い。これは速い。というか、これが普通。今までのipv6タイムアウト状態があり得なかった。
というわけで、新居でも快適なネット環境となりましたとさ。めでたしめでたし。
[参考にしたサイト]

[http://cocorat.com/blog/entry/solaris10%E3%81%A7ipv6%E3%82%92%E7%84%A1%E5%8A%B9%E3%81%AB%E3%81%97%E3%81%9Fbind9%E3%82%92%E5%B0%8E%E5%85%A5%E3%81%99%E3%82%8B](http://cocorat.com/blog/entry/solaris10%E3%81%A7ipv6%E3%82%92%E7%84%A1%E5%8A%B9%E3%81%AB%E3%81%97%E3%81%9Fbind9%E3%82%92%E5%B0%8E%E5%85%A5%E3%81%99%E3%82%8B)

[http://sakanade.asablo.jp/blog/2008/05/29/3548480](http://sakanade.asablo.jp/blog/2008/05/29/3548480)
