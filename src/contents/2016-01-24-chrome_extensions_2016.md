---
layout: post
title: 2016年度版 僕が考えたChrome拡張機能を作るときのデザインパターン
categories:
- Chrome extension
---
4年近く前の2012年に[僕が考えたChrome拡張機能を作るときのデザインパターン](https://www.eisbahn.jp/yoichiro/2012/11/chrome_extension_basic_structure.html)というエントリを書きました。最近参加したイベントで「よういちろうさんの拡張機能の記事見て作ってみました〜」と声をかけてくれた人がいて嬉しかったのですが、2012年のそのエントリは、すでに内容が古くなってしまっています。最近の状況を踏まえて、内容を新しくした「2016年度版」を書いてみようと思います。

変更しようと思った点は、以下です。

* prototype.jsは使わず、ECMAScript 2015で書く。
* Background Page(常駐型)ではなく、Event Page(非常駐型)にする。
* そもそも最初のコードセットは自分で書かない。

本文やコード的には、2012年度版をコピペしています。

- - -

前にいくつかのChrome拡張機能を作っていて、すでに数千人のユーザを獲得できているものが出てきてたりします。

* [Image collector extension](https://chrome.google.com/webstore/detail/image-collector-extension/fhffefhdkeibnkdldinbncimlojchnie)
* goo.gl URL Shortener extension (公開停止してます)
* mixi Check button extension （オワコン）
* [Semantic inspector](https://chrome.google.com/webstore/detail/semantic-inspector/jobakbebljifplmcapcooffdbdmfdbjh)

これだけ作ってると、何となく自分でのChrome拡張機能を作り出す際のデザインパターン・・・っていう大それたものじゃないけど、ようはテンプレが確立されてきます。全く褒められない書き方をしているかもしれませんが、ここでそのテンプレ達を晒しておこうかな、と思います。

# 最初のコードセットの入手

昔であればゼロから自分でコードを書き始めていたのですが、今では便利なツールが登場しています。決まりきった「みんな同じようなコードでスタートするよね？」というコードセットを入手するためのツールが、[yeoman](http://yeoman.io/)です。

yeomanを利用するには、事前に[node.js](https://nodejs.org/en/)をインストールしておく必要があります。nodejs.orgからパッケージを落としてきてインストールしても良いでしょうし、macであれば[homebrew](http://brew.sh/)を使っても良いでしょう。node.jsを継続的に使っていくのであれば、[nodebrew](https://github.com/hokaccha/nodebrew)や[nvm](https://github.com/creationix/nvm)を使うのも良いことです。

node.jsをインストールした後、yoやその他必要なソフトウェアをインストールします。

{% highlight bash %}
$ npm install -g yo bower gulp
{% endhighlight %}

次に、Chrome拡張機能のコードテンプレートを生成するためのジェネレータをインストールします。

{% highlight bash %}
$ npm install -g generator-chrome-extension
{% endhighlight %}

これでコードを生成するための準備が整いました。ディレクトリを新規に作成して、その中にコードを生成させます。

{% highlight bash %}
$ mkdir my_extension
$ cd my_extension
$ yo chrome-extension
{% endhighlight %}

すると、いろいろ質問されます。これに全てENTERキーのみで答えていきます。

{% highlight text %}
? What would you like to call this extension? my_extension
? How would you like to describe this extension? My Chrome Extension
? Would you like to use UI Action? No
? Would you like more UI Features?
? Would you like to set permissions?
{% endhighlight %}

全ての質問に答えた後、自動的にコードがいくつか作成され、さらに依存ライブラリのダウンロードが自動的に行われます。

{% highlight text %}
   create bower.json
   create package.json
   create gulpfile.babel.js
   create .gitignore
   create .gitattributes
   create .bowerrc
   create .jshintrc
   create .editorconfig
   create app/manifest.json
   create app/scripts.babel/background.js
   create app/scripts.babel/chromereload.js
   create .babelrc
   create app/_locales/en/messages.json
   create app/images/icon-16.png
   create app/images/icon-128.png
   create test/spec/test.js
   create test/index.html


I'm all done. Running npm install & bower install for you to install the required dependencies. If this fails, try running the command yourself.

...
{% endhighlight %}

これでコードの生成は完了です。さっそく作成されたChrome拡張機能をChromeに登録してみたいところですが、一つだけ前準備が必要です。生成されたJavaScriptコードはapp/scripts.babelというディレクトリに入っているのですが、ECMAScript 2015で書かれているために、そのままだとChromeで実行できません（使っているChromeのバージョンでサポートされていない構文を使った時の話）。そのため、コードを修正したら、gulpによってビルド作業を行う必要があります。

一見面倒そうな作業ですが、「何かコードを修正したら、それを検知してビルドを行い、さらにChromeに再読み込みさせる」ということまでやってくれるようになっています。以下のコマンドを実行してみて下さい。

{% highlight bash %}
$ gulp watch
{% endhighlight %}

すると、以下のような出力がされます。

{% highlight text %}
[08:06:32] Requiring external module babel-core/register
[08:06:34] Using gulpfile .../my_extension/gulpfile.babel.js
[08:06:34] Starting 'lint'...
[08:06:35] Starting 'babel'...
[08:06:37] Starting 'html'...
[08:06:38] Finished 'html' after 1.53 s
[08:06:39] Finished 'lint' after 4.51 s
[08:06:39] Finished 'babel' after 3.28 s
[08:06:39] Starting 'watch'...
[08:06:39] Finished 'watch' after 301 ms
[08:07:28] Starting 'lint'...
[08:07:28] Starting 'babel'...
[08:07:29] Finished 'lint' after 108 ms
[08:07:29] Finished 'babel' after 122 ms
{% endhighlight %}

この状態のまま、Chromeにこの拡張機能を登録します。[chrome://extensions](chrome://extensions)ページを表示して、デベロッパーモードにチェックを入れます。そして、「パッケージ化されていない拡張機能を読み込む...」ボタンを押します。

![chrome_extension_1.png]({{ "/images/2016/01/chrome_extension_1.png" | prepend: site.baseurl }})

ディレクトリを聞いてくるので、先ほどコードを生成したディレクトリ内のappディレクトリをしていします。コードを生成したディレクトリではないことに注意しましょう。正しく登録されれば、以下のようにmy_extension拡張機能が表示されたはずです。

![chrome_extension_2.png]({{ "/images/2016/01/chrome_extension_2.png" | prepend: site.baseurl }})

まだ何の機能も持っていないので、Chromeの外観的にも何も変化は起きません。一応動作していることを確認するために、上記の中にある「バックグラウンド ページ」というリンクをクリックして下さい。DevToolsが表示され、以下のように出力されているはずです。

![chrome_extension_3.png]({{ "/images/2016/01/chrome_extension_3.png" | prepend: site.baseurl }})

ここで、自動再読み込み機能が働くかどうか試してみましょう。テキストエディタなどで、app/scripts.babel/background.jsを開きます。以下のようにして、1行追加してみてください。

{% highlight js %}
'use strict';

chrome.runtime.onInstalled.addListener(details => {
  console.log('previousVersion', details.previousVersion);
});

console.log('\'Allo \'Allo! Event Page');
// 以下の行を追加
console.log(chrome.runtime.getManifest().name);
{% endhighlight %}

保存した直後に、gulpを実行したシェルに以下のような表示が追加されていると思います。

{% highlight text %}
[08:09:36] Starting 'lint'...
[08:09:36] Starting 'babel'...
[08:09:36] Finished 'lint' after 76 ms
[08:09:36] Finished 'babel' after 89 ms
[08:09:36] .../my_extension/app/scripts/chromereload.js reloaded.
[08:09:36] .../my_extension/app/scripts/background.js reloaded.
{% endhighlight %}

そして、先ほど開いたDevToolsの表示が変わっていることに気がつくでしょう。

![chrome_extension_4.png]({{ "/images/2016/01/chrome_extension_4.png" | prepend: site.baseurl }})

このように、いちいちChrome側でリロードすることなく、コードを編集したらすぐに動作確認ができるようになっています。これは本当に便利ですので、活用していきましょう。

# manifest.json

Chrome拡張機能で唯一必須となるmanifest.jsonファイルですが、これはyeomanによって自動生成されたものに追記していくことになります。誰が書いてもさほど変わらない記述内容になるはずです。以下のようになるでしょう。

{% highlight json %}
{
  "name": "__MSG_appName__",
  "version": "0.0.1",
  "manifest_version": 2,
  "description": "__MSG_appDescription__",
  "icons": {
    "16": "images/icon-16.png",
    "128": "images/icon-128.png"
  },
  "default_locale": "en",
  "background": {
    "scripts": [
      "scripts/chromereload.js",
      "scripts/analytics.js",
      "scripts/background.js"
    ]
  },
  "permissions": [
    ...
  ],
  "optional_permissions": [
    ...
  ],
  "content_security_policy": "script-src 'self' https://ssl.google-analytics.com; object-src 'self'",
  "options_page": "options.html",
  "browser_action": {
    "default_icon": "images/icon-16.png",
    "default_title": "タイトル文字列",
    "default_popup": "popup.html"
  }
}
{% endhighlight %}

ここでポイントとしているのは以下です。

* バージョン番号は、XX.XX.XXの形式。大きな機能追加があった場合は左、小さな機能追加や変更をした場合は真ん中、ユーザに見えない修正あるいはバグフィックスをした場合は右、のそれぞれの数字を大きくしていく。
* 拡張機能の利用状況を把握するために、必ずGoogle Analyticsを仕込んでおく。そのために、CSPにてGoogle Analyticsの通信先を指定しておく。
* アイコン画像のファイル名は「icon_\**.png」に統一。\**の部分にサイズを入れて、すぐにわかるようにしておく。
* browser_actionやpage_actionを使う場合のポップアップウィンドウのコンテンツは、「popup.html」というファイル名とする。
* バックグラウンドは必ず作り、「background.js」というファイル名とする。
* 設定ページが必要な場合は、「options.html」という名前のファイルを作成する。
* どんなに小さな拡張機能でも、国際化はやっておく。最低限enとjaはサポートする。

意外とGoogle Analyticsは盲点かなと。最初から仕込んでおくことで、利用ユーザの増加をグラフで把握することができるようになって、毎日「うひひ」と喜ぶことができるようになります。オススメです。

注意: Google Analyticsのポリシーにて、Google Analyticsを利用する際にはちゃんと「Google Analyticsを使っていることを開示し、トラッキングデータの収集のためにCookieが使われていることも明記する」ことが必要だと書かれています。Chrome WebStoreの拡張機能の説明文や、拡張機能の中の何らかのページにて、Google Analyticsが使われていることを記載し、ユーザにそのことがわかるようにしておきましょう。

# analytics.js

そのGoogle Analyticsのアクセスコードを、一つのjsファイルにまとめておきます。app/scripts.babel/analytics.jsというファイルを以下の内容で作っておきます。

{% highlight js %}
(function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
(i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
})(window,document,'script','//www.google-analytics.com/analytics.js','ga');

ga('create', 'UA-XXXXX-Y', 'auto');
ga('send', 'pageview');
{% endhighlight %}

これをEvent Pageや各htmlファイルで読み込むようにしておくことで、この拡張機能の利用状況をGoogle Analyticsで見れるようになります。たとえば、Event Pageに仕込んでおけば、拡張機能を使っているユーザ数がわかります。さらに、popup.htmlに仕込んでおけば、実際にbrowser_actionやpage_actionを起動したユーザ数がわかるようになります。便利です。

# background.js

Chrome拡張機能を作る上でポイントとなるのが、Event Pageの設置です。ちょっとした拡張機能だと必要がないこともあると思うのですが、僕の場合は必ず作るようにしています。yeomanで自動生成したコードにも、最初からEvent Pageに必要な記載やファイルが作られています。yeomanで自動生成されたbackground.jsを以下のように書き換えます。

{% highlight js %}
'use strict';

(function() {
    class Background {
        constructor() {
            this.assignEventHandlers();
        }
        assignEventHandlers() {
            ...
        }
        getServerUrl() {
            return 'http://backend.server.name/';
        }
        load***Data(callbacks) {
            let url = this.getServerUrl() + 'ajax/get_***';
            fetch(url).then((response) => {
                callbacks.onSuccess(response);
            });
        }
        get***Config() {
            let value = localStorage['***'];
            if (value) {
                return value;
            } else {
                return '初期値の値';
            }
        }
        set***Config(value) {
            localStorage['***'] = value;
        }
    }

    window.bg = new Background();
})();
{% endhighlight %}

ここでのポイントは以下です。

* assignEventHandlers()関数内で、例えばタブの切り替えイベントの監視など、Chromeの各種操作イベントに対するイベントハンドラを登録する。
* Ajax処理は「load***」という関数名にして、さらにこのbackground.jsに集約しておく。
* 設定ページで設定されるような、このChrome拡張機能で使われる動的な設定値の格納と取得処理を各関数にして、このbackground.jsに集約しておく。

特に2つ目は重要です。Chrome拡張機能の開発では、Dev toolsを頻繁に使います。ポップアップウィンドウや設定ページ、その他何かページを拡張機能に持たせた際に、その通信内容を見たりエラーの内容を把握したりするために、Dev toolsと行ったり来たりすることになります。特にポップアップウィンドウは開いたり閉じたりするために、ポップアップウィンドウが閉じてしまえば、それに対応したDev toolsのウィンドウも閉じてしまうため、デバッグが非常に面倒です。ポップアップウィンドウ内のHTMLやJavaScriptに対するデバッグであれば仕方ないのですが、せめてAjax通信に関することについては、background.jsに切り出しておいた方が、Dev toolsを開いた状態を維持できるようになるため、開発を楽にすることができます。設定項目についても同様です。

図にすると、以下のような感じですね。

![design1.png](http://www.eisbahn.jp/yoichiro/images/2012/11/design1.png)

他のページからは、

{% highlight js %}
chrome.runtime.getBackgroundPage((backgroundPage) => {
  let bg = backgroundPage.bg;
  // Call functions of bg, and do something...
});
{% endhighlight %}

でBackgroundオブジェクトにアクセスします。chrome.runtime.getBackgroundPage()の戻り値で直接得られるのではなく、コールバック関数で得られる点に注目です。

assignEventHandlers()関数内では、コンテンツスクリプトを仕掛ける処理なども、タブ関連のイベント発生に応じてその延長で行うようにしています。僕が作ってきた拡張機能は、コンテンツ内の情報を収集してそれに応じた処理を拡張機能内で行う、といったことをやることが多いので、コンテンツ側から拡張機能へのメッセージ送信＝イベント発生、という解釈です。

また、定期的に実行するようなタイマー処理を仕込むのも、バックグラウンドの仕事として、assignEventHandlers()関数内で登録＆起動をしています。setTimeout()は使えず、ここでは[chrome.alarms](http://developer.chrome.com/extensions/alarms.html) APIを使うことになります。

# options.html, options.js

次に設定ページを作るためのhtmlファイルおよびjsファイルです。まずはhtmlファイルから紹介します。

{% highlight html %}
<!DOCTYPE html>
<html>
  <head>
    <title>Chrome拡張機能の名称</title>
    <script type="text/javascript" src="options.js"></script>
  </head>
  <body>
    <script type="text/javascript" src="analytics.js"></script>
    <div>
      <img src="images/icon-128.png" />
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
{% endhighlight %}

ここでのポイントは、それほど多くありませんが非常に重要です。

* analytics.jsをbodyタグの直下で読み込むようにして、Google Analyticsで計測できるようにしておく。
* 表示文字列は、このoptions.htmlには一切記載しない。その代わりにid属性はしっかり付けておく。

特に2番目のポリシーは重要で、これによって国際化を行えるようにしてあります。これは、options.jsファイルを見ていくことでわかると思います。

{% highlight js %}
'use strict';

(function() {
    class Options {
        constructor() {
            window.addEventListener("load", (evt) => {
                this.start();
            });
        }
        start() {
            this.assignMessages();
            this.assignEventHandlers();
            this.restoreConfigurations();
        }
        assignMessages: function() {
            let hash = {
                "opt***": "opt***",
                ...
            };
            for (var key in hash) {
                document.querySelector("#" + key).innerHTML =
                        chrome.i18n.getMessage(hash[key]);
            }
        }
        assignEventHandlers() {
            document.querySelector("#***").addEventListener("click", (evt) => {
                this.onClick***(evt);
            });
        }
        restoreConfigurations: function() {
            chrome.runtime.getBackgroundPage((backgroundPage) => {
                let bg = backgroundPage.bg;
                document.querySelector("#***").value = bg.get***Config();
            });
        }
        onClick***(evt) {
            chrome.runtime.getBackgroundPage((backgroundPage) => {
                let bg = backgroundPage.bg;
                let value = document.querySelector("#***").value;
                bg.set***Config(value);
            });
        }
    }

    new Options();
})();
{% endhighlight %}

ちょっと長いですが、ポイントは以下となります。

+ start()関数がonloadイベント発生時に呼び出されるように登録しておく。
* start()関数内では、各種初期処理を呼び出すようにしておく。
* assignMessages()関数内で、HTML内の各プレースホルダ(id属性を振っておいたタグ)に対して、メッセージリソースから読み込んだi18n文字列(?)をセットしていく。
* assignEventHandlers()関数内で、HTML上のUI項目に対するイベントハンドラを登録する。
* イベントハンドラの関数名は、「on + イベント種別 + UI項目名」を基本とする。
* restoreConfigurations()関数内で、バックグラウンドから現状の設定値を持ってきて、せっせとUIにセットしていく。
* イベントハンドラ関数内で、UIから入力値を取得して、やはりバックグラウンドが提供する設定値格納用関数をコールして保存する。

特に大それたことはやってませんが、上記のポリシーで統一感を持たせて可読性を上げる努力をしています。

# popup.html, popup.js

次にbrowser_actionやpage_actionを作る際に必要となるhtmlファイルおよびjsファイルです。これはほとんど設定ページの時と変わりません。

{% highlight html %}
<!DOCTYPE html>
<html>
  <head>
    <script type="text/javascript" src="popup.js"></script>
  </head>
  <body>
    <script type="text/javascript" src="analytics.js"></script>
    ・・・
  </body>
</html>
{% endhighlight %}

特にこれというものはないのですが、ポイントは以下です。

* analytics.jsをbodyタグの直下で読み込むようにして、Google Analyticsで計測できるようにしておく。
* 表示文字列は、このpopup.htmlには一切記載しない。その代わりにid属性はしっかり付けておく。
* DOCTYPE宣言は必ず付けておく。ポップアップの大きさなどのCSS指定の解釈やtableタグを使った表の動的生成などによるサイズ変更の挙動に影響するので。

次にJavaScriptコードです。

{% highlight js %}
'use strict';

(function() {
    class Popup {
        constructor() {
            window.addEventListener("load", (evt) => {
                this.start();
            });
        }
        start() {
            this.assignMessages();
            this.assignEventHandlers();
        }
        assignMessages: function() {
            let hash = {
                "popup***": "popup***",
                ...
            };
            for (var key in hash) {
                document.querySelector("#***").innerHTML = chrome.i18n.getMessage(hash[key]);
            }
        }
        assignEventHandlers() {
            document.querySelector("#***").addEventListener("click", (evt) => {
                this.onClick***(evt);
            });
        }
        onClick***(evt) {
            chrome.runtime.getBackgroundPage((backgroundPage) => {
                // 設定値を取得
                let bg = backgroundPage.bg;
                let ***Config = bg.get***Config();
                // Ajax通信
                bg.load***({
                    onSuccess: (response) => {
                        ...
                    }
                });
                ...
            });
        }
    }

    new Popup();
})();
{% endhighlight %}

ここでのポイントは、設定ページの時とほとんど一緒です。

* start()関数がonloadイベント発生時に呼び出されるように登録しておく。
* start()関数内では、各種初期処理を呼び出すようにしておく。
* assignMessages()関数内で、HTML内の各プレースホルダ(id属性を振っておいたタグ)に対して、メッセージリソースから読み込んだi18n文字列(?)をセットしていく。
* assignEventHandlers()関数内で、HTML上のUI項目に対するイベントハンドラを登録する。
* イベントハンドラの関数名は、「on + イベント種別 + UI項目名」を基本とする。
* 設定値の取得や、Ajax通信は、全てバックグラウンドが提供する機能を利用する。

# その他

あとは各種画像ファイルやcssファイルを作ってパッケージに入れることになります。上記で紹介したhtmlファイルではclass属性などによる指定を一切省略していますが、本来であればふんだんに書かれることになるはずです。

以上、今まで僕が作ってきたChrome拡張機能におけるテンプレ的なものを紹介してみました。これらを参考に、ぜひChrome拡張機能の開発をやってみてください。
