---
layout: post
status: publish
published: true
title: JSFアプリケーションの単体テスト with Shale Test Framework (2)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 291
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=291
date: '2006-12-21 09:34:47 +0900'
date_gmt: '2006-12-21 00:34:47 +0900'
categories:
- Shale
---

[前回のエントリ](http://www.eisbahn.jp/yoichiro/2006/12/jsf_with_shale_test_framework.html)で予告した通り，今回はShale Test Framework（以下STFと略す）を試すための題材を紹介することにしよう。

STFの対象問題領域は，JSF APIやServlet APIに依存したコードを持つManagedBeanクラスが主な領域となる。「
[JSF+AjaxでUIコンポーネント開発 with Shale Remoting](http://www.eisbahn.jp/yoichiro/2006/12/jsfajaxui_with_shale_remoting_15.html)」の一連のエントリの中で，double-selectコンポーネントの選択肢をJSON形式のテキストで返却するManagedBean，SelectItemsProducerクラスを作成した（
[このエントリ](http://www.eisbahn.jp/yoichiro/2006/12/jsfajaxui_with_shale_remoting_13.html)を参照）。SelectItemsProcuderクラスが持つメソッドでは，以下のような依存関係が存在している。

* リクエストパラメータ値の取得 - ExternalContext（JSF）に依存

* 使用するResponseWriterの生成 - ResponseFactory（Shale Remoting）に依存

* JSON形式テキストの返送 - ResponseWriter（JSF）に依存

* フェーズの完了 - FacesContext（JSF）に依存

STFを試す題材としてはうってつけのコードであることがわかるだろう。では，実際のSelectItemsProducerクラスのコードを以下に示す。今回はSTFを試すことが目的なので，
[前のエントリ](http://www.eisbahn.jp/yoichiro/2006/12/jsfajaxui_with_shale_remoting_13.html)と比べて，メソッドを１つに限定した。

package jp.eisbahn.testprograms.jsf.stf;
import java.io.IOException;
import java.util.Map;
import javax.faces.context.FacesContext;
import javax.faces.context.ResponseWriter;
import org.apache.shale.remoting.faces.ResponseFactory;
public class SelectItemsProducer {
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
} else if ("2".equals(selectedValue)) {
sb.append("{'label':'日比谷線', 'value':'1'},\n");
}
sb.append("]");
String response = sb.toString();
writer.write(response);
context.responseComplete();
}
}

"fv"という名前のリクエストパラメータ値に従って，返送する選択肢を変化させている。import文を見てもわかる通り，JSFやShale Remotingに依存したコードとなっている。

では，単体テストについて考えていこう。まず，このクラスのgetRightSelectItems()メソッドの単体テストを行うためには，以下の条件が必要となる。

* FacesContextオブジェクトを取得できること。

* ExternalContextオブジェクトが持つRequestParameterMapコレクションに，"fv"パラメータが格納されていること。

* ResponseWriterオブジェクトに対して出力された内容を取り出すことができること。

これらの条件は，全てSTFによってクリアすることが可能である。

SelectItemsProdcer#getRightSelectItems()メソッドは，2種類の"fv"パラメータの値を受け付ける。"1"を受け取った場合はJRの路線を，"2"を受け取った場合は東京メトロの路線を，それぞれ選択肢として出力している。カバレッジを確保するためには，それ以外の値だったら，とか，○○例外が発生したら，などのパターンをテストしなければならないが，今回はそれが本質ではないので省略する。

実際にテストを体験できるプロジェクトを，Maven2のpomファイルと共に作成してある。
[アーカイブをここからダウンロード](http://www.eisbahn.jp/yoichiro/test-stf.tar.gz)して，任意の場所に展開し，展開の結果作られたtest-stfディレクトリで以下のコマンドを実行して欲しい。

>mvn eclipse:eclipse


その後，Eclipseの外部プロジェクトのimport機能を使って，test-stfディレクトリをEclipseプロジェクトとして登録する。さっそくテストケースを実行してみよう。src/test/javaディレクトリにSelectItemsProducerTestクラスがあるので，このクラスをJUnit TestとしてRunしてみる。

![select-item-producer-test-success.jpg](http://www.eisbahn.jp/yoichiro/images/select-item-producer-test-success.jpg)

上の図のように，緑になればOK。JSFの実行時環境がシミュレートされた中で，SelectItemsProducerTestクラスのテストメソッドが実行された瞬間である。めでたしめでたし。

test-stfプロジェクトのpom.xmlファイルでは，以下のライブラリを使用するように定義してある。

[Compile scope]

* javax.faces.jsf-api.1.1_02

* javax.faces.jsf-impl.1.1_02

* org.apache.shale.shale-remoting.1.0.3

[Test scope]

* junit.junit.3.8.1

* org.apache.shale.shale-test.1.0.3

* net.sf.json-lib.json-lib.0.9

[json-lib](http://json-lib.sourceforge.net/)は，JSON形式のテキストとJavaBeansやXML形式のテキストとの相互変換を行ってくれる，とっても便利なライブラリである。これをテストメソッドの中で使用して，JSON形式のテキストの検証に使ってみようという企みだ。

次回は，いよいよSTFを使ったテストケースの作成を紹介する予定である。
