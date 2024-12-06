---
layout: post
title: macOSでのChrome Appsや拡張機能の通知に関する変更
categories:
- Chrome Apps
- Chrome Extensions
---
Chromeウェブストアに開発者登録している方は、おそらく「Changes to Apps and Extensions notifications on macOS」という題名のメールを受け取っていると思います。Chrome 59から、chrome.notifications API経由で送信された通知について、Chromeが独自に提供してきた機構ではなく、macOS側が持つ通知システムにて表示されるように変更される、とのことです。

![](https://developers.google.com/web/updates/images/2017/04/macos-notifications/image00.png)

以下、僕のところに届いたメールを日本語訳してみましたので、掲載しておきます。特にコードの修正は必要ないのですが、自分で作ったChrome Appsや拡張機能にてchrome.notifications APIをお使いの場合は、念のため動作を確認しておくと良いでしょう。

---

Dear Developer,<br>
親愛なる開発者へ,

We're writing to let you know of some upcoming changes to the appearance of notifications shown on macOS by Chrome Apps and Extensions.<br>
Chrome Appsおよび拡張機能によるmacOS上で表示される通知の表示に関するいくつかの変更が行われることをお知らせしたく、本メールを書いています。

Chrome has historically included its own notification system for web and extension developers to send notifications to users. We're now making the step to contribute to a more consistent user experience with most modern operating systems providing rich notification capabilities.<br>
Chromeは歴史的に、ユーザに通知を送るために、ウェブおよび拡張機能の開発者向けにChrome自身が持つ通知システムを含んでいました。私たちは今、モダンなオペレーティングシステムが提供するリッチな通知能力を使って、より首尾一貫したユーザ体験に貢献するための一歩を踏み出します。

Starting in Chrome 59, now available in [Chrome Beta](https://www.google.com/chrome/browser/beta.html?platform=mac&extra=betachannel), notifications sent via [chrome.notifications](https://developer.chrome.com/apps/notifications) will be shown directly by the macOS notification system. This dramatically improves the user experience for the vast majority of notifications, but we recommend reviewing the changes to ensure your notifications are optimized for this new treatment, particularly those using list and progress rich notification templates.<br>
[Chrome Beta](https://www.google.com/chrome/browser/beta.html?platform=mac&extra=betachannel)では現在利用可能ですが、Chrome 59から、[chrome.notifications](https://developer.chrome.com/apps/notifications)を通じて送信された通知は、直接macOSの通知システムにて表示されるようになります。これにより、ほとんどの通知のユーザ体験は大幅に改善されますが、特にリストや進捗といったリッチな通知テンプレートを使っている場合には、この新しい扱いに対して通知が最適されることを保証するために、変更を確認することをお勧めします。

The full changes, including before and after screenshots, are available in the [migration guide](https://developers.google.com/web/updates/2017/04/native-mac-os-notifications).<br>
適用前後のスクリーンショットを含む全ての変更は、[マイグレーションガイド](https://developers.google.com/web/updates/2017/04/native-mac-os-notifications)にて掲載されています。
