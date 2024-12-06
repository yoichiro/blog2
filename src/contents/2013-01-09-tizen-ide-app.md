---
layout: post
status: publish
published: true
title: Tizen IDEでアプリを作って動かしてみた
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2049
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2049
date: '2013-01-09 22:24:02 +0900'
date_gmt: '2013-01-09 13:24:02 +0900'
categories:
- Web Technologies
---

[Tizen OSのEmulatorを先ほどは動かしてみた](http://www.eisbahn.jp/yoichiro/2013/01/first-tizen-os.html)のですが、インストールされたのは実行環境だけではなく、IDEも入っています。Emulatorの起動に引き続いて、Tizen IDEを使って自分で作ったアプリをそのEmulatorで動かしてみました。

やってみた結果の感想は、とにかくIDEがバギーです。落ちる落ちる。「アプリを動かす」というゴールまではたどり着きましたが、IDEとして使えるかというと、全くだめなレベルですね。今後に期待です。

Tizen IDEは、Androidと同じく、Eclipseがベースになっている開発環境です。Tizen一式をインストールしたディレクトリ内にあるideディレクトリの中を見れば、Eclipse一式を見つけることができるでしょう。もちろん、インストーラがスタートメニューにランチャー登録してくれていますので、「Tizen SDK - Tizen IDE」を選んで起動することができます。

スプラッシュウィンドウと、おなじみのワークスペースの選択ダイアログが表示され、いよいよTizen IDEがその姿を現します。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_1-300x225.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_1.png)

Welcomeなんぞ速攻閉じて、さっそく新規プロジェクト作成ウィザードを使います。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_2-300x155.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_2.png)

Tizen Web Projectというのがあります。いいですねー。それを選んで次へ進みます。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_3-300x248.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_3.png)

作りたいアプリ、というか、使いたい技術？ごとにプロジェクトのひな形がいくつか準備されています。ただアプリを実行したいだけなので、Basicを選んでみました。いよいよTizenアプリのコードが手に入ります。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_4-300x248.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_4.png)

Tizenさまはお黙りになってしまいました。厳しいです。「そう簡単にコードが手に入るなんて思うなよっ！」って言われている気分です。

これは実は問題解決ができます。Tizen IDE、というか、Eclipseの実行時のJavaVMのメモリ不足が原因です。Android SDKやってる人にとっては、取るに足りない話ですね！Tizen SDKのideディレクトリにあるeclipse.iniファイルをテキストエディタで開いて、mxとかmsとかを大きな値(1024とか)に指定しなおして大きくメモリが使えるようにしてあげます。一旦IDEを終わらせて、eclipse.iniを書き換えて、再度IDEを起動すると、起動スピードも上がっていることが体感できるでしょう。もう一回プロジェクト生成してあげると、今度はうまくいくと思います。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_5-300x225.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_5.png)

各ディレクトリの内容を展開してみると、以下のような感じです。HTMLとかJavaScriptとかCSSとかでコードが構成されているのがわかります。Web技術ですね！

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_6-300x225.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_6.png)

この中に、config.xmlというファイルがあります。これが所謂マニフェストファイルってやつです。このアプリのいろんな設定事項が書かれるファイルです。config.xmlファイルを開くと、専用のエディタで開かれます。いっぱいタブがあって、分野ごとに異なるUIで設定値を入力あるいは選択できます。便利そうですね！

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_7-300x225.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_7.png)

Overviewは、説明しなくても何となくわかる項目ばかりです。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_8-300x225.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_8.png)

Widgetは、正直全くわかりません。ここでわかることは、タブを変更するたびにエラーダイアログが開く、ということです。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_9-300x225.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_9.png)

Featuresは、なんだか殺風景なUIですが、Addボタンを押すと、きっと非常に重要な設定なんだな、ということがわかります。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_10-300x245.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_10.png)

おそらく、このアプリから使いたいTizen OSの各機能を選ぶんだと思います。選んだ機能は、きっとJavaScriptから利用可能になるって感じだと思われます。勝手な想像だけど。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_11-300x225.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_11.png)

Access、この割り切った感じのワーディングが好きです。ここでは、このアプリから外部に通信したいときの通信先を指定することになります。たぶん。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_12-300x225.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_12.png)

