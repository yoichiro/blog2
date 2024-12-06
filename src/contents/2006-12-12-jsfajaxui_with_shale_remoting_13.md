---
layout: post
status: publish
published: true
title: JSF+AjaxでUIコンポーネント開発 with Shale Remoting (14)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 283
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=283
date: '2006-12-12 10:55:02 +0900'
date_gmt: '2006-12-12 01:55:02 +0900'
categories:
- Shale
---

double-selectコンポーネントの動作確認を行うためのtest-dsプロジェクト作りも２回目となった。
[前回のエントリ](http://www.eisbahn.jp/yoichiro/2006/12/jsfajaxui_with_shale_remoting_12.html)では，double-selectコンポーネントをページに配置するためのJSPファイルを作成し，double-selectタグの属性値としていくつかManagedBeanとの連携を記述した。今回は，連携するManagedBeanのクラスを紹介しよう。

まずは簡単な方から。double-selectコンポーネントの選択値を得るためのManagedBeanを，ParameterBeanという名前のクラスとして作成する。

package jp.eisbahn.testprograms.jsf.test;
public class ParameterBean {
private String[] selectedValues;
public String[] getSelectedValues() {
return selectedValues;
}
public void setSelectedValues(String[] selectedValues) {
this.selectedValues = selectedValues;
System.out.println("setSelectedValue: " + selectedValues[0] + ", " + selectedValues[1]);
}
}

selectedValueプロパティを持つだけのシンプルなクラスである（本来はSerializableインタフェースを実装する必要があるが省略）。
[前のエントリ](http://www.eisbahn.jp/yoichiro/2006/12/jsfajaxui_with_shale_remoting_10.html)で，double-selectコンポーネントの２つのselectタグがサブミットされた結果は，Stringの配列でUIコンポーネントに渡されると紹介した。今回はコンバータの指定を行っていないので，EditableValueHolder#setSubmittedValue()メソッドに渡したオブジェクトがそのままバリューバインディングされる。よって，selectedValueプロパティもString[]型にしている。setterメソッドが呼び出されたことがわかるように，メッセージを出力するようにしておこう。

ParameterBean.javaファイルを
[ここからダウンロード](http://www.eisbahn.jp/yoichiro/test-ds/ParameterBean.java)して，以下の場所に配置して欲しい。

・[Eclipseワークスペース]/test-ds/src/main/java/jp/eisbahn/testprograms/jsf/test

では，難しい方に取り掛かろう。double-selectコンポーネントの選択肢をJSON形式のテキストで出力する処理を持つManagedBeanを，SelectItemsProducerという名前のクラスで作成する。

package jp.eisbahn.testprograms.jsf.test;
import java.io.IOException;
import java.util.Map;
import javax.faces.context.FacesContext;
import javax.faces.context.ResponseWriter;
import org.apache.shale.remoting.faces.ResponseFactory;
public class SelectItemsProducer {
・・・
}

SelectItemsProducerクラスも，特に変わったことはない，普通のPOJOである（本来はSerializableインタフェースを実装する必要があるが省略）。このクラスは，左右の選択肢を出力するための２つのメソッドを持つ。では最初に，左の選択肢を返す処理を行うgetLeftSelectItems()メソッドから見ていこう。

public void getLeftSelectItems() throws IOException {
FacesContext context = FacesContext.getCurrentInstance();
ResponseWriter writer = (new ResponseFactory()).getResponseWriter(context, "text/javascript;charset=UTF-8");
StringBuffer sb = new StringBuffer();
sb.append("[\n");
sb.append("{'label':'JR', 'value':'1'},\n");
sb.append("{'label':'東京メトロ', 'value':'2'},\n");
sb.append("]");
String response = sb.toString();
writer.write(response);
context.getResponseComplete();
}

getLeftSelectItems()メソッドでは，選択肢をJSON形式の文字列として構築し，それをResponseWriterオブジェクトを使用してWebブラウザに送信している。

ResponseWriterオブジェクトは，Shale Remotingが提供するResponseFactoryクラスを使用して取得している。FacesContextクラスにもResponseWriterオブジェクトを得るためのgetResponseWriter()メソッドはあるのだが，これは基本的にHTMLを送信するためのResponseWriterオブジェクトを返す。例えば，Sun RIで提供されるHtmlResponseWriterクラスでは，script，style，そして"javascript:"で始まるURIへの対応などが処理される。それに対して，Shale Remotingが提供するBasicResponseWriterクラスでは，HTMLに特化した処理が省略されているため，XML形式やJSON形式でコンテンツを出力するのであれば，HtmlResponseWriterクラスに比べて効率が良い。よって，ここではResponseFactoryクラスを用いてBasicResponseWriterオブジェクトを取得し，それを使ってJSON形式のテキストを出力している。

![json-ds-left-select-items.jpg](http://www.eisbahn.jp/yoichiro/images/json-ds-left-select-items.jpg)

JSON形式のテキストを送信する場合は，Content Typeとして"text/javascript"を指定する。また，マルチバイト文字を正しくWebブラウザに処理してもらうために，UTF-8をキャラクタセットとして同時に指定している。選択肢は，ここでは"JR"と"東京メトロ"の２つとしている。JSON形式とするために，各選択肢を中括弧で括り，全体を大括弧で括っている。この文字列を，取得したResponseWriterオブジェクトのwrite()メソッドに渡して出力している。

AjaxとShale Remotingの機能によって呼び出されたこのメソッドの最後の処理として，FacesContextオブジェクトのresponseComplete()メソッドを呼び出している。Shale Remotingによる動的ManagedBean呼び出しでは，基本的にそのメソッドが処理結果をWebブラウザに出力することで処理が完結するため，以降のJSFのフェーズを実行する必要がない。よって，ここでresponseComplete()メソッドを呼び出すことで，LifeCycleオブジェクトにフェーズの完了を指示している。

もう一つの右の選択肢を出力する処理を行うgetRightSelectItems()メソッドは，基本的にgetLeftSelectItems()メソッドとやっていることは変わらない。ただし，Webブラウザから送られた左の選択値を得て，出力内容を変化させている。

public void getRightSelectItems() throws IOException {
FacesContext context = FacesContext.getCurrentInstance();
Map parameterMap = context.getExternalContext().getRequestParameterMap();
Object selectedValue = parameterMap.get("fv");
ResponseWriter writer = (new ResponseFactory()).getResponseWriter(context, "text/javascript;charset=UTF-8");
StringBuffer sb = new StringBuffer();
sb.append("[\n");
if ("1".equals(selectedValue)) {
sb.append("{'label':'宇都宮線', 'value':'1'},\n");
sb.append("{'label':'高崎線', 'value':'2'},\n");
sb.append("{'label':'京浜東北線', 'value':'3'},\n");
} else if ("2".equals(selectedValue)) {
sb.append("{'label':'日比谷線', 'value':'1'},\n");
sb.append("{'label':'東西線', 'value':'2'},\n");
}
sb.append("]");
String response = sb.toString();
writer.write(response);
context.responseComplete();
}

Webブラウザから送信されたリクエストパラメータの取得方法は，
[前のエントリ](http://www.eisbahn.jp/yoichiro/2006/12/jsfajaxui_with_shale_remoting_10.html)で紹介した，double-selectコンポーネントのDoubleSelectRendererクラスのdecode()メソッドと同じである。つまり，ExternalContextオブジェクトにあるRequestParameterMapオブジェクトから取得すれば良い。

test-dsプロジェクトでは，double-selectコンポーネントの動作確認ができれば良いので，左右の選択肢は固定的なものを出力している。double-selectコンポーネントを実際に開発案件の中で使用するときには，選択肢の内容をデータベースから取得したりするなどの処理が行われるだろう。それに伴ってエラー処理などが必要となるだろうが，説明したいことの本質ではないので内容としないでおく。

SelectItemsProducer.javaファイルを
[ここからダウンロード](http://www.eisbahn.jp/yoichiro/test-ds/SelectItemsProducer.java)して，以下の場所に配置して欲しい。

・[Eclipseワークスペース]/test-ds/src/main/java/jp/eisbahn/testprograms/jsf/test

上記の例では，JSON形式の文字列を原始的な手段で構築したが，実際には「
[JSON-lib](http://json-lib.sourceforge.net/)」などのJavaBeans-JSON変換を行ってくれるライブラリを使用すると良いだろう。

今回の内容で，説明すべき内容はほぼ網羅した。あとはfaces-config.xmlファイルとweb.xmlファイルを記述するのみである。次回は，残り２つのファイルを紹介し，いよいよtest-dsプロジェクトを実行してdouble-selectコンポーネントの動作を確認してみよう。つまり，次回が最終回となる，予定である。が，たぶん最終回にならない。。。
