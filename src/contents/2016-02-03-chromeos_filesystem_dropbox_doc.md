---
layout: post
title: ChromeOS向けFile System for Dropboxの構造
categories:
- Chrome Apps
---
ChromeOS向けに開発したDropboxをマウントするためのアプリ「[File System for Dropbox](https://chrome.google.com/webstore/detail/file-system-for-dropbox/hlffpaajmfllggclnjppbblobdhokjhe)」は、Chromeウェブストア上では97,000ユーザ以上がインストールしていると今日見たら表示されていました。

![chromeos_filesystem_dropbox_1.png]({{ "/images/2016/02/chromeos_filesystem_dropbox_1.png" | prepend: site.baseurl }})

そして、Dropbox側のアプリコンソールを見ると、今もユーザ数やAPIコール数は増え続けています。嬉しい限りです。

![chromeos_filesystem_dropbox_2.png]({{ "/images/2016/02/chromeos_filesystem_dropbox_2.png" | prepend: site.baseurl }})

もちろん、[yoichiro/chromeos-filesystem-dropbox](https://github.com/yoichiro/chromeos-filesystem-dropbox)という場所でオープンソースにしてます。

さて、最初に作り始めてから1年経過したのですが、機能的に十分満足しているからなのか、Issueも最近では全く登録されないし、ましてやPull Requestは1回も受け取ったことがありません。Star数はそれなりにあるので、興味を持ってくれてる人は多いと思うのですが、何かを変えてみよう！と考える人は出てきていません。

その原因をいくつか考えてみました。

* そもそも現状で満足。
* Chrome Apps自体、なんだかよくわからない。
* ChromeOS向け、という特殊環境なので、よくわからない。
* chrome.fileSystemProvider APIとか知らない。
* それなりのコード量なので、把握するのが面倒だ。

こんな感じでしょうか。

ライブラリ的なものであれば、プログラマは嫌でもその構造あるいは少なくともインタフェース部分は触ることになります。そのため、内部構造を把握するためのきっかけを得やすいと思うんですが、今回のような単独アプリだと、まあ普通は「ブラックボックス」だと思うはずです。勝手に内部構造を見て把握してコードを何とか修正してやろう、って意識を持てという方が酷です。

そんなわけで、「もしかしたらドキュメントでもちょっと書けば、誰かコードいじる気になってくれるんじゃないか？」と思い、コードの構造を書いてみました。

[chromeos-filesystem-dropbox/docs/code_structure.md](https://github.com/yoichiro/chromeos-filesystem-dropbox/blob/master/docs/code_structure.md)

![chromeos_filesystem_dropbox_3.png]({{ "/images/2016/02/chromeos_filesystem_dropbox_3.png" | prepend: site.baseurl }})

最悪誰も興味持ってくれなくても、あとで自分で読み返して不具合修正する際に感覚をすぐに取り戻すのに役に立つかな、と思ってます。本当にメモ書き程度ですけど、もう41歳になりましたし、昔ほど「自分で書いたコードを覚えられる脳内バッファ」も減ってきてるんじゃないか？（自覚はないけど）と思うので、いいかなと。

他の実装についても書いていきたいと思ってます。こういう地道な作業が、他の誰かを呼び込んでくれると信じて・・・。
