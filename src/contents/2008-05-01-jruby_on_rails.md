---
layout: post
status: publish
published: true
title: "「JRuby on Rails」について発表しました"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 541
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=541
date: '2008-05-01 21:30:48 +0900'
date_gmt: '2008-05-01 12:30:48 +0900'
categories:
- Ruby on Rails
---

昨日の4月30日、JJUG主催による「クロスコミュニティカンファレンス」にて、「JRuby on Rails」というお題目で話をしてきた。

[![SlideShare](http://static.slideshare.net/swf/logo_embd.png)](http://www.slideshare.net/?src=embed) | 
[View](http://www.slideshare.net/yoichiro/jruby-on-rails-383150?src=embed) | 
[Upload your own](http://www.slideshare.net/upload?src=embed)
聴衆の中に潜んでいたJRuby第一人者から、手痛い突っ込みの数々を頂いた。ここで上記資料の中で訂正（ってわけじゃないけど）してみようと思う。

* 32枚目の「CGIに比べてパフォーマンス的に有利」と記述してしまったが、現状ではmongrel_clusterで真面目に(?)構築した方が、JRubyよりもパフォーマンスはいい数値が得られている、とのこと。僕が実測したときには、初回のアクセス以外はかなりレスポンスは良かったのだが、全体的にはまだまだ、という印象のようである。

* 33枚目でセッションのクラスタリングの話をしたのだが、現在のJRuby+GoldSpikeでは、普通にJavaのHttpSessionを使ってくれるので、普通にAPサーバのセッションクラスタが効くよ、とのこと。

* 39枚目で、include_classを使っているが、現在はimportを使うよ、とのこと。
というわけで、上記に読み替えてスライドを見て欲しい。会場にきてくれた皆さま、申し訳ございません。いろんな方々から「わかりやすくて良かったよ！」と感想を頂いていただけに、ホントに恥ずかしい限りである。。。
