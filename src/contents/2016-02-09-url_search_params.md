---
layout: post
title: URLSearchParamsによる簡単URL操作
categories:
- chrome
---
JavaScriptでURL文字列を操作するのは、実は結構骨が折れる作業です。特に、クエリパラメータを個々に取り出す処理などは、毎回車輪の再発明を皆さんしてきたのではないでしょうか？URLSearchParamsを使うことで、もう再発明をすることはありません。Firefox 44、Opera 36で使えて、Chrome 49からも使えるようになります。

Google Developersに「[Easy URL manipulation with URLSearchParams](https://developers.google.com/web/updates/2016/01/urlsearchparams)」という記事がありましたので、日本語訳を作ってみました。

- - -

URLSearchParams APIは、URLの細々したものへの一貫性のあるインタフェースを提供し、そしてクエリ文字列（"?"の後のもの）のありふれた操作を可能にします。

慣習的に、URLからクエリパラメータを引き出すために、開発者は正規表現や文字列分割を使います。もし私たち自身が皆正直であれば、それは楽しいことではありません。それはうんざりさせ、そしてエラーを引き起こす傾向があります。私の秘密の一つは、GoogleサンタトラッカーやGoogle I/O 2015ウェブサイトを含むいくつかの大きなGoogle.comアプリにおいて、同じget、set、removeURLParameterというヘルパーメソッドを再利用しました。

この作業をするための適切なAPIを使う時が私たちに来ました！

# URLSearchParams

[デモを試す](https://googlechrome.github.io/samples/urlsearchparams/index.html)

Chrome 49は、[URL仕様](https://url.spec.whatwg.org/#urlsearchparams)から、URLクエリパラメータをあれこれいじくり回すAPIであるURLSearchParamsを実装しました。URLSearchParamsは、フォームのためにあったFormDataと同じような便利さをURLSearchParamsはURLにもたらすと考えています。

さて、それを使ってあなたは何ができるでしょう？URL文字列が与えられた時、あなたは簡単にパラメータ値を展開することができます:

{% highlight js %}
// もう一つのURLSearchParamsから作成することもできます
let params = new URLSearchParams('q=search+string&version=1&person=Eric');

params.get('q') === "search string"
params.get('version') === "1"
{% endhighlight %}

注意: もし一つのパラメータにいくつかの値がある場合は、getは最初の値を返します。複数のパラメータを横断して操作する場合は以下になります:

{% highlight js %}
for (let p of params) {
  console.log(p);
}
{% endhighlight %}

パラメータ値をセットする場合は以下になります:

{% highlight js %}
params.set('version', 2);
{% endhighlight %}

注意: もしいくつかの値がある場合は、setは同じ名前の全ての他のパラメータを削除します。

既存パラメータにもう一つ値を追加する場合は以下になります:

{% highlight js %}
params.append('person', 'Tim');
params.getAll('person') === ['Eric', 'Tim']
{% endhighlight %}

パラメータ（複数）を削除する場合は以下になります:

{% highlight js %}
params.delete('person');
{% endhighlight %}

注意: この例は、最初に出現したものだけでなく、全てのpersonクエリパラメータをURLから削除します。

# URLとの作用

ほとんどの時間、あなたはおそらくフルなURLを扱うか、もしくはあなたのアプリのURLを修正するでしょう。URL構築は、これらのケースに対して特に手軽に行えます。

{% highlight js %}
let url = new URL('https://example.com?foo=1&bar=2');
let params = new URLSearchParams(url.search.slice(1));
params.set('baz', 3);

params.has('baz') === true
params.toString() === 'foo=1&bar=2&baz=3'
{% endhighlight %}

URLを実際に変更するために、あなたはパラメータを取得し、それらの値を更新し、その後history.replaceStateを使ってUTLを更新することができます。

{% highlight js %}
// URL: https://example.com?version=1.0
let params = new URLSearchParams(location.search.slice(1));
params.set('version', 2.0);

window.history.replaceState({}, '', `${location.pathname}?${params}`);
// URL: https://example.com?version=2.0
{% endhighlight %}

Here, I’ve used ES6 template strings to reconstruct an updated URL from the app’s existing URL path and the modified params.

ここでは、アプリの既存URLと修正されたパラメータから更新されたURLを再構築するために、私はES6の[template string](https://googlechrome.github.io/samples/template-literals-es6/index.html)を使いました。

# URLが使われている他の場所への統合

初期動作として、fetch() APIリクエスト内のFormData送信は、マルチパートボディを作ります。もしあなたがそれを必要とするならば、URLSearchParamsは、MIMEマルチパートではなくURLエンコードされたPOSTデータのための代替機構を提供します。

{% highlight js %}
let params = new URLSearchParams();
params.append('api_key', '1234567890');

fetch('https://example.com/api', {
  method: 'POST',
  body: params
}).then(...)
{% endhighlight %}

Chromeではまだ実装されていませんが、URLSearchParamsはURLコンストラクタやタグとも統合します。この両方が、クエリパラメータにアクセスするための.searchParamsという読み込み専用のプロパティの提供によって、私たちの新しい仲間をサポートします:

{% highlight js %}
// 注意: URLの.searchParamsはChrome 49では実装されていません。

let url = new URL(location);
let foo = url.searchParams.get('foo') || 'somedefault';
{% endhighlight %}

リンクもまた.searchParamsプロパティを得ます:

{% highlight js %}
// 注意: リンクの.searchParamsはChrome 49では実装されていません。

let a = document.createElement('a');
a.href = 'https://example.com?filter=api';

// a.searchParams.get('filter') === 'api';
{% endhighlight %}

# 機能の発見とブラウザサポート

現在、Chrome 49、Firefox 44、そしてOpera 36が、URLSearchParamsをサポートしています。

{% highlight js %}
if ('URLSearchParams' in window) {
  // ブラウザはURLSearchParamsをサポートします
}
{% endhighlight %}

For polyfills, I recommend the one at github.com/WebReflection/url-search-params.
ポリフィルとして、私は[github.com/WebReflection/url-search-params](https://github.com/WebReflection/url-search-params)のものを勧めます。

# デモ

[sample](https://googlechrome.github.io/samples/urlsearchparams/index.html)を試してみて下さい！

現実世界のアプリでURLSearchParamsを見るために、[Polymer's material design Iconset Generator](https://poly-icon.appspot.com/)をチェックしてみてください。私はディープリンクからアプリの初期状態をセットアップするためにそれを使いました。とても手軽です。 :)
