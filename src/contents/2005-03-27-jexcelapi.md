---
layout: post
status: publish
published: true
title: JExcelApiの使い方
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 34
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=34
date: '2005-03-27 22:56:48 +0900'
date_gmt: '2005-03-27 13:56:48 +0900'
categories:
- Java
---

「
[Excelファイル生成ライブラリ調査！](http://blog.so-net.ne.jp/yoichiro/2005-03-26)」で取り上げたJExcelApi，さっそく実際に使ってみた。なかなかいい感じなので，基本的な使い方をここに残しておこう。

まずは，
[JExcelApiのプロジェクトページ](http://jexcelapi.sourceforge.net/)から，
[jexcelapi_2_5_3.zip](http://prdownloads.sourceforge.net/jexcelapi/jexcelapi_2_5_3.zip?download)ファイルを入手し，適当な場所に展開する。その中のjxl.jarファイルがJExcelApi本体である。これをクラスパスに通せば，使用準備は完了だ。

 さて，さっそくコーディングの方法に入ろう。トピックごとにコードを紹介する。

【ワークブックの新規作成】

>WorkbookSettings ws = new WorkbookSettings();


ws.setLocale(new Locale("ja", "JP");


ws.setEncoding("Windows-31J");


OutputStream os = ...;


WritableWorkbook workbook = Workbook.createWorkbook(os, ws);


【ワークブックの読み込み】

>InputStream is = ...;


Workbook workbook = Workbook.getWorkbook(is);


【ワークブックの新規作成(テンプレートから)】

>Workbook template = ...;


OutputStream os = ...;


WritableWorkbook workbook = Workbook.createWorkbook(os, template, ws);


【ワークシートの追加】

>Workbook workbook = ...;


WritableSheet sheet = workbook.createSheet("シート名", 0);


【既存ワークシートの取得】

>Workbook workbook = ...;


WritableSheet sheet = workbook.getSheet(0);


【セルの結合】

>WritableSheet sheet = ...;


sheet.mergeCells(0, 1, 4, 2);


【ラベルセルの作成(基本)】

>WritableSheet sheet = ...;


Label label = new Label(1, 2, "ラベル");


sheet.addCell(label);


【ラベルセルの作成(装飾付)】

>WritableSheet sheet = ...;


WritableCellFormat format = new WritableCellFormat();


// formatに対して装飾情報をセット


Label label = new Label(1, 2, "ラベル", format);


sheet.addCell(label);


【フォントのセット】

>FontName fontName = WritableFont.createFont("ＭＳ Ｐゴシック");


WritableFont font = new WritableFont(fontName, 12);


WritableCellFormat format = new WritableCellFormat(font);


【太字，下線のセット】

>WritableFont font = ...;


font.setBoldStyle(WritableFont.BOLD);


font.setUnderlineStyle(UnderlineStyle.SINGLE);


WritableCellFormat format = new WritableCellFormat(font);


【セル内の文字位置のセット】

>WritableCellFormat format = ...;


format.setAlignment(Alignment.CENTRE);


format.setVerticalAlignment(VerticalAlignment.CENTRE);


【セル内折り返しのセット】

>WritableCellFormat format = ...;


format.setWrap(true);


【罫線のセット】

>WritableCellFormat format = ...;


format.setBorder(Border.TOP, BorderLineStyle.THIN);


【シートのページ設定のセット】

>WritableSheet sheet = ...;


sheet.setPageSetup(PageOrientation.PORTRAIT, PaperSize.A4, 2, 1);


【ワークブックの出力＆後始末】

>WritableWorkbook workbook = ...;


workbook.write();


workbook.close();


上記の手順を，APIリファレンスとにらめっこしながら使用すれば，帳票的な内容のExcelファイルは生成することができるだろう。
