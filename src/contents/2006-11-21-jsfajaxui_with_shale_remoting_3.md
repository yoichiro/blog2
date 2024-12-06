---
layout: post
status: publish
published: true
title: JSF+AjaxでUIコンポーネント開発 with Shale Remoting (4)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 266
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=266
date: '2006-11-21 09:37:31 +0900'
date_gmt: '2006-11-21 00:37:31 +0900'
categories:
- Shale
---

「
[JSF+AjaxでUIコンポーネント開発 with Shale Remoting (3)](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting_2.html)」で予告した通り，今回はdouble-selectコンポーネントの構成について紹介する。それを通じて，Shale Remotingが便利だと思う根本理由を説明したいと思う。
JSFは，コンポーネント指向であることは何度も本ブログで述べていることだが，ではJSFコンポーネントの実体がどういったものなのかは，あまり一般的には説明されていない。
再配布可能なJSFコンポーネントは，クラスファイルと定義ファイル，それに実行時に使用されるリソースファイルを，１つのアーカイブファイルとしてパッケージングしたものだ。つまり，JSFコンポーネントの一般的な実体は，JARファイルである（Java Studio Creatorではさらにcomplibファイルにパッケージングする必要があるが）。JSP向けのJSFコンポーネントについてのパッケージングとしては，以下のお約束事がある。

* faces-config.xmlファイルを，META-INFディレクトリ下に配置する。

* タグライブラリディスクリプタ（tld）ファイルをMETA-INFディレクトリ下またはそのサブディレクトリ下に配置する。
基本的にはこれだけ。
JSFコンポーネントの定義は，faces-config.xmlファイルに記述される。FacesServletは，初期化過程において各JARファイルのMETA-INF/faces-config.xmlファイルを次々と探して定義内容を読み込む。これは，JSF1.1仕様の「10.3.2 Application Startup Behavior」にて規定されている。この動きによって，JARファイルをWebアプリケーションのWEB-INF/libディレクトリ下に入れておくだけで，JSFコンポーネントが使える状態になってくれる。
JSP APIで実装されたJSFコンポーネントは，基本的にはJSPカスタムタグの形態となるため，カスタムタグディスクリプタ，つまりtldファイルが必要となる。tldファイルを実行時に探し出す動作は，JSP2.0仕様の「JSP.7.3.4 Implicit Map Entries from TLDs」にて規定されている。JARファイル中のtldファイルは，META-INFディレクトリまたはそのサブディレクトリ中に配置する。拡張子がtldであればファイル名は任意の名前を持つことができる。
Javaクラスファイルについては，普通にJARファイル中に配置すればいいことは言うまでもないだろう。それよりも気掛かりとなるのは，クラスファイルや定義ファイル以外の，画像ファイルやその他Webブラウザ側で必要となるファイルはどう配置したら良いのか，という点だろう。特にこれから開発しようとしているAjax対応コンポーネントとなると，JavaScriptコードが記述されたjsファイルをコンポーネントパッケージの中に含めたくなる。しかし，相手はWARファイルではない。JARファイルである。
実際には，特にJSF仕様で決められているわけではないので，jsファイルや画像ファイルなどのリソースファイルをJARファイル中のどこに配置しても構わない。tldファイルを配置する場所などを考慮すると，リソースファイルはMETA-INFディレクトリ下のどこかに配置するのが妥当だろう。例えば，jsファイルであればMETA-INF/jsディレクトリ，画像ファイルであればMETA-INF/imagesディレクトリ，といった具合だ。
double-selectコンポーネントは，Ajaxを使用した非同期通信により選択肢を取得するため，JavaScriptコードが必要となる。JavaScriptコードをJavaコード内で生成するのはメンテナンスビリティの低下を招くため，今回はMETA-INF/jsディレクトリ下に，double-select.jsファイルとして提供することにする。
つまり，double-selectコンポーネントをパッケージングしたJARファイルは，以下のような構成となる。

![ds-jar-layout.gif](http://www.eisbahn.jp/yoichiro/images/ds-jar-layout.gif)
faces-config.xmlファイルはFacesServletにより扱われ，taglib.tldファイルはJSPコンテナにより扱われ，各種classファイルに関してもJSF機構によって利用される。言い換えると，どれもサーバ側で使用されるものだ。それに対して，double-select.jsファイルは，Webブラウザがそのファイルをそのまま読み込んで，Webブラウザが解釈して実行するファイルである。つまり，Webブラウザから直接double-select.jsにアクセスできなければならないのだ。しかし，WARファイル中にあるのなら話は簡単だが，JARファイルの，しかもMETA-INFディレクトリにあるものをWebブラウザが取得できるようにするには，どうしたらいいだろうか？
残念ながらJSFはそのようなAPIは規定されていないし，機構も提供されていない。Shaleの初期バージョンでは，静的なリソースファイルはcommons-chainライブラリを使用して，JSFのリクエストとは別処理を行うことで対処していた。さらに，PhaseListenerの実装を作成して，リクエストの内容を見てフェーズを横取りし，クラスパスからリソースを検索してWebブラウザに送信する処理を自作していた。
ここでShale Remotingの登場だ。JARファイルに含まれている静的なリソースファイルについて，Webブラウザからの取得要求の解釈と送信処理を，Shale Remotingが肩代わりしてくれる。例えば，double-select.jsファイルをWebブラウザが取得するには，
/static/META-INF/js/double-select.js.faces
というリクエストをWebブラウザから投げることで，Shale Remotingがクラスパスからファイルに対する入力ストリームを取得し，ファイルの内容を適切なContent-Typeが設定された状態でWebブラウザに返送してくれる。リクエストパスが特殊な形態となるが，Shale Remotingを使用することで，まるでWARファイルのようにJARファイル中のリソースを使用することができるようになる。これだけでもJSFコンポーネント開発の敷居を低く感じていただけたのではないだろうか。
Shale Remotingは，PhaseListenerの実装を提供して，リクエストの内容を見てフェーズを横取りするアプローチを取っている。RemotePhaseListenerクラスがそれだ。

![shale-remoting-static-seq.gif](http://www.eisbahn.jp/yoichiro/images/shale-remoting-static-seq.gif)
RemotePhaseListenerオブジェクトは，要求内容から処理種別を判断し（1），必要なプロセッサをMappingsHelperオブジェクトから得る（2）。静的なリソースの場合は，ClassResourceProcessorクラスが担当する。ClassResourceProcessorオブジェクトは，要求内容に記述されたリソースファイルをクラスパス中から検索し（3），その内容をWebブラウザに送信，その後FacesContext#responseComplete()メソッドを呼ぶことで全フェーズをここで完了とする（4）。
double-selectコンポーネントの構成要素として，カスタムタグハンドラクラスであるDoubleSelectRenderer.classと，カスタムレンダラクラスであるDoubleSelectRenderer.classの２つのクラスファイルも含まれる。これらはJSFの機構から適宜利用されることになるが，その説明は次回以降としよう。
以上，double-selectコンポーネントは，５つのファイルから構成される。これを多いと考えるか少ないと考えるかは個々人の判断となるだろうが，僕個人的には「これだけで作れるなら楽だな」と思う。
次回は，double-selectコンポーネントの処理の流れを紹介し，より詳細な動作内容を示してみたいと思う。
