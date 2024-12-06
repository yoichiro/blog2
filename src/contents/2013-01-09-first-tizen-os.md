---
layout: post
status: publish
published: true
title: Tizen OSを動かしてみました
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2030
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2030
date: '2013-01-09 16:55:25 +0900'
date_gmt: '2013-01-09 07:55:25 +0900'
categories:
- Web Technologies
---

ひょんなことから、Tizen OSを動かしてみることになり、せっかくなのでブログに残しておきます。

インストールしたのは、以下のものたち。ホントはUbuntuで最初やったんだけど、グラフィックのチップセットが貧弱なせいかEmulatorが起動せず、それなりのスペック&ドライバがちゃんと入ってるWindows機で試してみました。

* Oracle Java 7 SDK

* [Install Manager(for Windows 32bit) tizen-sdk-2.0-windows32.exe](https://developer.tizen.org/downloads/sdk)

順にインストールしていくと、スタートメニューに「Tizen IDE」があるのがわかりますが、それはガン無視。「Tizen SDK - Emulator Manager」を選択して、エミュレータを作ってみます。x86-standardの"<
>"をクリックして、ほぼデフォ設定でプロファイルを作成。Display Resolutionだけ480x800に落としました。

そして[Launch]ボタンを押すと、Androidの時と同じようにスマフォの画面が出てきます。Androidと違うのは、BIOSのメッセージがしっかりと「スマフォの画面内」に表示されること。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_2-300x240.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_2.png)

ちょっと待つと、言語選択の画面が出てきます。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_1-300x240.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_1.png)

[English]のボタンをクリックすると、他の言語を選べます。日本語も選択可能。まさかの「ひらがな」です。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_3-300x240.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_3.png)

UIが日本語になりました。フォントもそこそこまともっぽい。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_4-300x240.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_4.png)

[スタート]ボタンをクリックすると、日時の設定画面が出てきます。Time zoneのデフォが「ソウル」なのは開発元がそこだからでしょう。東京は一番下の方にあるので、根気よくスクロールします。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_5-300x240.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_5.png)

日時を設定するだけで、セットアップは終了です。簡単ですね！

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_6-300x240.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_6.png)

最初に入ってるアプリは、「インターネット」と「設定」の2つだけ。シンプルです。"インターネット"の文字が画面からはみ出しちゃってます。おちゃめですね！

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_7-300x240.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_7.png)

設定アプリは、こんな感じの設定項目が並んでます。AndroidやiPhoneの設定内容をイメージさせます。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_8-300x240.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_8.png)

インターネットアプリは、つまりWebブラウザです。Naverがデフォあるのも、開発元がそこにあるからですね。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_9-300x240.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_9.png)

キーボードは、英語仕様のものが標準で一つあるのみです。ただ、キーをクリックしても、残念ながら入力されませんでした。設定からUSBキーボードをONにすることもできるっぽいのですが、試してません。FacebookとかTwitterは、ちゃんとレンダリングされます。遅いけど。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_12-300x240.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_12.png)

Emulator上で右クリックすると、コンテキストメニューが出てきます。その中にShellというのがあって、sdbというデバッグ環境を起動できます。というか、sshでログインしてる状態ですね。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_10-300x240.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_10.png)

コンテキストメニューの[Close]で、電源を切ることができます。

[![](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_11-300x240.png)](http://www.eisbahn.jp/yoichiro/images/2013/01/tizen_11.png)

話題のTizen OSを少しでも体感していただけたのであれば嬉しいです。あ、もちろん新しもの好きな血が僕をTizen OSの起動までさせただけの話ですよ！個人的にはTizen OSには懐疑的な印象を持ってます。まぁ、せっかくインストールしたので、簡単なアプリを作ってみようかな。
