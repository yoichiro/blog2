---
layout: post
title: 終わってしまうChromeアプリ
categories:
- Chrome Apps
---
既に2ヶ月前の2016年9月8日、Google Developers Japan Blogに、僕にとって非常に興味深いブログが掲載されました。

[Chrome アプリからウェブアプリへ](https://googledevjp.blogspot.jp/2016/09/from-chrome-apps-to-the-web.html)

簡単にまとめると、「Chromeアプリは（ChromeOSを除いて）終了します」という内容でした。薄々感じ取ってはいたものの、改めて正式に言われてしまうと、とてもショックです。まあ、でも、確かに仕方のないことなんですけど、特に日本においてはChromebookを目にする機会はほとんどありませんし、そのためChromeアプリ開発者の数も非常に少なかったと思います。OpenSocialといい、Chromeアプリといい、そろそろ僕も「お前が注目した技術は数年後になくなる」とか声が聞こえてきそうですが、2年近く、かなり本気を出して取り組んできたChromeアプリの世界だけに、立ち直れるまでかなりの時間がかかったというか、今も信じられません。

そんな僕の思いを皆さんに伝えられる場が、実はありました。先月の10月9日に行われた[DevFest Tokyo 2016](http://gdg-tokyo.connpass.com/event/38927/)です。その中のLightning Talk枠を僕はいただくことができたのですが、持ち時間5分のところを、あふれる思いを抑えきれず、スライドを61枚も作ってしまい、もちろん半分くらいのところでTime Upとなってしまいました。もちろん、完全に自業自得なのですが、これでは僕の中のChromeアプリ熱が成仏できません。

そこで、本来話そうとしていたことを、このエントリでお伝えしようと思います。では、始まり始まり〜。

![chrome_apps_1.png]({{ "/images/2016/11/chrome_apps_1.png" | prepend: site.baseurl }})

さて、今日は皆さんに、素晴らしいChromeアプリの世界をぜひ知って帰っていただきたいと思います。

![chrome_apps_2.png]({{ "/images/2016/11/chrome_apps_2.png" | prepend: site.baseurl }})

自己紹介はしなくていいですよね。時間ないので飛ばします。

![chrome_apps_3.png]({{ "/images/2016/11/chrome_apps_3.png" | prepend: site.baseurl }})

さて皆さん、Chromeアプリってご存じですか？知ってる方手を上げて！はい、皆さん知ってますよね？では、作ったことある人？（ほとんど手が上がらず）はい、まあ、そんなもんですよね。

![chrome_apps_4.png]({{ "/images/2016/11/chrome_apps_4.png" | prepend: site.baseurl }})

こういうテキストエディタがChromeアプリで作れます。

![chrome_apps_5.png]({{ "/images/2016/11/chrome_apps_5.png" | prepend: site.baseurl }})

そして、Photoshopとほとんど同じ機能を持つアプリもあります。もちろんChromeアプリです。ちなみに、この写真は僕の娘です。似てますか？可愛いですよね。

![chrome_apps_6.png]({{ "/images/2016/11/chrome_apps_6.png" | prepend: site.baseurl }})

LINEだってChromeアプリ版があります。

![chrome_apps_7.png]({{ "/images/2016/11/chrome_apps_7.png" | prepend: site.baseurl }})

Chrome Dev EditorというWebアプリ開発環境もChromeアプリとして提供されていたんです。残念ながらもう開発は止まってますが。

![chrome_apps_8.png]({{ "/images/2016/11/chrome_apps_8.png" | prepend: site.baseurl }})

そして、美しい3DグラフィックスのゲームもChromeアプリとして数多く登場しました。これはその時のキャプチャですが、本当になめらかにグルングルン回ります。酔うほどに。

![chrome_apps_9.png]({{ "/images/2016/11/chrome_apps_9.png" | prepend: site.baseurl }})

そして、Chromeウェブストアにいけば、日本人向けのこういったChromeアプリ達をインストールして遊ぶことができるわけです。だんだんChromeアプリの素敵な世界がわかってきましたね？

![chrome_apps_10.png]({{ "/images/2016/11/chrome_apps_10.png" | prepend: site.baseurl }})

これらのChromeアプリは、HTML、JavaScript、CSSといった、ウェブ標準技術で作られています。そう、Chromeアプリなら、Chromeがインストールさえすれば、今まで紹介したChromeアプリ達はどこでも同じように動作するわけです。Write Once, Run Anywhereです。

![chrome_apps_11.png]({{ "/images/2016/11/chrome_apps_11.png" | prepend: site.baseurl }})

Chromeアプリ内で使えるAPIは、こんなにあります。

![chrome_apps_12.png]({{ "/images/2016/11/chrome_apps_12.png" | prepend: site.baseurl }})

その中でも、簡単なChromeアプリであれば、これくらいのAPIの使い方さえ知っていれば十分です。

![chrome_apps_13.png]({{ "/images/2016/11/chrome_apps_13.png" | prepend: site.baseurl }})

特徴的なのは、BluetoothやUSB、CPUなど、ハードウェアにアクセス可能なAPIが比較的充実していたことです。つまり、これらを駆使すれば、WindowsやMacなどのネイティブアプリに匹敵するChromeアプリを作ることができます。

![chrome_apps_14.png]({{ "/images/2016/11/chrome_apps_14.png" | prepend: site.baseurl }})

その中でも僕が目を付けたAPIが、sockets.tcp APIです。そうなんです。生ソケットを使えるんです！

![chrome_apps_15.png]({{ "/images/2016/11/chrome_apps_15.png" | prepend: site.baseurl }})

生ソケットが使えると言うことは、何でも作れるということです！作れないものなんてないんです。無限の時間を僕にくれれば、この生ソケットを使って何だって作ってあげます。はい、すぐに作れるなんて言ってませんよ。無限の時間があれば、です。

![chrome_apps_16.png]({{ "/images/2016/11/chrome_apps_16.png" | prepend: site.baseurl }})

僕はChromeアプリとsocket.tcp APIに惚れ込みました。そして、「普通だったらそんなもん作れないだろ」というものを作りたくなりました。そのときに、真っ先に思いついたのが、MySQLのドライバでも作ってみようかな、でした。幸いにも、MySQLの開発者向けページにプロトコルの説明が載っていましたので、

![chrome_apps_17.png]({{ "/images/2016/11/chrome_apps_17.png" | prepend: site.baseurl }})

せっせとこんなJavaScriptのコードを書いていったところ、

![chrome_apps_18.png]({{ "/images/2016/11/chrome_apps_18.png" | prepend: site.baseurl }})

はい、できました。socket.tcp APIを使って、MySQLと直接お話ができるドライバを100% JavaScriptで書きました。それだけでは面白くないので、

![chrome_apps_19.png]({{ "/images/2016/11/chrome_apps_19.png" | prepend: site.baseurl }})

mysqlコマンドみたいなコンソールアプリを作ってみたり、

![chrome_apps_20.png]({{ "/images/2016/11/chrome_apps_20.png" | prepend: site.baseurl }})

Mac向けに提供されているSequelProっていうMySQLのクライアントアプリがあるんですけど、それからインスパイアされて、AngularJSとかBootstrap使って、MySQLのクライアントアプリをChromeアプリとして作りました。これらはChromeウェブストアで公開しているので、興味のある方はぜひインストールしてみてください。ちゃんと動きますよもちろん。

![chrome_apps_21.png]({{ "/images/2016/11/chrome_apps_21.png" | prepend: site.baseurl }})

そして、どうしてもJavaScriptでは書けない処理、例えばSSHの暗号化通信で出てくる素数計算とかは、桁数がデカすぎてJavaScriptでは辛いとかあるわけです。そういう場合は、C++でもコードを書くことができます。Native Client Library、略してNaClと呼ばれている技術です。

![chrome_apps_22.png]({{ "/images/2016/11/chrome_apps_22.png" | prepend: site.baseurl }})

先ほどのMySQLのクライアントアプリの中では、とある外国人が「SSHのポートフォワーディングでないとMySQLに繋げられないから何とかしろ」とわがままを言ってきたので、libssh2というC言語向けのライブラリを組み込んで、それを実現しています。

![chrome_apps_23.png]({{ "/images/2016/11/chrome_apps_23.png" | prepend: site.baseurl }})

また、Secure ShellというChromeアプリがあって、SSHクライアントアプリなのですが、

![chrome_apps_24.png]({{ "/images/2016/11/chrome_apps_24.png" | prepend: site.baseurl }})

その中には、OpenSSHというライブラリがそっくりそのまま入ってます。

![chrome_apps_25.png]({{ "/images/2016/11/chrome_apps_25.png" | prepend: site.baseurl }})

そして、先ほどの3Dゲームの中では、

![chrome_apps_26.png]({{ "/images/2016/11/chrome_apps_26.png" | prepend: site.baseurl }})

Unityが使われています。

![chrome_apps_27.png]({{ "/images/2016/11/chrome_apps_27.png" | prepend: site.baseurl }})

さて、昨年、一昨年で、かなりの個数のChromeアプリを作りました。File Systemなんたらっていうのは、Chromebookから遠隔地にあるファイルシステム、例えばDropboxとかOneDriveとか、SFTPとか、そういったものをあたかもChromebookのローカルストレージとしてマウントすることができるっていうChromeアプリです。おかげさまで、海外ではかなり使われています。例えば、File System for Windowsでは、SMBプロトコルをパケットキャプチャで解析しながら全部自前で実装しました。そりゃー大変でした。

![chrome_apps_28.png]({{ "/images/2016/11/chrome_apps_28.png" | prepend: site.baseurl }})

朝と夜それぞれ2時間を実装にあてる、という生活を毎日送ったおかげで、いろいろ病気にもなりました。夏に患った横行結腸憩室炎では、1週間ほど入院も経験しました。睡眠不足は良くないですね。皆さん、気をつけましょう。

![chrome_apps_29.png]({{ "/images/2016/11/chrome_apps_29.png" | prepend: site.baseurl }})

一度書けばどこでも動くChromeアプリ、

![chrome_apps_30.png]({{ "/images/2016/11/chrome_apps_30.png" | prepend: site.baseurl }})

すごくないですか？半端ないですよね？もう世の中これだけでいいんじゃないでしょうか？

![chrome_apps_31.png]({{ "/images/2016/11/chrome_apps_31.png" | prepend: site.baseurl }})

・・・

![chrome_apps_32.png]({{ "/images/2016/11/chrome_apps_32.png" | prepend: site.baseurl }})

しかし・・・

![chrome_apps_33.png]({{ "/images/2016/11/chrome_apps_33.png" | prepend: site.baseurl }})

今年の8月16日に、Chromuim Blogにて、こんな投稿がありました。

![chrome_apps_34.png]({{ "/images/2016/11/chrome_apps_34.png" | prepend: site.baseurl }})

遅れること9月8日、Google Developers Japanブログにて、日本語訳が公開されました。そこに書かれていたことは何だったかというと、

![chrome_apps_35.png]({{ "/images/2016/11/chrome_apps_35.png" | prepend: site.baseurl }})

なんと、Chromeアプリの終了のお知らせだったのです！

![chrome_apps_36.png]({{ "/images/2016/11/chrome_apps_36.png" | prepend: site.baseurl }})

![chrome_apps_37.png]({{ "/images/2016/11/chrome_apps_37.png" | prepend: site.baseurl }})

![chrome_apps_38.png]({{ "/images/2016/11/chrome_apps_38.png" | prepend: site.baseurl }})

世界中が驚きました。つまり、

![chrome_apps_39.png]({{ "/images/2016/11/chrome_apps_39.png" | prepend: site.baseurl }})

Chromeアプリは、一度書けばChromeOさえあればどこでも動く、というものになってしまったのです。しかも、先ほどのブログをよく読むと、

![chrome_apps_40.png]({{ "/images/2016/11/chrome_apps_40.png" | prepend: site.baseurl }})

当面の間、という表現で書かれています。ってことは、そのうちChromeOSでも動かなくなってしまう、つまり完全終了のタイミングが来るってことですか？

![chrome_apps_41.png]({{ "/images/2016/11/chrome_apps_41.png" | prepend: site.baseurl }})

更に追い打ちをかける事態となっています。これからは、ChromeOS上でAndroidアプリが動作するようになります。既に一部のChromebookでは、GooglePlayをサポートし始めています。

![chrome_apps_42.png]({{ "/images/2016/11/chrome_apps_42.png" | prepend: site.baseurl }})

昔こんな画像がありましたが、

![chrome_apps_43.png]({{ "/images/2016/11/chrome_apps_43.png" | prepend: site.baseurl }})

僕にはこう見えてなりません。

![chrome_apps_44.png]({{ "/images/2016/11/chrome_apps_44.png" | prepend: site.baseurl }})

今日の司会を担当されている方が、自身の本でこんなことを書いていましたが、

![chrome_apps_45.png]({{ "/images/2016/11/chrome_apps_45.png" | prepend: site.baseurl }})

次に出す本には、こう書こうと思います。「お前もChromeアプリにしてやろうか」と。

![chrome_apps_46.png]({{ "/images/2016/11/chrome_apps_46.png" | prepend: site.baseurl }})

ちなみに、弊社ではQiitaを共に作ってくれるソフトウェアエンジニアを募集中です。興味があればぜひ。

![chrome_apps_47.png]({{ "/images/2016/11/chrome_apps_47.png" | prepend: site.baseurl }})

これから、ネイティブアプリに近いアプリをウェブ技術でこれからどう作ればいいんですか、という質問には、

![chrome_apps_48.png]({{ "/images/2016/11/chrome_apps_48.png" | prepend: site.baseurl }})

プログレッシブウェブアプリを作りましょう、という答えになります。そして、

![chrome_apps_49.png]({{ "/images/2016/11/chrome_apps_49.png" | prepend: site.baseurl }})

HTMLやJavaScriptだけでは作れないものを作りたくなったときは、

![chrome_apps_50.png]({{ "/images/2016/11/chrome_apps_50.png" | prepend: site.baseurl }})

asm.jsや、これからはWebAssemblyが来るわけです。しかし！

![chrome_apps_51.png]({{ "/images/2016/11/chrome_apps_51.png" | prepend: site.baseurl }})

生ソケットはきっと代替案がない！どうしてくれるんだ！僕のMySQLドライバは！

![chrome_apps_52.png]({{ "/images/2016/11/chrome_apps_52.png" | prepend: site.baseurl }})

というわけで、Chromeアプリがなくなってしまうとできなくなってしまうことがあったらこちらへ、っていうフォームがあったので、

![chrome_apps_53.png]({{ "/images/2016/11/chrome_apps_53.png" | prepend: site.baseurl }})

「俺の生ソケットを返せ」と投稿しておきましたが、

![chrome_apps_54.png]({{ "/images/2016/11/chrome_apps_54.png" | prepend: site.baseurl }})

Electronで十分です。

![chrome_apps_55.png]({{ "/images/2016/11/chrome_apps_55.png" | prepend: site.baseurl }})

完敗です。

![chrome_apps_56.png]({{ "/images/2016/11/chrome_apps_56.png" | prepend: site.baseurl }})

僕の中でのChromeアプリは、終わりを告げました。

![chrome_apps_57.png]({{ "/images/2016/11/chrome_apps_57.png" | prepend: site.baseurl }})

実際のところ、Chromeアプリはそろそろ終わってしまいそうですが、プログレッシブウェブアプリもそうですし、ウェブ標準技術の進化が最近めざましく、確かにChromeアプリがこのままいくよりは、普通のウェブページでも同様のことができるようになっていくことの方を歓迎すべきかな、と思います。そんなわけで、

![chrome_apps_58.png]({{ "/images/2016/11/chrome_apps_58.png" | prepend: site.baseurl }})

Chromeアプリ開発から、僕は引退しようと思います。

![chrome_apps_59.png]({{ "/images/2016/11/chrome_apps_59.png" | prepend: site.baseurl }})

今日、私は引退してしまいますが、

![chrome_apps_60.png]({{ "/images/2016/11/chrome_apps_60.png" | prepend: site.baseurl }})

僕が愛したChromeアプリは、永久に不滅です！

![chrome_apps_61.png]({{ "/images/2016/11/chrome_apps_61.png" | prepend: site.baseurl }})

ご清聴ありがとうございました。
