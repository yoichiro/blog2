---
layout: post
status: publish
published: true
title: Event pageを複数回取ろうとしたときの罠
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2150
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2150
date: '2013-02-07 22:08:59 +0900'
date_gmt: '2013-02-07 13:08:59 +0900'
categories:
- Chrome extension
---

【追記: 2013/02/08】以下の挙動はChromeのバグっぽいので、
[バグレポートを提出](https://code.google.com/p/chromium/issues/detail?id=175116)しています。

Chrome拡張機能を作っていて、最近はまったことをここで紹介したいと思います。それは、「複数回Event pageを取得しようとしたときの挙動」についてです。常駐しないエコタイプのBackground pageである「Event page」ですが、これをポップアップウィンドウなどから取得する際には、以下のように記述します。非同期な感じです。

```
chrome.runtime.getBackgroundPage(function(bg) {
    // Event pageに何かお願いする
    bg.foobar();
});
```

なんで非同期的な感じになっているかというと、もしgetBackgroundPage()関数を呼び出した際にEvent pageが無効化されていた場合、Event pageの記述内容によっては生成に時間がかかるかもしれず、その場合getBackgroundPage()関数が長時間ブロックされてしまう可能性があり、それを避けるために非同期になっているという経緯があります。Event pageの処理を作るのは各開発者になりますから、どんな処理をかかれてしまうか想定できないわけです。安全策として非同期的な扱いになっていることは、むしろ嬉しいことだと思います。

では、以下のようなコードを実行した場合、どのような挙動となるでしょうか？

```
chrome.runtime.getBackgroundPage(function(backgroundPage) {
    console.log("1: " + backgroundPage);
});
chrome.runtime.getBackgroundPage(function(backgroundPage) {
    console.log("2: " + backgroundPage);
});
chrome.runtime.getBackgroundPage(function(backgroundPage) {
    console.log("3: " + backgroundPage);
});
chrome.runtime.getBackgroundPage(function(backgroundPage) {
    console.log("4: " + backgroundPage);
});
chrome.runtime.getBackgroundPage(function(backgroundPage) {
    console.log("5: " + backgroundPage);
});
chrome.runtime.getBackgroundPage(function(backgroundPage) {
    console.log("6: " + backgroundPage);
});
chrome.runtime.getBackgroundPage(function(backgroundPage) {
    console.log("7: " + backgroundPage);
});
```

別に7回もやらなくていいんですけど、わかりやすいかなと思って試してみました。上記のコードを、Browser actionのポップアップウィンドウで動くJavaScriptとして実行します。もちろん、Event pageが無効な状態を確認したあとに行いました。結果は以下のようになりました。

![](http://www.eisbahn.jp/yoichiro/images/2013/02/extension1.png)

僕の想像では、全てのgetBackgroundPage()関数のコールバックにて、活性化されたEvent pageのコンテキストが渡されると思っていました。しかし、正しくコンテキストが得られたのは最初の1回目のgetBackgroundPage()関数のコールバックのみで、残りは全てnullが渡されてきてしまいました。しかも、順番的に最初にgetBackgroundPage()関数で登録したコールバック関数が、結果として最後に呼び出されています。ちなみに、Event pageが生存している時に上記のコードを実行すると、全てのコールバックで正しくコンテキストが渡ってきます。

たった7回の呼び出しではまだわかりません。100回呼び出してみましょう。以下のコードを実行してみます。

```
for (var i = 0; i < 100; i++) {
    chrome.runtime.getBackgroundPage(function(backgroundPage) {
        console.log(backgroundPage);
    });
}
```

結果はこうなりました。7回呼び出したときと同じ挙動です。

![](http://www.eisbahn.jp/yoichiro/images/2013/02/extension2.png)

もしかしたら、もっと回数を増やしてみると、ループの途中でEvent pageの生成が完了し、正しくコンテキストが渡される回数が1回だけではなくもっと増えるかも知れません。ループの回数を100倍の1万回に変更してみます。

```
for (var i = 0; i < 10000; i++) {
    chrome.runtime.getBackgroundPage(function(backgroundPage) {
        console.log(backgroundPage);
    });
}
```

結果はこうなりました。予想通りです。

![](http://www.eisbahn.jp/yoichiro/images/2013/02/extension3.png)

ここから読み取れる挙動は、以下だと考えられます。

* 1回目のchrome.runtime.getBackgroundPage()関数で指定したコールバック関数については、Event pageの生成が完了したあとにそのコンテキストがコールバック関数に正しく渡される。* * 2回目以降のchrome.runtime.getBackgroundPage()関数の呼び出しについて、もしEvent pageがまだ生成されていない場合は、nullが渡される。もしEvent pageの生成が完了していれば、そのコンテキストが渡される。

この挙動は、残念ながら
[リファレンス](http://developer.chrome.com/extensions/runtime.html#method-getBackgroundPage)には載っていない、Undocumentedな挙動です。chrome.runtime.getBackgroundPage()関数の説明には、

>If the background page is an event page, the system will ensure it is loaded before calling the callback.
（よういちろう訳: もしBackground pageがEvent pageだった場合、システムはコールバックを呼び出す前にそれが読み込まれることを保証するでしょう）


と書かれているのですが、これはあくまでchrome.runtime.getBackgroundPage()関数の1回目の呼び出しに関してのみ、ということになります。

これを知らないと、うまく処理が行えずに不具合を生んでしまいます。例えば、僕はEvent pageに移行する際に、以下のようにしてしまいました。

```
var Popup = function() {
    this.initialize();
};
Popup.prototype = {
    initialize: function() {
        this.init1();
        this.init2();
        this.init3();
    },
    init1: function() {
        chrome.runtime.getBackgroundPage(function(bg) {
            ...
        });
    },
    init2: function() {
        chrome.runtime.getBackgroundPage(function(bg) {
            ...
        });
    },
    init3: function() {
        chrome.runtime.getBackgroundPage(function(bg) {
            ...
        });
    },
    ...
};
```

これだと、init2(), init3()の中でEvent pageがnullになり、初期化処理に失敗してしまいます。これは、以下のようにすべきです。

```
var Popup = function() {
    this.initialize();
};
Popup.prototype = {
    initialize: function() {
        chrome.runtime.getBackgroundPage($.proxy(function(bg) {
            this.init1(bg);
            this.init2(bg);
            this.init3(bg);
        }, this));
    },
    init1: function(bg) {
        ...
    },
    init2: function(bg) {
        ...
    },
    init3: function(bg) {
        ...
    },
    ...
};
```

このように、Event pageを複数回使った一連の処理が必要な場合は、Event pageのコンテキストを最初に取得してしまって、それを使って各処理を行う、という風にコーディングを行いましょう。
