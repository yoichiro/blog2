---
layout: post
status: publish
published: true
title: java -versionの出力先って
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 246
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=246
date: '2006-10-17 14:06:59 +0900'
date_gmt: '2006-10-17 05:06:59 +0900'
categories:
- Java
---

標準エラー出力だったのか！？知らなかった。。。
java.exeの「-version」や「-showversion」オプションによって表示される，

>java version "1.5.0_08"
Java(TM) 2 Runtime Environment, Standard Edition (build 1.5.0_08-b03)
Java HotSpot(TM) Client VM (build 1.5.0_08-b03, mixed mode, sharing)

ってメッセージ。てっきり標準出力に出されているものと思い込んでいたが，実は標準エラー出力に対して出力されている。これは，以下のVBScriptを実行してみてわかったことである。

>Set shell = Wscript.CreateObject("Wscript.Shell")
Set exec = shell.Exec("%JAVA_HOME%¥bin¥java.exe -version")
Set std = exec.
StdOut
Do Until std.AtEndOfStream
　　line = std.ReadLine
　　Wscript.Echo line
Loop

この内容を例えばtest.vbsというファイル名で保存し，コマンドプロンプトから「cscript test.vbs」で実行する。その結果WSHのバージョン情報しか表示されないが，リスト中の赤の部分「StdOut」を「StdErr」に変更すると，めでたくJava VMのバージョン情報が表示される。
うーん，特に「-showversion」の場合，起動されるアプリケーションが標準出力を使うという仮定において，Java VMのバージョン情報がノイズにならないように，あえて標準エラー出力に出す仕様にした，ということなのだろうか。。。
