---
layout: post
title: このブログをレスポンシブにした話
categories:
- Other
---
今年の元旦にリニューアルしたこのブログですが、Nexus6で見たら、以下のように非常に残念なことになってました。

![10583971_10153836236394539_4105311014366597681_n.jpg]({{ "/images/2016/01/10583971_10153836236394539_4105311014366597681_n.jpg" | prepend: site.baseurl }})

Polymerを使ってれば勝手にレスポンシブ対応されると思っていたのですが、そんなに世の中甘くなかったです。というか、Polymerが期待する書き方をしていれば勝手にレスポンシブになったんですけど、今回の僕はそう書いてはいかなったので、上記のような結果になってしまいました。何とかしなければなりませんが、何とかしました。

ここでは、モバイル端末のWebブラウザで見ても大丈夫なようにするために何をしたのか、そもそも何がダメだったのか、紹介してみたいと思います。

# どうしたかったのか？

まず、どのようなレイアウトにしたかったのかを説明しておきましょう。いまこのブログをPCで見ている場合は、ブラウザのウィンドウサイズの横幅を縮めていただければわかると思います。具体的には、ブラウザのウィンドウサイズが十分に広い場合（PCで見ている状態）は、以下のような3つの区画で配置します。

![responsive_1.png]({{ "/images/2016/01/responsive_1.png" | prepend: site.baseurl }})

そして、横幅が狭い場合（スマートフォンで見ている状態）は、以下のように3つの区画が縦に並ぶように配置したいと考えました。

![responsive_2.png]({{ "/images/2016/01/responsive_2.png" | prepend: site.baseurl }})

ブラウザのクライアント領域の横幅に応じて、サイドバーの位置を右と下に自動的に切り替わる、という感じですね。普通のレスポンシブだと思います。ここでのポイントは、「ヘッダ領域は"1つ"で、必ず"画面上部に固定されている"」ということです。完全に僕の主観ですが、ここは「絶対に譲れない仕様」です。そして、ヘッダ領域はPolymerの_paper\-header\-panelのwaterfall\-tallモードを使って、縦に伸縮するUIとする、というのも外せません。

waterfall\-tallモードは、以下のページで見ることができますが、つまりはこのブログの上部の「にょき、にょき」がそうです。

