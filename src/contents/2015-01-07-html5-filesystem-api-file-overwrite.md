---
layout: post
status: publish
published: true
title: HTML5 FileSystem APIを使って上書き処理をする方法
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 3222
wordpress_url: https://www.eisbahn.jp/yoichiro/?p=3222
date: '2015-01-07 20:41:34 +0900'
date_gmt: '2015-01-07 11:41:34 +0900'
categories:
- Web Technologies
---

HTML5 FileSystem APIを使うと、ユーザのローカルファイルシステムにファイルを書いたりすることができます。もちろん自由にファイルを読み込めるわけではなく、Chrome Webブラウザが持っているサンドボックス内に通常は限定されます（Chromeアプリの場合はその限りではないですが）。オフライン対応アプリケーションを開発する場合には、非常に魅力的なAPIです。

僕の場合はChromeアプリ内でFileSystem APIを使っていたのですが、「既存ファイルの上書き処理」をする際に困ったことと解決策を、本エントリで紹介してみたいと思います。FileSystem API自体の説明は、「
[FileSystem API について知る](http://www.html5rocks.com/ja/tutorials/file/filesystem/)」をご覧ください。

まず、普通にファイルに何か書き込みを行いたい場合、以下のようにコーディングすると思います。

```
function onInitFs(fs) {
  fs.root.getFile('hoge.txt', {create: true, exclusive: false}, function(fileEntry) {
    fileEntry.createWriter(function(fileWriter) {
      fileWriter.onerror = function(e) {
        // Do something...
      };
      fileWriter.onwriteend = function(e) {
        console.log('Write completed.');
      };
      var contents = "...";
      var blob = new Blob([contents], {type: "text/pain"});
      fileWriter.write(blob);
    }, errorHandler);
  }, errorHandler);
}
window.requestFileSystem(window.TEMPORARY, 1*1024*1024, onInitFs, errorHandler);
```

上記のコードにおいて、もし既にhoge.txtファイルが存在していた場合、既存ファイルの内容と、書き込みたい内容（上記のコードのcontents変数値）次第で、ファイルの内容が壊れたものになります。具体的には、上書きはされるのですが、内容の大きさが「書き込みたい内容 ＜ 既存ファイルの内容」だった場合、既存ファイルの内容が一部末尾に残ってしまいます。例をあげると、例えば既存ファイルの内容が、

"Hello, world!"

だった場合、contents = "Hi, world!" を上記コードで書き込んだ場合、

"Hi, world!ld!"

となってしまいます。

これを避けるためにいくつかの方法が考えられますが、最も手軽なコードとして、僕は以下に行き着きました。

```
function onInitFs(fs) {
  fs.root.getFile('hoge.txt', {create: true, exclusive: false}, function(fileEntry) {
    fileEntry.createWriter(function(fileWriter) {
      fileWriter.onerror = function(e) {
        // Do something...
      };
      fileWriter.onwriteend = function(e) {
        if (fileWriter.length === 0) {
          var contents = "...";
          var blob = new Blob([contents], {type: "text/pain"});
          fileWriter.write(blob);
        } else {
          console.log('Write completed.');
        }
      };
      fileWriter.truncate(0);
    }, errorHandler);
  }, errorHandler);
}
window.requestFileSystem(window.TEMPORARY, 1*1024*1024, onInitFs, errorHandler);
```

まず、createWriter()関数に渡したコールバック関数で得られるfileWriterについて、その時点でのファイル内でのポジションはファイルの先頭です。ここで直ちにtruncate()関数に0を渡して、ファイルの長さを0にします。つまり、一旦ファイルの内容を消去するのです。この副作用として、onwriteendイベントが発火します。最初にこのイベントが発火したときのファイルの長さは0のはずなので、もしlengthが0だったときは書き込みたい内容をwrite()関数で書き込みます。書き込み終了後、再度onwriteendイベントが発火しますが、そのときにはlengthが0ではなくなってるはずなので、書き込み終了処理を行えば良い、ということです。

少なくともChromeアプリ内では上記のコードで正しくファイルの上書き処理（というか置き換え処理と言うべきか・・・）が行われているので、通常のWebページでも同じように動作すると思います。

[database_panel_controller.js#L142](https://github.com/yoichiro/chrome_mysql_admin/blob/master/app/scripts/window/controllers/database_panel_controller.js#L142)

もし「ダメだったよ！」という場合は、僕に教えてください。。。
