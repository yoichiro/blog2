---
layout: post
status: publish
published: true
title: JSF+AjaxでUIコンポーネント開発 with Shale Remoting (15)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 284
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=284
date: '2006-12-13 23:49:27 +0900'
date_gmt: '2006-12-13 14:49:27 +0900'
categories:
- Shale
---

Shale Remotingを使ったJSFコンポーネントの作成について長らくエントリしてきたが，今回でtest-dsプロジェクトも完成し，実行ができるようになる。残りの３つ（
[前回のエントリ](http://www.eisbahn.jp/yoichiro/2006/12/jsfajaxui_with_shale_remoting_13.html)で２つと紹介したが１つ忘れてた）をサクッとやっつけることにしよう。

まずは，double-selectコンポーネントが使用する２つのManagedBeanを，faces-config.xmlファイルに定義する。

version="1.0" encoding="Shift_JIS"?


parameterBean

jp.eisbahn.testprograms.jsf.test.ParameterBean

request

selectItemsProducer

jp.eisbahn.testprograms.jsf.test.SelectItemsProducer

application

ParameterBeanクラスを"parameterBean"という名前で，SelectItemsProducerクラスを"selectItemsProducer"という名前でそれぞれ定義している。スコープについてはrequestとapplicationを指定しているが，これについて特別な理由はない。

では，faces-config.xmlファイルを
[ここからダウンロード](http://www.eisbahn.jp/yoichiro/test-ds/faces-config.xml)し，以下の場所に配置して欲しい。

・[Eclipseワークスペース]/test-ds/src/main/webapp/WEB-INF

次に，Webアプリケーションとするためのweb.xmlファイルを紹介しよう。

version="1.0" encoding="Shift_JIS"?

FacesServlet

javax.faces.webapp.FacesServlet

1

FacesServlet

*.faces

FacesServletサーブレットの定義のみであることがわかるだろう。test-dsプロジェクトでは，JSFリクエストを"*.faces"というマッピングにしている。

とかくweb.xmlファイルなどは，初期化パラメータやタグライブラリの宣言，フィルタやリスナーなどの記述で結構派手になりがちだが，JSFの場合はfaces-config.xmlファイルの扱いが比較的高級（jarファイル分割＆自動検索機構がしっかり考慮されている）なために，最低限の記述のみで駆動させることが可能となっている。JSFはコンポーネント指向が強いことがわかると思う。

このweb.xmlファイルを
[ここからダウンロード](http://www.eisbahn.jp/yoichiro/test-ds/web.xml)して，以下の場所に配置して欲しい。

・[Eclipseワークスペース]/test-ds/src/main/webapp/WEB-INF

さあ最後のファイルだ。別になくても構わないのだが，一応ログ出力のためのlog4j.xmlファイルを準備しておこう。

version="1.0" encoding="UTF-8" ?



















ま，これは説明するまでもないだろう。このlog4j.xmlファイルを
[ここからダウンロード](http://www.eisbahn.jp/yoichiro/test-ds/log4j.xml)して，以下の場所に保存して欲しい。

・[Eclipseワークスペース]/test-ds/src/main/webapp/WEB-INF/classes

以上で全ファイルが揃った。test-dsプロジェクトは以下のようになっているはずだ。

![test-ds-full.jpg](http://www.eisbahn.jp/yoichiro/images/test-ds-full.jpg)

さっそく実行してみよう。

まず前提として，double-selectコンポーネントがmavenのローカルリポジトリにインストールされていることが必要なので，以下のコマンドをコマンドプロンプトやシェルで実行して欲しい。

>> cd [Eclipseワークスペース]/double-select
> mvn install


次に，test-dsプロジェクトをWebアプリケーションとしてTomcatに登録する。それには，Package Explorerビュー内のtest-dsプロジェクトノード上で右クリックし，コンテキストメニューの[Tomcat プロジェクト] - [コンテキスト定義を更新]を選択する。「操作が成功しました。」と表示されれば，メッセージ通り成功。

![update-test-ds-context.jpg](http://www.eisbahn.jp/yoichiro/images/update-test-ds-context.jpg)

では，いよいよtest-dsプロジェクトを実行しよう。TomcatプラグインのTomcat起動ボタンを押下し，起動後にWebブラウザで以下のURLにアクセスしてみよう。

http://localhost:8080/test-ds/index.faces

左のコンボボックスの選択を変更すると，右の選択肢が変更されることがわかると思う。さらに，「Go!」ボタンを押下すると，EclipseのConsoleビューに選択値が表示されるのが見てとれるはずだ。

---
実は「Go!」ボタンを押すたびに，左右両方のコンボボックスの選択肢が消えてしまう現象が，FireFoxを利用している場合に発生してしまう。これは，double-select.jsファイルがFireFoxのキャッシュに入った際に，FireFoxがキャッシュの有効性をサーバに問い合わせる時に送信する更新日時の書式が，Tomcatが期待する米国ロケールではなく日本ロケールになっていて，結果としてHttpServletRequest#getDateHeader()メソッドでIllegalArgumentException例外が発生する，という不具合（実際にはdouble-select.jsをレスポンスする際にヘッダに更新日時を入れる時に日本ロケールが使われてしまっていることが原因）である。これは，イタリア人によって既に不具合報告が下記のようにされているので，次の1.0.4のバージョンアップで解決する予定である（Nightlyビルドでは既に修正済み）。

「
[SHALE-270 Illegal data format](http://issues.apache.org/struts/browse/SHALE-270)」- Shale JIRA

ソースコードを追って原因を突き止めた時に「よし！バグ報告で貢献できる！」と思ったのだが。。。ちょっと悔しい。
---

ここまでくるのに15回も費やしてしまったが，以上でdouble-selectコンポーネントの解説は終わりである。JSFコンポーネントの作成方法も同時に説明をしてきたので，Shale Remotingを使わない素のJSFコンポーネントを作る際にも参考になるだろう。くどくどと説明してきたので，JSFコンポーネントの作成がすごく大変な作業に思えてしまったかも知れないが，わかってしまえば非常にシンプルだということに気付いてくれることだろう。もちろんコンバータやバリデータ，HTMLのスタイル属性などについては完全に説明を省略したので，実用的なコンポーネントに仕上がっているとは言えない。しかし，Ajax対応JSFコンポーネントの作成が，Shale Remotingを使用することで，より直感的に，より手軽に開発可能なことは伝わったのではないだろうか。

皆さんがJSFを利用する時は必ず来る。その時の準備知識として役に立てば幸いである。
