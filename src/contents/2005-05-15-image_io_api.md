---
layout: post
status: publish
published: true
title: Image I/O APIを使ったイメージの取り扱い
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 64
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=64
date: '2005-05-15 01:03:42 +0900'
date_gmt: '2005-05-14 16:03:42 +0900'
categories:
- Java
---

日々進化しているJava。AWT時代は何かと大変だった（というか何もできなかった）画像関連のAPIを久々に使ってみた。いつの間にかImage I/O APIが搭載されていて，画像の読み書きが簡単になっている。

例えば，画像を読み込むには以下の行のみでOKだ。

　　BufferedImage image = ImageIO.read(InputStream);

BufferedImageクラスには，getWidth()やgetHeight()が搭載されていて，幅や高さを簡単に取得できる。AWTでは，MediaTrackerクラスなどを使ってちゃんとイメージの読み込みを自前でやってあげないと幅や高さを取得できなかった。それに比べて，さくっと値を取得できるのは非常に楽である。

しかも驚きなのが，読み込んだ画像を，ある形式を指定して保存することがいとも簡単にできることだ。

　　ImageIO.write(image, "jpeg", OutputStream);

例えばread()で読み込んだGIFファイルは，これでJPEGに変換される。標準でサポートされている形式は，ImageIO.getWriterFormatNames()で取得できるが，JPEGとPNGのみのようだ（Windows-Java2SDK1.4.2_08）。

[「Java Image I/O APIガイド」](http://java.sun.com/j2se/1.4/ja/docs/ja/guide/imageio/spec/imageio_guideTOC.fm.html)を見ると，サムネイルつき画像を扱うことができたり，プラグインの形で扱い可能な画像形式を追加できたりするみたいだ。

今後リッチクライアントが増えてくることは確実だし，特にJavaではGUIを構築できる技術を持っていると重宝される。Image I/O APIは外せないものだろう。
