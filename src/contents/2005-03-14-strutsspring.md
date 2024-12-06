---
layout: post
status: publish
published: true
title: Struts+Springの設定ファイル自動生成
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 14
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=14
date: '2005-03-14 10:40:07 +0900'
date_gmt: '2005-03-14 01:40:07 +0900'
categories:
- Java
---

StrutsとSpring Frameworkでシステムを開発する場合，その動作を決定する設定ファイル群をどう作っていくかがポイントとなる。具体的には，struts-config.xmlと[Webアプリ名]-servlet.xmlの２ファイルだが，とにかくこれらのファイル，大きくなってしまうのだ。

チーム開発では，成果物が細かければ細かいほど，並行作業がし易くなる。ただし，その分上記のような設定ファイルにしわ寄せがいってしまう。大きな設定ファイルを複数人でメンテしていく作業が難しいことは，改めて言うことではないだろう。

ではどうやって解決するのか？対応は２つ。

* 巨大なファイルの編集を助けてくれるツールを利用or自作* 巨大なファイルを自動生成

僕の趣味的には1.に軍配があがる。何のために設定内容をソースコードから分離しているのかを考えたらファイルが巨大になるのは当たり前。巨大になるってことは把握しにくくなるってことなので，把握しやすくしてくれるツールがあれば解決するはず。複数人での開発なら，別にCVSなりVSSなりを使えばいいだけのことだ。

とはいえ，利口な開発者は多くないため，おバカな修正をかけて他の開発者に迷惑をかけかねない。それを考えると，2.を採用するのも手だ。ちゃんと実装を見据えた設計書が書かれていれば，2.の方が実装の手間というか，混乱は少ないはずだ。

というわけで，StrutsとSpringの設定ファイルを自動生成させるにはどんな手があるかな，と調査開始。やっぱりXDoclet使うのが普通かなと思ったけど，StrutsとSpringの設定ファイルを一気に生成させるには，標準のXDocletじゃダメかなと思い，自分でタグを定義して，自分で出力ファイルを制御するためのやり方をネットで探してみた。

* [dw: Java technology : XDocletでコードの肥大化を防ぐ](http://www-6.ibm.com/jp/developerworks/java/041112/j_j-xdoclet.html)

なるほど，複雑なことをやらない限り，xdtテンプレートファイルを作っていくだけで，元となるソースコードから設定ファイルとかを出力するのはさほど難しくなさそうだ。
とか
とかを，テンプレートに埋めていけばよさそう。

ということがわかったところで，そもそもXDocletってどこまでできるんだろう・・・という疑問が沸いた。ActionオブジェクトをSpring管理下におくためには，struts-config.xmlでtype属性にDelegatingActionProxyを指定する必要がある。でも，XDoclet使った場合は，きっとtype属性は指定できないんだろうなぁ，と勝手に思い込んでいた俺。

その思い込みは間違いだったと以下のページを見て思い知らされた。

* @struts Class Level Tags - 
[@struts.action](http://xdoclet.sourceforge.net/xdoclet/tags/apache-tags.html#@struts_action__0____)

これのtypeパラメータの説明には，しっかりと，

>「The class to instantiate for this action. Defaults to the current class, but can be overridden for certain uses, such as integration with Spring. 」


というように書いてあって，Spring向けにtype属性をオーバーライトできるようになっていることが判明。最新のXDocletでは，Struts+Springの組み合わせがしっかりとサポートされていたのだ。

というわけで，自作する必要はなさそう。素直にXDocletでActionクラスのソースにXDocletタグを書いていく方法で，設定ファイルの自動生成は実現できるそうだ。
