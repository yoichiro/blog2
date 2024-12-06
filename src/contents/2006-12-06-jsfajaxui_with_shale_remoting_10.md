---
layout: post
status: publish
published: true
title: JSF+AjaxでUIコンポーネント開発 with Shale Remoting (11)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 277
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=277
date: '2006-12-06 13:13:13 +0900'
date_gmt: '2006-12-06 04:13:13 +0900'
categories:
- Shale
---

今回は，JSFコンポーネント，特に値の入力が可能なコンポーネントを作る際の基礎知識と，実装方法について紹介していこうと思う。Shale Remotingを使用したAjax対応コンポーネントの開発がメインテーマだが，今回はAjaxもShaleも関係なく，JSFのみの話になる。実は，
[前回のエントリ](http://www.eisbahn.jp/yoichiro/2006/12/jsfajaxui_with_shale_remoting_9.html)でdouble-selectコンポーネントのAjax関連の話は終わってしまっているんだけど，入力可能なJSFコンポーネントの作成方法はネットでも詳しい説明はなかったりするので，是非お付き合い頂きたい。

JSFコンポーネントにユーザが何か入力（コンボボックスやチェックボックスの選択行為も入力と表現する）をした場合，その入力値は以下のような人生を辿る。

![input-value-lifecycle.jpg](http://www.eisbahn.jp/yoichiro/images/input-value-lifecycle.jpg)

JSFコンポーネントが配置されたフォームがサブミットされると，まず入力値は所定の変換が行われ，さらに変換後の値に対して妥当性検証（いわゆる入力チェック）が実施される。検証の結果がOKだった場合は，バリューバインディング式に従ってManagedBeanオブジェクトのプロパティ値としてセットされる。

JSFでは，コンポーネントビューを中心として，処理のフェーズがいくつか定義されている。サブミットされた値を受け取るのはApply Request Valuesフェーズ，入力値を変換して検証するのはProcess Validationsフェーズがそれぞれ担当する。入力値の処理は，各フェーズの理解がないと把握するのは厳しい。JSFの仕様書の冒頭だけでも読んでおくといいかもしれない。

では，各フェーズで行われる処理を担当するはどのオブジェクトなのかを，紹介していこう。

![jsf-comp-input-value-sequence.jpg](http://www.eisbahn.jp/yoichiro/images/jsf-comp-input-value-sequence.jpg)

Apply Request Valuesフェーズにおいて，UIコンポーネントはレンダラにサブミットされたリクエストパラメータ値の取得を要求する（これをデコードと呼ぶ）(1)。その後Process Validationsフェーズにおいて，UIコンポーネントは，一旦取得した値をレンダラに返して，適切な変換を依頼する(2)。レンダラの初期実装では，UIコンポーネントに登録されたコンバータに変換処理を委譲する(3)。そして変換後の値を得たUIコンポーネントは，登録されているバリデータに値の検証処理を委譲する(4)。

このように，UIコンポーネントが他のオブジェクトに処理を委譲することで，拡張性や柔軟性が保たれている。

では，double-selectコンポーネントについて，サブミットされた値をUIコンポーネントにセットする処理を細かく見ていこう。

![jsf-comp-submitted-value-seq.jpg](http://www.eisbahn.jp/yoichiro/images/jsf-comp-submitted-value-seq.jpg)

Apply Request Valuesフェーズの動作として，LifecycleオブジェクトによってUIコンポーネントのprocessDecodes()メソッドが呼び出される。UIInputオブジェクトは，登録されているDoubleSelectRendererレンダラオブジェクトのdecode()メソッドを呼び出し，デコード処理を依頼する。

Webブラウザからサブミットされた値は，ExternalContextオブジェクトが保持している。ExternalContext#getRequestParameterMap()メソッドを呼び出すことによって，リクエストパラメータが保持されたMapオブジェクトを得ることができる。そのMapオブジェクトには，
[前のエントリ](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting_8.html)で解説した，double-selectコンポーネントのレンダリング時に決定された左右のselectタグのname属性がキーとなって，選択値が格納されている。つまり，「myForm:_id0:leftSelect = 1」といった感じである。

![get-submitted-value-from-rpm.jpg](http://www.eisbahn.jp/yoichiro/images/get-submitted-value-from-rpm.jpg)

double-selectコンポーネントでは，取得した左右の選択値を文字列の配列にして，UIInput#setSubmittedValue()メソッドを使ってUIコンポーネントに渡す。

では，実際のコードを見てみよう。

@Override
public void decode(FacesContext context, UIComponent component) {
super.decode(context, component);
String clientId = component.getClientId(context);
Map requestParameterMap = context.getExternalContext().getRequestParameterMap();
String leftValue = (String)requestParameterMap.get(clientId + UIForm.SEPARATOR_CHAR + "leftSelect");
String rightValue = (String)requestParameterMap.get(clientId + UIForm.SEPARATOR_CHAR + "rightSelect");
String[] submittedValue = {leftValue, rightValue};
EditableValueHolder vh = (EditableValueHolder)component;
vh.setSubmittedValue(submittedValue);
}

UIInputクラスは，EditableValueHolderインタフェースを実装している。EditableValueHolderインタフェースは，値を変更することができるUIコンポーネントが実装すべきインタフェースだ。DoubleSelectRendererクラスをUIInput以外のUIコンポーネントでも使用できるように，UIInputでダウンキャストするのではなく，あえてEditableValueHolderインタフェースでキャストすることによって，再利用性を高めている。setSubmittedValue()メソッドは，EditableValueHolderインタフェースが規定しているメソッドなので，それを使って左右のコンボボックスの選択値をUIコンポーネントにセットしている。

double-selectコンポーネントは，その主旨がAjaxとShale Remotingにあるため，変換や検証については考慮しない。よって，double-selectコンポーネントにおける入力値に対する処理は，decode()メソッドを実装するだけで完了とする。ただし，setSubmittedValue()メソッドで渡した値がどのように処理されていくのかについては，簡単に触れておこう。

UIInputでは，valueプロパティは特別視されている。JSPファイルに書かれたdouble-selectタグのvalue属性の値は，DoubleSelectTag#setProperties()メソッド内で，ValueBindingオブジェクトに変換されてUIコンポーネントに"value"という名前で予めセットされている（
[前のエントリ](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting_6.html)参照）。setSubmittedValue()メソッドで渡されて変換と検証の処理を経た値は，このvalueプロパティのValueBindingオブジェクトを使用して，ManagedBeanオブジェクトのプロパティ値としてセットされる。![uiinput-value-property.jpg](http://www.eisbahn.jp/yoichiro/images/uiinput-value-property.jpg)

これによって明らかとなるのは，UIInputが対象とする入力値はあくまで１つであり，よってバリューバインディングが行われる値も１つであるということ。例えば左右の選択肢それぞれを，別のバリューバインディング式を指定して別々のManagedBeanのプロパティ値にセットしたいときは，UIInputクラスでは役不足であり，UIコンポーネント自体を自作する必要がある。

setSubmittedValue()メソッドでUIInputオブジェクトに渡された文字列の配列は，登録されているコンバータやバリデーションがなければ，そのまま入力値として確定する。その際，setSubmittedValue()メソッドに渡した型がそのまま採用される。Stringだろうが，配列だろうが，プリミティブのラッパー型だろうが，コレクションだろうが，自作クラスだって構わない。オブジェクトであれば，それがそのままバリューバインディングの対象となる。

上記のDoubleSelectRenderer#decode()メソッド内でsetSubmittedValue()メソッドに渡されている値はString[]型なので，ManagedBeanクラス側も，バリューバインディング対象のプロパティの型はString[]を受け付ける型でなければならない。ただし，setSubmittedValue()で渡したオブジェクトがString以外の場合は，JSFの実装が標準で提供してくれるコンバータやバリデータは簡単には適用できないため，その型に依存するカスタムコンバータやカスタムバリデータを作成し提供することや，標準コンバータや標準バリデータで対応可能なようにUIコンポーネントやレンダラを拡張する必要が出てくる。以上がdouble-selectコンポーネントの入力値の処理に関する説明である。DoubleSelectRenderer.javaファイルは
[ここからダウンロード](http://www.eisbahn.jp/yoichiro/double-select/DoubleSelectRenderer.java)できるので，以下の場所に配置して欲しい。
[前のエントリ](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting_8.html)で既に保存している人は，同じファイルなのでそのままでOK。

・[Eclipseワークスペース]/double-select/src/main/java/jp/eisbahn/testprograms/jsf/ds

double-selectコンポーネントに必要なコードは，あと１つ。レンダラをJSFに登録するためのfaces-config.xmlファイルの説明を次回すれば，double-selectコンポーネントの完成だ。エントリは，もう10回を超えてしまった。簡潔な説明ができない自分に自己嫌悪である。。。
