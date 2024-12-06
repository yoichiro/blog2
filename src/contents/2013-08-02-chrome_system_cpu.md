---
layout: post
status: publish
published: true
title: Chrome extension向けにCPU情報取得APIが登場しそうです
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2503
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2503
date: '2013-08-02 21:02:05 +0900'
date_gmt: '2013-08-02 12:02:05 +0900'
categories:
- Chrome extension
---

たまに「何か新しいAPIが登場してないかなぁ」とChrome extensionのAPIリファレンスを眺めに行きます。今日も眺めていたら、見慣れないAPIがリストに追加されているのに気がつきました。

その名は、「chrome.system.cpu」です。

[http://developer.chrome.com/extensions/system_cpu.html](http://developer.chrome.com/extensions/system_cpu.html)

これはCPUに関するメタ情報を得るためのAPIです。まだソースコードのtrunkにしかなく、自分でビルドしないとこのAPIを試すことはできません。

本当にシンプルなAPIです。以下にAPIリファレンスの和訳を載せます。

****


# chrome.system.cpu

chrome.system.cpu APIを使うことで、CPUメタ情報を問い合わせることができます。

# Method

getInfo

```
chrome.system.cpu.getInfo(function callback)
```

システムの基本的なCPU情報を問い合わせます。



* callback(function): 問い合わせ結果が渡されます。



このcallbackパラメータには、以下のような関数を指定します。

```
function(object info) {...};
```

* info (object): 

* numOfProcessors (integer): 論理的なプロセッサの数です。

* archName (string): プロセッサのアーキテクチャ名です。

* modelName (string): プロセッサのモデル名です。
