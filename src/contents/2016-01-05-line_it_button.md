---
layout: post
title: LINEで送るボタンが新しくなった模様です
categories:
- Other
---
今まで「LINEで送る」ボタンというものが提供されていました。

[LINEで送るボタンの設置方法](http://media.line.me/howto/ja/)

以下のようなボタンがWebページに設置されているのを見たことがある人も多いと思います。

![lineit_1.png]({{ "/images/2016/01/lineit_1.png" | prepend: site.baseurl }})

これが最近新しくなってました。昔はLINEアプリがインストールされているスマートフォンのWebブラウザ内でしか機能しませんでした（PCで押してもline.meのWebサイトに飛ばされるだけ）。しかし、新しいボタンはPCでもちゃんと機能する模様です。

え？嘘でしょ、ですって？この記事の上にある「LINEで送る」ボタンを押してみてください。ほら、以下のようなウィンドウが表示されましたよね？

![lineit_2.png]({{ "/images/2016/01/lineit_2.png" | prepend: site.baseurl }})

どうやら新しくなってたことがわかっていただけたかと思います。

# どう変わったの？

昔からあるLINEで送るボタンとの機能差は、さほど変わっていません。できることと言えば、以下の2つです。

* URLをLINEの友だちにトークメッセージとして送る。
* URLを自分のタイムラインに投稿する。

ただ、さっきも書いたとおり、これがPCからでもできるようになりました。上記の画像のように、ボタンを押すとウィンドウが表示されます。あ、忘れてました。新しいLINEで送るボタンを押した初回は、ユーザ認証が求められます。

![lineit_3.png]({{ "/images/2016/01/lineit_3.png" | prepend: site.baseurl }})

で、何かメッセージを添えたい場合は、「コメントを入力して下さい。」という蘭に自由にメッセージを書きます。「これ読んでよ！」「さっき言ってたの、これです」「これはブラクラなんで踏まないで下さい。絶対踏んじゃダメですよ！」とか書くと良いですね。

で、特定の誰かにLINEのトークメッセージとして送りたい場合は、「トーク」ってボタンを押します。そうすると、送る相手を選択する画面になります。

![lineit_4.png]({{ "/images/2016/01/lineit_4.png" | prepend: site.baseurl }})

相手を選択して送信ボタンを押せば、相手にメッセージが飛びます。簡単ですね！

特定の誰かではなくて、自分のLINE内のタイムラインに投稿したいだけなら、「タイムライン」というボタンを押せば良いです。これも簡単ですね！

# 設置方法は？

これを読んでいる方も、新しいLINEで送るボタンを自分のWebサイトに設置したくなってきましたよね？設置方法は非常に簡単です。たぶんFacebookのLikeボタンやGoogleの+1ボタンと並べて置くことが多いと思いますので、同じような大きさで配置するためのタグセットは以下となります。

{% highlight html %}
<div class="line-it-button" style="display: none;" data-type="share-a" data-lang="ja"></div>
<script src="//scdn.line-apps.com/n/line_it/thirdparty/loader.min.js" async="async" defer="defer"></script>
{% endhighlight %}

この2行を設置したい場所に書けば良いです。簡単ですね！

# その他詳しい情報は？

LINE社からの公式ドキュメントは、以下にあるようです。ボタンの種類とかいろいろ揃ってるようですね。

[Using the LINE it! Button on Websites](https://developers.line.me/line-it/line-it-button-for-websites)

ぜひ設置してみてください。

# 最後に

・・・白々しい書き方して申し訳ございません。
