---
layout: post
status: publish
published: true
title: JSF+AjaxでUIコンポーネント開発 with Shale Remoting (7)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 270
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=270
date: '2006-11-23 19:26:39 +0900'
date_gmt: '2006-11-23 10:26:39 +0900'
categories:
- Shale
---

JSF+Ajax with Shale Remotingと題したエントリだが，やっとJavaコードの紹介を始めるときが来た。今回は，double-selectコンポーネントを構成する２つのクラスの一つ目，カスタムタグハンドラクラスについて解説しようと思う。
JSP実装のJSFコンポーネントを作成する際，それはカスタムタグの形態となるために，タグハンドラクラスを作成する必要がある。JSFでは，以下のどちらかのクラスを継承して作成する。

* UIComponentTag - 本体部を持たない場合に使用

* UIComponentBodyTag - 本体部を持つ場合に使用
double-selectコンポーネントは本体部を持たないため，UIComponentTagクラスを継承して作成する。前回のエントリで登場したtaglib.tldファイルに既にタグハンドラクラス名をDoubleSelectTagと明記しているので，その名前にする。

>public class DoubleSelectTag extends UIComponentTag {
}

double-selectコンポーネントのタグには，4つの属性（id, value, leftSelcetItems, rightSelectItems）がtaglib.tldファイルにて定義されている。id属性については，UIComponentTagクラスが受け持ってくれるので，残り3つの属性を扱うためのフィールドやメソッドを作成する。これはJSFに特化したものではなく，JSPのカスタムタグを作成する場合と同じである。つまり，3つの属性についてsetterメソッドを作成すれば良い。getterを作る人も見受けられるが，今回は使用する用途がないため作成しない。

>private String value;
private String leftSelectItems;
private String rightSelectItems;
public void setValue(String value) {
　　this.value = value;
}
public void setLeftSelectItems(String leftSelectItems) {
　　this.leftSelectItems = leftSelectItems;
}
public void setRightSelectItems(String rightSelectItems) {
　　this.rightSelectItems = rightSelectItems;
}

さて，ここで普通ならdoStartTag()メソッドなどをオーバーライドしていくのだが，JSFコンポーネントでは全く違ったメソッドをオーバーライドしていくことになる。
まずは，double-selectコンポーネントが使用するUIコンポーネントの種別を返すgetComponentType()メソッドをオーバーライドする。

>@Override
public String getComponentType() {
　　return UIInput.COMPONENT_TYPE;
}

ここでは，JSFが標準で提供しているUIInputクラスのCOMPONENT_TYPE定数値を返却することで，double-selectコンポーネントがUIInputクラスを使用することを宣言している。UIInputクラスは，入力を伴うJSFコンポーネントについての多くの共通的な機能を持つUIコンポーネントクラスである。ほとんどの場合は，継承して自作などをすることなく，このUIInputクラスをそのまま使うことができるだろう。UIInputクラスはかなり汎用的にできているということである。
次に，double-selectコンポーネントのレンダラの種別を返すgetRendererType()メソッドをオーバーライドする。

>@Override
public String getRendererType() {
　　return "jp.eisbahn.jsf.DoubleSelect";
}

JSFコンポーネントは，HTMLやその他のデバイス向けに何らかのマークアップ言語を使ってコンポーネントをレンダリングする処理を，カスタムタグ内で行うのではなく，レンダラクラスを使ってレンダリングする。double-selectコンポーネントはレンダラクラスを自作するので，getRendererType()メソッドに自作レンダラを識別するための文字列を返させている。この識別子と実際に使用されるレンダラクラスの対応は，faces-config.xmlファイルに記述されることになる。
ここまではジャブ，前座，前説である。JSFコンポーネントのタグハンドラクラスの最も重要な役割は，JSP中に記述された内容をUIコンポーネントにセットすることである。それを行うために，setProperties()メソッドをオーバーライドする。

>@SuppressWarning("unchecked")
@Override
protected void setProperties(UIComponent component) {
　　super.setProperties(component);
　　Map attributes = component.getAttributes();
　　if (value != null) {
　　　　if (isValueReference(value)) { - (1)
　　　　　　setValueBinding(component, "value", value); - (2)
　　　　} else {
　　　　　　attributes.put("value", value); - (3)
　　　　}
　　}
　　attributes.put("leftSelectItems", leftSelectItems);
　　attributes.put("rightSelectItems", rightSelectItems);
}

