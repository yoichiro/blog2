---
layout: post
status: publish
published: true
title: "怒りと共にlingrclipseをバージョンアップ"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 358
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=358
date: '2007-04-20 22:36:51 +0900'
date_gmt: '2007-04-20 13:36:51 +0900'
categories:
- Eclipse
---

先ほど，lingrclipse-coreを1.3.0にバージョンアップした。昨日あたりからlingrclipseを使ってLingrサービスに入室ができなくなっているはずなので，ぜひすぐにバージョンアップを行って欲しい。
今回の修正は，LingrのAPIに変更が生じたために必要となった。具体的には，Room情報にcreated_at属性（たぶんそのルームが作られた日時）が追加された結果，JSONからJavaBeanへのマッピングに失敗してしまうことが修正理由である。現状の作りは，JSONに含まれる属性については，すべてJavaBeanのプロパティとして定義されていなければならないため，こういったAPI変更については，毎度毎度追随して修正しなければならない。
このことは実装の方針を決めたときにはある程度覚悟していたことだ。それは，
[LingrのAPIを説明したWiki](http://wiki.lingr.com/dev/show/HomePage)によって，APIのリリースの度にちゃんと事前に予告されることを期待していたことが判断材料である。しかし，今回のAPI変更について，Wikiには何も書かれていない。もしかしたら他に書かれているサイトがあるのかもしれないが，WikiはLingrのオフィシャルページから誘導されてたどり着くものであり，ここを頼りにアプリケーションを開発している人も多いはずだ。その頼りの場所に何の通知もなく，暗黙的にAPIが変更される影響を，Lingr側の人間はどう考えているのだろうか？
公開されたサービスをを使って構築されたサイトやアプリケーションは，非常に重要なサービスの入り口であり，それらの数が増えることでサービスの価値が向上していく。第3者によって開発されたアプリケーションがあるからこそ，サービスの価値が生まれるのではないだろうか。Lingrの場合は，RESTサービスとしてAPIが公開されていることに価値があるのであり，それを除いたら単なる１チャットサイトである。
例えば「属性の追加くらいは影響がないだろう」なんて考えでAPIを修正されてしまっては，そのサービスの価値はゼロ。
影響が発生することは全くもって構わない。緊急な変更も致し方ない。何も通知がなく，開発されたアプリケーションがある日動かなくなって，利用者からアプリの作者に「動かねーぞ」と連絡が入る状況自体が，最低な状態なのだ。
開発者向けのMLや，API変更が必ず通知されるRSSフィードなど，いくらでも方法はある。Lingr側の体制や意識，そして情報伝達の仕組みについて，今一度考え直していただきたいものである。
