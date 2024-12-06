---
layout: post
status: publish
published: true
title: JSF+AjaxでUIコンポーネント開発 with Shale Remoting (10)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 274
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=274
date: '2006-12-01 15:31:02 +0900'
date_gmt: '2006-12-01 06:31:02 +0900'
categories:
- Shale
---

[前回](http://www.eisbahn.jp/yoichiro/2006/11/jsfajaxui_with_shale_remoting_8.html)までの説明で，double-selectコンポーネントのサーバでのレンダリング処理が行われるようになった。DoubleSelectRendererクラスのencodeBegin()メソッドによって，左右2つのselectタグがレンダリングされる。しかし，レンダリングされる内容の中で，いくつかJavaScript関数が登場したが，どれもdouble-select.jsファイルに記述されるとしか説明しなかった。今回は，Webブラウザ上で実行されるJavaScript関数を記述したdouble-select.jsファイルの内容について紹介し，double-selectコンポーネントの実行時の動作について見ていくことにしよう。
double-selectコンポーネントの特徴として，左右のコンボボックスのそれぞれが持つ選択肢の内容を，DoubleSelectRendererクラス内でoptionタグをレンダリングせず，Ajaxによって非同期にサーバから取得することがあげられる。そのためには，Webブラウザ上で実行されるJavaScript関数がどうしても必要になる。double-select.jsファイルに書かれる関数の内容は，選択肢を非同期でサーバから取得する処理となる。
double-selectコンポーネントでは，optionタグを動的に生成して左右のselectタグに反映するきっかけとして，以下の２つが存在する。

* 初期表示時

* 左のコンボボックスの選択状態が変化したとき
初期表示時には，左右それぞれのoptionタグを生成することになる。それに対して，左のコンボボックスの選択状態が変化した際は，右のoptionタグのみを生成し入れ替えるようにする。
では，初期表示の際の動作から説明しよう。DoubleSelectRendererクラスのencodeBegin()メソッドの中で，初期表示を行うために，以下のscriptタグを生成したことを思い出して欲しい。


ページの読み込み完了時に，loadLeftSelectItems()関数を呼び出すように，prototype.jsが提供する機能を使用して，イベントハンドラを登録している。
loadLeftSelectItems()関数が呼び出されてから，左右のコンボボックスに選択肢がセットされるまでの処理の流れが，以下の図である。

![ds-init-process-js.jpg](http://www.eisbahn.jp/yoichiro/images/ds-init-process-js.jpg)
loadSelectItems()関数では，左のコンボボックスの選択肢の取得を，サーバにあるManagedBeanオブジェクトに非同期通信で依頼する。ManagedBeanオブジェクトがJSON形式で返答すると，予めonCompleteプロパティとして登録しておいた関数がコールバック関数として実行される。その関数の中で呼び出したloadSelectItems()関数の中では，受け取った結果を元にしてoptionタグを生成する。生成されたoptionタグ一式を，selectタグのinnerHTML属性の値としてセットする。
さらに続いて，右のコンボボックスの選択肢取得に関しても，loadRightSelectItems()関数を呼び出して実施する。この際，先ほど取得した左の選択肢の最初のvalue値を，loadRightSelectItems()関数に引数として渡す。loadRightSelectItems()関数では，受け取った値をfvパラメータの値として，右の選択肢の取得を，やはりサーバにあるManagedBeanオブジェクトに非同期通信で依頼する。その結果は，先ほどと同じように，コールバック関数からloadSelectItems()関数を呼び出して，右のコンボボックスにoptionタグとして反映する。
では，loadLeftSelectItems()関数のコードを見てみよう。

>function loadLeftSelectItems(leftSelectId, rightSelectId,
　　　　　　funcLeftSelectItems, funcRightSelectItems) {
　　new Ajax.Request(
　　　　funcLeftSelectItems,
　　　　{
　　　　　　method: 'get',
　　　　　　onComplete: function(req) {
　　　　　　　　var select = document.getElementById(leftSelectId);
　　　　　　　　var result = loadSelectItems(req, select);
　　　　　　　　if (result) {
　　　　　　　　　　loadRightSelectItems(
　　　　　　　　　　　　result, rightSelectId, funcRightSelectItems);
　　　　　　　　}
　　　　　　}
　　　　}
　　);
}

引数の内容は，以下の通り。

* leftSelectId - 左のコンボボックスのID

* rightSelectId - 右のコンボボックスのID* * funcLeftSelectItems - 左の選択肢を得るためのリクエストパス

* funcRightSelectItems - 右の選択肢を得るためのリクエストパス
loadLeftSelectItems()関数では，prototype.jsが提供するAjax.Requestオブジェクトを使用して，非同期通信を実現している。Ajax.Requestオブジェクトのコンストラクタには，第１引数にリクエストパスを，第２引数には非同期通信のための連想配列を渡している。リクエストパスは，DoubleSelectRendererクラスのencodeBegin()メソッドがレンダリングした”.../dynamic/...faces”という文字列である。連想配列のonCompleteプロパティには，ManagedBeanオブジェクトの結果を処理するためのコールバック関数を指定している。
コールバック関数の中では，最初に左のselect要素を取得し，その後loadSelectItems()関数を呼び出している。

>function loadSelectItems(req, targetSelect) {
　　var items = eval(req.responseText);
　　var options = "";
　　for (var i = 0; i < items.length; i++) {
　　　　options += "

" + items[i].label + "";
　　}
　　targetSelect.innerHTML = options;
　　return items[0].value;
}

コールバック関数が呼び出されるときに渡されるreqオブジェクトのresponseTextプロパティには，ManagedBeanオブジェクトが生成したJSON形式のテキストがセットされている。eval()関数にそれを指定し，JSON形式のテキストからオブジェクトに変換する。変換されたオブジェクト群をfor文で回して，それぞれoptionタグを作成し，結果をtargetSelect引数で指定されたselectタグのinnerHTMLプロパティにセットする。

![json2option.jpg](http://www.eisbahn.jp/yoichiro/images/json2option.jpg)
loadSelectItems()関数では，ManagedBeanが応答した選択肢の１つ目のvalue値を，return文で返却している。先ほどのコールバック関数では，loadSelectItems()関数の結果，つまり左の選択肢の１つ目の値を元にして，引き続きloadRightSelectItems()関数を呼び出している。

>function loadRightSelectItems(
　　　　selectedLeftValue, rightValueId, funcRightSelectItems) {
　　new Ajax.Request(
　　　　funcRightSelectItems,
　　　　{
　　　　　　method: 'get',
　　　　　　parameters: 'fv=' + escape(selectedLeftValue),
　　　　　　onComplete: function(req) {
　　　　　　　　var select = document.getElementById(rightValueId);
　　　　　　　　loadSelectItems(req, select);
　　　　　　}
　　　　}
　　);
}

以下の点が異なっているだけで，loadRightSelectItems()関数でやっていることは，loadLeftSelectItems()関数とほぼ同じである。

* リクエストパスが，右の選択肢を得るためのパスであること。

* 引数で左のコンボボックスの選択値を得ていること。

* 引数で受け取った左の選択値を値とするfvパラメータを，リクエストパスに追加していること。

* コールバック関数内でloadSelectItems()関数を呼び出す際に，右のselectタグの要素を指定していること。
初期表示時は左のコンボボックスの選択値は選択肢の１つ目なので，loadRightSelectItems()関数では，その選択値をManagedBeanオブジェクトに渡すことによって，それに対応する右の選択肢を返すように指示しているわけである。
左のコンボボックスの選択値が変更された際の動作は，初期表示時の動作よりも単純である。

![ds-value-changed-process-js.jpg](http://www.eisbahn.jp/yoichiro/images/ds-value-changed-process-js.jpg)
DoubleSelectRendererクラスのencodeBegin()メソッドの中で，左のコンボボックスのselectタグには，onchange属性をレンダリングするようにしたのを思い出して欲しい。


onchange属性の値として，leftValueChanged()関数を呼び出すように指定している。leftValueChanged()関数では，その時に選択されている左のコンボボックスの選択肢のvalue値をloadRightSelectItems()関数に渡して，右のコンボボックスの選択肢の内容を変更している。

>function leftValueChanged(leftSelectId, rightSelectId, funcRightSelectItems) {
　　var leftSelect = document.getElementById(leftSelectId);
　　var selectedLeftValue = leftSelect.value;
　　loadRightSelectItems(
　　　　selectedLeftValue, rightSelectId, funcRightSelectItems);
}

loadRightSelectItems()関数の呼び出しにより，右のselectタグのinnerHTMLプロパティが新たにManagedBeanが返す選択肢に置き換えられて，結果として右のコンボボックスの選択肢が動的に変更されるようになる。AjaxなJSFコンポーネントらしい動きの完成だ。
例によって，double-select.jsファイルを
[ここからダウンロード](http://www.eisbahn.jp/yoichiro/double-select/double-select.js)して，以下の場所に配置して欲しい。
・[Eclipseワークスペース]/double-select/src/main/resources/META-INF/js
だらだらと長らく解説をしてきたが，faces-config.xmlファイルを除けば，double-selectコンポーネントの「表示のみの機能」は一通り揃った。つまり，左右のコンボボックスを表示し，Ajax＆Shale RemotingでManagedBeanから非同期で選択肢を取得してコンボボックスに反映する，という動作のためのコードは全て記述したことになる。ただし，コンボボックスは表示のみでは使い物にならず，もちろん左右のコンボボックスでそれぞれ選択された値をManagedBeanのプロパティにバリューバインディング可能でないといけない。
次回は，double-selectコンポーネントの選択値をManagedBeanにバリューバインディングするためのコードを紹介しようと思う。
