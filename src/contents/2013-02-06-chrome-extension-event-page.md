---
layout: post
status: publish
published: true
title: Chrome拡張機能におけるエコ対策（Event pageへの移行方法）
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2147
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2147
date: '2013-02-06 22:30:41 +0900'
date_gmt: '2013-02-06 13:30:41 +0900'
categories:
- Chrome extension
---

いくつかChrome拡張機能を作ってChromeウェブストアに公開しているのですが、それを最初に公開したときはmanifest.jsonファイルのバージョンがまだ最初の頃でした。現在ではバージョン2が主流であり、そろそろ古いバージョンの拡張機能は撲滅される予定になっています。そのため、開発者はそろそろ急いで新しいバージョンに変更すべく、せっせと移行作業を進めなければなりません。これは何もGoogleの開発者いじめではなく、ちゃんとした理由があります。その理由は、どれも開発者およびユーザへのメリットがあるものばかりです。

このバージョンアップの中で、ユーザにとって大きなメリットとなるものがあります。それは「Event pageへの移行」です。Chromeはメモリを非常に大きく消費することで有名ですが、拡張機能がメモリを大きく使ってしまうことも問題視されていました。拡張機能は「常駐して機能するもの」であり、メモリを圧迫してナンボっていうイメージがあります。これを払拭すべく、開発者は自身が開発した拡張機能のメモリ食い状態を、このEvent pageへの移行という作業によって改善することができます。

# 従来のBackground page

Event pageと聞くと、「何か新しい画面を作れるようになったのかな？」と考えてしまいますが、これはつまりBackground pageです。従来からBackground pageはありました。決して表に出ることのない、裏方作業を黙々と行ってくれる、あのBackground pageです。manifest.jsonファイルの中で、以下のように定義するとBackground pageを作ることができます。

```
{
  ...
  "background": {
    "page": "background.html"
  }
  ...
}
```

これにより、拡張機能が動き出したと同時にbackground.htmlファイルも読み込まれ、このHTMLが持つJavaScriptのコンテキストが生成されて「常駐」します。例えて言うなれば、見えないタブにてbackground.htmlが常に表示された状態、と想像すると良いでしょう。他のページ、例えばBrowser actionで表示されるポップアップウィンドウなどから、以下のようにしてそのBackground pageのコンテキストを得ることが可能です。具体的には、Background pageのwindowオブジェクトが得られるということです。

```
var background = chrome.extension.getBackgroundPage();
```

Background pageとしてbackground.htmlファイルだけでなく、background.jsファイルもセットで作成されるのが一般的です。従来であれば、Background pageは常駐するため、一時的な値を保持する場所として最適でした。例えば、以下のようなbackground.jsファイルの内容があったとします。

```
var Background = function() {
  this.token = null;
};
Background.prototype = {
  getToken: function() {
    return this.token;
  },
  setToken: function(newToken) {
    this.token = newToken;
  }
};
var background = new Background();
```

backgroundオブジェクトはtoken属性値を持ち、setter/getter関数によって値の出し入れができるようになっています。従来のBackground pageでは、これを例えばBrowser actionのポップアップウィンドウから以下のように参照することができました。

```
var backgroundPage = chrome.extension.getBackgroundPage();
var background = backgroundPage.background;
background.setToken("foobar123");
var token = background.getToken(); // "foobar123"
```

「使いたいときにあなたのすぐそばにいる」、Background pageはそんな存在でした。

# 新しいpersistentモード

上記の例を見れば、Background pageはとても便利に感じると思います。しかし、この「常駐する」という特性が、Chrome全体のメモリ富豪状態を加速させる要因でした。

最近ではBackground pageを持っていない拡張機能を探すことのほうが困難なほど、ほとんどの拡張機能がBackground pageを持っています。そのため、インストールした拡張機能の数だけ、JavaScriptのコンテキストをドカッと確保されていたわけです。しかも、まだ全力で仕事をしてくれているのであれば許せますが、拡張機能が実力を発揮するのなんて、Chromeの中でほんの一握りの時間です(タブを開いたとき、ボタンを押したとき、とか)。それだけのために貴重なメモリ資源を確保した上でidleにしておくなんて、もったいないお化けが出ても不思議じゃありません。

そこでBackground pageは、新しいモードを持つことになりました。それが「persistent」です。manifest.jsonファイルのbackground属性にて指定可能なものであり、その値は論理型(true/false)となります。それぞれの意味は、以下となります。

* true: 従来通りの常駐するBackground pageになる

* false: 常駐せず必要なときのみ活性化されるEvent pageになる

つまり「永続化しない」って指定をすることで、Event pageとすることができるようになっています。明示的に指定しない場合は、従来通りとなります。つまり、trueが指定された状態です。Event pageとしたい場合は、以下のようにmanifest.jsonファイルを記載することになります。

