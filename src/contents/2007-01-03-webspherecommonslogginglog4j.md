---
layout: post
status: publish
published: true
title: WebSphere+commons-logging+log4jは使い物にならない
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 305
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=305
date: '2007-01-03 11:10:15 +0900'
date_gmt: '2007-01-03 02:10:15 +0900'
categories:
- Java
---

IBM WebSphere6.1(WAS)とBEA Weblogic9.2(WL)のどちらを採用するかを検証する機会が昨年あった。政治的な理由でWASに大きく傾きかけていたのだが，致命的な問題がWASに存在することが発見された。

それは，commons-loggingとlog4jの組み合わせをWebアプリケーションで使っていた場合に，WLやTomcatの場合と比べて，出力ログパターンによって処理性能が著しく低下するというもの。具体的には，「%C」「%M」といった，保守や不具合の原因の発見に非常に有効な情報となるパターンを使った場合に，性能劣化が激しく生じる。

例えば，Strutsやcommonsシリーズなどのjakarta系のライブラリに対してDEBUGレベルのログ出力を「%C」や「%M」を含めて行おうとした場合，Webアプリケーションの起動だけでも数十分以上かかってしまう。TRACEレベルなど指定してしまったら，きっと時間単位になってしまうだろう。

これは，以下のサーブレットコードとlog4j.xmlファイルの組み合わせを実行することで，簡単に再現可能だ。

// package，import文は省略
@SuppressWarnings("serial")
public class LoggingServlet extends HttpServlet {
private static Log logger1 = LogFactory.getLog("test1");
private static Log logger2 = LogFactory.getLog("test2");
@Override
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
logger1.debug("*** logger1.debug() call. (1)");
logger1.debug("*** logger1.debug() call. (2)");
logger1.debug("*** logger1.debug() call. (3)");
logger1.debug("*** logger1.debug() call. (4)");
logger1.debug("*** logger1.debug() call. (5)");
logger1.debug("*** logger1.debug() call. (6)");
logger1.debug("*** logger1.debug() call. (7)");
logger1.debug("*** logger1.debug() call. (8)");
logger1.debug("*** logger1.debug() call. (9)");
logger1.debug("*** logger1.debug() call. (10)");
logger1.debug("*** logger1.debug() call. (11)");
logger1.debug("*** logger1.debug() call. (12)");
logger1.debug("*** logger1.debug() call. (13)");
logger1.debug("*** logger1.debug() call. (14)");
logger1.debug("*** logger1.debug() call. (15)");
logger1.debug("*** logger1.debug() call. (16)");
logger1.debug("*** logger1.debug() call. (17)");
logger1.debug("*** logger1.debug() call. (18)");
logger1.debug("*** logger1.debug() call. (19)");
logger1.debug("*** logger1.debug() call. (20)");
logger2.debug("*** logger2.debug() call. (1)");
logger2.debug("*** logger2.debug() call. (2)");
logger2.debug("*** logger2.debug() call. (3)");
logger2.debug("*** logger2.debug() call. (4)");
logger2.debug("*** logger2.debug() call. (5)");
logger2.debug("*** logger2.debug() call. (6)");
logger2.debug("*** logger2.debug() call. (7)");
logger2.debug("*** logger2.debug() call. (8)");
logger2.debug("*** logger2.debug() call. (9)");
logger2.debug("*** logger2.debug() call. (10)");
logger2.debug("*** logger2.debug() call. (11)");
logger2.debug("*** logger2.debug() call. (12)");
logger2.debug("*** logger2.debug() call. (13)");
logger2.debug("*** logger2.debug() call. (14)");
logger2.debug("*** logger2.debug() call. (15)");
logger2.debug("*** logger2.debug() call. (16)");
logger2.debug("*** logger2.debug() call. (17)");
logger2.debug("*** logger2.debug() call. (18)");
logger2.debug("*** logger2.debug() call. (19)");
logger2.debug("*** logger2.debug() call. (20)");
response.setContentType("text/html; charset=Windows-31J");
PrintWriter writer = response.getWriter();
writer.println("OK");
writer.close();
}
}

version="1.0" encoding="UTF-8" ?


コンソール出力















「%C」「%M」を含まないパターン（test1カテゴリ）と，それらを含むパターン（test2カテゴリ）で，それぞれ２０回連続でログ出力を行うコードである。まず，これをWLで実行した場合の結果は，以下のようになる。

