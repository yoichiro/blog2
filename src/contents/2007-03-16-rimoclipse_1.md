---
layout: post
status: publish
published: true
title: Rimoclipseは後始末が必要だった
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 340
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=340
date: '2007-03-16 18:17:19 +0900'
date_gmt: '2007-03-16 09:17:19 +0900'
categories:
- Eclipse
---

さっそくだが，知り合いのブログのエントリより。

「
[Eclipse のプラグインは超簡単にできるよ](http://yoshiori.org/blog/2007/03/eclipse.php)」- Yoshioriの日記

>Eclipseのプラグインは超簡単に作れます！！
いや、マジで。


その通り。特に「画像を表示する」「Webページを表示する」「何か一覧を表示する」ようなViewを作りたいだけだったら，BrowserコンポーネントやCanvasコンポーネント，TableコンポーネントなどのSWTコンポーネントの使い方をAPIドキュメントなどで把握するだけで，あとはウィザードがよろしく各種ファイルを作ってくれる。

さて，上記のエントリ内で，

>たぶん、Rimoclipse なんか自動生成部分抜かせば
書いたコードは２，３行じゃないかなぁ･･･
(と、また適当なこと言ってみる)


と言及されているが，僕も最初にRimoclipseを作ろうと思ったときは「２，３行で終わりそうだな」と思った。しかし，実際には２，３行では済まず，５，６行は書いている（一緒だろ，という突っ込みはもうちょっと待って欲しい）。

RimoclipseのViewクラスからコードを抜粋してみる。

public class RimoView extends ViewPart {
private Browser browser;
@Override
public void createPartControl(Composite parent) {
browser = new Browser(parent, SWT.NONE);
browser.addDisposeListener(new DisposeListener() {
public void widgetDisposed(DisposeEvent e) {
browser.setUrl("about:blank");
browser.setVisible(false);
}
});
browser.setUrl("http://rimo.tv/");
}
・・・
}

見ての通り，Browserコンポーネントを生成して，setUrl()メソッドでrimoのサイトを指定しているだけである。しかし，特にMacOS Xで実行したときに，Viewを閉じてもRimoの音だけがずっと鳴りっぱなしになってしまうという現象に出くわした。Browserコンポーネント自体は，dispose()がちゃんとかかっていることは確認できている。だが，Rimoのサウンドは鳴り止むことがない。

試行錯誤の結果，Browserコンポーネントが破棄されるタイミング，つまりDisposeListenerを登録して，そのコールバックメソッドが呼び出された際に，表示していたRimoのページではなく，空白ページに移動して，さらにBrowserコンポーネントを非表示にして，その後に破棄させるということが必要だった。ま，もしかしたら非表示にする処理は結果的に必要なかったかもしれないが，何はともあれ「Browserコンポーネント貼っちゃえば終わりさっ！」なんて感じの甘さではダメダメだったというわけである。

もしかしたらWindowsで作っていれば，このコードの必要性は気がつかなかったかもしれない。うーん，SWTはネイティブな動作がウリなだけに，こういう落とし穴も多分に潜んでいるということなのだろう。

とはいえ，Eclipseプラグインの開発は知ってしまえば簡単だ。そして知っているのと知らないのでは雲泥の差なので，ぜひチャレンジしてみると視野が広くなるだろう。
