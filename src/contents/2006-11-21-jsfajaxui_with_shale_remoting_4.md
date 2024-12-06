---
layout: post
status: publish
published: true
title: JSF+AjaxでUIコンポーネント開発 with Shale Remoting (5)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 267
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=267
date: '2006-11-21 20:38:33 +0900'
date_gmt: '2006-11-21 11:38:33 +0900'
categories:
- Shale
---

「
[JSF+AjaxでUIコンポーネント開発 with Shale Remoting (4)](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting_3.html)」で予告した内容を今回は置いておいて，大事なことを書くのを忘れていたので，今回はそれを先に取り上げる。
double-selectコンポーネントのJAR内の構成など，突然実装寄りな話に前回は終始してしまったが，肝心のdouble-selectコンポーネントを利用する際のカスタムタグの記述がどういうものになるかを示していなかったことに気がついた。まずは下のリストを見て欲しい。


double-select要素がdouble-selectコンポーネントを表している。これは本体部を持たない。そして，以下の4つの属性を持つ。

* id - このコンポーネントを特定するためのID

* leftSelectItems - 左のコンボボックスの選択肢を得るためのMethod Binding式

* rightSelectItems - 右のコンボボックスの選択肢を得るためのMethod Binding式

* value - 左右のコンボボックスの選択値の格納先となるValue Binding式
leftSelectItems属性とrightSelectItems属性に書かれたメソッドバインディング式は，メソッドバインディングが行われる訳ではなく，実際にはShale RemotingにてAjaxでの動的呼び出しが行われる。id属性は省略可。
このカスタムタグをJSP内に記述すると，実行時に以下のようなdouble-selectコンポーネントが出現するというわけだ。
さて，今回のdouble-selectコンポーネントと検証用アプリの構成要素も紹介しておこう。前回のJARの構成内容とかぶっている箇所もあるが，まずはどんなファイルがあって，それぞれの役割はどんなもんなのかを，ざっくりと把握してほしい。
[double-selectコンポーネント]

* DoubleSelectTag.java - JSF専用カスタムタグハンドラクラス
　　　　JSPに記述されたタグの内容を取得し，UIコンポーネントにセットする。

* DoubleSelectRenderer.java - double-selectコンポーネントレンダラクラス
　　　　コンポーネントのレンダリングおよびサブミット値の解釈を行う。

* double-select.js - Ajaxリクエスト＆レスポンス担当JavaScript関数
　　　　選択肢の取得とコンボボックスへの反映を行う。

* faces-config.xml - コンポーネント定義
　　　　double-selectコンポーネントのレンダラキットを定義する。

* taglib.tld - タグライブラリディスクリプタ
　　　　double-selectカスタムタグの記述内容を定義する。
[double-selectコンポーネント検証用アプリ]

* ParameterBean.java - 値格納用ManagedBeanクラス
　　　　double-selectコンポーネントの選択値がセットされる。

* SelectItemsProducer.java - 選択肢作成用ManagedBeanクラス
　　　　double-selectコンポーネントの選択肢を提供する。

* index.jsp - テストページJSPファイル
　　　　double-selectコンポーネントを配置する。

* prototype.js - JavaScriptライブラリ
　　　　Ajaxリクエストの扱いを楽にするために使用する。

* faces-config.xml - ManagedBean定義
　　　　ParameterBeanおよびSelectItemsProducerをManagedBeanとする。

* web.xml - Webアプリケーション定義
　　　　FacesServletの定義を行う。

* log4j.xml - log4j設定ファイル
　　　　ログ出力の内容を定義する。
これらがどのような連携の元に動作を行うのかについて，次回紹介したいと思う。
ここで，せっかちな人のために，double-selectコンポーネントの開発用プロジェクトおよび検証用プロジェクトを，それぞれ下記からダウンロードできるようにしてみた。

* [double-select.tar.gz](http://www.eisbahn.jp/yoichiro/double-select.tar.gz) - double-selectコンポーネント開発用プロジェクト

* [test-ds.tar.gz](http://www.eisbahn.jp/yoichiro/test-ds.tar.gz) - double-selectコンポーネント検証用プロジェクト
EclipseやTomcatプラグイン，Maven2に慣れている方は，説明がなくても大丈夫だと思うので，ご参考まで。
