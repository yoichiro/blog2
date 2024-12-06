---
layout: post
status: publish
published: true
title: JSF+AjaxでUIコンポーネント開発 with Shale Remoting (9)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 273
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=273
date: '2006-11-29 11:19:03 +0900'
date_gmt: '2006-11-29 02:19:03 +0900'
categories:
- Shale
---

[前回](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting_7.html)はdouble-selectコンポーネントのレンダリング結果を説明して終わってしまった。今回という今回は，レンダラクラスの説明を行おうと思う。前回紹介したレンダリング結果と対応させながら以降の説明を見ていって欲しい。
JSFコンポーネントをレンダリングする機能を持つクラスは，Rendererクラスを継承して作成する。double-selectコンポーネントでは，DoubleSelectRendererというクラス名とした。

>public class DoubleSelectRenderer extends Renderer {
}

double-selectコンポーネントは，Shale Remotingに依存したリクエストパスを出力する箇所がいくつか存在する。コンテキストパスやFacesServletを呼び出すためのURLマッピングなど，実行時の環境を考慮したリクエストパスを作ってくれるXhtmlHelperというユーティリティクラスが，Shale Remotingで提供されている。これをクラスフィールドとして準備する。

>private static XhtmlHelper helper = new XhtmlHelper();

レンダリング処理は，親のRendererクラスが規定しているencodeBegin()，encodeChildren()，そしてencodeEnd()メソッドに記述する。JSPのTagインタフェースにdoStartTag()メソッドやdoEndTag()メソッドがあったように，Rendererクラスでもマークアップされたテキストのレンダリングが便利になるよう，メソッドが分割されている。

