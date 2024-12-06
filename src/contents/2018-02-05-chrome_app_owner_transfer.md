---
layout: post
title: 僕が作ったChromeアプリを会ったこともないイギリスの開発者に移管した話
categories:
- Chrome Apps
---

昨年末に、[OneDriveとWevDAVのChromeOS向けFile System Provider実装のメンテナンスをやめます](https://www.eisbahn.jp/yoichiro/2018/01/stop_two_fsps.html)というエントリをポストしました。これは、過去に5つ作ったChromeアプリのうち2つをもうメンテナンスせずに、Chromeウェブストアからも非公開にする、つまりオワコンにしますよっていう宣言でした。ただ、OneDrive向けのChromeアプリに関しては、実はオワコンにはなっていません。現在もChromeウェブストアにて公開されています。

![chrome_app_owner_transfer_1.png]({{ "/images/2018/02/chrome_app_owner_transfer_1.png" | prepend: site.baseurl }})

[File System for OneDrive](https://chrome.google.com/webstore/detail/file-system-for-onedrive/jbfdfcehgafdbfpniaimfbfomafoadgo)

Chromeウェブストアの表示内容をよーく見てみると、提供者は僕の名前ではありません。そう、実は僕はOneDrive向けの実装を、他の開発者に移管したのです。

本当に数えるほどですが、今までも何回か「こんな修正をしてみたから取り込んでよ」っていう依頼が来ることがありました。その度に取り込んでいたのですが、OneDrive向けの実装に対して、2017年の冬にメールが来て「OneDrive Businessでも対応できるように修正してみたから、差分確認してくれる？」って依頼が来ていて、「あ、見てみるね」って返事をしたっきり、確認もせずに放置していました。この姿勢は今思うと最悪だったのですが、何せOneDrive向けの実装は評判が悪く（アクセストークンが1時間で切れる問題）、すっかりやる気をなくしていました。オワコンにしちゃおう、と思ったくらいに。

# 移管の提案

で、そのコンタクトを取ってきてくれた人に何の相談もせず、昨年末にOneDrive向け実装のChromeウェブストアでの掲載を僕は取り下げました。すると、すぐにその彼（名前はMichaelです）からメールが再び来ました。

> Hey buddy,
>
> Are you still out there?
>
> It's been noted that your version of Filesystem for Onedrive is now absent from the chrome store.
>
> Have you given up on it now? I did notice you made a few commits at the beginning of the year, anything I should incorporate?

まあ、はい、そりゃ、「どうなってんだよおい」って言いますよねー。で、ここでやっと僕は彼が「本気」だったことに気がつきます。少なくとも、確認して欲しいと言ってきた差分を見る限り、OneDrive APIについて僕よりも詳しそうです。

「これはもう彼にOneDrive向けの実装をあげちゃった方が良いのでは？」

と思い、メールの返事を書きました。

> Hi Michael,
>
> First, I really would like to say that thank you for that you have an interest for the FSP OneDrive app and the implementation. And, I'm sorry for a long delay of the response from me.
>
> As you have already noticed, a publication of the FSP OneDrive app has already been stopped (at the same time, the FSP WebDAV app has also been stopped). That is, I turned it off from the Chrome Developer Dashboard on the end of last year. Since before a few months, I was thinking of stopped a maintenance of the app. The reasons are described on my blog entry (see the following URL). I intend to concentrate maintenances for three apps: Dropbox, SFTP and SMB.
>
> https://www.eisbahn.jp/yoichiro/2018/01/stop_two_fsps.html
>
> As the result, I could not merge and release your code. Sorry.
>
> BTW, the app has already been dropped from the Chrome WebStore, but all source codes are still hosted on my repository of the GitHub:
>
> https://github.com/yoichiro/chromeos-filesystem-onedrive
>
> The UI was broken, because the Polymer 0.5.4 cannot be worked from Chrome 63. But, I have already repaird the problem by using a Bootstrap instead of the Polymer. The diffs was already committed and merged to the master branch.
>
> Well, I would like to propose that I transfer the ownership of the repository of the OneDrive app to you, if you are interested in that. Over 50 thousands users was using the app. I have already decided to stop the maintenance, however, if you maintain the app, many users will be happy, I guess.
>
> How about above?

中盤までの文はどうでもよく、最後の提案が大事。OneDrive向けアプリのオーナーシップを移管するから、メンテナンスしてみない？と誘ってみました。

すると、早速Michaelから返事が来ます。

> Yes that would be fine and I really appreciate your latest commit.
>
> Let me know what I need to do to assume control of the github source.

お、引き受けてくれるっぽいです。やったー。

# 移管するためにやること

すでにWeeklyで5万人以上のユーザが利用しているChromeアプリなので、ソースコードだけでなく、そのユーザ達もMichaelに移管できると良さそうです。そうなると、やることは2つです。

* GitHubのリポジトリについて、Transfer Ownershipする。
* ChromeウェブストアへのChromeアプリ登録について、Transfer Ownershipする。

## GitHubリポジトリの所有権変更

一つ目のGitHubリポジトリの移管については、ボタンをポチッとするだけです。Danger ZoneのTransfer Ownershipから、MichaelのGitHubユーザ名入れて、移管完了。簡単です。

![chrome_app_owner_transfer_2.png]({{ "/images/2018/02/chrome_app_owner_transfer_2.png" | prepend: site.baseurl }})

## Chromeアプリ登録の所有権変更

次のChromeアプリの登録を僕名義ではなくてMichael名義に変更する手続きを行います。これがすごく時間がかかった！Chrome Developer Dashboardでは、所有権を移管するために、以下のフォームから申請します。

[Chrome Web Store account transfer request](https://support.google.com/chrome_webstore/contact/dev_account_transfer)

基本的には7日間以内で返事が来るはずが、7日間を過ぎても返事が来ず。別のサポート窓口から「どうなってるの？」と送ったところ、どうやら放置されてたっぽい。そのサポートの人が社内で回してくれて、移管プロセスが進み、無事MichaelにChromeアプリの所有権が移りました。

# 現在はバージョンアップ＆公開継続中

無事Michaelの手に渡ったOneDrive向け実装ですが、すでに彼が作った差分も入って、1回バージョンアップされた状態でChromeウェブストアに引き続き公開中です。移管したので、Chromeアプリの再登録にはならず、既存のユーザ達も特に何もすることなく、Michaelが作った新しいバージョンが行き渡っているはずです。

ちなみに、最近はChrome拡張機能を買い取ったりして、そこに広告や「怪しいコード」を入れて再配布し、多くのユーザに迷惑を与える、っていうことが流行っています。今回その一端を僕が担ってしまわないように、「Michaelって誰？」っていうのを少しだけ調査してみましたが、どうやらイギリスのORACLE支社の社員のようでした。

所有権が全て彼の手に渡った後に、

> Muwhahahahahahaha
>
> (That's my Dr evil laugh)

というおちゃめ？なメールをくれたMichaelを応援していきたいと思っています。

コードを書いていると、いろいろなことが予期せぬとも起きます。面白いなー、と思ってこのエントリにまとめてみました。
