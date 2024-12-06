---
layout: post
status: publish
published: true
title: "自らDoS攻撃を食らった話（onStartupやonInstalledの使い方）"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2168
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2168
date: '2013-02-10 10:08:42 +0900'
date_gmt: '2013-02-10 01:08:42 +0900'
categories:
- Chrome extension
---

自分が作って公開しているChrome拡張機能をEvent page対応したことは
[前のエントリ](http://www.eisbahn.jp/yoichiro/2013/02/chrome-extension-event-page.html)で紹介しました。一般的にはそのエントリの内容で十分であり、Chrome拡張機能の動作自体は問題なかったのですが、思わぬところで落とし穴が一つありました。

それは、Ajax通信によるバックエンドサーバとの通信料の増加、でした。

Image Collector extensionでは、Dropboxなどのクラウドストレージサーバに画像をアップロードする機能を、Chrome拡張機能内ではなく、僕の自宅で動いているサーバ内で行っています。クラウドストレージに画像をアップロードするためにはもちろん認証が必要になりますので、Chrome拡張機能と自宅サーバ間でセッションを確立するようにしています。具体的には、サーバ内でセッションIDを発行し、Chrome拡張機能の起動時にそのセッションIDを受け取ってlocalStorageに入れておきます。Chrome拡張機能からバックエンドサーバに処理を依頼する時には、Ajax通信にそのセッションIDを載せておくことで、どこから(=誰から)のアクセスかを把握します。

このセッションIDの取得処理は、Background pageが読み込まれる際に行うようにしていました。具体的には以下のようなコードです。

```
var Background = function() {
  this.initialize();
};
Background.prototype = {
  initialize: function() {
    ...
    this.establishSession();
  },
  establishSession: function() {
    // バックエンドサーバにセッションIDの
    // 発行をAjax通信で依頼する
  },
  ...
};
var background = new Background();
```

backgroundオブジェクトが生成される度にestablishSession()関数が呼び出され、Ajaxでの通信がバックエンドサーバに対して行われます。

この処理は、manifest.jsonファイル内で "persistent": true 相当の定義だった場合、つまり従来の常駐型Background pageであれば問題はありません。上記のコードが評価されるのは、拡張機能が起動するとき、つまりほとんどの場合Chromeが起動したときの1回だけです。Ajaxでのバックエンドサーバへの通信も、1回のみとなります。

しかし、"persistent": false がmanifest.jsonファイルで指定されていたとき、つまり上記のコードがEvent pageであった場合は、途端に問題となります。上記のコードの評価タイミングはChromeの起動時1回のみではなく、Event pageが持つ処理が呼び出される度に評価されます。Image Collector extensionでは、以下のタイミングが存在していました。

* Chrome起動時

* Page action呼び出し時

* 設定ページ呼び出し時

* 新規にタブが生成された時

* タブの内容が更新された時

* タブが削除された時

* Content Scriptからメッセージが届いた時

* コンテキストメニューがクリックされた時

1回で済んでいたものが、簡単に言うと8倍の確率でAjaxな通信が行われるようになってしまったわけです。セッションID発行のリクエストがChrome拡張機能の新規バージョンの広がりに従って増加していき、自宅サーバで動いているバックエンドサーバのLoad average値は50/1分をずっと超えている状況になりました。CPUを4つ割り当てた自作PC内の仮想VMでしたので、常時12個以上のプロセス待ち状態、これはかなり重いというか、SSHでログインなんてとてもじゃないけどできない状態ですね。

バージョンアップ時のGoogle Alanyticsで見たセッション数は800。世界中からDoS攻撃を食らってる状態を自ら作り出してしまったわけです。

セッションIDの発行は、その拡張機能が動き出した時だけ行えばいいわけです。そのため、backgroundオブジェクトの生成時ではなく、ちゃんとイベントリスナを登録し、そのタイミングでセッションIDを得るように変更をしました。具体的には以下のように修正しています。ちなみに、prototype.jsを使っています。

```
var Background = function() {
  this.initialize();
};
Background.prototype = {
  initialize: function() {
    ...
    this.assignEventHandlers();
  },
  assignEventHandlers: function() {
    ...
    chrome.runtime.onStartup.addListener(function() {
      this.establishSession();
    }.bind(this));
    chrome.runtime.onInstalled.addListener(function() {
      this.establishSession();
    }.bind(this));
  },
  establishSession: function() {
    // バックエンドサーバにセッションIDの
    // 発行をAjax通信で依頼する
  },
  ...
};
var background = new Background();
```

chrome.runtime.onStartupイベントまたはchrome.runtime.onInstalledイベント発生時、つまりChromeの起動時か拡張機能のインストール完了時にestablishSession()関数が呼び出されるようにしています。これにより、セッションID発行のリクエスト数は元に戻り、自宅サーバも守られました。

Event pageに移行するときは、思わぬ通信料の増大を招かないために、何がいつ行われているかをちゃんと把握して修正に取り組みましょう。