![encode-methods.jpg](http://www.eisbahn.jp/yoichiro/images/encode-methods.jpg)
JSFの想定として，開始タグをencodeBegin()メソッドで，閉じタグをencodeEnd()メソッドでレンダリングする。そしてencodeChildren()メソッドでは，このコンポーネントが持つ子供のコンポーネントのレンダリングを行う。
RendererクラスのencodeChildren()メソッドには，既に子コンポーネントの各種エンコードメソッドを呼び出す処理が記述されている。しかし，デフォルトではgetRendersChildren()メソッドの戻り値がfalseとなっているために，子コンポーネントのレンダリング処理は抑止されている。もし子コンポーネントを持つことができるカスタムコンポーネントを作成した場合は，getRendersChildren()メソッドをオーバーライドしてtrueを返却し，encodeChildren()メソッドの処理を有効にする必要がある。
話を元に戻そう。double-selectコンポーネントでは，

* 子コンポーネントを持たない。

* 一つのタグのレンダリングだけではない。
という理由から，encodeBegin()メソッド内で全てのレンダリングを行っている。

>@Override
public void encodeBegin(FacesContext context,
　　　　UIComponent component) throws IOException {
　　String leftSelectItems = convertRemotingPath(
　　　　context, (String)component.getAttributes().get("leftSelectItems")); - (1)
　　String rightSelectItems = convertRemotingPath(
　　　　context, (String)component.getAttributes().get("rightSelectItems"));
　　String clientId = component.getClientId(context);
　　String leftSelectId =
　　　　clientId + UIForm.SEPARATOR_CHAR + "leftSelect"; - (2)
　　String rightSelectId =
　　　　clientId + UIForm.SEPARATOR_CHAR + "rightSelect";
　　ResponseWriter writer = context.getResponseWriter();
　　helper.linkJavascript(context, component, writer,
　　　　Mechanism.CLASS_RESOURCE, "/META-INF/js/double-select.js"); - (3)
　　writeLeftSelect(writer, component, leftSelectId,
　　　　rightSelectId, rightSelectItems); -(4)
　　writeRightSelect(writer, component, rightSelectId);
　　writeInitialScript(writer, leftSelectId, rightSelectId,
　　　　leftSelectItems, rightSelectItems); - (5)
　　writer.flush();
}

処理内容としては，

* 左右の選択肢を得るためのリクエストパスを作成。

* 左右のコンボボックスのIDを作成。

* double-select.jsファイルの読み込みタグを出力。

* 左右のselectタグを出力。

* 初期選択肢取得処理登録を行うためのscriptタグを出力。
である。順を追って見ていこう。
左右の選択肢を得るためのリクエストパスを作成する処理だが，UIComponent#getAttributes()メソッドで得られたMapオブジェクトから，UIコンポーネントに格納した属性値を取り出す。属性値は，
[前々回](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting_6.html)にDoubleSelectTag#setProperties()メソッドでUIコンポーネントにセットしたものだ。”leftSelectItems”および”rightSelectItems”という名前で格納してあるので，もちろんその名前を指定して取得する。次に，取得した文字列をconvertRemotingPath()メソッドに渡して，Shale Remotingで使用可能なリクエストパス文字列に変換する。

>private String convertRemotingPath(
　　　　FacesContext context, String methodBindingEx) {
　　String result = "/" + methodBindingEx.substring(
　　　　2, methodBindingEx.length() - 1).replace('.', '/');
　　result = helper.mapResourceId(context,
　　　　Mechanism.DYNAMIC_RESOURCE, result);
　　return result;
}

JSP上に書かれたleftSelectItems属性値はメソッドバインディング式なので，まず前後の”#{”と”}”を除去し，”.”を”/”に変換する。Shale Remotingでは，ManagedBeanの動的呼び出しには接頭語として”/dynamic”を付け，語尾にFacesServletで処理されるためのurl-mapping文字列を記述しなければならないが，この処理はXhtmlHelperクラスのmapResourceId()メソッドにMechanism.DYNAMIC_RESOURCE定数を渡すことで行ってくれる。

![mb2srp.jpg](http://www.eisbahn.jp/yoichiro/images/mb2srp.jpg)
rightSelectItems属性についても，同様にconvertRemotingPath()メソッドを使って変換しておく。
次に，レンダリングするselectタグのid属性およびname属性の値を決定する。id属性値は，JavaScript関数内でselectタグを特定するために使用される。また，name属性値は，double-selectコンポーネントを配置したフォームがサブミットされた際に，それぞれの選択値をリクエストパラメータから取得する際に使用される。このdouble-selectコンポーネントのClient IDは，UIComponent#getClientId()メソッドで取得できる。これに，UIForm.SEPARATOR_CHAR定数値”:”を区切りとして，左右を特定するための”leftSelect”および”rightSelect”を追加し，それぞれのid属性値およびname属性値とする。
さて，やっと実際にレンダリングする処理にきた。レンダリングは，それ専用のWriterオブジェクトが存在する。FacesContext#getResponseWriter()メソッドを使うと，ResponseWriterオブジェクトが得られる。これを使うことで，マークアップ文字列を生成するのが楽になり，エスケープなどもやってくれる。
レンダリングの最初は，double-select.jsファイルを読み込むためのscriptタグの出力だ。これはXhtmlHelperクラスが専用の機能を提供してくれている。linkJavascript()メソッドにMechanism.CLASS_RESOURCE定数およびdouble-select.jsファイルのJARファイル内での格納場所を指定するだけで，あとは勝手にscriptタグを作って出力してくれる。

![resource2srp.jpg](http://www.eisbahn.jp/yoichiro/images/resource2srp.jpg)
さらに，linkJavascript()メソッドは，一度出力したことを記憶してくれている。つまり，同一ページ内に何度もscriptタグが出力されることによって何度も同じファイルがWebブラウザに読み込まれることを防いでくれるのだ。これは非常に便利である。
左右のselectタグの出力については，それぞれ専用のメソッドを作成している。まずは左のselectタグの出力を行うwriteLeftSelect()メソッドから見ていこう。

>private void writeLeftSelect(ResponseWriter writer, UIComponent component,
　　　　String leftSelectId, String rightSelectId, String rightSelectItems)
　　　　　　　　　　　　　　　　　　　　　　　　　　　　throws IOException {
　　writer.startElement("select", component);
　　writer.writeAttribute("id", leftSelectId, null);
　　writer.writeAttribute("name", leftSelectId, null);
　　writer.writeAttribute("onchange",
　　　　"leftValueChanged('" + leftSelectId + "', '"
　　　　　　+ rightSelectId + "', '" + rightSelectItems + "');",
　　　　null);
　　writer.endElement("select");
}

ResponseWriterオブジェクトの各種メソッドを使用して，selectタグと各種属性を出力している。使用しているメソッドは，以下の通り。括弧内は，writeText()メソッドを使用しなかった（＝本体部を出力しなかった）場合の出力例である。

* startElement() - タグの開始文字列を出力する。（

* writeAttribute() - タグの属性およびその値を出力する。（ name="value"）

* endElement() - タグの終了文字列を出力する。（ />）
writeAttribute()メソッドの第3引数は，出力する属性と関連するUIコンポーネントのプロパティ名を指定するようにAPIリファレンスで記述されているが，SunのReference Implementationで提供されているHtmlResponseWriterクラスでは未使用なようだ。もし関連するものがあれば，という但し書きもAPIリファレンスで見受けられるので，ここではnullを渡している。
左のselectタグには，onchange属性にleftValueChanged関数（double-select.jsファイルに記述）を指定している。これによって，左のコンボボックスの選択が変更された際に，leftValueChanged関数が呼び出されるようになる。leftValueChanged関数の第3引数には，右のコンボボックスの選択肢を得るためのリクエストパスを指定しているのがわかるだろう。
右のselectタグの出力を行うwriteRightSelect()メソッドは，ほとんどwriteLeftSelect()メソッドと同じである。

>private void writeRightSelect(ResponseWriter writer,
　　　　UIComponent component, String rightSelectId) throws IOException {
　　writer.startElement("select", component);
　　writer.writeAttribute("id", rightSelectId, null);
　　writer.writeAttribute("name", rightSelectId, null);
　　writer.endElement("select");
}

先ほどに比べて，onchange属性の出力を行っていないだけで，後は属性値が異なるだけである。
最後の初期選択肢取得処理登録を行うためのscriptタグの出力だが，これは何のテクニックも使わず，scriptタグの文字列構築とそれの出力を行っているのみだ。

>private void writeInitialScript(ResponseWriter writer,
　　　　String leftSelectId, String rightSelectId,
　　　　String leftSelectItems, String rightSelectItems) throws IOException {
　　StringBuffer sb = new StringBuffer();
　　sb.append("\n");
　　sb.append("
\n");
　　writer.write(sb.toString());
}
>private void writeInitialScript(ResponseWriter writer,
　　　　String leftSelectId, String rightSelectId,
　　　　String leftSelectItems, String rightSelectItems) throws IOException {
　　StringBuffer sb = new StringBuffer();
　　sb.append("\n");
　　sb.append("
\n");
　　writer.write(sb.toString());
}
>private void writeInitialScript(ResponseWriter writer,
　　　　String leftSelectId, String rightSelectId,
　　　　String leftSelectItems, String rightSelectItems) throws IOException {
　　StringBuffer sb = new StringBuffer();
　　sb.append("\n");
　　sb.append("
\n");
　　writer.write(sb.toString());
}

かなり長くなってしまったが，以上がレンダリング処理の詳細である。基本的にはResponseWriterクラスを使った泥臭い出力処理が並ぶことになるのがわかるだろう。しかし，ちょこっとだけShale Remotingの便利さを見ることができたのではないだろうか。XhtmlHelperクラスによるjsファイルの複数回読み込みの抑止などは，Java PetStoreでは自前で書かれていることに比べると，非常にありがたく効果の大きい機能と言えるだろう。
例のごとく，DoubleSelectRenderer.javaファイルは
[ここからダウンロード](http://www.eisbahn.jp/yoichiro/double-select/DoubleSelectRenderer.java)して，以下の場所に配置して欲しい。
・[Eclipseワークスペース]/double-select/src/main/java/jp/eisbahn/testprograms/jsf/ds
DoubleSelectRendererクラスのencodeBegin()メソッドを見てきたが，レンダラクラスの役割は実はこれだけではない。フォームサブミット時にdouble-selectコンポーネントに関するリクエストパラメータを適切に処理する機能（これをデコードと呼ぶ）も実装しなければならない。が，それはとりあえず放置して，次回はdouble-select.jsファイルに記述されることになるJavaScript関数を紹介していきながら，Ajax + Shale Remotingを使ったdouble-selectコンポーネントの実行時の動作を紹介しようと思う。
