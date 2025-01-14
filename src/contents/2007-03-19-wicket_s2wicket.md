---
layout: post
status: publish
published: true
title: Wicketへの挑戦 〜S2Wicketの次なる野望〜（大げさ）
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 344
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=344
date: '2007-03-19 21:49:43 +0900'
date_gmt: '2007-03-19 12:49:43 +0900'
categories:
- Wicket
---

Wicketは，WebアプリケーションをSwingなどに代表されるGUIアプリケーションと同じように開発することができるようにと考え出されたフレームワークである。機構自体は非常に複雑だが，その複雑さは表には一切出てこない。
コンポーネントベースという点ではJSFと同じだが，JSFは拡張性やVisualな開発ツールを意識するあまり，APIセットがプログラマに全くもって親しくない。そして，結局JSPをデフォルトとして採用しているため，相変わらず開発メンバーのロールをはっきりとすることができなくなっている。
WicketはPOHPを最初から見据えることができ，さらにSwingレベルのコーディングを由としたため，非常にプログラマに優しいAPIセットとなった。それは，Java言語の特性を存分に活用してプログラミングができることを意味する。UIコンポーネントの組み立ては継承を使ってもいいし，委譲を使っても良い。さらにイベントハンドラのコードは，抽象UIコンポーネントクラスを継承してイベントハンドラメソッドを実装する，というスタイルだ。ここでは，匿名クラスや内部クラスを多用することになるだろう。Wicketは，プログラマが楽しいフレームワークであることは，言うまでもないだろう。
Wicketの特徴は，矢野さんによって非常に分かりやすくまとめられている。
「
[Wicketはライトウェイトなフレームワークじゃない。でも気持ちがいい。](http://d.hatena.ne.jp/t_yano/20070116/1168972384)」- 矢野勉のはてな日記

>静的型付けオブジェクト指向言語Javaらしいやり方で、この言語を使いこなせるプログラマたちに本来あるべきオブジェクト指向環境を提供している。だからWicketはJavaプログラマにとって快適で、軽量なんです。

しかし，僕にはそんなWicketが，JSFが極端なように，非常に偏った出来に見える。APIの良い悪いを判断する尺度として，「IDEが作りやすいかどうか」という視点があると思っている。Wicketは，言うなればAWTやSwing，いや，SWTのためのVisualなGUI構築ツール（EclipseのVisualEditorが代表的）と同じくらいの難易度だと思う（ほんのちょっとそれより簡単かもしれないが）。
MicrosoftのVisualBasic（僕が知ってるのはVer.6）は，GUIの構築結果についてformファイルに「宣言的な記述」で作成される。つまりBasicコードではない。宣言的な記述は，Visualな開発ツールを作りやすい。文法的に簡単であり，条件分岐などが含まれず，さらにプログラマが手を入れる領域が限られる，という理由から言えることだ。しかし，AWTやSwing，SWTに対するVisualなGUI構築ツールは，その結果をJavaコードという動的な記述で出力しなければならない。これは，開発ツールが作りやすいとする理由のどれをとっても，正反対である。
開発ツールが作りやすいということは，結果が機械的であるということだ。Visual Basicは「機械的」な部分と「動的」な部分を分けることによって，プログラマに絶妙なバランスを提供した。しかし，AWTやSwing，SWTは「動的」な部分が大部分であり，プログラマが頑張らなければならない領域ばかり，という言い方ができる。そして，Wicketも残念ながら「動的」な部分ばかりなのだ。
常にプログラミングが「楽しい」わけがない。動きのある部分を作ることが楽しいと感じても，やはりGUIの構築をコードで書くのは非常にしんどい。少なくとも僕は，WebPageクラスの中でシコシコとUIコンポーネントを組み立てていくコードを書くのは苦手だ。
Wicketは，「動的だからこそ嬉しい部分」と「動的だからこそウザい部分」が共存している。そして，ウザい部分に何とか宣言的な要素を持ち込むことが出来れば，Wicketはより楽しいフレームワークになると思う。

[S2Wicket](http://s2wicket.sandbox.seasar.org/)は，「S2Containerとの統合」という意味で，最低限の機能は既に完成されたといって良いだろう。次に目指すべきことは，Wicketを使ってアプリケーションを作るときに「ウザい」と感じる部分への解決策を提供したいと思っている。
まずは，WebPageクラスやFormクラスの中で，各種UIコンポーネント（wicket.Componentのサブクラス）のインスタンス生成＆親コンテナへの登録処理を宣言的に書けるようにしてみたい。そこには，アノテーションを適用し，さらに命名規約による暗黙的な動作も持ち込む。UIコンポーネントの役割が入出力される情報のモデルへのマッピングであれば，そのマッピングについても宣言的に書けるようにしてみたい。さらに，FormやAjaxコンポーネントの役割がビジネスロジックの駆動なのであれば，ビジネスロジックの呼び出しさえも宣言的に書けるようにしてみたい。
極限的には，WebPageクラスの中身が，UIコンポーネントとモデルの各種フィールド定義と，各フィールドに付与されたアノテーションのみ，なんて感じになるかな，と。
これには賛否両論あるだろうが，作ってみないと僕自身もその効果はわからない。やってみようと思う（こんな大それたもんじゃないけど・・・）。
