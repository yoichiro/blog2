---
layout: post
status: publish
published: true
title: Safariでdojoを使うと文字化けする件
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 441
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=441
date: '2007-08-24 11:34:50 +0900'
date_gmt: '2007-08-24 02:34:50 +0900'
categories:
- dojo
---

こみゅすけは，dojoというライブラリをかなりの勢いで使用している。dojoは数多くあるJavaScriptライブラリの中で，おそらく最も大きなコードセットだろう。イベント処理やIO関連，各種ウィジェットなど，Ajaxでアプリケーションを作る上で必要となる全ての分野について網羅している。今まではそのコードの大きさのために「遅いっ！！」という悪印象があり，利用を敬遠してきた人も多くいることだろう。しかし，0.4.2以降では，テンプレートの読み込みの削減など，動作の遅さを根本解決するための修正が施されているために，結構軽くなってきたな，と個人的には思っている。次の0.9もβ版がそろそろ登場するはずなので，世間も今後dojoを採用することを積極的に検討しても良いのでは，と真面目に思っている。

しかし，dojoを使う上で，1点だけやばいことがある。それは，

「Safariで文字化けしちゃう」

ということだ。dojoを使う時に，簡単なものであれば，htmlファイル内にスクリプトを含め全て書いてしまえば問題はない。しかし，dojoを使い込んでいくためには，jsファイルをどんどん作っていって，例えばdojoのウィジェットを継承してカスタムコンポーネントを作ったりしていくことが求められる(と書くと難しそうだけど，わかってしまえば決して難しいことではない)。で，そのjsファイルや，ウィジェットが使用するテンプレートのhtmlファイルの中に日本語を直書きしてしまうと，見事にSafariは文字化けしてしまう。以下が文字化けのスナップショットである。見るも無惨だ。

![commusuke-safari1.jpg](http://www.eisbahn.jp/yoichiro/images/commusuke-safari1.jpg)

この文字化け問題，dojoに限った話ではなく，Ajaxの結果をresponseTextで取得する際にUTF-8が正しく処理されずに化ける，という結構有名な話である。下記のURLを叩けばわかるのだが，ググってみるとかなりヒットする。実際にこみゅすけで体感するまで，僕はこの問題を知らなかった。心より恥じる。

[「safari ajax 文字化け」のGoogle検索結果](http://www.google.co.jp/search?source=ig&hl=ja&q=safari+ajax+%E6%96%87%E5%AD%97%E5%8C%96%E3%81%91&btnG=Google+%E6%A4%9C%E7%B4%A2&lr=)

で，解決策としては，以下のページが参考になる。

* [Safari の responseText で UTF-8 コード文字化け回避](http://kawa.at.webry.info/200511/article_9.html)

* [Safari の Ajax 文字化け対処](http://www.koikikukan.com/archives/2007/05/04-012703.php)

さて，自分でAjax回りのコーディングをしているのであれば，上記で示されている対処を施すのは簡単である。しかし，dojoは違う。リッチすぎて，通信回りの処理はdojoの奥底に隠されているのだ。「dojo.require」している箇所や，dojo.widget系のtemplatePathでテンプレートファイルを指定している場合は，その読み込みはdojoが自動的にやってくれるため，上記の対処方法を「dojoをいじることなしに」適用することは残念ながら不可能である(頑張ればできるかもしれないけど，僕はそこまで頑張れる自信がないorz)。

というわけで，dojoのコードを追っていって，文字化け解消のためのコードを埋めてみる(= dojoをいじる)ことにした。修正は2ヶ所。

まず，「dojo.require」で読み込まれるファイルについての文字化け対応から。dojo.jsファイルに書かれているloadUri()関数を修正する。dojo 0.4.3では，dojo.jsファイルの209行目あたり。

・・・
dojo.hostenv.loadUri=function(uri,cb){
if(this.loadedUris[uri]){
return true;
}
var _33=this.getText(uri,null,true);
if(!_33){
return false;
}
/* for Safari { */
if (navigator.appVersion.indexOf("KHTML") > -1) {
var esc = escape(_33);
if (esc.indexOf("%u") < 0 && esc.indexOf("%") > -1) {
_33 = decodeURIComponent(esc);
}
}
/* } for Safari */
this.loadedUris[uri]=true;
if(cb){
_33="("+_33+")";
}
・・・

getText()関数の戻り値は，resnponseTextで得られた文字列が返される。これに対して，「
[Safari の responseText で UTF-8 コード文字化け回避](http://kawa.at.webry.info/200511/article_9.html)」で示されている対処法を適用している。これだけで，普通にdojoを使っているアプリであれば，Safariでも文字化けすることなく動作するようになる。

次に，例えば，

・・・
dojo.widget.defineWidget('commusuke.widget.BlogLink', dojo.widget.HtmlWidget, {
templatePath: dojo.uri.dojoUri('commusuke/widget/templates/BlogLink.html'),
・・・

というように，自作テンプレートファイルを使うようにしていて，上記であればBlogLink.htmlファイル中に日本語を直書きしていた場合，SafariではやはりresponseText問題となってしまう。これの対処として，widget/DomWidget.jsファイルのfillFromTemplateCache()関数を修正する。dojo 0.4.3では，54行目あたり。

if ((!obj.templateNode) && (!avoidCache)) {
obj.templateNode = ts["node"];
}
if ((!obj.templateNode) && (!obj.templateString) && (tpath)) {
/* for Safari { */
var s = dojo.hostenv.getText(tpath);
if (navigator.appVersion.indexOf("KHTML") > -1) {
var esc = escape(s);
if (esc.indexOf("%u") < 0 && esc.indexOf("%") > -1) {
s = decodeURIComponent(esc);
}
}
var tstring = this._sanitizeTemplateString(s);
/* } for Safari */
obj.templateString = tstring;
if (!avoidCache) {
tmplts[wt]["string"] = tstring;
}
}

元々「var tstring = this._sanitizeTemplateString(dojo.hostenv.getText(tpath));」とされている処理を，やはりgetText()関数の戻り値であるresponseTextの文字列について，同様の対処策を施すようにしている。これで，templatePathに自作テンプレートファイルを適用している場合についても，文字化けすることなく動作するようになる。

上記の対処を行うことにより，こみゅすけもSafariで元気に動作するようになった。

![commusuke-safari2.jpg](http://www.eisbahn.jp/yoichiro/images/commusuke-safari2.jpg)

「もうみんなFirefoxでいいじゃん」と，「Firefox以外のWebブラウザは滅びてくれ」と，「Firefoxでなくてもいいから，どれか１つにしようよ」と，冗談抜きでそう思う。orz