233211.320 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test1 | *** logger1.debug() call. (1)
233211.351 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test1 | *** logger1.debug() call. (2)
233211.351 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test1 | *** logger1.debug() call. (3)
233211.351 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test1 | *** logger1.debug() call. (4)
233211.351 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test1 | *** logger1.debug() call. (5)
233211.351 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test1 | *** logger1.debug() call. (6)
233211.351 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test1 | *** logger1.debug() call. (7)
233211.351 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test1 | *** logger1.debug() call. (8)
233211.351 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test1 | *** logger1.debug() call. (9)
233211.351 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test1 | *** logger1.debug() call. (10)
233211.361 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test1 | *** logger1.debug() call. (11)
233211.361 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test1 | *** logger1.debug() call. (12)
233211.361 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test1 | *** logger1.debug() call. (13)
233211.361 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test1 | *** logger1.debug() call. (14)
233211.371 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test1 | *** logger1.debug() call. (15)
233211.371 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test1 | *** logger1.debug() call. (16)
233211.371 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test1 | *** logger1.debug() call. (17)
233211.371 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test1 | *** logger1.debug() call. (18)
233211.371 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test1 | *** logger1.debug() call. (19)
233211.371 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test1 | *** logger1.debug() call. (20)
233211.371 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test2 | LoggingServlet.doGet(42) | *** logger2.debug() call. (1)
233211.401 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test2 | LoggingServlet.doGet(43) | *** logger2.debug() call. (2)
233211.401 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test2 | LoggingServlet.doGet(44) | *** logger2.debug() call. (3)
233211.401 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test2 | LoggingServlet.doGet(45) | *** logger2.debug() call. (4)
233211.411 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test2 | LoggingServlet.doGet(46) | *** logger2.debug() call. (5)
233211.411 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test2 | LoggingServlet.doGet(47) | *** logger2.debug() call. (6)
233211.411 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test2 | LoggingServlet.doGet(48) | *** logger2.debug() call. (7)
233211.411 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test2 | LoggingServlet.doGet(49) | *** logger2.debug() call. (8)
233211.421 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test2 | LoggingServlet.doGet(50) | *** logger2.debug() call. (9)
233211.421 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test2 | LoggingServlet.doGet(51) | *** logger2.debug() call. (10)
233211.421 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test2 | LoggingServlet.doGet(52) | *** logger2.debug() call. (11)
233211.431 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test2 | LoggingServlet.doGet(53) | *** logger2.debug() call. (12)
233211.431 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test2 | LoggingServlet.doGet(54) | *** logger2.debug() call. (13)
233211.431 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test2 | LoggingServlet.doGet(55) | *** logger2.debug() call. (14)
233211.431 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test2 | LoggingServlet.doGet(56) | *** logger2.debug() call. (15)
233211.441 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test2 | LoggingServlet.doGet(57) | *** logger2.debug() call. (16)
233211.441 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test2 | LoggingServlet.doGet(58) | *** logger2.debug() call. (17)
233211.441 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test2 | LoggingServlet.doGet(59) | *** logger2.debug() call. (18)
233211.441 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test2 | LoggingServlet.doGet(60) | *** logger2.debug() call. (19)
233211.441 DEBUG [[ACTIVE] ExecuteThread: '1' for queue: 'weblogic.kernel.Default (self-tuning)'] - test2 | LoggingServlet.doGet(61) | *** logger2.debug() call. (20)

全体でも121msしか処理時間はかかっていない。特に「%C」などが含まれたとしても，問題はない。この結果は，Tomcat5.5で行った場合もほぼ同じである。

次に，WASでの結果を以下に示す。

