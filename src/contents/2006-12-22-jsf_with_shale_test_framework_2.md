---
layout: post
status: publish
published: true
title: JSFアプリケーションの単体テスト with Shale Test Framework (3)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 293
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=293
date: '2006-12-22 10:22:45 +0900'
date_gmt: '2006-12-22 01:22:45 +0900'
categories:
- Shale
---

[前回のエントリ](http://www.eisbahn.jp/yoichiro/2006/12/jsf_with_shale_test_framework_1.html)では，Shale Test Framework（STF）を試すための題材を紹介した。さらに，STFを試すためのtest-stfプロジェクトも提供を行った。入手していない方は，是非
[ここからダウンロード](http://www.eisbahn.jp/yoichiro/test-stf.tar.gz)して欲しい。test-stfプロジェクトには，「
[JSF+AjaxでUIコンポーネント開発 with Shale Remoting](http://www.eisbahn.jp/yoichiro/2006/12/jsfajaxui_with_shale_remoting_15.html)」で取り上げたdouble-selectコンポーネントに選択肢を提供するManagedBean，SelectItemsProducerクラスを含めた。SelectItemsProducerクラスには，単体テスト対象のgetRightSelectItems()メソッドを設置してある。今回は，このgetRightSelectItems()メソッドをテストするためのテストケースクラスを，STFを前提に紹介していこうと思う。

STFでは，JSFの実行時環境をシミュレートするために，AbstractJsfTestCaseクラスを提供している。JSFのAPIに依存したコードをテストするには，このクラスを継承してテストケースを作成すれば良い。

package jp.eisbahn.testprograms.jsf.stf;
import java.io.IOException;
import junit.framework.Test;
import junit.framework.TestSuite;
import net.sf.json.JSONArray;
import net.sf.json.JSONObject;
import org.apache.shale.test.base.AbstractJsfTestCase;
import org.apache.shale.test.mock.MockPrintWriter;
public class SelectItemsProducerTest extends AbstractJsfTestCase {
public SelectItemsProducerTest(String name) {
super(name);
}
public static Test suite() {
return new TestSuite(SelectItemsProducerTest.class);
}
}

SelectItemsProducerTestクラスをAbstractJsfTestCaseクラスを継承して作成している。AbstractTestCaseクラスはデフォルトコンストラクタを持っていないため，SelectItemsProducerTestクラスでもname引数を持つコンストラクタを定義している。

そして次がちょっとしたハマりポイントなのだが，AbstractJsfTestCaseクラスを使用する場合は，suite()メソッドをオーバーライドする必要がある。これを行わないと，いくらテストメソッドを作っても，それが実行されることはない。suite()メソッドの中では，自分自身のクラスオブジェクトを元にしたTestSuiteオブジェクトを生成して返す。これによって，めでたくテストメソッドが実行されるようになる。

次に，テスト対象のSelectItemsProducerオブジェクトを準備しよう。

private SelectItemsProducer target;
protected void setUp() throws Exception {
super.setUp();
target = new SelectItemsProducer();
}
protected void tearDown() throws Exception {
target = null;
super.tearDown();
}

SelectItemsProducerオブジェクトをインスタンスフィールドとして定義し，setUp()メソッドおよびtearDown()メソッドによってライフサイクルを管理している。

ここで注意点。AbstractJsfTestCaseクラスでシミュレートされるJSF実行時環境は，AbstractJsfTestCase#setUp()メソッドで準備され，AbstractJsfTestCase#tearDown()メソッドで後始末が行われる。よって，具体的なテストケースクラスでは，setUp()メソッドおよびtearDown()メソッドのオーバーライドを行った場合は，必ずスーパークラスの処理を呼び出してあげなければならない。これを忘れると，JSFとは関係ない単なるJUnitテストケースクラスと成り下がるので，気をつけたい。

各テストメソッドの実行の直前に呼び出されるsetUp()メソッドの作用によって，JSFでおなじみのFacesContextオブジェクトやApplicationオブジェクトなどが準備完了となる。あとは，テスト対象のメソッドをテストメソッド内で呼び出して，結果を検証するだけ。なんとも簡単だ。

AbstractJsfTestCaseクラスが提供してくれるJSF関連のオブジェクトを，以下に示しておこう。括弧内に，実際の型も示しておく。これらのモッククラスは，STFで提供されるものだ。

* application (MockApplication)

* config (MockServletConfig)

* externalContext (MockExternalContext)

* facesContext (MockFacesContext)

* lifecycle (MockLifecycle)

* request (MockHttpServletRequest

* response (MockHttpServletResponse)

* servletContext (MockServletContext)

* session (MockHttpSession)

では，テストメソッドを見ていこう。下のコードは，fvパラメータ値が"1"だった際に，JRの選択肢がJSON形式で正しく得られるかどうかを確認するためのコードである。

public void testGetRightSelectItemsValue1() throws Exception {
externalContext.addRequestParameterMap("fv", "1");
target.getRightSelectItems();
assertEquals("ContentType", "text/javascript;charset=UTF-8", response.getContentType());
JSONArray jsonArray = JSONArray.fromString(getContent());
assertEquals("jsonArray.length", 2, jsonArray.length());
assertSelectItem(jsonArray, 0, "宇都宮線", "1");
assertSelectItem(jsonArray, 1, "高崎線", "2");
}

外部（Webブラウザ）からサブミットされたリクエストパラメータは，ExternalContextオブジェクトに格納される。"fv"パラメータの値に関しても，実行時をシミュレートしてあげるためには，テスト対象のメソッドを呼び出す前に，ExternalContextオブジェクトに値を詰めてあげないといけない。

![select-items-producer-test-rqp.jpg](http://www.eisbahn.jp/yoichiro/images/select-items-producer-test-rqp.jpg)

AbstractJsfTestCase#setUp()メソッドにて，ExternalContextオブジェクトはMockExternalContextクラスのインスタンスとしてしっかり準備完了状態になっている。MockExternalContextクラスには，テスト用のリクエストパラメータ値をセットアップするために，addRequestParameterMap()メソッドが提供されている。このメソッドを使用して，"fv"パラメータをセットしている。

リクエストパラメータの準備が整ったところで，テスト対象のメソッドを呼び出している。getRightSelectItems()メソッドの処理によって，ResponseWriterオブジェクトに対して出力処理が行われる。

・・・と，ここから先の説明は長くなると想像できるので，今回はここで終わり。getRightSelectItems()メソッドの出力結果の検証について，次回の内容としよう。
