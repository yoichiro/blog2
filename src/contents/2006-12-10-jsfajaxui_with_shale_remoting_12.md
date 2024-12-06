---
layout: post
status: publish
published: true
title: JSF+AjaxでUIコンポーネント開発 with Shale Remoting (13)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 280
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=280
date: '2006-12-10 10:24:33 +0900'
date_gmt: '2006-12-10 01:24:33 +0900'
categories:
- Shale
---

Shale Remotingを使ったAjax対応JSFコンポーネントの作成も，今回で１３回目を迎えた。かなり気合いが入ったエントリが続いているが，前回までの１〜１２回で，double-selectコンポーネントができ上がった。今回からは，double-selectコンポーネントを利用するためのtest-dsプロジェクトを解説していこうと思う。

まずは，test-dsプロジェクトをどんなアプリケーションにするかだが，単純に１つのページにdouble-selectコンポーネントを配置して，左右のコンボボックスの選択肢がAjaxとShale Remotingによって動的に変更されるのを確認する。そして，コマンドボタンも配置して，double-selectコンポーネントの選択値がManagedBeanに正しくバリューバインディングされてるかどうかについても確認をする。

では，さっそくJSPから見ていこう。全体をドカンと紹介してもいいのだが，もったいぶって小出しにする。では，htmlタグの前の部分から。



注目は４行目。double-selectコンポーネントのタグライブラリをtaglibディレクティブによって使用可能としている。この際，前のエントリで決定したURIを記述し，接頭語を"my"とした。これに関してはJSFだからといって特別なことはない。

では，htmlタグからdouble-selectコンポーネントの前までのコードを示す。

Test my custom component

ここでの注目は５行目。double-selectコンポーネントは，double-select.jsファイルの中で，prototype.jsを前提としたJavaScriptコードが記述されている。よって，上記５行目でprototype.jsファイルを読み込んでいる。

１ページ内にShale Remotingを使ったJSFコンポーネントのみが配置されるのであれば，このような汎用的に使うjsファイルを，JSFコンポーネント自身がレンダリングしても良いかもしれない（
[前のエントリ](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting_8.html)参照）。しかし，例えばmyfacesのtomahawkなどに代表されるサードベンダー提供のJSFコンポーネントも同一ページで使用するとなると，prototype.jsファイルのような有名なライブラリを複数回読み込んでしまう危険性が増してしまう。よって，極力prototype.jsファイルのような汎用的なjsファイルは，JSFコンポーネントが提供するのではなく，JSFコンポーネントを利用する側が準備するようにした方が問題が出にくくなるのではないかと思う。

さて，目玉となるdouble-selectコンポーネントを配置する部分のコードを示そう。



ビュールートを示すf:viewタグとh:formタグで，double-selectコンポーネントとコマンドボタンのh:commandButtonコンポーネントを括っている。h:formタグは，HTMLのフォームになると共に，その中に配置される各コンポーネントのIDを決定する際にも使用される。そして，接頭語"my"を使って，double-selectタグを記述している。

![ds-binding.jpg](http://www.eisbahn.jp/yoichiro/images/ds-binding.jpg)

value属性に記述したバリューバインディング式は，"parameterBean"という名前のManagedBeanオブジェクトが持つselectedValueプロパティを指定している。また，leftSelectItems属性とrightSelectItems属性に記述したメソッドバインディング式は，"selectItemsProducer"という名前のManagedBeanオブジェクトが持つgetLeftSelectItems()メソッドおよびgetRightSelectItems()メソッドを指定している。このメソッドバンディング式は，実際にはメソッドバインディングされずに，AjaxとShale Remotingを使って選択値を取得する際に使用される。

最後に，bodyタグとhtmlタグを閉じるための記述を行って，index.jspファイルの記述が完了する。



protoptye.jsファイルをインポートしてはいるが，JavaScriptコードを一切記述していないのがわかるだろう。さらに，選択肢をAjaxで取得するために呼び出すManagedBeanのメソッドの記述として，メソッドバンディング式で記述できることも直感的である。

今回紹介したindex.jspファイルを
[ここからダウンロード](http://www.eisbahn.jp/yoichiro/test-ds/index.jsp)して，以下の場所に保存して欲しい。

・[Eclispeワークスペース]/test-ds/src/main/webapp

次回は，double-selectコンポーネントの選択肢を提供するManagedBeanとしてdouble-selectタグの属性値で指定した，SelectItemsProducerクラスを紹介する予定である。
