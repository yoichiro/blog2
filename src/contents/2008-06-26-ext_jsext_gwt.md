---
layout: post
status: publish
published: true
title: Ext JS/Ext GWT勉強会に参加してきました
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 557
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=557
date: '2008-06-26 10:32:39 +0900'
date_gmt: '2008-06-26 01:32:39 +0900'
categories:
- OpenSocial
---

昨日行われた「
[第1回Ext JS/Ext GWT勉強会](https://rs.goga.co.jp/member/ext/)」にお呼ばれしていたので、参加をしてきた。主催された方々は皆Mash up Awardつながりな方ばかりだったので、変に緊張することもなく楽しんできた。

内容は、以下の通り。

* [Ext Japan](http://extjs.co.jp/)について

* ユーザ事例を３つ紹介* * フリーディスカッション

やはりExt.jsはまだまだ日本語の情報が少ないとのこと。確かに検索してみても、ヒット数は少ない。しかし、嬉しいことに
[翻訳プロジェクト](http://www.ext-japan.org/forum/?CategoryID=2)がすでに立ち上がっているので、今後日本語の情報も一気に増えてくるだろう。
[APIリファレンス](http://www.ext-japan.org/docs/)などは、かなりの量が翻訳済みなので、非常に参考になるだろう。

事例として紹介されていた「
[CRESCAT](http://www.crescat.jp/cresfield/client/)」と「
[Afrous](http://www.afrous.com/)」は、どちらもExt.jsを使った力作。ここまでできるんだ、という良いお手本である。もちろん、
[ONGMAP](http://ongmap.com/)も。
[Ver.2](http://ongmap.com/v2/)が出ていたのは、申し訳ないことに昨日知った。

フリーディスカッションの中で、やはり皆さん気になっていたのは、以下の2点。

* いかにも！というExt.jsのデザインは何とかならんのか？

* ライセンスってどうよ

デザインの話は、テーマの仕組みが備わっているとは言え、firebugなどを使ってDOM構成を見て、適用されているCSSを調査し、地道に調整していく、というのが現実解とのこと。IDEの登場を期待したい、との意見も出たが、個人的にはせめてWIndowsのコンパネにある画面のデザイン設定くらいの少ない項目設定数で、全体の色調が変更できる、というくらいの取っつきやすさが欲しいな、と。

ライセンスの話は、どうもまだ詳細はCase by Caseのようだ。今なら有利な価格を組めるかも！？なんて妄想はさておき、事例が今後増えてくることによって、その辺も確立されていくということになりそう。そもそもExt.js LLCの本家でもこれからという状況らしいので、今後に期待ということで。まずはExt Japanにご相談を、と。

僕が質問したのは「Extって『いぃえっくすてぃ』って読み方でいいんですか？」。開発者も「いぃえっくすてぃ」「イーエックスティー」とアルファベット読みをしているとのことなので、それが正解。ずっと悩んでいた最大の疑問が晴れてすっきりした。

さて、手ぶらでいくのも失礼というものなので(?)、OpenSocialアプリケーションでExt.jsを使えるかどうか簡単に検証して、デモを作って紹介してみた。検証結果としては、何の苦労もなくあっさりとExt.js 2.1を使うことができた。

![opensocial-ext.jpg](http://www.eisbahn.jp/yoichiro/images/opensocial-ext.jpg)

Ext.jsのGridPanelを使って、OpenSocial APIにより友達一覧を取得、それをGridPanelにレンダリングしてもらうというデモ。orkutでも、hi5でも、MySpaceでも、iGoogleでも、ちゃんとそのまま動くこともお見せしてみた。実際のコードが以下。Ext.jsのJavaScriptファイルとCSSファイルの場所は偽物に変えてあるが、そこを直せばちゃんと動くコードである。

version="1.0" encoding="UTF-8"?

]]>

今後は定期的に勉強会などが開かれるそうなので、興味をお持ちの方はぜひ参加してみて欲しい。もちろん楽しい方ばかりなので、充実した時間を過ごせること請け合いです！

もしOpenSocialに興味を持っていただいた方には、
[こちらがオススメ](http://groups.google.co.jp/group/opensocial-japan)である。