```
{
  ...
  "background": {
    "page": "background.html",
    "persistent": false
  }
  ...
}
```

Event pageとすること自体は上記のように非常に簡単です。新規に拡張機能を作る場合は、迷わずEvent pageとするようにしましょう。間違っても開発の手間だけを理由にpersistent=trueなBackground pageを作ってしまう事のないように。

# 既に公開している拡張機能を持っている場合は・・・

迷わずEvent pageへの移行を考え、手を動かし始めるべきです。それが、Chromeを使っていてその拡張機能をインストールしてくれている全ユーザに対してのエコとなります。迷う理由はありません。バージョンアップを行うべきです。

とはいえ、単にmanifest.jsonファイルに追記するだけで簡単に済むかというと、そんなことはありません。いろいろと変更をしていく必要があります。最近個人的に、拡張機能を2つほどEvent page対応しました。その差分は以下となります。

* Image Collector extensionの
[Event page対応の差分](https://github.com/yoichiro/image_collector_extension/commit/a1af0d57a291f2a9037baa4a66431fc9e4850e62)

* goo.gl URL Shortener extensionの
[Event page対応の差分](https://github.com/yoichiro/url_shortener_extension/commit/92f9ae52bb4c113e79579418d03d57024d4433f7)

その中で、Event pageに移行するために必要となる修正作業が見えてきたので、ここでまとめておきたいと思います。もし拡張機能を軽くしたいとか新しく作りたいと思っている開発者の方は、ぜひ参考にしていただければ幸いです。

## 保持している情報の扱いを変更

Event pageに変更すると、background.htmlおよびbackground.jsのコンテキストは、一定時間をもって「破棄」されます。そのため、今までのような情報の保持の仕方はNGになります。先ほどの例をもう一度取り上げるとすると、backgroundオブジェクトに保持されているtokenプロパティ値は、Event pageの破棄タイミングと同時に消去されます。というか、そもそもbackgroundオブジェクトもなくなります。

```
var Background = function() {
  this.token = null;
};
Background.prototype = {
  getToken: function() {
    return this.token;
  },
  setToken: function(newToken) {
    this.token = newToken;
  }
};
var background = new Background();
```

Event pageは、必要になったタイミングで自動的に再生成されます。そのタイミングで、background.htmlおよびbackground.jsのコンテキストが生まれます。上記の例であれば、再生成される度に、backgroundオブジェクトが作られるわけです。this.token値もその度にリセットされてしまうわけです。

Background pageにて保持していた値は、「Browser actionなどのUIなどからいつでも使えるように」一時的に保持していたという理由なはずです。Event pageに変更するとなると、代わりの格納場所を確保しなければならなくなります。候補としては、以下が考えられます。

* Web Storageに入れる(localStorage, sessionStorage)

* IndexedDBに入れる

* chrome.storageを使ってlocalまたはsyncに入れる

* Ajaxで外部サーバに記録する

これらのどれかを駆使して、値の保持をすることになります。例えばlocalStorageを使って以下のように変更します。

```
var Background = function() {
};
Background.prototype = {
  getToken: function() {
    return localStorage["token"];
  },
  setToken: function(newToken) {
    localStorage["token"] = newToken;
  }
};
var background = new Background();
```

これであれば、backgroundオブジェクト(というかJavaScriptのコンテキスト)のライフサイクルに左右されることなく、値の保持と取り出しができるようになります。上記のコードはnewTokenが文字列を持つと仮定していますが、オブジェクトの場合はJSON形式にエンコードやデコードする作業が必要になりますのでご注意を。

おそらくそれほど大きな情報をBackground pageにて保持することはないと思いますが、格納先によって最大容量が異なってくるので、気を付ける必要があります。localStorageであれば5MBの壁があります。

## Background pageへのアクセス方法を変更

Event pageに変更すると、Browser actionなどのJavaScriptコードからBackground pageへアクセスするための手順を変更する必要があります。今までは、以下のようにしてBackground pageのコンテキストを得られたはずです。

```
var backgroundPage = chrome.extension.getBackgroundPage();
var background = backgroundPage.background;
background.setToken("foobar123");
var token = background.getToken(); // "foobar123"
```

すごく手軽だったのですが、Event pageになると以下のように非同期的な取得方法を行う必要があります。getBackgroundPage()関数も、chrome.extensionではなくchrome.runtimeが提供するものを使っていることに注意しましょう。

```
chrome.runtime.getBackgroundPage(function(backgroundPage) {
  var background = backgroundPage.background;
  background.setToken("foobar123");
  var token = background.getToken(); // "foobar123"
});
```

なんで非同期的な感じになってしまったかというと、Event pageになってしまうと、アクセスしたい時にBackground pageがあるとは限らなくなる(というかほとんどの場合ない)わけで、background.htmlおよびbackground.jsの活性化に時間がかかってしまった場合に、getBackgroundPage()関数が長時間ブロックされてしまう可能性が出てきます。これを避けるために、非同期な感じに変更になったって経緯となります。

僕が過去に作ってきた拡張機能では、以下のポップアップウィンドウのJavaSriptコード例のように「最初にBackground pageへのパスを確保しちゃって、いつでも手軽にアクセス！」って感じで作っていました。

```
var Popup = function() {
  this.initialize();
};
Popup.prototype = {
  initialize: function() {
    ...
    this.background = chrome.extension.getBackgroundPage().background;
  },
  ...
  onClickSendButton: function() {
    ...
    this.background.sendRequest(...);
  },
  ...
};
var popup = new Popup();
```

もうこんな横着は許されません。こんなに仲良しだったBackground pageは、呼ばないと振り返ってくれないわけです。上記のコードは、以下のように修正する必要があります。

```
var Popup = function() {
  this.initialize();
};
Popup.prototype = {
  initialize: function() {
    ...
  },
  ...
  onClickSendButton: function() {
    ...
    chrome.runtime.getBackgroundPage(function(backgroundPage) {
      var background = backgroundPage.background;
      background.sendRequest(...);
    });
  },
  ...
};
var popup = new Popup();
```

こんな感じで、都度Background pageのコンテキストを得る必要があります。Background pageにアクセスしている箇所全てに対して、上記の修正を加えていくことが必要です。場合によっては、処理の組み立てをいじる必要が出てくることもあるでしょう。

## setTimeout()、setInterval()関数の変更

Background pageを作る理由の一つとして、一定時間ごとに何か処理を行いたい、ということがあると思います。従来のBackground pageであれば、setTimeout()関数やsetInterval()関数を使って実現してきたことですが、Event pageになってしまうと、これらの関数が所属するwindowオブジェクトが一定時間で削除されてしまうという状況になります。このため、以下のいずれかの挙動となることが考えられます。どっちになるかは、ドキュメント上では言及されていません。

* windowオブジェクトが破棄され、各関数の動作がキャンセルされてしまう。

* windowオブジェクトが破棄されなくなる。←Event pageにならない！

Event pageにて一定時間ごとに何らかの処理を行う際には、setTimeout()やsetInterval()関数は使用できないと考えるべきです。その代わりに、chrome.alarmsという機構が提供されましたので、そっちに置き換えます。

例えば、以下のようなコードをbackground.jsファイルに記載していたとします(一部jQuery使ってます)。

```
var Background = function() {
  this.initialize();
};
Background.prototype = {
  ...
  updateSomething: function() {
    ...
    if (this.isContinueTimer()) {
      setTimeout($.proxy(function() {
        this.updateSomething();
      }, this), 5000);
    }
  },
  ...
};
```

isContinueTimer()関数がfalseを返さない限り、updateSomething()関数が5秒ごとに繰り返し呼び出されるコードです。これをchrome.alarmsを使って書き換えます。

```
var Background = function() {
  this.initialize();
};
Background.prototype = {
  initialize: function() {
    ...
    this.assignEventHandlers();
    this.updateSomething();
  },
  ...
  assignEventHandlers: function() {
    ...
    chrome.alarms.onAlarm.addListener($.proxy(function(alarm) {
      this.onAlarmReceived(alarm);
    }, this));
  },
  onAlarmReceived: function(alarm) {
    if (alarm.name == "alarm_update_something") {
      this.updateSomething();
    } else if (alarm.name == "...") {
      ...
    }
  },
  updateSomething: function() {
    ...
    if (this.isContinueTimer()) {
      chrome.alarms.create("alarm_update_something", {
        delayInMinutes: 5
      });
    }
  }
};
```

かなり長くなってしまいましたが、ポイントは以下となります。

* chrome.alarmsでは、予めイベントハンドラをonAlarm.addListener()関数を使ってリスナー登録しておく必要がある。

* chrome.alarmsで登録できるのは「処理」ではなく「名前」。一定時間ごとに登録された名前でアラームが来るだけで、やりたい処理をキックするのは開発者の責務。

* イベントハンドラの引数で渡ってくる"alarm.name"が、登録した名前を示す。

* アラームの登録はcreate()関数を使って行う。第一引数に名前を指定する。

* delayInMinutes値を指定すれば「○○分後 = setTimeout()」、periodInMinutes値を指定すれば「○○分ごと = setInterval()」にアラームイベントが来る。

そしてもっと大事なポイントがあります。それは「アラームイベントを起こせる間隔は5分以上」ということです。残念ながら
[リファレンス](http://developer.chrome.com/extensions/alarms.html)にてこのことは言及されていません。しかし、delayInMinutesやperiodInMinutesに1を指定しても、実際にはエラーとなって登録に失敗します。setTimeout()やsetInterval()関数がミリ秒の指定を許容していたことから比べると、大きな制限となります。

「そんな頻繁に処理を呼び出すなよ、エコじゃないだろおぃ」というGoogleからのメッセージ(僕の勝手な想像です)だと思って、受け入れましょう。僕も最初気がつかずに、リリースしたあと修正を行っています(その時の差分が
[これ](https://github.com/yoichiro/url_shortener_extension/commit/0b923c8e53cb5ed52d6255d432cfc1baa7c39522))。

## コンテキストメニューのイベントハンドラの変更

Event pageになってBackground pageが常駐しなくなったことは、コンテキストメニューにも影響を及ぼします。具体的には、イベントハンドリングの仕方が変わってきます。

従来のやり方の場合、コンテキストメニューの作成とそのイベントハンドリングは、以下のように記述していたと思います。

```
var Background = function() {
  this.initialize();
};
Background.prototype = {
  initialize: function() {
    ...
    this.createContextMenus();
  },
  createContextMenus: function() {
    chrome.contextMenus.create({
      type: "normal",
      title: chrome.i18n.getMessage("ctxmenuHello"),
      contexts: ["page", "link"],
      onclick: $.proxy(function(info, tab) {
        this.onClickHello(info, tab);
      }, this)
    });
    ...
  },
  onClickHello: function(info, tab) {
    ...
  },
  ...
};
var background = new Background();
```

chrome.contextMenusのcreate()関数を使って、コンテキストメニューを作成しています。この際、onclickプロパティに関数を渡すことで、それがイベントハンドラとして登録されます。実にシンプルです。

これがEvent pageになると、残念ながらonclickプロパティが使えなくなります。その代わり、予めコンテキストメニューのクリックイベントをハンドリングするためのリスナー登録が必要になります。また、イベントのハンドリングのために登録するものは「処理」ではなく「ID」となります。言ってることがわからないと思いますので、実際のコード例を示しましょう。上記の例であれば、以下のように書き換える必要があります。

```
var Background = function() {
  this.initialize();
};
Background.prototype = {
  initialize: function() {
    ...
    this.assignEventHandlers();
    this.createContextMenus();
  },
  assignEventHandlers: function() {
    chrome.contextMenus.onClicked.addListener($.proxy(function(info, tab) {
      this.onClickContextMenu(info, tab);
    }, this));
  },
  createContextMenus: function() {
    chrome.contextMenus.create({
      id: "ctxmenu_hello",
      type: "normal",
      title: chrome.i18n.getMessage("ctxmenuHello"),
      contexts: ["page", "link"]
    });
    ...
  },
  onClickContextMenu: function(info, tab) {
    var id = info.menuItemId;
    if (id == "ctxmenu_hello") {
      this.onClickHello(info, tab);
    } else if (id == "...") {
      ...
    }
  },
  onClickHello: function(info, tab) {
    ...
  },
  ...
};
var background = new Background();
```

ポイントは以下となります。

* コンテキストメニューのイベントハンドリングは、onClicked.addListener()関数にて登録したイベントハンドラで全て行われる。

* create()関数で登録するときに、onclickプロパティは使えない。その代わりに、idプロパティを使って必ず一意のID文字列を付けておく。

* イベントハンドラでは、info.menuItemIdプロパティに押されたコンテキストメニューのID文字列が示されるので、それを元に処理の振り分けを開発者自身で行う。

先ほどのchrome.alarmsの時と一緒で、イベントの発火時に知ることができるのは「どのIDに対してイベントが発火したか」だけです。Background pageが常駐しなくなった代わりに、Chromeは各イベントを処理するために識別子のみを管理するようになった、と考えれば良いでしょう。

## その他

僕が作ってきた拡張機能で修正が必要になった内容は上記となります。これらに加えて、Message Passingをやってる場合は、使わなくなったポートをちゃんと閉じましょう。もし閉じないと、いつまで経ってもBackground pageが捨てられずに常駐状態になってしまいます。

# まとめ

そんなこんなで、Event pageへの移行による拡張機能の軽量化について、そのやり方を紹介しました。手軽さがなくなって面倒なコーディングを強いられることもありますが、その分ユーザのChrome環境が良くなることを考えれば、やる気も出てくることでしょう。拡張機能を作るのは簡単ですが、その分良くない実装をしてしまうことも容易いため、ぜひ本エントリを頭の片隅に置きながら開発をしていただけると嬉しいです。
