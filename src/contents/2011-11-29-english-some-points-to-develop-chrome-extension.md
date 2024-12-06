---
layout: post
status: publish
published: true
title: Chrome extensionを開発するためのいくつかのポイント
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1193
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1193
date: '2011-11-29 09:31:14 +0900'
date_gmt: '2011-11-29 00:31:14 +0900'
categories:
- Google
---

今日はここでChrome extensionを開発するためのいくつかのポイントを書きたいと思います。

ポップアップウィンドウのリロード

Browser actionを登録すると、extensionはポップアップバルーンを開くためのボタンが表示されます。開発者は、manifest.jsonファイルの中で、ポップアップバルーンの中に描画されるHTMLファイルの名前を指定することができます。

一般的に、いくつかのJavaScriptファイルがポップアップバルーン内で使われるでしょう。HTMLファイルやいくつかのJavaScriptはいつロードされるでしょうか？はい、ポップアップバルーンを開く度です。もちろん、JavaScriptオブジェクトは、それが閉じられるときに破棄されるでしょう。

それ故に、ポップアップバルーンコンテキスト内のいかなる情報は、そのバルーンのライフサイクルを超えて保持されません。もしそのような情報がある場合は、それらをlocalStorageやCookie、またはbackground HTMLコンテキストとして格納すべきです。

ポップアップバルーンのサイズ

僕が自分のextensionを開発したとき、そのポップアップバルーンが正しく描画されないことに遭遇しました。その理由は、そのバルーンのサイズが絶対的に決定されなかったことにあります。僕はそのサイズ（特に高さだと思います）が自動的に計算されると期待していましたが、Chromeはそれを計算できず、そのバルーンは中途半端に開かれ、さらに継続して震えていました。

この状況を避けるために、CSSファイルを使ってポップアップバルーンのサイズを絶対的に指定すべきです。それはbodyの幅や高さかもしれませんし、各要素のそれらかもしれません。

Notificationのクローズ

NotificationはWebkitの機能を使って以下のコードで表示することができます:

```
var notification = webkitNotifications.createNotification(
    "./foobar.png",
    "A title of this notification",
    "Message of this notification"
);
notification.show();
```

加えて、"cancel()"関数を使って、開かれたNotificationを自動的に閉じることができます。

```
...
notification.show();
notification.ondisplay = function() {
    setTimeout(function() {
        notification.cancel();
    }, 5000);
};
```

Notificationを閉じるために、ユーザは何もする必要がありません。

※ 
[吉川さんからのコメント](http://www.facebook.com/yoichiro6642/posts/10150426476104539?notif_t=feed_comment)を受け、修正しました。

テキストのクリップボードへのコピー

クリップボードにテキスト文字列をコピーするために、テキストフィールドとdocument.execCommand()巻数を使うことができます。

最初に、HTML中にテキストフィールドを配置する必要があります：

```

```

次に、クリップボードにコピーしたいテキストをセットし、そしてコマンドを実行します：

```
var text = "foobar";
$("tf").value = text;
$("tf").focus();
$("tf").select();
document.execCommand("copy");
```

重要なポイントとして、このテクニックはbackgroundコンテキストでも使用可能です。

---

これらのポイントは、
[goo.gl URL Shortener extension](https://github.com/yoichiro/url_shortener_extension)内で使われているものです。そして、あなたの開発に役立つと嬉しいです。
