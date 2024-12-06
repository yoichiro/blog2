---
layout: post
status: publish
published: true
title: JSF+AjaxでUIコンポーネント開発 with Shale Remoting (3)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 265
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=265
date: '2006-11-20 10:55:27 +0900'
date_gmt: '2006-11-20 01:55:27 +0900'
categories:
- Shale
---

「
[JSF+AjaxでUIコンポーネント開発 with Shale Remoting (2)](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting_1.html)」で予告した通り，カスタムコンポーネントを開発するための開発環境の構築について紹介する。NetBeansを使えば簡単にJSFを使った開発をすることができるが，Maven2との連携がとれないことや，やっぱりEclipseは多数派だろうと思うので，ここではEclipseを使うことにした。
今回は「JSFカスタムコンポーネントを開発するプロジェクト」と「作ったカスタムコンポーネントを利用するプロジェクト」の２つを準備して開発を進める。複数のプロジェクトを束ねて開発を進める場合，Maven2を使うと楽ができる。まずは，以下のソフトウェアをダウンロード＆インストールを行おう。手順はここでは割愛する。

* [JDK 5.0](http://java.sun.com/j2se/1.5.0/ja/download.html)

* [Tomcat 5.5.20](http://tomcat.apache.org/download-55.cgi)

* [Eclipse 3.2.1](http://www.eclipse.org/downloads/)

* [Maven2](http://maven.apache.org/download.html)

* [Sysdeo Tomcat Plugin](http://www.sysdeo.com/eclipse/tomcatplugin)
そして，Eclipseのプロジェクトを作成する。Eclipseのワークスペース内に，以下の名前のディレクトリを作成する。

* double-select - カスタムコンポーネント開発用プロジェクト

* test--ds - カスタムコンポーネント利用プロジェクト
さらに，各ディレクトリ内に，以下のサブディレクトリを作成しておく。

* double-select


　　src


　　　　main


　　　　　　java


　　　　　　resources


　　　　　　　　META-INF
　　　　　　　　　　js

* test-ds


　　src


　　　　main


　　　　　　java


　　　　　　resources


　　　　　　webapp


　　　　　　　　js
　　　　　　　　WEB-INF


　　　　　　　　　　classes
次に，double-selectプロジェクト用のpom.xmlファイルを作成する。記述内容のポイントは以下のような感じである。作成済みのファイルを
[ここからダウンロード](http://www.eisbahn.jp/yoichiro/double-select-pom.xml)できるようにしたので利用されたい。ブログの管理上ファイルを変えているが，実際はpom.xmlでOK。

* artifactId: double-select

* パッケージ形態: jar

* Javaコンパイラを1.5に設定

* 依存ライブラリは「org.apache.shale.shale-remoting-1.0.3」を設定。
shale-remotingを依存ライブラリで指定しておけば，myfaces-apiに関しても依存ライブラリとして追加されるため，JSFのAPIをプロジェクトから利用することが可能になる。
このpom.xmlファイルをdouble-selectディレクトリに配置し，double-selectディレクトリをカレントディレクトリとして「mvn eclipse:eclipse」を実行すれば，カスタムコンポーネント開発用プロジェクトは準備完了である。Eclipseの外部プロジェクトインポート機能を使って，ワークスペースにdouble-selectプロジェクトとして登録しておこう。ちなみに下図は，外部プロジェクトインポート機能の一コマ。

![external-project-import.jpg](http://www.eisbahn.jp/yoichiro/images/external-project-import.jpg)
インポート終了後，シェルやコマンドプロンプトで，double-selectディレクトリをカレントディレクトリとして「mvn install」を実行しておこう。
次にdouble-selectコンポーネントを利用するプロジェクトのpom.xmlファイルを作成する。先ほどと比べて，こちらのpom.xmlファイルはいくつかの工夫が必要となる。記述内容の概要を以下に示す。先ほどと同じく，作成済みのファイルを
[ここからダウンロード](http://www.eisbahn.jp/yoichiro/test-ds-pom.xml)できるようにしたので利用されたい。ファイル名はpom.xmlにする。

* artifactId: test-ds

* パッケージ形態: war

* Javaコンパイラを1.5に設定

* Tomcatプラグインをプロジェクトで有効になるように設定
こちらの依存ライブラリは，以下のものが必要となる。

* javax.faces.jsf-api-1.1_02

* javax.faces.jsf-impl-1.1_02

* jstl.jstl-1.1.2

* taglib.standard-1.1.2

* commons-digester.commons-digester-1.7

* commons-logging.commons-logging-1.1

* commons-collections.commons-collections-3.1

* commons-beanutils.comons-beanutils-1.7.0

* log4j.log4j-1.2.12

* org.apache.shale.shale-remoting-1.0.3

* javax.servlet.servlet-api-2.3 (*)

* org.apache.tomcat.servlet-api-5.5.15 (*)

* org.apache.tomcat.jsp-api-5.5.15 (*)

* myfaces.myfaces-api-1.1.1 (*)

* myfaces.myfaces-impl-1.1.1 (*)

* jp.eisbahn.testprograms.jsf.double-select-0.1
上記の(*)印がついている依存ライブラリは，scope属性をprovidedにして，実行時にこのライブラリがtest-dsパッケージ内に含まれないようにする。例えば，myfaces-implに対しては，

>　　
myfaces
　　
myfaces-impl
　　
1.1.1
　　
provided

というようにする。今回はJSFの実装として，Sunの参照実装を使用する。しかし，shale-remotingに依存設定されているmyfaces関連のライブラリが実行時に含まれて予期せぬ動作をしてしまう可能性があるため，実行時に除外するためにprovided指定を行うことで解決している。
加えて，先ほど作成したdouble-selectプロジェクトの成果物であるdouble-select-0.1に関しても依存設定を行っている。
double-selectプロジェクトと同じように，こちらもtest-dsディレクトリをカレントディレクトリとして「mvn eclipse:eclipse」を実行し，Eclipseの外部プロジェクトインポート機能を使って，ワークスペースにtest-dsプロジェクトとして登録しておこう。test-dsプロジェクトのプロパティにあるTomcat設定が既に施されているのが見てとれるはずだ。

![tomcat-project.jpg](http://www.eisbahn.jp/yoichiro/images/tomcat-project.jpg)
最後に，Tomcatにコンテキストを登録するために，test-dsプロジェクト上で右クリックし，「Tomcatプロジェクト」-「コンテキスト定義を更新」メニューを実行する。さらに，ワークスペースのクラスパス変数「M2_REPO」についても，自分の環境に合わせて作成しておく。
以上で開発準備は完了である。
開発作業の流れとしては，double-selectプロジェクト内を変更した場合は，「mvn install」を実行してローカルリポジトリに反映し，test-dsプロジェクトに反映して実行，という手順となる。ま，Maven2が想定する開発手順なので，特別なことではない。
特にtest-dsプロジェクトの上記の準備手順は，「Eclipse+Maven2+Tomcatプラグイン」という組み合わせでJSFを開発するときの共通的な手順となるので，参考になるだろう。
次回は，いよいよdouble-selectコンポーネントの作成に入ろうと思う。まずは，double-selectコンポーネントの構成から説明したい。
