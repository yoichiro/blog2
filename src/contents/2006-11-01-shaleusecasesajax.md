---
layout: post
status: publish
published: true
title: shale-usecasesでのAjaxリクエストの処理過程
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 252
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=252
date: '2006-11-01 23:25:07 +0900'
date_gmt: '2006-11-01 14:25:07 +0900'
categories:
- Shale
---

JSFを利用したアプリケーションを作るための一つの現実解として，Shaleが挙げられる。Shaleにはshale-usecasesと呼ばれる簡単かつ要点を非常に良く突いたサンプルアプリケーションが公開されているが，Ajaxによる要求に対してShaleがどのように対処しているのかを追ってみた。
[郵便番号から町＆州を取得する]

* zipCode.jsp内のHTMLのコンボボックス（h:selectOneMenu）の選択状態がユーザの操作により変化する。

* onchange属性にて指定されたzipChanged()関数が呼び出される。

* 選択された郵便番号（zip）をパラメータに伴って，「/dynamic/remoting$business/cityAndStateForZip.faces」要求をサーバに送出する。

* commons-chain定義にヒットしない（要求が「〜.remote」ではないから）ので，FacesServletに要求が渡される。

* shale-remotingで提供されるRemotingPhaseListener内（RESTORE_VIEWフェーズ直後に適用）で，MappingsHelperによって「/dynamic/*」に適合すると判断され，メソッドバインディング呼び出しをするためにMethodBindingProcessorが選択される。

* MethodBindingProcessorは「remoting$business/cityAndStateForZip」の”/”を”.”に置換し，前後にJSFのEL記号を付与して，要求をメソッドバインディング式（#{remoting$business.cityAndStateForZip}）に変換する。

* メソッドバインディング式が解釈され，faces-config.xmlにて定義されたremoting$businessという名前のManagedBean（Business）が持つcityAndStateForZip()メソッドが呼び出される。

* サーバへの要求送出時に渡された郵便番号を，リクエストパラメータマップから取得する。

* shale-coreのAbstractFacesBeanにより提供されるgetBean()メソッドを使って，domainsという名前のManagedBean（Domains）を取得する。

* DomainsのgetCityAndStateItems()メソッドを呼び出し，結果（SelectItemの配列）を得る。

* 結果からXML形式の文字列を生成し，text/xmlコンテントタイプをセットした後に，shale-remotingで提供されるResponseWriterを使ってWebブラウザに返送する。

* FacesContextのresponseComplete()メソッドを呼び出し，移行のフェーズをスキップする。

* 結果のXMLから
要素を取得し，CityおよびStateフィールドに結果の文字列をセットする。
Shaleの初期の頃は，Ajaxリクエストの処理はcommons-chainで振り分けられる「〜.remote」要求によって対処していたが，現在はshale-remotingによって対処が行われている。
shale-remotingでは，/dynamicの他に，/staticや/webappといったパスもMappingHelperで識別しているようだ。/dynamicを使うことによって，ManagedBeanが持つメソッドを直接Webブラウザ上から呼び出せる感覚を得ることができる。
shaleは多くの層が存在し，更にJSFのフェーズも絡んでくるために，動きを追うのが大変である。しかし，把握できたときの嬉しさはなかなかのものだ。ハッキングの醍醐味って感じかな。
