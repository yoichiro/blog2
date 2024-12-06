---
layout: post
status: publish
published: true
title: JSF+AjaxでUIコンポーネント開発 with Shale Remoting
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 285
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=285
date: '2006-12-14 09:10:04 +0900'
date_gmt: '2006-12-14 00:10:04 +0900'
categories:
- Shale
---

Ajax対応JSFコンポーネントをApache Shale Remotingを使って作成する方法を，全15回に渡ってエントリした。

****

「
[JSF+AjaxでUIコンポーネント開発 with Shale Remoting (1)](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting.html)」
　[全般] やってみようと思った理由と，Shale Remotingの紹介。

****

「
[JSF+AjaxでUIコンポーネント開発 with Shale Remoting (2)](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting_1.html double-select)」
　[全般] 作成するコンポーネント（double-selectコンポーネント）の紹介。

****

「
[JSF+AjaxでUIコンポーネント開発 with Shale Remoting (3)](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting_2.html)」
　[全般] 開発のための準備（Eclipseプロジェクトの作成）。

****

「
[JSF+AjaxでUIコンポーネント開発 with Shale Remoting (4)](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting_3.html)」
　[全般] JSFコンポーネントのパッケージング方法と，double-selectコンポーネントのファイル構成の説明。Shale Remotingの基本的な機能紹介（なぜShale Remotingが便利なのか）。

****

「
[JSF+AjaxでUIコンポーネント開発 with Shale Remoting (5)](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting_4.html)」
　[全般] double-selectコンポーネントのJSP上でのタグ記述の内容と，double-selectプロジェクトおよびtest-dsプロジェクトのファイル構成の説明。

****

「
[JSF+AjaxでUIコンポーネント開発 with Shale Remoting (6)](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting_5.html)」
　[double-selectプロジェクト] taglib.tldファイルの説明

****

「
[JSF+AjaxでUIコンポーネント開発 with Shale Remoting (7)](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting_6.html)」
　[double-selectプロジェクト] DoubleSelectTagクラスの説明。

****

「
[JSF+AjaxでUIコンポーネント開発 with Shale Remoting (8)](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting_7.html)」
　[double-selectプロジェクト] double-selectコンポーネントのレンダリング内容と，Shale Remotingの動的ManagedBeanメソッド呼び出し機構の説明。

****

「
[JSF+AjaxでUIコンポーネント開発 with Shale Remoting (9)](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting_8.html)」
　[double-selectプロジェクト] DoubleSelectRendererクラスの説明（エンコード処理）。Shale Remotingが提供しているXhtmlHelperクラスの使い方も紹介。

****

「
[JSF+AjaxでUIコンポーネント開発 with Shale Remoting (10)](http://www.eisbahn.jp/yoichiro/2006/12/jsfajaxui_with_shale_remoting_9.html)」
　[double-selectプロジェクト] double-select.jsファイルの説明。

****

「
[JSF+AjaxでUIコンポーネント開発 with Shale Remoting (11)](http://www.eisbahn.jp/yoichiro/2006/12/jsfajaxui_with_shale_remoting_10.html)」
　[double-selectプロジェクト] DoubleSelectRendererクラスの説明（デコード処理）。

****

「
[JSF+AjaxでUIコンポーネント開発 with Shale Remoting (12)](http://www.eisbahn.jp/yoichiro/2006/12/jsfajaxui_with_shale_remoting_11.html)」
　[double-selectプロジェクト] faces-config.xmlファイル（renderKitの定義）の説明と，double-selectコンポーネントの仕上げ。

****

「
[JSF+AjaxでUIコンポーネント開発 with Shale Remoting (13)](http://www.eisbahn.jp/yoichiro/2006/12/jsfajaxui_with_shale_remoting_12.html)」
　[test-dsプロジェクト] 検証用ページ（index.jspファイル）の説明。

****

「
[JSF+AjaxでUIコンポーネント開発 with Shale Remoting (14)](http://www.eisbahn.jp/yoichiro/2006/12/jsfajaxui_with_shale_remoting_13.html)」
　[test-dsプロジェクト] 検証用ManagedBeanクラス（ParameterBean，SelectItemsProducer）の説明。

****

「
[JSF+AjaxでUIコンポーネント開発 with Shale Remoting (15)](http://www.eisbahn.jp/yoichiro/2006/12/jsfajaxui_with_shale_remoting_14.html)」
　[test-dsプロジェクト] faces-config.xml，web.xml，log4j.xmlファイルの説明と，動作確認手順の紹介。まとめ。

****

第１回目が11月18日で，第15回目が12月13日。気がつけば1ヶ月近くもかかっている。もっと簡潔に説明できただろうに。。。改めて自己嫌悪。orz
最後の回でも書いたが，JSFコンポーネントの一般的な作り方も合せて解説したつもりなので，JSFコンポーネントを作ってみたいと思っている人にも役に立つ情報かと思っている。もちろんAjaxという言葉が重要なキーワードとなっている現在では，Shale Remotingを使ったJSFコンポーネントの直感的かつ迅速な開発は非常に有効だろう。
日々の開発に役立てて欲しい。
[2006/12/26 追記] 以下のURLで実際に動作するものを公開したので，試してみて欲しい。

[http://www.eisbahn.jp:8080/test-ds/index.faces](http://www.eisbahn.jp:8080/test-ds/index.faces)
