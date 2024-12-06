---
layout: post
status: publish
published: true
title: JSFアプリケーションの単体テスト with Shale Test Framework (4)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 295
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=295
date: '2006-12-26 10:02:14 +0900'
date_gmt: '2006-12-26 01:02:14 +0900'
categories:
- Shale
---

Shale Test Framework（STF）を紹介しているエントリも，早くも3回目になってしまった。
[前回のエントリ](http://www.eisbahn.jp/yoichiro/2006/12/jsf_with_shale_test_framework_2.html)では，テストメソッドの説明の途中で強引に終わらせてしまった。今回はその続きを見ていこう。

まず，テストメソッドのソースコードを再び示しておこう。
[前回のエントリ](http://www.eisbahn.jp/yoichiro/2006/12/jsf_with_shale_test_framework_2.html)では，テスト対象のgetRightSelectItems()メソッドを呼び出すところまで解説した。

public void testGetRightSelectItemsValue1() throws Exception {
externalContext.addRequestParameterMap("fv", "1");
target.getRightSelectItems();
assertEquals("ContentType", "text/javascript;charset=UTF-8", response.getContentType());
JSONArray jsonArray = JSONArray.fromString(getContent());
assertEquals("jsonArray.length", 2, jsonArray.length());
assertSelectItem(jsonArray, 0, "宇都宮線", "1");
assertSelectItem(jsonArray, 1, "高崎線", "2");
}

今回のテスト対象のManagedBeanのメソッドでは，ResponseWriterオブジェクトに対して文字列をwriteしている。その検証のためには，テストメソッドからwriteされた文字列を取得する必要がある。

![MockResponseWriter.jpg](http://www.eisbahn.jp/yoichiro/images/MockResponseWriter.jpg)

AbstractJsfTestCaseクラスが提供するResponseWriterオブジェクトは，実際はMockResponseWriterクラスのインスタンスであり，これはMockRenderKitクラスが生成する。HttpServletResponseオブジェクトに関してもモッククラスが提供されていて，getWriter()メソッドで得られるWriterオブジェクトに関しても，MockPrintWriterクラスのインスタンスとなっている。MockPrintWriterオブジェクトは，CharArrayWriterオブジェクトに対して出力することが規定されているので，結果としてResponseWriterオブジェクトに対して出力された文字列は，CharArrayWriterオブジェクトの内部に蓄積され，MockPrintWriter#content()メソッドを使用して取り出すことができる。

話を元に戻そう。SelectItemsProducer#getRightSelectItems()メソッドでは，ResponseWriterオブジェクトに対して，コンテントタイプを"text/javascript;charset=UTF-8"というように指定している。これを4行目で検証している。AbstractJsfTestCaseクラスでは，実行時に存在するHttpServletResponseオブジェクトを，MockHttpServletResponseクラスのインスタンスとして，responseインスタンスフィールドにセットしてくれている。responseフィールドのgetContentType()メソッドの戻り値について，正しくコンテントタイプがセットされたかどうかを検証できる。

さて，いよいよ出力されたJSON形式のテキストの検証だが，テキストのまま検証するのはかなり難しく，手間である。幸いにも，JSON形式のテキストをパースしてオブジェクトとして扱うことを可能にするライブラリが，今日ではいくつか提供されている。今回は，
[json-lib](http://json-lib.sourceforge.net/)というライブラリを使ってみよう。

![json-lib.jpg](http://www.eisbahn.jp/yoichiro/images/json-lib.jpg)

と，その前に，SelectItemsProducer#getRightSelectItems()メソッドが出力した文字列を取得しなければならない。その処理を，getContent()メソッドにまとめてみた。

private String getContent() throws IOException {
MockPrintWriter writer = (MockPrintWriter)response.getWriter();
char[] cs = writer.content();
return String.valueOf(cs);
}



responseフィールドのgetWriter()メソッドの戻り値をMockPrintWriterクラスでダウンキャストする。そして，content()メソッドを呼び出して，出力された文字列を文字配列として取得，その後１つの文字列として返却している。

ここで，SelectItemsProducer#getRightSelectItems()メソッドが出力するテキストがどういうものかを把握しておこう。

![getRightSelectItems-result.jpg](http://www.eisbahn.jp/yoichiro/images/getRightSelectItems-result.jpg)

大括弧で括られていることから，配列形式であることがわかる。その中に，"label"や"value"という要素が含まれている。json-libでは，配列をJSONArrayクラスで，個々の要素をJSONObjectクラスで，それぞれ扱うことが出来る。

まずは，getContent()メソッドで得られた文字列全体を，JSONArrayオブジェクトにパースする。それが5行目である。JSONArray#fromString()メソッドを使って，JSONArrayオブジェクトに変換する。仮に，出力結果がJSON形式として不正なものだった場合は，パースに失敗し，JSONException例外が発生する。このパース作業によって，出力結果がJSON形式として妥当かどうかを検証できる。

パースした結果のJSONArrayオブジェクトには，各要素をJSONObjectオブジェクトとして保持しているはずである。まずは，その件数を検証する。6行目において，JSONArray#length()メソッドを呼び出すことで要素の個数を取得し，宇都宮線と高崎線の２個かどうか検証している。

次に，各要素の検証だが，これはassertSelectItem()メソッドにまとめてみた。

private void assertSelectItem(JSONArray jsonArray, int index, String label, String value) {
JSONObject jsonObj = jsonArray.getJSONObject(index);
assertEquals("jsonArray[" + index + "].label", label, jsonObj.getString("label"));
assertEquals("jsonArray[" + index + "].value", value, jsonObj.getString("value"));
}

JSONArrayオブジェクトのgetJSONObject()メソッドにインデックスを渡すことによって，各要素をJSONObjectクラスのインスタンスとして取得することができる。さらに，JSONObject#getString()メソッドに名前を渡すことで，それに対する値を得ることができる。ここで検証したい値は，"label"と"value"の値。引数に正しい値を指定して，それぞれが一致するかどうかを検証している。

STFを使用することによって，JSFの実行時の環境をシミュレートした上で，テストメソッドを記述することができる。特に，Shale Remotingを使ったManagedBeanや，JSFコンポーネントを自作した場合について，STFは手放せないライブラリとなるはずだ。

[Shale Remoting](http://www.eisbahn.jp/yoichiro/2006/12/jsfajaxui_with_shale_remoting_15.html)と合わせて，是非試してみて欲しい。