国際化のためのメッセージリソースをここで編集できるんだと思います。メッセージリソースの編集エディタって、どんなIDEでも代わり映えしないですよね。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_13-300x225.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_13.png)

「Weight APIを通じて取得される名前と値の組です」って言われても・・・。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_14-300x225.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_14.png)

これは何となく重要そうな設定に見えますね。Tizenアプリを特定するためのIDとか、要求するTizen OSの最低バージョンとか、向きとか、スマートフォンアプリっぽい設定が並んでます。が、そろそろconfig.xmlファイル、飽きてきました。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_15-300x225.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_15.png)

良かった、最後のタブは、config.xmlファイルの内容そのものを編集できるタブでした。これだけUIがいっぱいあるのに、記述量はわずかですね。昔「設定より規約」って言葉があったなぁ、と思い出しました。

拡張子が「wgt」っていうのがあって、それを開いたら、以下のようなエディタの表示になりました。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_16-300x225.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_16.png)

このアプリが最終的にパッケージングされたときの内容、でしょうか？わかりませんがそんな予感がします。ちなみに、これを強引にText editorで開いてみたら、思いっきりバイナリでした。

次に、index.htmlファイルを開いてみます。普通にダブルクリックしてしまった僕は本当に浅はかでした。延々とエラーダイアログが、閉じても閉じても表示されます。もうどうしようもありません。「きっと僕の忍耐力、我慢力を試されているんだ！負けないぞ！」としばらくOKボタンを連射(5分はやってたと思います)するも、僕の負けです。タスクマネージャからKILLしてやりました。エンジニアなめんな。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_17-300x240.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_17.png)

気を取り直して、Text editorで開きます。中身は、何てことはない、普通のHTMLです。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_18-300x225.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_18.png)

次にmain.jsファイルです。このファイルを普通に開くと、おそらくTizen IDE専用エディタで開かれています。その結果、カーソル移動するだけで、高確率でエラーダイアログに遭遇します。内容は、これも至って普通のJavaScriptコードです。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_19-300x225.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_19.png)

style.cssファイルなんぞ、ホントにひどかったです。こんな体験は二度とごめんです。開くたびに、IDEがすっといなくなります。再現率100%です。僕らの味方、心の友、Text editorで開くと、これもまぁ普通のCSSです。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_20-300x225.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_20.png)

さて、いよいよアプリを実行してみましょう。プロジェクトで右クリックして、Run AsでTizen Web Applicationメニューを選択してみます。「きっとどのEmulatorを使うか聞かれるぞ！わくわく」と思うも、すぐにそれは裏切られます。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_21-300x89.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_21.png)

「ターゲットに接続されていない」、そう言われています。どう接続するんでしょう？途方に暮れます。

ふとIDEを見てみると、Connection ExplorerっていうViewがあるのを見つけました。そこに一つだけEnableなボタンがあります。押してみると、前のエントリで使ったEmulator Managerが起動しました。そこから、すでに作ってあるEmulatorをLaunchします。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_22-300x240.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_22.png)

起動を待っていると、あるタイミングでConnection ExplorerにそのEmulatorが自動的にリストアップされます。つまり、これで接続されたということになります。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_23-300x240.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_23.png)

これで準備ができました。再度アプリを実行してみましょう。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_24-300x240.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_24.png)

アプリの起動プロセスが始まります。時間がかかるんですが、じっと待ちます。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_25-300x240.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_25.png)

Emulatorのウィンドウを手前にして待っていると、ホーム画面（って呼ぶのかな？）にアイコンが追加されるのがわかります。アプリの登録が成功した証拠です！

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_26-300x240.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_26.png)

そのまま待っていれば、アプリの画面に自動的に切り替わります。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_27-300x240.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_27.png)

最初のTizenアプリが目の前で動き出しました。やったぁ！しかし、重い・・・。この画面になるのに何分かかったことか。。。このアプリは、Clockボタンを押すと、ボタンが消えて、その場所に時計が表示されます。これも、表示が切り替わるまで、ぐっと待ちます。まったくもぉ。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_28-172x300.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_ide_28.png)

IDEとEmulatorの連携は、すでに問題なくできていそうです。デバッガがいけるかどうかは、試してません。IDEの安定度が全くもってまだまだなのと、Emulatorの動作速度もすっごいもっさりしてるので、これから改善されていくことを期待します。

でも、そもそも、Tizenどうなんだろうなぁ。