[06/11/10 23:11:48:874 JST] 00000029 SystemOut     O 231148.864 DEBUG [WebContainer : 2] - test1 | *** logger1.debug() call. (1)
[06/11/10 23:11:48:874 JST] 00000029 SystemOut     O 231148.874 DEBUG [WebContainer : 2] - test1 | *** logger1.debug() call. (2)
[06/11/10 23:11:48:874 JST] 00000029 SystemOut     O 231148.874 DEBUG [WebContainer : 2] - test1 | *** logger1.debug() call. (3)
[06/11/10 23:11:48:874 JST] 00000029 SystemOut     O 231148.874 DEBUG [WebContainer : 2] - test1 | *** logger1.debug() call. (4)
[06/11/10 23:11:48:874 JST] 00000029 SystemOut     O 231148.874 DEBUG [WebContainer : 2] - test1 | *** logger1.debug() call. (5)
[06/11/10 23:11:48:874 JST] 00000029 SystemOut     O 231148.874 DEBUG [WebContainer : 2] - test1 | *** logger1.debug() call. (6)
[06/11/10 23:11:48:874 JST] 00000029 SystemOut     O 231148.874 DEBUG [WebContainer : 2] - test1 | *** logger1.debug() call. (7)
[06/11/10 23:11:48:884 JST] 00000029 SystemOut     O 231148.884 DEBUG [WebContainer : 2] - test1 | *** logger1.debug() call. (8)
[06/11/10 23:11:48:884 JST] 00000029 SystemOut     O 231148.884 DEBUG [WebContainer : 2] - test1 | *** logger1.debug() call. (9)
[06/11/10 23:11:48:884 JST] 00000029 SystemOut     O 231148.884 DEBUG [WebContainer : 2] - test1 | *** logger1.debug() call. (10)
[06/11/10 23:11:48:884 JST] 00000029 SystemOut     O 231148.884 DEBUG [WebContainer : 2] - test1 | *** logger1.debug() call. (11)
[06/11/10 23:11:48:884 JST] 00000029 SystemOut     O 231148.884 DEBUG [WebContainer : 2] - test1 | *** logger1.debug() call. (12)
[06/11/10 23:11:48:884 JST] 00000029 SystemOut     O 231148.884 DEBUG [WebContainer : 2] - test1 | *** logger1.debug() call. (13)
[06/11/10 23:11:48:884 JST] 00000029 SystemOut     O 231148.884 DEBUG [WebContainer : 2] - test1 | *** logger1.debug() call. (14)
[06/11/10 23:11:48:884 JST] 00000029 SystemOut     O 231148.884 DEBUG [WebContainer : 2] - test1 | *** logger1.debug() call. (15)
[06/11/10 23:11:48:884 JST] 00000029 SystemOut     O 231148.884 DEBUG [WebContainer : 2] - test1 | *** logger1.debug() call. (16)
[06/11/10 23:11:48:884 JST] 00000029 SystemOut     O 231148.884 DEBUG [WebContainer : 2] - test1 | *** logger1.debug() call. (17)
[06/11/10 23:11:48:884 JST] 00000029 SystemOut     O 231148.884 DEBUG [WebContainer : 2] - test1 | *** logger1.debug() call. (18)
[06/11/10 23:11:48:894 JST] 00000029 SystemOut     O 231148.884 DEBUG [WebContainer : 2] - test1 | *** logger1.debug() call. (19)
[06/11/10 23:11:48:894 JST] 00000029 SystemOut     O 231148.894 DEBUG [WebContainer : 2] - test1 | *** logger1.debug() call. (20)
[06/11/10 23:11:48:924 JST] 00000029 SystemOut     O 231148.894 DEBUG [WebContainer : 2] - test2 | LoggingServlet.doGet(42) | *** logger2.debug() call. (1)
[06/11/10 23:11:48:954 JST] 00000029 SystemOut     O 231148.924 DEBUG [WebContainer : 2] - test2 | LoggingServlet.doGet(43) | *** logger2.debug() call. (2)
[06/11/10 23:11:49:014 JST] 00000029 SystemOut     O 231148.954 DEBUG [WebContainer : 2] - test2 | LoggingServlet.doGet(44) | *** logger2.debug() call. (3)
[06/11/10 23:11:49:054 JST] 00000029 SystemOut     O 231149.014 DEBUG [WebContainer : 2] - test2 | LoggingServlet.doGet(45) | *** logger2.debug() call. (4)
[06/11/10 23:11:49:074 JST] 00000029 SystemOut     O 231149.054 DEBUG [WebContainer : 2] - test2 | LoggingServlet.doGet(46) | *** logger2.debug() call. (5)
[06/11/10 23:11:49:114 JST] 00000029 SystemOut     O 231149.074 DEBUG [WebContainer : 2] - test2 | LoggingServlet.doGet(47) | *** logger2.debug() call. (6)
[06/11/10 23:11:49:225 JST] 00000029 SystemOut     O 231149.215 DEBUG [WebContainer : 2] - test2 | LoggingServlet.doGet(48) | *** logger2.debug() call. (7)
[06/11/10 23:11:49:245 JST] 00000029 SystemOut     O 231149.225 DEBUG [WebContainer : 2] - test2 | LoggingServlet.doGet(49) | *** logger2.debug() call. (8)
[06/11/10 23:11:49:265 JST] 00000029 SystemOut     O 231149.255 DEBUG [WebContainer : 2] - test2 | LoggingServlet.doGet(50) | *** logger2.debug() call. (9)
[06/11/10 23:11:49:275 JST] 00000029 SystemOut     O 231149.265 DEBUG [WebContainer : 2] - test2 | LoggingServlet.doGet(51) | *** logger2.debug() call. (10)
[06/11/10 23:11:49:295 JST] 00000029 SystemOut     O 231149.275 DEBUG [WebContainer : 2] - test2 | LoggingServlet.doGet(52) | *** logger2.debug() call. (11)
[06/11/10 23:11:49:295 JST] 00000029 SystemOut     O 231149.295 DEBUG [WebContainer : 2] - test2 | LoggingServlet.doGet(53) | *** logger2.debug() call. (12)
[06/11/10 23:11:49:305 JST] 00000029 SystemOut     O 231149.295 DEBUG [WebContainer : 2] - test2 | LoggingServlet.doGet(54) | *** logger2.debug() call. (13)
[06/11/10 23:11:49:315 JST] 00000029 SystemOut     O 231149.305 DEBUG [WebContainer : 2] - test2 | LoggingServlet.doGet(55) | *** logger2.debug() call. (14)
[06/11/10 23:11:49:315 JST] 00000029 SystemOut     O 231149.315 DEBUG [WebContainer : 2] - test2 | LoggingServlet.doGet(56) | *** logger2.debug() call. (15)
[06/11/10 23:11:49:335 JST] 00000029 SystemOut     O 231149.315 DEBUG [WebContainer : 2] - test2 | LoggingServlet.doGet(57) | *** logger2.debug() call. (16)
[06/11/10 23:11:49:345 JST] 00000029 SystemOut     O 231149.335 DEBUG [WebContainer : 2] - test2 | LoggingServlet.doGet(58) | *** logger2.debug() call. (17)
[06/11/10 23:11:49:345 JST] 00000029 SystemOut     O 231149.345 DEBUG [WebContainer : 2] - test2 | LoggingServlet.doGet(59) | *** logger2.debug() call. (18)
[06/11/10 23:11:49:365 JST] 00000029 SystemOut     O 231149.345 DEBUG [WebContainer : 2] - test2 | LoggingServlet.doGet(60) | *** logger2.debug() call. (19)
[06/11/10 23:11:49:375 JST] 00000029 SystemOut     O 231149.365 DEBUG [WebContainer : 2] - test2 | LoggingServlet.doGet(61) | *** logger2.debug() call. (20)