[paper-header-panel#waterfall-mode](https://elements.polymer-project.org/elements/paper-header-panel?view=demo:demo/index.html&active=paper-header-panel)

# レスポンシブ未対応だった最初の構成

このブログをリニューアルしたいなと思ってた動機の一つとして、Polymerを使いたかった、というのがあります。リニューアル当初の構成は、メイン領域とサイドバー領域をPolymerのレイアウト機構を使って左右に並べる、という単純なものでした。

{% highlight html %}
<paper-header-panel mode="waterfall-tall">

  <div class="paper-header">
    <!-- ブログ名称とか -->
  </div>

  <div class="layout horizontal">
    <div class="flex main">
      <!-- メイン領域 -->
    </div>
    <div class="sidebar">
      <!-- サイドバー領域 -->
    </div>
  </div>

</paper-header-panel>
{% endhighlight %}

まあ単純ですね。これで「レスポンシブだぜ」と思った自分は一体何を血迷っていたのでしょうか。Polymerを使いたかった最大の理由「にょきにょきヘッダ領域」も無事実現し、メイン領域とサイドバー領域も思っていたとおりの配置になりました。PCでの確認だけですけど。

ここでのポイントは、メイン領域のdiv要素に*flex*が付いていることですね。そして、*.sidebar*のスタイル定義で"width: 310px;"と固定幅を指定していた（リキッドではなかった）こともあり、結果として最初の画像のように残念な結果になりました。つまり、メイン領域が横幅を十分に使うことができる代わりに、ブラウザの横幅を狭めれば当然メイン領域だけが狭まっていく、という副作用(?)も出てしまった、ということです。

![10583971_10153836236394539_4105311014366597681_n.jpg]({{ "/images/2016/01/10583971_10153836236394539_4105311014366597681_n.jpg" | prepend: site.baseurl }})

この現象に気がついた時に初めて、「あ、真面目にレスポンシブ対応せにゃあかん」と思いました。

# paper\-drawer\-panel in paper\-header\-panelは大失敗

Polymerの*layout_ _horizontal*指定がレスポンシブではない、と知った後、ではPolymerでレスポンシブ対応するにはどうしたら良いのかを学ばなければなりません。というか、最初から気がついていたのですが、*paper\-drawer\-panel*というコンポーネントを使うことで、ブラウザの横幅に応じて「横に並べるか」「縦に並べるか」を切り替えてくれます。以下のデモを見ると良いでしょう。

[paper-drawer-panel](https://elements.polymer-project.org/elements/paper-drawer-panel?view=demo:demo/index.html&active=paper-drawer-panel)

上記のページをPCのWebブラウザで表示した状態で、そのブラウザの横幅を小さくしてみてください。*drawer*をつけたdiv要素が非表示になり、左上のハンバーガーボタン押すことで、左からにょきっと出てくる動作になることがわかります。

今回のブログではサイドバー領域を右に置きたいので、*right\-drawer*を付与すれば良さそうです。概念図は以下のような感じですね。

![responsive_3.png]({{ "/images/2016/01/responsive_3.png" | prepend: site.baseurl }})

当初の僕の想定は、ブラウザの横幅が狭い場合は自動的に以下のようになると思ってました。まあ、隠れるか、下に来るか、その差だけなので、どちらもレスポンシブ対応としては成り立ってると思います。

![responsive_4.png]({{ "/images/2016/01/responsive_4.png" | prepend: site.baseurl }})

*paper\-header\-panel*を使う都合上、メイン領域とサイドバー領域をその中に収めないといけないので、*panel\-drawer\-panel*がその中に収まります。タグ構成は以下のようになります。

{% highlight html %}
<paper-header-panel mode="waterfall-tall">

  <div class="paper-header">
    <!-- ブログ名称とか -->
  </div>

  <paper-drawer-panel right-drawer>
    <div main>
      <!-- メイン領域 -->
    </div>
    <div drawer>
      <!-- サイドバー領域 -->
    </div>
  </paper-drawer-panel>

</paper-header-panel>
{% endhighlight %}

「これで完璧だぜ！」と思って動作確認しましたが、見事に機能しませんでした。メイン領域とサイドバー領域はちゃんと左右に描画されたのですが、*paper\-header\-panel*が全く機能しなくなりました。縦に伸縮しなくなったどころか、ページがスクロールできなくなってしまいました。試しに*paper\-drawer\-panel*をdiv要素で囲ってみたりしてみましたが、何の効果もありませんでした。

![responsive_5.png]({{ "/images/2016/01/responsive_5.png" | prepend: site.baseurl }})

*paper\-header\-panel*の機能不全の原因を調査したかったのですが、軽くネット検索しても特に何もヒットせず、気になることとしては、PolymerのPaper Element Catalogの*paper\-drawer\-panel*の[Docs](https://elements.polymer-project.org/elements/paper-drawer-panel)を見ると、*paper\-drawer\-panel*の中で*paper\-header\-panel*を使っている例が出ていました。以下のコードが、Docsページに掲載されていた例です。

{% highlight html %}
<paper-drawer-panel>
  <paper-header-panel drawer>
    <paper-toolbar></paper-toolbar>
    <div> Drawer content... </div>
  </paper-header-panel>
  <paper-header-panel main>
    <paper-toolbar></paper-toolbar>
    <div> Main content... </div>
  </paper-header-panel>
</paper-drawer-panel>
{% endhighlight %}

Polymerが適用されているようなWebサイトをいくつか見てみましたが、サイドバー領域を持っていて、ヘッダ領域がブラウザの横幅いっぱいに配置されているようなWebサイトを見つけることができませんでした。

ここで、嫌な予感がしました。実は「*paper\-header\-panel*の中に*paper\-drawer\-panel*を入れて使うことは想定外なのではないか・・・」ということです。たぶん、この予想は当たってます。

# paper-drawer-panelがそもそも使えなかった問題

「わかったよ、そこまで言うなら、Polymerの言う通りにするよ！」という勢いで、絶対に譲れないと考えてた*paper\-header\-panel*の上部幅100%固定化を諦めて、*paper\-header\-panel*と*paper\-drawer\-panel*の親子関係を入れ替えることにしました。ブログのレイアウトが以下のようになります。

![responsive_6.png]({{ "/images/2016/01/responsive_6.png" | prepend: site.baseurl }})

タグ構成は以下のように変更してみました。

{% highlight html %}
<paper-drawer-panel right-drawer>

  <div main>

    <paper-header-panel mode="waterfall-tall">
      <div class="paper-header">
        <!-- ブログ名称とか -->
      </div>
      <!-- メイン領域 -->
    </paper-header-panel>

  </div>

  <div drawer>
    <!-- サイドバー領域 -->
  </div>

</paper-drawer-panel>
{% endhighlight %}

この結果、特に1つの記事を表示するページが、以下のようにひどい壊れ方をしてしまいました。

![responsive_7.png]({{ "/images/2016/01/responsive_7.png" | prepend: site.baseurl }})

なんでこんな風な壊れ方をするのか、それこそ原因が掴めませんでした。気になることとすれば、記事一覧ページと比べて*paper\-toolbar*を使っている程度なのですが、そんなことでここまでの壊れ方をするもんなのかと途方に暮れました。また、サイドバー領域に貼ったFacebookやTwitterなどのガジェットがiframeなのですが、それらを消すと表示が少しまともになったりはしました。しかし、少なくともPC向けにはこれらのガジェットは貼っておきたいので、消すことは解決策になりません。

Polymerの正攻法を使っても上記のような感じでダメで、もしかしたらもう少し頑張れば解決できたかもしれないけど、元々の大前提「*paper\-header\-panel*を画面上部に幅100%で固定させる」を捨ててるわけで、それでもダメだったので萎えました。

# 結局自前でレスポンシブ対応

「もうPolymerやめてBootstrapに浮気しようかな」とも思いましたが、浮気は良いことではありません。「Polymer使うんだ！*paper\-header\-panel*使うんだ！レスポンシブにするんだ！」という初心を大事にすべきです。コードを元に戻して、最初からやり直すことにしました。

まず、当初書いたコードを再度見直してみます。

{% highlight html %}
<paper-header-panel mode="waterfall-tall">

  <div class="paper-header">
    <!-- ブログ名称とか -->
  </div>

  <div class="layout horizontal">
    <div class="flex main">
      <!-- メイン領域 -->
    </div>
    <div class="sidebar">
      <!-- サイドバー領域 -->
    </div>
  </div>

</paper-header-panel>
{% endhighlight %}

上記の場合、メイン領域とサイドバー領域を横に並べたいので、div要素に*layout*と*horizontal*を指定しました。では、ブラウザの横幅が狭い時にメイン領域とサイドバー領域を縦に並べたいときはどうしたら良いか？それは、*horizontal*ではなく*vertical*を指定すれば良いわけです。

{% highlight html %}
...
  <div class="layout vertial">
    <div class="flex main">
      <!-- メイン領域 -->
    </div>
    <div class="sidebar">
      <!-- サイドバー領域 -->
    </div>
  </div>
...
{% endhighlight %}

「よし、ブラウザの横幅に応じて、div要素のclass属性値を変化させれば良いのか！」と思いましたが、それだとJavaScriptを書くことになってしまいそうなので、それはやりたくありません。CSSレベルで解決したい。そのためには、*layout*、*horizontal*、*vertical*がスタイル定義にどう反映されているかを知る必要があります。

調べたところ、以下のような感じでした。

* layout: "display: flex;"
* horizontal: "flex-direction: row;"
* vertical: "flex-direction: column;"

つまり、CSSメディアクエリを使って、*flex\-direction*の値を切り替えるようにすれば、レスポンシブ対応ができそうです。

まず、HTMLを以下のように書き換えました。*layout*と*horizontal*の指定ではなく、新規に*container*というクラスを指定しています。

{% highlight html %}
...
  <div class="container">
    <div class="flex main">
      <!-- メイン領域 -->
    </div>
    <div class="sidebar">
      <!-- サイドバー領域 -->
    </div>
  </div>
...
{% endhighlight %}

そして、cssファイルに以下の記載を行いました。

{% highlight css %}
.container {
  display: flex;
}

@media only screen and (min-width: 769px) {
    .container {
        flex-direction: row;
    }
    .sidebar {
        width: 310px;
    }
}

@media only screen and (max-width: 768px) {
    .container {
        flex-direction: column;
    }
    .sidebar {
        width: 100%;
    }
}
{% endhighlight %}

これによって、ブラウザの横幅が768ピクセル以下であれば、column値が適用されてメイン領域とサイドバー領域が縦に並ぶようになりました。ついでに、サイドバー領域の横幅も固定値ではなく横幅いっぱいになるようにしています。

![responsive_8.png]({{ "/images/2016/01/responsive_8.png" | prepend: site.baseurl }})

実際のこのブログでは、スマホ向けにはFacebookやTwitterのガジェットを非表示にしたり、シンタックスハイライトされるコードの文字の大きさやmarginを変化させたりしています。Polymerの*paper\-drawer\-panel*を使った場合にそれらをどう変化させれば良いかわかりませんが、上記のように自前で行ってしまったおかげで自由が効くようになりました。

# まとめ

今後*layout*や*horizontal*のスタイル定義がPolymerのバージョンアップによって変更された場合が心配ですが、とりあえず上記の対応によって、当初行いたかったことはできるようになりました。Polymerのセオリー（つまりMaterial Designのポリシー）には反してしまったかもしれませんが、もし*paper\-header\-panel*を上部に幅いっぱいに固定したままメインコンテンツをレスポンシブ対応したくなった際には、ここで述べた小手先テクニックをぜひお試しください。
