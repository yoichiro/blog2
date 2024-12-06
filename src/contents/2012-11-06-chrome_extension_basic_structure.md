---
layout: post
status: publish
published: true
title: "僕が考えたChrome拡張機能を作るときのデザインパターン"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1821
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1821
date: '2012-11-06 11:44:02 +0900'
date_gmt: '2012-11-06 02:44:02 +0900'
categories:
- Chrome extension
---

最近いくつかのChrome拡張機能を作っていて、すでに数千人のユーザを獲得できているものが出てきてたりします。

* [Image collector extension](https://chrome.google.com/webstore/detail/image-collector-extension/fhffefhdkeibnkdldinbncimlojchnie)

* [goo.gl URL Shortener extension](https://chrome.google.com/webstore/detail/googl-url-shortener-exten/pjnggipjiafeklgjdclhhkeefdebipmm)

* [mixi Check button extension](https://chrome.google.com/webstore/detail/mixi-check-button-extensi/fegaibmngajmjdfcbhjllfbojeghgdic)

* [Semantic inspector](https://chrome.google.com/webstore/detail/semantic-inspector/jobakbebljifplmcapcooffdbdmfdbjh)

これだけ作ってると、何となく自分でのChrome拡張機能を作り出す際のデザインパターン・・・っていう大それたものじゃないけど、ようはテンプレが確立されてきます。全く褒められない書き方をしているかもしれませんが、ここでそのテンプレ達を晒しておこうかな、と思います。

# manifest.json

まずはChrome拡張機能の要であるマニフェストファイルです。これは誰が書いてもさほど変わらない記述内容となるでしょう。

```
{
    "manifest_version": 2,
    "name": "拡張機能の名前",
    "description": "__MSG_extDescription__",
    "version": "バージョン番号(XX.XX.XX形式)",
    "browser_action": {
        "default_icon": "./icon_**.png(**はサイズ)",
        "default_title": "タイトル文字列",
        "default_popup": "./popup.html"
    },
    "permissions": [
        ・・・
    ],
    "optional_permissions": [
        ・・・
    ],
    "content_security_policy": "script-src 'self' https://ssl.google-analytics.com; object-src 'self'",
    "icons": {
        "16": "./icon_16.png",
        "48": "./icon_48.png",
        "128": "./icon_128.png"
    },
    "background": {
        "page": "./background.html"
    },
    "options_page": "options.html",
    "default_locale": "en",
    ・・・
}
```

もちろん必要な宣言は上記に追記していきます。ここでポイントとしているのは以下です。

* バージョン番号は、XX.XX.XXの形式。大きな機能追加があった場合は左、小さな機能追加や変更をした場合は真ん中、ユーザに見えない修正あるいはバグフィックスをした場合は右、のそれぞれの数字を大きくしていく。

* 拡張機能の利用状況を把握するために、必ずGoogle Analyticsを仕込んでおく。そのために、CSPにてGoogle Analyticsの通信先を指定しておく。

* アイコン画像のファイル名は「icon_**.png」に統一。**の部分にサイズを入れて、すぐにわかるようにしておく。

* browser_actionやpage_actionを使う場合のポップアップウィンドウのコンテンツは、「popup.html」というファイル名とする。

* バックグラウンドは必ず作り、「background.html」というファイル名とする。

* 設定ページが必要な場合は、「options.html」という名前のファイルを作成する。

* どんなに小さな拡張機能でも、国際化はやっておく。最低限enとjaはサポートする。

意外とGoogle Analyticsは盲点かなと。最初から仕込んでおくことで、利用ユーザの増加をグラフで把握することができるようになって、毎日「うひひ」と喜ぶことができるようになります。オススメです。

注意: 
[Google Analyticsのポリシー](http://www.google.com/intl/ja/analytics/privacyoverview.html)にて、Google Analyticsを利用する際にはちゃんと「Google Analyticsを使っていることを開示し、トラッキングデータの収集のためにCookieが使われていることも明記する」ことが必要だと書かれています。Chrome WebStoreの拡張機能の説明文や、拡張機能の中の何らかのページにて、Google Analyticsが使われていることを記載し、ユーザにそのことがわかるようにしておきましょう。

# analytics.js

そのGoogle Analyticsのアクセスコードを、一つのjsファイルにまとめておきます。

```
var _gaq = _gaq || [];
_gaq.push(['_setAccount', 'UA-xxxxxx-xx']);
_gaq.push(['_trackPageview']);
(function() {
    var ga = document.createElement('script');
    ga.type = 'text/javascript';
    ga.async = true;
    ga.src = 'https://ssl.google-analytics.com/ga.js';
    var s = document.getElementsByTagName('script')[0];
    s.parentNode.insertBefore(ga, s);
})();
```

これを各htmlファイルで読み込むようにしておくことで、この拡張機能の利用状況をGoogle Analyticsで見れるようになります。たとえば、background.htmlに仕込んでおけば、拡張機能を使っているユーザ数がわかります。さらに、popup.htmlに仕込んでおけば、実際にbrowser_actionやpage_actionを起動したユーザ数がわかるようになります。便利です。

# background.html、background.js

Chrome拡張機能を作る上でポイントとなるのが、バックグラウンドの設置です。ちょっとした拡張機能だと必要がないこともあると思うのですが、僕の場合は必ず作るようにしています。その際、htmlファイルとjsファイルをそれぞれ作っておいて、background.htmlの中でbackground.jsファイルを読み込んで実行するようにしています。

```
<html>
  <head>
    <script type="text/javascript" src="./prototype.js"></script>
    <script type="text/javascript" src="./background.js"></script>
  </head>
  <body>
    <script type="text/javascript" src="./analytics.js"></script>
  </body>
</html>
```

ここでポイントは2つ。

* prototype.jsを使うことに慣れているので、Chrome拡張機能の便利ライブラリとしてそれを含めて使っています。

* bodyタグのすぐ後に、analytics.jsを読み込むコードを仕込んでおいて、Google Analyticsに記録されるようにしています。

次にbackground.jsファイルについてです。まずはコードを紹介しましょう。

```
var Background = Class.create({
    initialize: function() {
        this.assignEventHandlers();
    },
    assignEventHandlers: function() {
        ・・・
    },
    getServerUrl: function() {
        return "http://backend.server.name/";
    },
    load***: function(callbacks) {
        var url = this.getServerUrl() + "ajax/get_***";
        new Ajax.Request(url, {
            method: "get",
            onSuccess: function(req) {
                callbacks.onSuccess(req);
            }.bind(this)
        });
    },
    get***Config: function() {
        var value = localStorage["***"];
        if (value) {
            return value;
        } else {
            return "初期値の値";
        }
    },
    set***Config: function(value) {
        localStorage["***"] = value;
    }
    ・・・
});
var bg = new Background();
```

ここでのポイントは以下です。

* assignEventHandlers()関数内で、例えばタブの切り替えイベントの監視など、Chromeの各種操作イベントに対するイベントハンドラを登録する。

* Ajax処理は「load***」という関数名にして、さらにこのbackground.jsに集約しておく。

* 設定ページで設定されるような、このChrome拡張機能で使われる動的な設定値の格納と取得処理を各関数にして、このbackground.jsに集約しておく。

特に2つ目は重要です。Chrome拡張機能の開発では、Dev toolsを頻繁に使います。ポップアップウィンドウや設定ページ、その他何かページを拡張機能に持たせた際に、その通信内容を見たりエラーの内容を把握したりするために、Dev toolsと行ったり来たりすることになります。特にポップアップウィンドウは開いたり閉じたりするために、ポップアップウィンドウが閉じてしまえば、それに対応したDev toolsのウィンドウも閉じてしまうため、デバッグが非常に面倒です。ポップアップウィンドウ内のHTMLやJavaScriptに対するデバッグであれば仕方ないのですが、せめてAjax通信に関することについては、background.jsに切り出しておいた方が、Dev toolsを開いた状態を維持できるようになるため、開発を楽にすることができます。設定項目についても同様です。

図にすると、以下のような感じですね。

![](http://www.eisbahn.jp/yoichiro/images/2012/11/design1.png)

他のページからは、

>chrome.extension.getBackgroundPage().bg


でBackgroundオブジェクトにアクセスします。

assignEventHandlers()関数内では、コンテンツスクリプトを仕掛ける処理なども、タブ関連のイベント発生に応じてその延長で行うようにしています。僕が作ってきた拡張機能は、コンテンツ内の情報を収集してそれに応じた処理を拡張機能内で行う、といったことをやることが多いので、コンテンツ側から拡張機能へのメッセージ送信＝イベント発生、という解釈です。

また、定期的に実行するようなタイマー処理を仕込むのも、バックグラウンドの仕事として、assignEventHandlers()関数内で登録＆起動をしています。これについては、今後は
[chrome.alarms](http://developer.chrome.com/extensions/alarms.html)を使うようになっていくため、setTimeout()関数からchrome.alarmsに置き換えていく必要があります。

# options.html, options.js

次に設定ページを作るためのhtmlファイルおよびjsファイルです。まずはhtmlファイルから紹介します。

```
<!DOCTYPE html>
<html>
  <head>
    <title>Chrome拡張機能の名称</title>
    <script type="text/javascript" src="./prototype.js"></script>
    <script type="text/javascript" src="./options.js"></script>
  </head>
  <body>
    <script type="text/javascript" src="./analytics.js"></script>
<div>
      <img src="./icon128.png" />
<div>Chrome拡張機能の名称</div>
    </div>
<div><span id="opt***"></span></div>
<table>
<tr>
<td>
          <input type="checkbox" id="***" />
        </td>
<td>
          <span id="opt***"></span>
        </td>
      </tr>
    </table>
  </body>
</html>
```

ここでのポイントは、それほど多くありませんが非常に重要です。

* analytics.jsをbodyタグの直下で読み込むようにして、Google Analyticsで計測できるようにしておく。

* 表示文字列は、このoptions.htmlには一切記載しない。その代わりにid属性はしっかり付けておく。

特に2番目のポリシーは重要で、これによって国際化を行えるようにしてあります。これは、options.jsファイルを見ていくことでわかると思います。

```
(function() {
  var Options = Class.create({
    initialize: function() {
      this.bg = chrome.extension.getBackgroundPage().bg;
      window.addEventListener("load", function(evt) {
        this.start();
      }.bind(this));
    },
    start: function() {
      this.assignMessages();
      this.assignEventHandlers();
      this.restoreConfigurations();
      ・・・
    },
    assignMessages: function() {
      var hash = {
        "opt***": "opt***",
        ・・・
      };
      for (var key in hash) {
        $(key).innerHTML = chrome.i18n.getMessage(hash[key]);
      }
    },
    assignEventHandlers: function() {
      $("***").onclick = this.onClick***.bind(this);
    },
    restoreConfigurations: function() {
      $("***").value = this.bg.get***Config();
    },
    onClick***: function(evt) {
      var value = $("***").value;
      this.bg.set***Config(value);
    },
    ・・・
  });
  new Options();
})();
```

ちょっと長いですが、ポイントは以下となります。

* バックグラウンドにアクセスしやすいように、bg変数を作っておく。

* start()関数がonloadイベント発生時に呼び出されるように登録しておく。

* start()関数内では、各種初期処理を呼び出すようにしておく。

* assignMessages()関数内で、HTML内の各プレースホルダ(id属性を振っておいたタグ)に対して、メッセージリソースから読み込んだi18n文字列(?)をセットしていく。

* assignEventHandlers()関数内で、HTML上のUI項目に対するイベントハンドラを登録する。

* イベントハンドラの関数名は、「on + イベント種別 + UI項目名」を基本とする。

* restoreConfigurations()関数内で、バックグラウンドから現状の設定値を持ってきて、せっせとUIにセットしていく。

* イベントハンドラ関数内で、UIから入力値を取得して、やはりバックグラウンドが提供する設定値格納用関数をコールして保存する。

特に大それたことはやってませんが、上記のポリシーで統一感を持たせて可読性を上げる努力をしています。

# popup.html、popup.js

次にbrowser_actionやpage_actionを作る際に必要となるhtmlファイルおよびjsファイルです。これはほとんど設定ページの時と変わりません。

```
<!DOCTYPE html>
<html>
  <head>
    <script type="text/javascript" src="./prototype.js"></script>
    <script type="text/javascript" src="./popup.js"></script>
  </head>
  <body>
    <script type="text/javascript" src="./analytics.js"></script>
    ・・・
  </body>
<table>
```

特にこれというものはないのですが、ポイントは以下です。

* analytics.jsをbodyタグの直下で読み込むようにして、Google Analyticsで計測できるようにしておく。

* 表示文字列は、このpopup.htmlには一切記載しない。その代わりにid属性はしっかり付けておく。

* DOCTYPE宣言は必ず付けておく。ポップアップの大きさなどのCSS指定の解釈やtableタグを使った表の動的生成などによるサイズ変更の挙動に影響するので。

次にJavaScriptコードです。

```
(function() {
  var Popup = Class.create({
    initialize: function() {
      this.bg = chrome.extension.getBackgroundPage().bg;
        window.addEventListener("load", function(evt) {
          this.start();
      }.bind(this));
    },
    start: function() {
      this.assignMessages();
      this.assignEventHandlers();
    },
    assignMessages: function() {
      var hash = {
        "popup***": "popup***",
        ・・・
      };
      for (var key in hash) {
        $(key).innerHTML = chrome.i18n.getMessage(hash[key]);
      }
    },
    assignEventHandlers: function() {
      $("***").onclick = this.onClick***.bind(this);
    },
    onClick***: function(evt) {
      // 設定値を取得
      var ***Config = this.bg.get***Config();
      // Ajax通信
      this.bg.load***({
        onSuccess: function(res) {
          ・・・
        }.bind(this)
      });
      ・・・
    },
    ・・・
  });
  new Popup();
})();
```

ここでのポイントは、設定ページの時とほとんど一緒です。

* バックグラウンドにアクセスしやすいように、bg変数を作っておく。

* start()関数がonloadイベント発生時に呼び出されるように登録しておく。

* start()関数内では、各種初期処理を呼び出すようにしておく。

* assignMessages()関数内で、HTML内の各プレースホルダ(id属性を振っておいたタグ)に対して、メッセージリソースから読み込んだi18n文字列(?)をセットしていく。

* assignEventHandlers()関数内で、HTML上のUI項目に対するイベントハンドラを登録する。

* イベントハンドラの関数名は、「on + イベント種別 + UI項目名」を基本とする。

* 設定値の取得や、Ajax通信は、全てバックグラウンドが提供する機能を利用する。

# その他

あとは各種画像ファイルやcssファイルを作ってパッケージに入れることになります。上記で紹介したhtmlファイルではclass属性などによる指定を一切省略していますが、本来であればふんだんに書かれることになるはずです。

以上、今まで僕が作ってきたChrome拡張機能におけるテンプレ的なものを紹介してみました。これらを参考に、ぜひChrome拡張機能の開発をやってみてください。