全体では501msと，WLの結果と比べて５倍近い性能劣化が見てとれる。特に，後半のtest2カテゴリでのログ出力に性能劣化が認められるのがわかるだろう。

これだけを見ると，単に５倍程度の劣化だが，ログ出力の箇所は通常ものすごい多くなるだろうし，ただでさえレスポンスタイムの顧客からの要求はシビアなのに，５倍という数字はとても話にならないものである。また，マシン自体の負荷が高まってくると，５倍では済まず，３０倍から５０倍程度の性能劣化が生じたこともある。ひどい結果であると言わざるを得ない。

IBMのこれに関するサポートについても，良いとは言えなかった。

* 「%C」「%M」を使わなければ遅くならないのであれば，使わないでください。（ファーストコンタクト）

* そのような報告は今までにありませんし，検証しても再現させることができませんでした。（ログ内容を送った結果その１）

* それはアプリケーションの問題であり，WASには何ら問題はありません。（ログ内容を送った結果その２）

* サポート範囲外です。購入後に受け付けます。（検証アプリにより性能劣化がIBMで認められた結果）


びっくりである（担当者は，消費者であるこちらに歩み寄る姿勢を最後まで見せなかった）。

もしWASでこのような問題が発生した経験を持っている方や，根本的な解決策をお持ちの方は，ぜひコメントを寄せて欲しい。
