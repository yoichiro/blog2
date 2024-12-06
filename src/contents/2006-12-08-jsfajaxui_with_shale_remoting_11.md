---
layout: post
status: publish
published: true
title: JSF+AjaxでUIコンポーネント開発 with Shale Remoting (12)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 278
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=278
date: '2006-12-08 09:49:01 +0900'
date_gmt: '2006-12-08 00:49:01 +0900'
categories:
- Shale
---

今回はdouble-selectコンポーネントの残り一つのファイルである，faces-config.xmlファイルを紹介する。このファイルをもって，double-selectコンポーネントは完成となる。加えて，maven2でのパッケージング＆ローカルリポジトリへのインストールまで行うことにしよう。残念ながら，今回もAjaxやShale Remotingは関係しない。

まず，カスタムタグ，UIコンポーネント，そしてレンダラがどのように関連しているかを示そう。

![jsf-comp-relation.jpg](http://www.eisbahn.jp/yoichiro/images/jsf-comp-relation.jpg)

図を見て分かる通り，クラス（黒文字）が直接依存していることはなく，識別子（青文字）または設定ファイル（赤文字）で関連が作られている。例えば，UIComponentTag#getComponentType()メソッドが"hogeComponentType"を返しているとき，それに対応するUIコンポーネントの実装クラスは設定ファイルによって決定されるため，もちろん設定ファイルの記述を変えればUIコンポーネントの実装クラスを変えることができる。また，UIComponentTag#getRendererType()メソッドが"hogeRenderer"を返しているとき，それに対応するレンダラの実装クラスもまた設定ファイルによって決定されるため，その記述を変えればレンダラの実装クラスを変更することが可能である。

この執拗なまでの依存関係の排除により，JSFは多くのデバイスへの対応や各クラスの再利用性を高めることに成功している，というわけだ。たぶん。

さて，double-selectコンポーネントでは，JSFが標準で提供しているUIInputクラスをUIコンポーネントとして使用した。SunのReference Implementationでは，faces-config.xml（実際にはjsf-ri-runtime.xml）設定ファイルにより，Component TypeおよびComponent Familyが以下のように定義されている。

* Component Type - javax.faces.Input

* Component Family - javax.faces.Input

* Component Class - javax.faces.component.UIInput

TypeとFamilyが実質同じ意味で扱われているのは，何か間違いと思えてならないが，仕方ないので従うしかないだろう。

double-selectコンポーネントで自作したのは，レンダラクラスである。UIInputクラスが所属するComponent Family，DoubleSelectTag#getRendererType()メソッドが返すRenderer Type，そしてDoubleSelectRendererクラスの対応を，以下のような内容でfaces-config.xmlファイルに記述する。

version="1.0" encoding="Shift_JIS"?


javax.faces.Input

jp.eisbahn.jsf.DoubleSelect

jp.eisbahn.testprograms.jsf.ds.DoubleSelectRenderer

UIコンポーネントとレンダラの対応ではなく，あくまでUIコンポーネントファミリとレンダラの対応を定義していることに注意したい。各レンダラは，RenderKitという単位で括られる。このマッピングに従って，UIComponentBase#getRenderer()メソッドは上記マッピングで決定されたレンダラクラスのオブジェクトを返す。SunのRIでは，

protected Renderer getRenderer(FacesContext context) {
String rendererType = getRendererType();
if (rendererType != null)
return context.getRenderKit().getRenderer(getFamily(), rendererType);
else
return null;
}

という感じのコードになっている。

UIコンポーネントを自作した場合は，faces-config.xmlファイルにcomponentタグなどを記述する必要があるが，レンダラのみの自作であれば，render-kit定義の記述で十分である。では，上記のfaces-config.xmlファイルを
[ここからダウンロード](http://www.eisbahn.jp/yoichiro/double-select/faces-config.xml)して，以下の場所に配置して欲しい。

・[Eclipseワークスペース]/double-select/src/main/resources/META-INF/

double-selectコンポーネントを構成するファイルがこれで一通り揃った。さっそくパッケージングを行おう。まず，double-selectプロジェクト内が以下のようになっていることを確認して欲しい。

![double-select-project-view.jpg](http://www.eisbahn.jp/yoichiro/images/double-select-project-view.jpg)

コマンドプロンプトやターミナルを起動し，以下のコマンドを実行する。

>cd [Eclipseワークスペース]/double-select
mvn install


もちろんmaven2がインストールされていて，binディレクトリにパスが通っていることが前提条件となる。うまくいけば，targetディレクトリ内にdouble-select-0.1.jarファイルが作られているはずだ。同時に，ローカルリポジトリ（$USER_HOME/.m2/repository内）にも，double-select-0.1.jarファイルが登録されている。

くどくどと長く説明してきたが，一刻も早くdouble-selectコンポーネントを使いたいとお思いのことだろう。次回からは，double-selectコンポーネントを動作させるtest-dsプロジェクトの説明に入っていく予定である。
