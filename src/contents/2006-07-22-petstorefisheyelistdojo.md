---
layout: post
status: publish
published: true
title: PetStoreで使われている「FisheyeList」Dojoウィジェット
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 232
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=232
date: '2006-07-22 00:10:14 +0900'
date_gmt: '2006-07-21 15:10:14 +0900'
categories:
- dojo
---

Java PetStoreのトップページは，ペットの種類を選択する画面となっている。この画面では，中央に鳥のイメージとその周囲に各ペットの種類を表すイメージが配置されている。そして何よりも目を引くのが，左側にあるペットの種類のイメージリストだ。この中にマウスカーソルを合わせると，マウスカーソルの下にあるイメージが盛り上がるようにズームアップされる。更にマウスカーソルを上下に移動させると，その盛り上がりがマウスカーソルに追随されるような効果を見ることができる。まるで，Mac OS XのDockのような効果である。

![FisheyeList1.gif](http://www.eisbahn.jp/yoichiro/images/FisheyeList1.gif)
これは，Dojoのウィジェットを使うことによって実現されている。使われているウィジェットは「FisheyeList」。魚眼リスト，と日本語で直訳できるが，ようは凸レンズを通して見たときのような効果を得られるウィジェットだ。
このページは，petstore/Web Pages/index.jspファイルにより構成される。index.jspファイル中の


Dojoに搭載されている各種モジュールを使用するには，dojo.require()メソッドを使って，予め使用を宣言しておく必要がある。これは，必要な分だけWebブラウザにコードを読み込むようにするためである（といってもdojo.jsだけでも大きいけど）。index.jspでは，dojo.widget.FisheyeListという文字列をrequire()メソッドに渡すことにより，FisheyeListウィジェットを読み込んでいる。
そして，実際にFisheyeListウィジェットを適用している箇所が，以下のコードだ。

>　

　

　

　

　・・・

タグのclass属性にdojo-FisheyeListを指定することにより，この要素がFisheyeListウィジェットとして適用される。そして，FisheyeListウィジェットが必要とする各種パラメータを，dojo名前空間で修飾された各種属性によって与えている。属性の意味は，以下の通り。

* itemWidth, itemHeight - 通常時(マウスカーソルが乗っていないとき)の大きさ

* itemMaxWidth, itemMaxHeight - 最大時(マウスカーソルが乗っているとき)の大きさ

* orientation - アイテムを並べる方向(horizontal=水平，それ以外=垂直)

* effectUnits - アイテムの大きさが変化するときの増分値

* itemPadding - アイテム間の間隔

* attachEdge - アイテムの効果の基準となる軸

* labelEdge - キャプション文字列を表示する位置

* enableCrappySvgSupport - 謎
大体どれも想像がつくのではないだろうか。attachEdge属性などを変更すると，その効果がわかりやすい。例えば，現在centerとなっているのをleftにすることで，アイテムの左端を基準として，アイテムが膨張されるようになる。

![FisheyeList2.gif](http://www.eisbahn.jp/yoichiro/images/FisheyeList2.gif)
dojo-FisheyeListが指定された

タグの子要素として，各アイテムを定義していく。アイテムは，class属性にdojo-FisheyeListItemを指定する。アイテムが持つ属性は，以下の通り。

* onClick - アイテムがクリックされたときに呼び出されるイベントハンドラ

* iconsrc - アイテムとして表示するイメージのパス

* caption - アイテムがフォーカスされたときに表示するキャプション文字列
caption属性は使われていないが，例えば，iconsrc属性にcats_icon.gifファイルを指定している

タグを以下のように変更してみる。

>　　

さらに，labelEdge属性をbottomからrightに変更すると，以下のようにキャプションが表示されるようになる。

![FisheyeList3.gif](http://www.eisbahn.jp/yoichiro/images/FisheyeList3.gif)
イメージの伸縮は，FisheyeListウィジェットが勝手にやるので，僕らはアイテムとしたい画像ファイルをそれぞれ用意するだけで良い。あとは各アイテムの

タグのonClick属性で，アイテムがクリックされたときの動作を記述するだけで，クールなメニューが出来上がる。なんとも楽ちんだ。
ちなみにPetStoreでは，petstore/Web Pages/styles.cssファイル中に以下のように記述することにより，アイテムにマウスカーソルを置いた際にマウスポインタが変更されるようにしている。

>.dojoHtmlFisheyeListBar {
　　cursor:pointer;
　　margin: 0;
　　width: 300px;
　　text-align: right;
}

PetStore 2.0ea1の中で使われているDojoのウィジェットは，とりあえずFisheyeListだけのようだ。しかし，このFisheyeListウィジェットを見ただけでも，Dojoの潜在能力を知ることができる。難しいこと抜きにして，Dojoってとっても面白そうだ。
FisheyeListウィジェットをトップページに配置するあたり，PetStore作者の気持ちが伝わってくる。
