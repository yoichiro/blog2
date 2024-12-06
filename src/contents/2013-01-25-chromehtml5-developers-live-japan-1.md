---
layout: post
status: publish
published: true
title: 'Chrome+HTML5 Developers Live Japan #1で話してきました'
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2117
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2117
date: '2013-01-25 19:21:21 +0900'
date_gmt: '2013-01-25 10:21:21 +0900'
categories:
- Chrome extension
---

昨日1月24日の夜に、
[Chrome+HTML5 Developers Live Japan #1](https://plus.google.com/u/0/events/c69j2rgemti2cqm8vh11sgnu37k)でChrome extensionの話をしてきました。その時の模様は以下で見れます。7分過ぎくらいから音量が大きくなりますので、それまではVolume MAXでお願いします。



使ったスライドは以下です。



内容としては、ちょっと前に書いた「
[僕が考えたChrome拡張機能を作るときのデザインパターン](http://www.eisbahn.jp/yoichiro/2012/11/chrome_extension_basic_structure.html)」の内容をベースに、Chrome拡張機能のHello, worldを作るための方法や、拡張機能でできることを紹介してみました。すでにBackground pageは非永続化させてEvent pageとして作るのが推奨なので、Liveの内容も最新の動向に合わせてあります。

そして訂正があります！間違った説明をしてしまいました。chrome.runtime.getBackgroundPage()関数は、直接Background Pageのコンテキストを返すのではなく、コールバック関数を介した非同期の取得手順を踏まないといけないことを忘れていました。具体的には、以下となります。

```
chrome.runtime.getBackgroundPage(function(bg) {
  ...
});
```

persist: trueのBackground Pageのコンテキスト取得は、chrome.extension.getBackgroundPage()関数で行えるのですが、それと混同してしまいました。ホントにごめんなさい。。。

Chrome 拡張機能はちょっとしたコーディングでホントに面白いことがいろいろ組み込めます。ぜひ開発にチャレンジしてみてください！
