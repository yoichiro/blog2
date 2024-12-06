---
layout: post
status: publish
published: true
title: Chrome Apps for mobileの開発方法
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2589
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2589
date: '2014-02-05 20:49:20 +0900'
date_gmt: '2014-02-05 11:49:20 +0900'
categories:
- Chrome extension
---

一部で話題になっていたGithubにある「
[MobileChromeApps / mobile-chrome-apps](https://github.com/MobileChromeApps/mobile-chrome-apps)」ですが、先日遂に
[正式にGoogleからアナウンスがありました](http://blog.chromium.org/2014/01/run-chrome-apps-on-mobile-using-apache.html)。デスクトップPC向けに開発されたChrome Appを元にして、このツールを使ってモバイル向けアプリ(Android/iOS)を自動的に作成することが可能です。HTML5とJavaScript、CSSを使って、Android/iOSアプリを開発することができるだけでなく、その中で各種Chrome APIを使うことができます。Cordovaベースなので、Cordovaそのものが提供する機能も利用することができるでしょう。

今日は、このツールの使い方を説明した文書を日本語訳してみました。以下の手順をやっていくことで、モバイル向けChrome Appを作ることを試すことができます。ぜひ体験してみてください！

# Step 1: 開発ツールのインストール (
[原文](https://github.com/MobileChromeApps/mobile-chrome-apps/blob/master/docs/Installation.md))

Chrome Apps for mobile ツールチェーンは、iOS6以上、Android 4.x以上を対象としています。

全てのプラットフォーム向けの開発依存物

* [Node.js](nodejs.org) version 0.10.0以上(npm同梱)が必要とされます。

* Windows: 
[nodejs.org](nodejs.org)からダウンロード可能な実行可能インストーラを使ってNode.jsをインストールします。

* OS XまたはLinux: 同じく
[nodejs.org](nodejs.org)からダウンロード可能な実行可能インストーラを使うことができます。もしrootアクセスの必要性を回避したい場合は、nvm経由でのインストールがより便利です。以下は例です:

```
curl https://raw.github.com/creationix/nvm/master/install.sh | sh
source ~/.bash_profile || source ~/.profile || source ~/.bashrc
nvm install 0.10
nvm alias default 0.10
```

Androidが対象

Android向けにアプリケーションを開発するときは、以下のものもインストールすることが必要です:

* [Java JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

* [Android SDK](http://developer.android.com/sdk/index.html) version 4.4.2 (またはそれ以上)

* Android ADT BundleがバンドルされてくるAndroid SDKおよびAndroid Developer Toolsをインストールします。

* sdk/toolsおよびsdk/platform-toolsを、
[あなたのPATH](https://www.google.com/search?q=how+to+add+sdktools+to+path)環境変数に追加します。

* OS X: Android SDKと一緒に付いてくるEclipseのバージョンは、JRE 6を必要とします。もしEclipse.appを開いた時にJRE 6のインストールのためのプロンプトが表示されない場合は、Mac App Storeを通じてそれを入手してください。

* Linux: Android SDKは、32bitをサポートするライブラリを必要とします。Ubuntuの場合、"apt-get install ia32-libs"を使ってそれらを入手してください。

* [Apache Ant](http://ant.apache.org/bindownload.cgi)

* apache-ant-x.x.x/binを、
[あなたのPATH](https://www.google.com/search?q=how+to+add+sdktools+to+path)環境変数に追加します。

iOSが対象

iOS開発はOS Xでのみ可能であることに注意してください。加えて、以下のインストールが必要になるでしょう:

* Xcode command line toolsを含む
[Xcode 5](https://developer.apple.com/xcode/)

* [ios-deploy](https://github.com/phonegap/ios-deploy) (iOSデバイスへ配布するために必要になります)

* npm install -g ios-deploy

* [ios-sim](https://github.com/phonegap/ios-sim) (iOSシミュレータに配布するために必要になります)

* npm install -g ios-sim

* 任意: iOS開発者として登録

* これは、実際のデバイスでテストするため、およびAppStoreに提出するために必要です。

* もしiPhone/iPadシミュレータを使う予定の場合は、登録を省略することが可能です。

ccaコマンドラインツールのインストール

npmを通じてccaをインストールします:

```
npm install -g cca
```

後でツールチェーンを新しいリリースに更新する時: npm update -g cca

コマンドラインからこのコマンドを実行することで、全てが正しくインストールされたかどうか確認します:

```
cca checkenv
```

出力されたccaのバージョン番号や、AndroidやiOS SDKのインストールについての確認をすることができるでしょう。

完了しましたか？Step 2: プロジェクトの作成に進みましょう。

# Step 2: プロジェクトの作成 (
[原文](https://github.com/MobileChromeApps/mobile-chrome-apps/blob/master/docs/CreateProject.md))

"YourApp"という名前のディレクトリで空のモバイルChrome Appプロジェクトを作成するには、以下のコマンドを実行します:

```
cca create YourApp
```

もし既にビルドされたChrome Appを持っていて、それをモバイルプラットフォームに移植したい場合は、それに対してsymlinkを作成するために、"--link-to"を使うことができます:

```
cca create YourApp --link-to=path/to/manifest.json
```

そうではなく、もし既に存在するChrome Appファイルをコピーしたい場合は、"--copy-from"フラグを使うことができます:

```
cca create YourApp -copy-from=path/to/manifest.json
```

まだあなた自身のChrome Appを持ってないですか？その場合は、多くの
[モバイルをサポートするChrome Appsのサンプル](https://github.com/GoogleChrome/chrome-app-samples#mobile-support)から1つ選んで試してみてください。

完了しましたか？Step 3: 開発に進みましょう。

# Step 3: 開発 (
[原文](https://github.com/MobileChromeApps/mobile-chrome-apps/blob/master/docs/Develop.md))

あなたは2つの方法でアプリケーションをビルドおよび実行することができます:

* 選択肢 A: コマンドラインから"cca"ツールを使う。

* 選択肢 B: EclipseやXcodeといったIDEを使う。IDEの利用は、あなたのハイブリッドなモバイルアプリケーションの起動、設定変更、デバッグの補助については全体的にオプション(しかししばしば便利)です。

選択肢 A: コマンドラインを使った開発とビルド

ccaを使って作られたプロジェクトフォルダのルートから:

Android

* Androidエミュレータでアプリケーションを実行する: "cca emulate android"

* 注意: これはエミュレータがセットアップされていることを要求します。これをセットアップするために、"android avd"を実行してください。"android"を実行することで、追加のエミュレータイメージをダウンロードしてください。Intelイメージの実行を速くさせるために、
[Intel's HAXM](http://software.intel.com/en-us/articles/intel-hardware-accelerated-execution-manager/)をインストールしましょう。

* 接続されたAndroidデバイスでアプリケーションを実行する: "cca run android"

iOS

* iOSシミュレータでアプリケーションを実行する: "cca emulate ios"

* 接続されたiOSデバイスでアプリケーションを実行する: "cca run ios"

* 注意: これはあなたのデバイスに対して
[Provisioning Profile](http://stackoverflow.com/questions/3362652/what-is-a-provisioning-profile-used-for-when-developing-iphone-applications)がセットアップ済みであることが要求されます。

選択肢 B: IDEを使った開発とビルド

Android

* Eclipseにて、"File"->"Import"を選択する。

* "Android">"Existing Android Code Into Workspace"を選択する。

* "cca"を使って作成したパスからインポートする。

* 2つのプロジェクトがインポートされることを期待するはずです。そのうちの1つは、"*-CordovaLib"という名前です。

* アプリケーションを実行するために、Playボタンをクリックする。

* あなたは、Run Configuration(全てのJavaアプリケーションと同じように)を作成する必要があるでしょう。通常初回は自動的にプロンプトが表示されます。

* 同じように、初回にデバイス/エミュレータを管理する必要があるでしょう。

iOS

* ターミナルウィンドウの中で以下のようにタイピングすることで、Xcodeにてプロジェクトを開く。
    ```
    cd YourApp
    open platforms/ios/*.xcodeproj
    ```

* 正しい対象をビルドしているか確認する。
    左上(RunやStopボタンの側)に、ターゲットプロジェクトやデバイスを選択するためのドロップダウンがあります。YourAppが選択されていて、"CordovaLib"が選択されていないことを保証してください。

* Playボタンをクリックする。

アプリケーションのソースコードが変更されたとき

HTML、CSS、JavaScriptファイルは、あなたのccaプロジェクトフォルダの"www"ディレクトリの中にいます。あなたのファイルの変更が反映されるために、あなたのプロジェクトから"cca prepare"を実行しなければなりません。

デバッグ

通常のCordovaアプリケーションをデバッグするのと同じ方法で、あなたはモバイル向けChrome Appをデバッグすることが可能です。

完了しましたか？次のステップに進みましょう。

# Step 4: 次のステップ (
[原文](https://github.com/MobileChromeApps/mobile-chrome-apps/blob/master/docs/NextSteps.md))

今、あなたは動作するChrome Appを持っていますが、モバイルデバイス向けに体験を改善するための多くの方法があります。

アイコン

モバイルアプリケーションは、デスクトップ向けChrome Appsに比べて、より多くのアイコンの解像度が必要です。

Android向けに、これらのサイズが必要になります:

* 36px, 48px, 78px, 96px

iOS向けには、iOS 6、iOS 7のどちらをサポートするかに依存して、要求されるサイズが異なります。要求されるアイコンの最低限の数は以下となります:

* iOS 6: 57px, 72px, 114px, 144px

* iOS 7: 72px, 120px, 152px

完全なアイコンの一覧は、あなたのmanifest.jsonファイルの中で以下のようになるでしょう:

```
"icons": {
  "16": "assets/icons/icon16.png",
  "36": "assets/icons/icon36.png",
  "48": "assets/icons/icon48.png",
  "57": "assets/icons/icon57.png",
  "72": "assets/icons/icon72.png",
  "78": "assets/icons/icon78.png",
  "96": "assets/icons/icon96.png",
  "114": "assets/icons/icon114.png",
  "120": "assets/icons/icon120.png",
  "128": "assets/icons/icon128.png",
  "144": "assets/icons/icon144.png",
  "152": "assets/icons/icon152.png"
}
[/javascript]

アイコンは、あなたが"cca prepare"を実行する度に毎回、各プラットフォームに適切な場所にコピーされるでしょう。

スプラッシュ画面

iOSアプリケーションは、そのアプリケーションが読み込まれている時に、短くスプラッシュ画面を表示します。デフォルトのCordovaスプラッシュ画面は、"platform/ios/[AppName]/Resources/splash"に含まれています。

そのサイズは以下が必要になります:

* 320px x 480px + 2x

* 768px x 1024px + 2x (iPad ポートレート)

* 1024px x 768px + 2x (iPad ランドスケープ)

* 640px x 1146px

スプラッシュ画面イメージは、現在"cca"によって変更されません。

完了しましたか？Cordovaで開発する際の特別な考慮事項に進みましょう。

# Cordovaを使って開発する際の特別な考慮事項 (
[原文](https://github.com/MobileChromeApps/mobile-chrome-apps/blob/master/docs/CordovaConsiderations.md))

Chrome Appは、モバイルデバイスの中で機能しないかもしれません。モバイル向けに移植する際に、いくつかの共通的な問題があります:

* 小さな画面によるレイアウト問題、特にポートレート方向。

* 対応への提案: より小さな画面にコンテンツを最適化するために、
[CSS media queries](http://www.html5rocks.com/en/mobile/mobifying/#toc-mediaqueries)を使用してください。

* [chrome.app.window](http://developer.chrome.com/apps/app_window.html)を経由してセットされたChrome Appウィンドウサイズは、無視されるでしょう。代わりに、デバイスのネイティブな大きさを使ってください。

* 対応への提案: ハードコーディングされたウィンドウの大きさを削除します。異なるサイズがあることを念頭に置いてアプリケーションをデザインしてください。

* 小さなボタンやリンクは指でタップするのは厳しいでしょう。

* 対応への提案: タッチ対象が少なくとも44x44ポイントになるように調整してください。

* マウスホバーに依存した際の予期せぬ挙動は、タッチ画面では存在しません。

* 対応への提案: ホバーに加えて、タップの場合にドロップダウンやツールチップのようなUI要素をアクティブにしてください。

* 300msのタップの遅延。

* 対応への提案: 
[FastClick by FT Labs](https://github.com/ftlabs/fastclick) JavaScript polyfillを使ってください。

* いくつかの背景事情は、
[HTML5Rocks記事](http://updates.html5rocks.com/2013/12/300ms-tap-delay-gone-away)を読んでください。

サポートされるChrome API

私たちは、Chrome Apps for Mobileに対して、コアChrome APIの多くを利用可能にします。現在は以下が含まれます:

* [identitiy](http://developer.chrome.com/apps/identity.html) - OAuth2を使ったユーザサインイン

* [payments](https://github.com/MobileChromeApps/chrome-cordova/blob/master/plugins/google.payments/README.md) - あなたのモバイルアプリ内での仮想グッズ販売

* [pushMessaging](http://developer.chrome.com/apps/pushMessaging.html) - あなたのサーバからアプリケーションへのメッセージプッシュ

* [sockets](http://developer.chrome.com/apps/sockets.html) - TCPおよびUDPを使ったネットワーク越しの送受信

* [notifications](http://developer.chrome.com/apps/notifications.html) (Androidのみ) - あなたのモバイルアプリからのリッチ通知送信

* [storage](http://developer.chrome.com/apps/storage.html) - ローカルでのKey-Valueの格納と取得

* [syncFileSystem](http://developer.chrome.com/apps/syncFileSystem.html) - バックエンドがGoogle Driveによるファイルの格納と取得

* [alarms](http://developer.chrome.com/apps/alarms.html) - 定期的なタスクの実行

* [idle](http://developer.chrome.com/apps/idle.html) - マシンのアイドル状況の変更検知

* [power](http://developer.chrome.com/apps/power.html) - システムの電源管理機能のオーバーライド

しかしながら、全てのChrome JavaScript APIは実装されません。そして、全てのChromeデスクトップ向け機能は、モバイルにおいて利用可能にはなりません。

* タグはありません

* IndexedDBはありません

* getUserMedia()はありません

* NaClはありません

私たちの
[API Status](https://github.com/MobileChromeApps/mobile-chrome-apps/blob/master/docs/APIStatus.md)ページから、あなたは進捗を追うことができます。

完了しましたか？Step 5: 公開に進みましょう。

# Step 5: 公開 (
[原文](https://github.com/MobileChromeApps/mobile-chrome-apps/blob/master/docs/Publish.md))

あなたのプロジェクトの"platforms"ディレクトリ内に、あなたは2つの完全にネイティブなプロジェクトを持っています: 1つはAndroid向け、もう1つはiOS向けです。これらのプロジェクトのリリースバージョンをビルドし、それらをGoogle PlayやiOS App Storeに公開することができます。

Play Storeへの公開

Play StoreにあなたのAndroidアプリケーションを公開するためには、以下を行います:

* "platforms/android/AndroidManifest.xml"を編集し、"versionCode"および"versionName"プロパティを正しくセットします。

* "platforms/android/ant.properties"を編集(または作成)し、"key.store"および"key.alias"プロパティをセットします(
[Android developer docs](http://developer.android.com/tools/building/building-cmdline.html#ReleaseMode)にて説明されています)。

* あなたのプロジェクトをビルドします。
    ```
    cca build android --release
    ```

* "platforms/android/ant-build"の中に入っている署名された.apkを見つけます。

* Google Play developer consoleへ署名されたアプリケーションをアップロードします。

iOS App Storeへの公開

"platforms/ios"ディレクトリの下で見つかるXcodeプロジェクトファイルを開きます:

```
open platforms/ios/*.xcodeproj
```

そして、後はAppleの
[App Distribution Guide](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)に従ってください。
