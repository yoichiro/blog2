---
layout: post
status: publish
published: true
title: JSF+AjaxでUIコンポーネント開発 with Shale Remoting (8)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 271
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=271
date: '2006-11-25 09:54:18 +0900'
date_gmt: '2006-11-25 00:54:18 +0900'
categories:
- Shale
---

今回は，double-selectコンポーネントをレンダリングするDoubleSelectRendererクラスの解説を行おうと思う。その中で，Shale RemotingのAPIを使用してみる。

[前回紹介したDoubleSelectTagクラス](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting_6.html)のsetProperties()メソッドによって，JSPに記述されたカスタムタグの属性値がUIコンポーネントにセットされる。DoubleSelectTagクラスが継承するUIComponentTagクラスは，setProperties()メソッドの呼び出し後に，コンポーネントをレンダリングするためにUIコンポーネントのencodeBegin()メソッドを呼び出す。double-selectコンポーネントが使用するUIInputクラスのencodeBegin()メソッドでは，faces-config.xmlにて関連づけられたレンダラクラスにレンダリングを委譲する。つまり，double-selectコンポーネントでは，DoubleSelectRendererクラスのencodeBegin()メソッドが呼び出される。

![ds-rendering.jpg](http://www.eisbahn.jp/yoichiro/images/ds-rendering.jpg)
あ，ちなみに「レンダリング」＝「エンコード」と読み替えて欲しい。どうもエンコードという言葉が分かりにくいだけの話なので，正確にはエンコード処理である。
DoubleSelectRendererクラスのencodeBegin()メソッドにて，double-selectコンポーネントを実際にレンダリングする処理を記述する。これは，Servletクラス内でHTMLの文字列をせっせと組んでストリームに送信していく処理にそっくりである。ページ全体を相手にするわけではないのでサーブレットのようにはならないが，それでも比較的泥臭い処理内容となるだろう。
具体的なレンダリング処理を紹介する前に，まずはdouble-selectコンポーネントのレンダリング結果を把握しよう。

>- (1)

[Client ID]:leftSelect"
　　name="
[Client ID]:leftSelect"
　　onchange="leftValueChanged('
[Client ID]:leftSelect', '
[Client ID]:rightSelect', '/test-ds/dynamic/
[rightSelectItems].faces');">  - (2)

[Client ID]:rightSelect"
　　name="
[Client ID]:rightSelect">  - (3)

 - (4)

大きく分けて，「double-select.jsファイル読み込み (1)」「左のコンボボックス (2)」「右のコンボボックス (3)」「初期選択肢取得処理登録 (4)」の４つから構成されている。
「double-select.jsファイル読み込み」は，scriptタグのsrc属性を使っている。ここでsrc属性値として指定しているものは，META-INF/jsディレクトリ下にあるdouble-select.jsファイルをShale Remotingを使って取得するためのパスである。これについては，
[前のエントリ](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting_3.html)で簡単に説明したので，ここでは省略。
「左のコンボボックス」は，一つ目のselectタグの部分である。id属性とname属性には，同じ文字列を値として指定する。ここでClient IDとは，ページ中に配置されたdouble-selectコンポーネントをJSFが扱うための識別子である。Client IDは，JSFがRestore Viewフェーズでコンポーネントツリーを構築する際に自動的に付与される。左のコンボボックスであることを識別するために，さらに「leftSelect」という文字列を追加している。例えば，myFormというIDを持つh:formタグ内にdouble-selectコンポーネントを一つ配置した場合は，JSFのSun RI実装（1.1_02）では，「myForm:_id0:leftSelect」というClient IDになる。
左のコンボボックスのselectタグには，更にonchange属性も記述している。これの値として，leftValueChanged()関数（double-select.jsファイルに記述される）の呼び出しを指定する。この関数は，3つの引数を指定する。順に「左のコンボボックスのid属性値」「右のコンボボックスのid属性値」「左のコンボボックスの選択肢を取得するためのリクエストパス」を渡している。特に3番目のリクエストパスは注目である。これは，Shale Remotingを使ってJSFの管理下にあるManaged Beanのメソッドを呼び出すためのパスである。
Shale Remotingを使ってWebブラウザから「bean1」という名前のManaged Beanオブジェクトが持つgetInfo()メソッドを呼び出す場合は，以下のように記述する。正確には，Webアプリケーションのコンテキストパスも先頭に付与する。

>/dynamic/bean1/getInfo.faces

この第３引数のリクエストパスは，leftValueChanged()関数内で選択肢を得るための非同期通信を行う際に利用される。
このリクエストパスは，DoubleSelectTagオブジェクトによってセットされたleftSelectItems属性値を元に作成する。leftSelectItems属性値は，
[前のエントリ](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting_4.html)ではメソッドバインディング式として記述されていた。これを「/dynamic」で始まるリクエストパスに変換してleftValueChanged()関数の引数とする。

![mb2sr.jpg](http://www.eisbahn.jp/yoichiro/images/mb2sr.jpg)
ここで，簡単にShale Remotingの動作を紹介しておこう。

![shale-remoting-dynamic.jpg](http://www.eisbahn.jp/yoichiro/images/shale-remoting-dynamic.jpg)
/dynamicで始まるリクエストパスをRemotePhaseListenerオブジェクトが受け取った場合，MappingsHelperオブジェクトはMethodBindingProcessorオブジェクトをRemotePhaseListenerオブジェクトに提供する。MethodBindingProcessorオブジェクトのprocess()メソッドでは，リクエストパスからMethodBindingオブジェクトを生成し，それを使ってManagedBeanオブジェクトのメソッドをinvokeする。

![MethodBindingProcessor.jpg](http://www.eisbahn.jp/yoichiro/images/MethodBindingProcessor.jpg)
その後，FacesContext#responseComplete()メソッドを呼び出して，JSFのフェーズをここで完了する。
「右のコンボボックス」については，左のコンボボックスを構成するselectタグとほぼ同様。ただし，onchange属性は必要ないので出力しない。また，id属性とname属性の値として，右のコンボボックスであることを識別するために，「rightSelect」という文字列をClient IDに追加する。
さて，最後の「初期選択肢取得処理登録」。double-selectコンポーネントは，左右のコンボボックスの選択肢をレンダリング時には出力しない。選択肢は，double-selectコンポーネントが配置されたページの読み込み完了時に非同期通信を行って情報を取得し，JavaScriptでoptionタグを動的生成する。そのための関数がloadLeftSelectItems()関数（double-select.jsファイルに記述）であり，
[prototype.js](http://prototype.conio.net/)が提供する
[Event.observe()関数](http://www.imgsrc.co.jp/~kuriyama/prototype/prototype.js.html#Reference.Extensions.Event)を使ってloadLeftSelectItems()関数をwindowオブジェクトのloadイベントハンドラとして登録する。
loadLeftSelectItems()関数の第3，第4引数に指定するリクエストパスは，leftValueChanged()関数の時と同様に，メソッドバインディング式を/dynamicで始まるリクエストパスに変換して引数値とする。
ここで突然prototype.jsが登場したが，prototype.jsはdouble-selectコンポーネントには含めない。Webアプリケーション全体でprototype.jsファイルは使用されることが想定されるため，double-selectコンポーネントはprototype.jsが使用可能な状態になっていることを前提とする。今回のサンプルでは，test-dsプロジェクト側でprototype.jsを準備する。以下のディレクトリに，
[prototype.jsファイル](http://prototype.conio.net/dist/prototype-1.4.0.js)を配置して欲しい。
・[Eclipseワークスペース]/test-ds/src/main/webapp/js
今回はレンダラクラスの内容を紹介するつもりだったが，思ったよりもレンダリング結果の説明が長くなってしまったので，次回に持ち越しとしよう。うーん，なかなかJavaコードの説明にならないな。。。
