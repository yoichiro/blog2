---
layout: post
status: publish
published: true
title: Event pageを複数回取ろうとしたときの罠はバグでした
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2173
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2173
date: '2013-02-13 15:35:16 +0900'
date_gmt: '2013-02-13 06:35:16 +0900'
categories:
- Chrome extension
---

ちょっと前に「
[Event pageを複数回取ろうとしたときの罠](http://www.eisbahn.jp/yoichiro/2013/02/event-page-retrieve-multi-time.html)」というエントリをしました。これは、以下のようなコードを実行した時に、Event pageのwindowオブジェクトを最初の1回しか取得できない、っていう現象を報告したものでした。

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

厳密に言うと「Event pageの生成が完了しないうちにgetBackgroundPage()関数を呼ぶと、コールバック関数にnullが渡される」という現象でした。「これは不具合なんじゃないか」という意見をいくつか頂いたので報告をしたところ、やっぱりバグ認定されました。

[Issue 175116: chrome.runtime.getBackgroundPage returns null if the event page has started but hasn't finished loading](http://code.google.com/p/chromium/issues/detail?id=175116)

すでにコードの修正も済んでいて、コードレビューも通ったみたいです。

[Diff of /trunk/src/chrome/browser/extensions/api/runtime/runtime_api.cc](http://src.chromium.org/viewvc/chrome/trunk/src/chrome/browser/extensions/api/runtime/runtime_api.cc?r1=181868&r2=181867&pathrev=181868)

これで将来のChromeのバージョンでは、getBackgroundPage()関数の呼び出しは特に何も気にすることなく使えるようになるでしょう。良かった良かった。