setProperties()メソッドでは，先ほど作成した3つのプロパティについて，setterメソッドによりセットされた各値を，UIコンポーネントにセットする処理を記述する。setProperties()メソッドの引数には，getComponentType()メソッドの戻り値によって決定するUIコンポーネントクラスのインスタンスが渡される。double-selectコンポーネントの場合は，UIInputクラスのインスタンスが渡されることになる。
double-selectコンポーネントのカスタムタグには4つの属性が存在するが，id属性については親クラス（UIComponentTag）にて特別扱いされているために，記述する必要はない。残りの3つの属性値について，UIコンポーネントにセットする処理を記述する。
まずvalue属性だが，これはValue Binding式が記述可能となっているために，それに対応した処理を行う必要がある。具体的には，まずvalue属性値がValue Binding式なのかどうかをチェックする(1)。その結果，もしValue Binding式であれば，ValueBindingオブジェクトを生成してUIコンポーネントにそれを渡す(2)。もしValue Binding式でなかった場合は，UIコンポーネントが持つ属性値を格納するためのコレクションを取得し，それに属性値を名前付きで渡す(3)。ま，value属性にValue Binding式を書かないことは現実的にはないと思うので，(3)の処理を行わずに実行時例外を投げてしまっても構わないかもしれない。
leftSelectItems属性値およびrightSelectItems属性値は，Value Binging式が書かれるのではなく，Method Binding式が記述される。通常ではここでMethod Binding式に対応した処理を記述するのだが，double-selectコンポーネントはShale Remotingを使ったAjax対応コンポーネントであり，両属性値はWebブラウザから選択値取得をサーバに要求するためのリクエストパスの作成に使用される。よって，Method Binding式としての処理は行わずに，属性値をそのままUIコンポーネントに渡している。
Value Binding式からValueBindingオブジェクト生成してUIコンポーネントにセットする処理は，(2)のsetValueBinding()メソッド呼び出しにより行っている。setValueBinding()メソッドのコードを以下に示す。

>private void setValueBinging(UIComponent component, String name, String value) {
　　FacesContext context = getFacesContext();
　　Application application = context.getApplication();
　　ValueBinding vb = application.createValueBinding(value);
　　component.setValueBinding(name, vb);
}

Value Binding式の解釈は，JSFの実装が実行時に管理する全てのManagedBeanオブジェクトに関係するために，ValueBindingオブジェクトの生成はApplicationオブジェクトが担っている。ApplicationオブジェクトをFacesContextオブジェクトから取得し，Application#createValueBinding()メソッドにValue Binding式を渡してValueBindingオブジェクトを生成してもらう。そして，UIコンポーネントのsetValueBinding()メソッドを使って，名前付きで渡す。
以上がShale Remotingの使用を前提としたAjax対応JSFコンポーネントのカスタムタグハンドラクラスの説明である。ここで，JSPに書かれた属性値がどう使われるのか，疑問に思うかも知れない。

![attr-tag-uic-rend.jpg](http://www.eisbahn.jp/yoichiro/images/attr-tag-uic-rend.jpg)
属性値は，UIコンポーネントに渡され，次にgetRendererType()メソッド＆faces-config.xmlファイルで特定されるレンダラクラス内で属性値が取り出され，コンポーネントのレンダリング（=HTMLなどの生成＆出力）に使用されるのである。
今回解説したDoubleSelectTag.javaファイルは，
[ここからダウンロード](http://www.eisbahn.jp/yoichiro/double-select/DoubleSelectTag.java)できる。これを以下のディレクトリに保存して欲しい。
・[Eclipseワークスペース]/double-select/src/main/java/jp/eisbahn/testprograms/jsf/ds
さて次回は，今回UIコンポーネントにセットした属性値を使ってコンポーネントをレンダリングする処理，すなわちレンダラクラスの紹介をしようと思う。Shale Remotingを直接使用することになるので，やっと深めな技術的話になるだろう。（ｗ
