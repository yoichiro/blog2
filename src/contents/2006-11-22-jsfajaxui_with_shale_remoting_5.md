---
layout: post
status: publish
published: true
title: JSF+AjaxでUIコンポーネント開発 with Shale Remoting (6)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 268
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=268
date: '2006-11-22 09:10:52 +0900'
date_gmt: '2006-11-22 00:10:52 +0900'
categories:
- Shale
---

「
[JSF+AjaxでUIコンポーネント開発 with Shale Remoting (4)](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting_3.html)」で予告した内容を今回紹介しようと思ったが，あまり概論ばかりでも退屈なので，実際のコードを今回から解説していこうと思う。その中で処理の流れを見ていけばいいかな，と。
JSFコンポーネントのJSP実装はカスタムタグの形態をとるため，まずはタグライブラリディスクリプタ，つまりtaglib.tldファイルから取り上げよう。taglib.tldファイルはJSFだからといって特別なことは何もない。JSPのカスタムタグを自作するときと同様に作成すれば良い。
そして，JARファイルを配置するだけでカスタムタグを使用可能とするために，uri要素はしっかりと記述しておく。

>1.0

double-select

http://www.eisbahn.jp/jsf/double-select

これにより，JSP内で以下のように宣言できるようになり，web.xmlへの記述も不要となる。


次に，double-selectタグの定義だが，要点としては，

* 本体部は持たない。

* 属性は「id」「value」「leftSelectItems」「rightSelectItems」の4つを定義する。
となる。「
[JSF+AjaxでUIコンポーネント開発 with Shale Remoting (5)](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting_4.html)」で示したdouble-selectタグの記述例を思い出して欲しい。double-selectタグ定義のコードは以下のようになる。

>　　
double-select
　　
jp.eisbahn.testprograms.jsf.ds.DoubleSelectTag
　　
empty
　　
　　　　
id
　　　　
false
　　
　　
　　　　
value
　　　　
true
　　
　　
　　　　
leftSelectItems
　　　　
true
　　
　　
　　　　
rightSelectItems
　　　　
true
　　

id属性のみを任意とし，他の属性は必須とした。leftSelectItems属性およびrightSelectItems属性は，選択肢を得るために必ず記述が必要となる。また，入力コンポーネントであるからvalue属性も事実上必須としても良いと考えられる。
タグ名は「double-select」とし，カスタムタグハンドラクラスとしてDoubleSelectTagクラスを指定している。特別JSFな記述は見当たらないことはわかるだろう。
taglib.tldファイルの完成形を
[ここからダウンロード](http://www.eisbahn.jp/yoichiro/double-select/taglib.tld)できるようにしてある。「
[JSF+AjaxでUIコンポーネント開発 with Shale Remoting (3)](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting_2.html)」で作成したEclipseプロジェクトの以下の場所に配置されたし。
・[Eclipseワークスペース]/double-select/src/main/resources/META-INF/
次回はいよいよJSFに依存したコーディングに入っていこう。
