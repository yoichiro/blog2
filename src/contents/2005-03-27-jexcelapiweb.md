---
layout: post
status: publish
published: true
title: JExcelApiとWebアプリの相性は？
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 35
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=35
date: '2005-03-27 23:40:43 +0900'
date_gmt: '2005-03-27 14:40:43 +0900'
categories:
- Java
---

Webアプリでは，Webブラウザにダウンロードさせたい情報を，一旦サーバ内でファイルとして作成し，それをWebブラウザに対して送信したりする。この場合，セッションが無効になるタイミングなどで，不要になったサーバ内のファイルを削除しなければならない。完全にファイルを削除することはできないため，ごみファイルが残ってしまうこともしばしばだ。さて，あるWebアプリにおいて，Excelファイルを自動生成してWebブラウザに返却する処理が存在すると仮定する。この場合，まず思いつくことは，POIやJExcelApiなどのツールを使ってExcelファイルを生成し，ServletやJSP，StrutsのActionなどでHttpServletResponseから得た出力ストリームに対して，Excelファイルを読み込んで出力ストリームに送信，というパターンだろう。しかし，JExcelApiを使えば，一時的にファイルを生成する必要はない。

>public class ExcelDownloadAction extends Action {


　　public ActionForward execute(..., HttpServletResponse response) {


　　　　...


　　　　WorkbookSettings ws = new WorkbookSettings();


　　　　ws.setLocale(new Locale("ja", "JP");


　　　　ws.setEncoding("Windows-31J");


　　　　OutputStream os = response.getOutputStream();


　　　　WritableWorkbook workbook = Workbook.createWorkbook(os, ws);


　　　　// workbookに対してシート作成＆内容作成


　　　　workbook.write();


　　　　workbook.close();


　　　　...


　　　　return null;


　　}


}


WritableWorkbookオブジェクトを，HttpServletResponseから得た出力ストリームを基に生成する。これによって，writeメソッドの実行の結果が出力ストリームに直接出力される。ワークブックの生成が，全てメモリ上で行われるようになるので，ファイルの後始末などを考える必要もなく，しかもサーバリソースに優しい。

POIでこれができるかどうかは，POIをやったことないのでわからない。少なくとも，JExcelApiはWebアプリとの相性はよさそうだ。
