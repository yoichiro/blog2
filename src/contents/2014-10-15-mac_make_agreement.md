---
layout: post
status: publish
published: true
title: macosxのmakeで"Agreeing to the Xcode/iOS license ..."と表示された時の対処方法
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 3001
wordpress_url: https://www.eisbahn.jp/yoichiro/?p=3001
date: '2014-10-15 09:34:54 +0900'
date_gmt: '2014-10-15 00:34:54 +0900'
categories:
- My PC environment
---

ほとんどC, C++を使った開発をすることがなく、さらにmakeコマンドを使うこともほとんどなかったのですが、本当に時々makeを使う場面がやってきます。今日、本当に久しぶりにXcode Command Line Toolsで入ってくるmakeコマンドを使いたくなって、おもむろに"make"と打ち込んでみました。

すると、つれない感じのメッセージが返ってきました。

```
[~] make
Agreeing to the Xcode/iOS license requires admin privileges, please re-run as root via sudo.
```

ん？「Xcode/iOSライセンスの同意には、管理権限が要求されます。sudo経由でrootとして再実行してください。」だと？何かのライセンスに同意してないから使えない状況のようです。

では、メッセージの通りにsudoで実行してみます。

```
[~] sudo make
You have not agreed to the Xcode license agreements. You must agree to both license agreements below in order to use Xcode.
Hit the Enter key to view the license agreements at '/Applications/Xcode.app/Contents/Resources/English.lproj/License.rtf'
```

また何やらメッセージが表示されました。読んでみると・・・

>あなたはXcodeライセンス契約に同意していません。Xcodeを使うためには、以下の両方のライセンス契約に同意する必要があります。
'/Applications/Xcode.app/...'にあるライセンス契約を閲覧するためには、Enterキーを押してください。


だそうです。素直にEnterキーを押してみます。すると、以下のように、ライセンス契約文書が表示されました。

![make_agreement](https://www.eisbahn.jp/yoichiro/images/2014/10/make_agreement.png)

Spaceキーで続きを表示、qキーで終わり、って書いてあるので、とりあえずSpaceキーを押しまくって最後まで進めます。最後まで行くと、

```
By typing 'agree' you are agreeing to the terms of the software license agreements.
Type 'print' to print them or anything else to cancel, [agree, print, cancel]
```

と表示されます。これも、意味は以下のような感じでしょう。

>'agree'と打ち込むことで、あなたはソフトウェアライセンス契約の条項に同意します。(もう一度)それら(=ライセンス契約文書)を表示するためには'print'を、キャンセルしたい場合はそれ以外を打ち込んでください。


まぁ同意しないと使えないので、agreeと打ち込み、Enteryキーを押します。すると、再度また「ここからライセンス文書読めるよ」とメッセージが表示され、コマンドが終了します。

```
You can view the license agreements in Xcode's About Box, or at
/Applications/Xcode.app/Contents/Resources/English.lproj/License.rtf
```

これでライセンス契約に同意したことになったはず。再度makeコマンドを実行して確認してみます。

```
[~] make
make: *** No targets specified and no makefile found.  Stop.
```

OK、大丈夫なようです。

ちなみに、"sudo make"としましたが、"sudo xcodebuild -license"でも良いようですね。

[OSX Mavericks and Git Error](http://mynameispagewood.com/web-development/osx-mavericks-git-error/)

もし同じような症状でお悩みの方がいたら、上記を参考に解決されると嬉しいです。
