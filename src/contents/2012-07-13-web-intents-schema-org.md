---
layout: post
status: publish
published: true
title: Web IntentsとSchema.orgの連携が模索され始めています
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1627
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1627
date: '2012-07-13 09:32:20 +0900'
date_gmt: '2012-07-13 00:32:20 +0900'
categories:
- Web Intents
- Semantic Web
---

Web Intentsは、Webアプリケーション間の連携を可能とする技術となりますが、そこで規定されていることの中心は「ユーザが何をしたいか」についてです。つまり「共有したい」「編集したい」「保存したい」といった行為に関することであり、「何を」という部分については、そのペイロードの部分の規定はあれど、厳密な説明は仕様に書かれていません。それは当たり前であり、Web Intentsの守備範囲外ということになりますが、では「何を」の部分が実際にどのように規定されるかについて、Schema.orgがその答えになるのではないか、という記事が
[semanticweb.com](http://semanticweb.com/)に投稿されていましたので、翻訳してみました。

---

[共に来る： Schema.orgとWeb IntentsはWin-Winの機会を作る](http://goo.gl/jpY7Y)

あなたがSchema.orgのマークアップ語彙とWeb Intents仕様と組んだ時、あなたは何を得るでしょうか？コンテンツの公開者と検索エンジンの両方がWin-Winになる、とMichael Hausenblas博士(Linked Data Research Centre, DERI, NUI Galway, Ireland)は言います。

今週Hausenblasは、Schema.orgのボキャブラリを使ってマークアップされたカメラの検索では、そのオブジェクトを取るためにWeb Intentsの波(既存および新規のもの)を提供することができる方法を説明し、その2つを繋ぐ"素晴らしさ"について書きました。

"Schema.orgを使って、私達は、本やカメラのような、Webページ上で公開する物事を説明するための方法を持ちます。そして、Web Intentsを使うことで、私達は柔軟な方法でそれらの物事を互いに作用させるための技術を手にします。"と彼は書いています。クライアント側のサービス発見とアプリケーション間の通信のためのフレームワークであるWeb Intentsを使って、サービスはユーザに代わってアクションを受け取ることができるという意図を登録します。

セマンティックWebブログでの話として、HausenblasはどのようにWin-Winが起きるかを説明しています： "コンテンツの公開者は、ページランク向上だけでなく彼らのコンテンツをよりインタラクティブにさせるために、そこにセマンティックなマークアップを使うことでインセンティブ性を追加します。"と彼は言っています。"そして、それは検索エンジンのためにもとても大きな事であり、ユーザは直接それらと影響し合うことができます。"

共に完成

GoogleのDeveloper AdvocateであるPaul Kinlanは、明示的に互いに知る必要がなく、でも互いに機能することができるアプリケーションやサービスを開発できるための方法として、2010年にWeb Intentsプロジェクトを始めました。それは今では、サービスによって実行されるためのユーザにより開始されるアクションのための仕様として、Web Intents部会はW3Cにてpublic-webappsおよびDAPグループと連携して開発が進んでいます。

```
<intent
  action="http://webintents.org/share"
  type="image/*"
  href="share.html"
/>
```

その説明として、Web Intentsはサービスが受け取れるインテントをリストアップできるための宣言的な書式を提供します。この方法を使って、サービスが受け取れるアクションは何か？サービスが期待するデータ型はどれか？といったことをマークアップします。インテントは、Webページやブログ投稿、または動画のようなオンライン素材を一般的に扱うためのアクション(discover、share、edit、view、pick、subscribe、そしてsave)を含みます。

Hausenblasは言います。schema.orgの用語における新しい事は、アクションがWeb Intentsの元々フォーカスしている範囲を超えることができるために、それが全ての種別のコンテンツのための用語(people、booksなど)を提供していることです。彼が言うキーポイントは、エンティティの確実な型を持つことです - 本のような現実世界のもの, 具体的には、それの購入のような他の相互作用が可能で、そのことに加えて、友達とそれを共有するような、Webの素材について行うことができます。

特定のタグ付けされたエントリ周りの共有アクションのような、いくつかの単純なことはおそらく簡単に実装され、そしてすぐに利用可能となるでしょう。しかし、"アクションの複雑性に依存して、大きな試みがおそらく必要になるでしょう。"と彼は言います。"例えば、何かを購入するために、あなたはおそらくセキュリティなどのようなことを深く考える必要が出てくるでしょう。"

Hausenblasは、schema.orgの用語とWeb Intentsのマッピングのために、コンセプトの証明を始めました。それは、Web Intentsを通じてschema.orgのデータを渡すことをディスカッションするために、W3CのWikiページにすでにあります。彼が約1年前にそれを発見して以来、彼がKinlanの仕事のサポーターになっていることを、セマンティックWebブログで話しています。数日前、Web Intentsコードラボがオンラインに掲載されたことがアナウンスされました。そしてまた、最近行われたGoogle I/O 2012のセッション(How to Build Apps that Love Each Other with Web Intents - Web Intentsを使って互いに密に連携可能なアプリケーションをどのように作るか)も投稿されました。そのセッションは、Kinlanと、WebにWeb Intentsをもたらすための仕事をしているChromeチームの技術リードであるJames Hawkinsによってホストされました。Kinlanは、"Googleにおいて、Chromeにこれを実装することで、人々が確信する大きな仕事が行われまし。そして今W3Cと共に、Web Intentsはより勢いが増しています。"、とHausenblasに言います。Hausenblasは、Googleでの内部的なラウンドを行うためのコンセプトの証明を生み出すことが可能な何かとなるであろうKinlanのヘルプと数日間の作業、一般的な語彙のワーキンググループへの取り組みへの移行のような、そこから決められる次のステップに注目しています。
