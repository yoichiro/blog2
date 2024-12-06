---
layout: post
status: publish
published: true
title: "省電力を抑制するchrome.power APIが利用可能になりました"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2421
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2421
date: '2013-06-19 10:11:25 +0900'
date_gmt: '2013-06-19 01:11:25 +0900'
categories:
- Chrome extension
---

「操作はしていないけれど、画面を表示しっぱなしにしておきたい」という場面があります。例えば、プレゼンテーションの真っ最中とか、電子書籍を読んでいる時などです。そのような種類のアプリや拡張機能をChrome向けに作った際に、
[chrome.power](https://developer.chrome.com/extensions/power.html) APIを使うことで、一時的に省電力に入ることを抑制することが可能です。

Chrome 27から、アプリと拡張機能の両方でchrome.power APIを使うことができます。そのAPI Referenceを和訳してみましたので、下に掲載します。

ただし、あくまでこのAPIは「一時的に」使うべきです。省電力モードへの移行抑止は、最低限にとどめましょう。何のための省電力なのか、わからなくなってしまいますので。。。

****


# chrome.power

システムの電源管理の関与を一時的に停止するために、chrome.powerモジュールを使います。Chrome 27から利用可能です。このモジュールを利用する際には、"power"パーミッションが必要です。

# 使い方

通常、ユーザがアクティブでないときに、オペレーティングシステムは画面を暗くし、最終的にはシステムをサスペンドします。パワーAPIを使うことで、アプリや拡張機能はシステムが生きている状態を保つことができます。

このAPIを使う際に、あなたは電源管理を停止させることに関するLevelを指定することができます。"system"レベルは、システムをアクティブに保ちますが、画面を暗くすることや消去することを許可します。例えば、コミュニケーションアプリは、画面がオフの間、メッセージを受信することを継続することが可能です。"display"レベルは、画面とシステムがアクティブになっていることを保ちます。例えば、電子書籍やプレゼンテーションアプリであれば、ユーザが読んでいる間、画面やシステムをアクティブに保つことができます。

ユーザが1つ以上のそれぞれのパワーレベルを持つアクティブなアプリや拡張機能を持っているときは、最も高く優先されるレベルが効果を持ちます。"display"は、常に"system"以上に優先されます。例えば、もしアプリAが"system"電源管理を要求し、アプリBが"display"を要求した場合は、"display"を要求するアプリBがアンロードあるいは解除されるまで、"display"が使われます。

# Methods

requestKeepAwake

```
chrome.power.requestKeepAwake(enumerated string ["system""display"] level)
```

電源管理を一時的に停止することを要求します。levelは、電源管理が停止されるべき度合いを説明します。



* level (enumerated string ["system" "display"]): 省電力を抑止する度合いです。

releaseKeepAwake

```
chrome.power.releaseKeepAwake()
```

requestKeeyAwake()を通じて行われた前回の要求を解除します。

# chrome.powerを使った拡張機能の例

[Keep Awake](https://developer.chrome.com/extensions/samples.html#458a30c5ab51842f1fe945f9d0590b46) - システムの電源節約設定を更新します。
