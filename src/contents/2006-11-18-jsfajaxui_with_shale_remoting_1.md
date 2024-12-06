---
layout: post
status: publish
published: true
title: JSF+AjaxでUIコンポーネント開発 with Shale Remoting (2)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 263
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=263
date: '2006-11-18 23:37:42 +0900'
date_gmt: '2006-11-18 14:37:42 +0900'
categories:
- Shale
---

「
[JSF+AjaxでUIコンポーネント開発 with Shale Remoting (1)](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting.html)」で予告した通り，さっそく作成するコンポーネントはどういったものかを紹介しよう。コンポーネントの内部動作を把握できれば目的達成なので，最低限かつ再利用可能な機能とする。
題して「DoubleSelectコンポーネント」。
DoubleSelectコンポーネントは，２つのコンボボックスから構成される。２つのコンポーネントは，横に２つ並べる。左のコンボボックスが大分類，そして右のコンボボックスが小分類，という想定だ。つまり，左のコンボボックスの選択が変更されたら，右のコンボボックスの選択肢が左の選択状態に依存して動的に変更される。
例えば，左のコンボボックスの選択肢が２つあり，最初に選択されている左の選択状態「JR」に依存した選択肢が右のコンボボックスにセットされている。

![s2.gif](http://www.eisbahn.jp/yoichiro/images/s2.gif)
そして，左のコンボボックスの選択状態を「東京メトロ」に変更すると，

![s3.gif](http://www.eisbahn.jp/yoichiro/images/s3.gif)
それに従って右のコンボボックスの内容が変更される。

![s4.gif](http://www.eisbahn.jp/yoichiro/images/s4.gif)
左右それぞれのコンボボックスの選択肢は，Ajaxによって非同期に取得する。この際，Shale Remotingを使用してManagedBeanのメソッド呼び出しを要求し，結果をJSON形式で受けとる。JSONテキストを評価してオブジェクトにし，

タグを生成して対象の
