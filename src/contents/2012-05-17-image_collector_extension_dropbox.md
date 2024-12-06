---
layout: post
status: publish
published: true
title: ChromeにてWebページ内の画像を一気にDropboxへ登録する方法
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1444
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1444
date: '2012-05-17 10:58:19 +0900'
date_gmt: '2012-05-17 01:58:19 +0900'
categories:
- Chrome extension
---

Webページ内の画像をいかに一気に自分のPCに保存するかを考えて開発を始めたChrome Webブラウザ向け拡張機能「Image Collector extension」ですが、昨夜にバージョン2.0.1をリリースしました。

![](http://www.eisbahn.jp/yoichiro/images/2012/05/unnamed.png)

このバージョンにおいて、大きな機能追加を行いました。それは「Dropboxへの画像の登録」をできるようにしたことです。スクリプトファイルを自分で実行することなく、リンククリックのみでWebページ上の条件にあった画像を一気にDropbox上のフォルダに登録することができます。そしてそれらの画像は、ほぼ同時に自分のPCにダウンロードされることでしょう。

手順としては、まずImage Collector extensionを以下のページからインストールします。

[Image Collector extension - Chrome Webストア](http://goo.gl/5Q4HT)

そして、Image Collector extensionのオプション画面を表示します。そのためには、以下のURLを開いたあとに、Image Collector extensionの[オプション]リンクをクリックします。

[chrome://chrome/extensions/](chrome://chrome/extensions/)

![](http://www.eisbahn.jp/yoichiro/images/2012/05/ics1.png)

オプション画面の下の方に「Dropbox」という項目があります。そこにある[接続]ボタンを押します。

![](http://www.eisbahn.jp/yoichiro/images/2012/05/ics2.png)

すると、Dropboxの認可画面が表示されます（未ログインの場合は、Dropboxへのログイン画面がまず表示されます）。そこで[許可]ボタンを押すことで、Image Collector extensionとDropboxが接続され、同時にオプション画面に戻ってきます。

![](http://www.eisbahn.jp/yoichiro/images/2012/05/ics3.png)

「Dropbox」の項目内で「認可されています。」と表示されれば成功です。

![](http://www.eisbahn.jp/yoichiro/images/2012/05/ics4.png)

これで準備は整いました。欲しい画像があるWebページを訪れて、Image Collector extensionのボタンを押し、ポップアップウィンドウを表示させます。すると、画像のリストの下に[Dropbox]リンクがあるのがわかります。そのリンクを押してちょっとすると、「登録タスクが予約されました。」と緑色で表示されます。これで、Image Collector extensionのバックエンドサーバにDropboxへの画像登録処理が予約されたことになります。

![](http://www.eisbahn.jp/yoichiro/images/2012/05/ics5.png)

あとはちょっと待てば、Dropboxのファイル同期が走り、ご希望の画像群が自分のPCに次々とダウンロードされてきます。一度接続してしまえば、次からは[Dropbox]リンクを押すのみで画像をDropboxに登録していくことができます。お手軽ですね！

![](http://www.eisbahn.jp/yoichiro/images/2012/05/ics6.png)

利用目的は様々ですが、もちろん使う人の自由です。ぜひご活用ください。
