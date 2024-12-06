---
layout: post
title: 洋一郎WordPress使うのやめるってよ
categories:
- Other
---
2009年11月にこのブログをMovableTypeからWordPressに変更して、それからずっとWordPressを使い続けてきたのですが、昨日の2016年元旦からWordPressを使うのをやめました。その代わりに、Jekyllによる静的サイトジェネレータでこのブログを運用していくことにしました。

# WordPressはもう限界だった

いや、もうWordPressは途中からホントにきつかったです。要因はいろいろあるんですけど、しばらくバージョンアップできず、きっと脆弱性の宝庫だったと思います。確か3.7から上にあげることができず、結局4に行き着きませんでした。

まず、qTranslateっていうWordPressを国際化するプラグインを使っていたんですけど、これの開発が途中で終わってしまいました。このqTranslateプラグイン、WordPressにがっつり依存しまくりなもので、WordPressをバージョンアップしてしまうと、ほぼ100%の確率でqTranslateプラグインは機能停止に陥ります。各WordPressのバージョンに対応したqTranslateプラグインのバージョンがあって、つまりqTranslateプラグインの対応バージョンが開発されるまでは、WordPressのバージョンアップを控えなければならない、って感じでした。

そして、qTranslateプラグインは「1つの記事に複数の言語の題名や本文を投稿できる」というものでしたが、これを実現している方法は以下のようなものでした。MySQLに格納されている投稿それぞれが汚されている感が日に日に強くなってました。

{% highlight html %}
<!--:ja-->日本語の本文<!--:--><!--:en-->Body written by English<!--:-->
{% endhighlight %}

昨年の後半に何度もqTranslateプラグインを捨ててWordPress 4に移行しようと思ったのですが、3.7から4にバージョンアップすると、なぜか管理ツール（wp-admin）に入れない（真っ白になる）現象が出て、最後まで解決策がわからないままでした。.htaccessファイルをいろいろいじったり、手前にあるnginxのProxy設定をいじったりしてみたものの、最後はPHP側のコードを追わないと何が起きているのかわからないってところまで来て、諦めました。

そんなこんなで、WordPressではない別のものでブログを運用したいなぁ、という気持ちが臨界点スレスレまで来てました。

# 静的サイトジェネレータ「Jekyll」

最近「Github Pages+静的サイトジェネレータ」が流行ってるのをずっと横目にしてたので、WordPressからの移行先としては「これしかない！」と思ってました。ただ、静的サイトジェネレータはメジャーなものでも複数あって、どれを選んで良いかわかりません。僕の今回の場合の選択ポイントとしては、以下のような感じでした。

* 慣れてる言語でカスタマイズできること（Rubyとか）
* WordPressからの移行ソリューションがあること
* 先駆者が残したノウハウがネット上に数多く転がっていること
* 開発が活発で、メジャーな部類なもの

これらのポイントで探してたところ、Jekyllが良さそうだなー、という結論に達しました。

## Jekyllのインストール

Jekyll使うぞ！と決めたあと、早速作業に取りかかりました。とにかくこの冬休みが終わる前までに、WordPressではないものに移行を完了しちゃいたかったので、深く理解することはせず、とにかく前に進むって感じで取り組みました。

インストール自体は簡単。既にrbenvでRuby環境は構築済みです。

{% highlight bash %}
$ gem install jekyll
{% endhighlight %}

これだけでJekyllの準備は完了です。ブログ用のひな形を以下のコマンド叩いて生成します。

{% highlight bash %}
$ jekyll new blog
{% endhighlight %}

その後、簡易的なサーバを起動します。

{% highlight bash %}
$ jekyll serve
{% endhighlight %}

http://127.0.0.1:4000/ にアクセスすると、記事がひとつだけ登録されたブログのひな形一式がちゃんと出来上がっていることがわかります。あとはこれらをコツコツといじっていって自分のブログに仕上げていけば良いわけです。

![change_blog_engine_1]({{ "/images/2016/01/change_blog_engine_1.png" | prepend: site.baseurl }})

# qTranslate都合の記述を補正

WordPressに入れていたqTranslateプラグインでいじられてしまっていた記事のタイトルや本文を補正します。具体的には、<!\--:en\-->, <!\--:ja\-->, <!\--:\-->を削除します。これらが挿入されているパターンは3種類あります。

1. <!\--:ja\--> 〜 <!\--:\--><!\--:en\--> 〜 <!\--:\-->
1. <!\--:ja\--> 〜 <!\--:\-->
1. <!\--:en\--> 〜 <!\--:\-->

記事のタイトルと本文は、WordPressのwp\_postsテーブルにあるpost\_title列とpost\_content列にそれぞれ入っています。最初のパターンを以下のSQL文で更新しました。

{% highlight sql %}
UPDATE
  wp_posts
SET
  post_content =
    replace(substring(post_content, locate('<!--:ja-->', post_content) + 10),
            '<!--:-->', '')
WHERE ID IN (select V from (
  select ID as V from wp_posts
    where post_content like '%<!--:ja-->%'
      and post_content like '%<!--:en-->%')
  as temp1)
{% endhighlight %}

上記のSQL文では、1つの投稿で日本語と英語の両方を持っていた場合は、日本語のみの内容にしています。2つ目と3つ目のパターンも、以下のSQL文を実行して変換しておきます。

{% highlight sql %}
update wp_posts set post_content = replace(post_content, '<!--:ja-->', '')
update wp_posts set post_content = replace(post_content, '<!--:en-->', '')
update wp_posts set post_content = replace(post_content, '<!--:-->', '')
{% endhighlight %}

上記について、*post\_content*だけでなく*post\_title*についても同じように実行しておきます。

これで、qTranslateプラグインの汚染を除去できました。

# WordPressからの記事の移行

*new blog*で出力したひな形を修正していく際に、WordPressで書いてきた投稿が移行された状態でやった方が捗りそうだな、と考えました。WordPressのDBから直接投稿内容をJekyllに移行するための便利なツールがあります。それが「[Jekyll-Import](https://github.com/jekyll/jekyll-import)」です。

まず、gemを使ってJekill-Importをインストールします。

{% highlight bash %}
$ gem install jekyll-import
{% endhighlight %}

Jekyll-Importは、ツールというか、Rubyコードから使うライブラリです。僕は以下のようなコードを書いて実行しました。

{% highlight ruby %}
require "jekyll-import"

print "Password: "
password = gets.strip

JekyllImport::Importers::WordPress.run({
  "dbname" => "wordpress", # DB名
  "user" => "root", # DBユーザ名
  "password" => password,
  "host" => "foo.bar.baz", # DBホスト名
  "prefix" => "wp_", # DBの各表のプレフィックス
  "clean_entities" => true,
  "comments" => false,
  "categories" => true,
  "tags" => false,
  "more_excerpt" => false,
  "more_anchor" => false,
  "status" => ["publish"]
})
{% endhighlight %}

これを実行してパスワードを入力すれば、\_postsディレクトリの中にWordPress内の記事が全てファイルとして出力されます。WordPress内で僕はHTMLで記事を投稿していたので、\_postsディレクトリ内にもhtmlファイルとして書き出されます。

## 閉じタグの補正

「WordPressから記事を引っこ抜けたし、あとはデザインしていくのみだぜ！」と思ったのですが、いざ*jekyll build*してみると、なにか変です。

![change_blog_engine]({{ "/images/2016/01/change_blog_engine_2.png" | prepend: site.baseurl }})

h3タグがちゃんと閉じられていません。他にも、記事中でHTMLタグを入れていた部分は、基本全て閉じられていませんでした。具体的には、以下のように閉じタグのスラッシュがエスケープされてしまってました。

{% highlight plain %}
<h3>foo bar baz<&#47;h3>
{% endhighlight %}

このような場合、DBではなくファイルとして記事が存在しているので、一気に文字列置換がしやすいです。以下のコマンドを使って、*&amp;#47;*を/に変換しました。

{% highlight bash %}
$ find . -type f | xargs sed -i.bak "s/&#47;/\//g"
{% endhighlight %}

念のため後で戻せるように.bakファイルでバックアップを取るようにしてましたが、無事変換されたことが確認できた後は、消してしまいます。

{% highlight bash %}
$ find . -name "*.bak" | xargs rm
{% endhighlight %}

## Syntax-Highlighterタグの補正

WordPressの前のMovableTypeの頃から、Syntax-Highlighterを使って、記事中のソースコードに色づけしてきました。特にWordPressでは、Syntax-Highlighterプラグインを使っていたので、記事中には以下のような独自タグを入れていました。

{% highlight html %}
[html]
...
<body>
  <h1>'Allo</h1>
  <input id="source" type="text" />
  <button id="btn">Calculate MD5</button>
  <div id="result"></div>
  <!-- build:js scripts/vendor.js -->
...
[/html]
{% endhighlight %}

実際には、Syntax-Highlighterプラグインが上記の[html]タグを解釈して、色づけをするとともに、エスケープ処理も施されます。しかし、Jekyll-Importで生成されたファイルを見てみると、上記そのままでした。

Jekyllが標準でシンタックスハイライトを持っているので、今後はそれを使うとして、今までの記事は引き続きSyntax-Highlightを使うことにしました。しかし、WordPressのSyntax-Highlightプラグインはもう使えないので、自力で今までの記事に対して問題ない内容にしなければなりません。具体的には、例えば上記のHTML断片は、以下のようになっている必要があります。

{% highlight html %}
<pre class="brush: html">
...
&lt;body&gt;
  &lt;h1&gt;&#39;Allo&lt;/h1&gt;
  &lt;input id=&quot;source&quot; type=&quot;text&quot; /&gt;
  &lt;button id=&quot;btn&quot;&gt;Calculate MD5&lt;/button&gt;
  &lt;div id=&quot;result&quot;&gt;&lt;/div&gt;
  &lt;!-- build:js scripts/vendor.js --&gt;
...
</pre>
{% endhighlight %}

上記の変換処理をやってくれる都合の良いツールなんてないので、自分でRubyコードを書いて一気に変換処理を行うことにしました。そのコードが以下になります。

{% highlight ruby %}
require 'cgi'

start_regexp = /\[((javascript)|(xml)|(html)|(java)|(bash)|(cpp)|(c))\]/

files = Dir.glob '/Users/yoichiro/temp/jekyll/blog/_posts/**/*'
files.each do |file|
  s = File.read(file, :encoding => Encoding::UTF_8)
  while start_pos = s.index(start_regexp, 0) do
    puts file
    keyword = $1
    end_regexp = Regexp.compile('\\[\\/' + keyword + '\\]')
    end_pos = s.index(end_regexp, start_pos)
    org = s[start_pos + keyword.length + 2 ... end_pos]
    org.gsub!(/<p>/, '')
    org.gsub!(/<\/p>/, '')
    org.gsub!(/&lsquo;/, '\'')
    org.gsub!(/&rsquo;/, '\'')
    org.gsub!(/&ldquo;/, '"')
    org.gsub!(/&rdquo;/, '"')
    escaped = CGI.escapeHTML(org)
    wrapped = '<pre class="brush: ' + keyword + '">' + escaped + '</pre>'
    s = s[0...start_pos] + wrapped + s[(end_pos + keyword.length + 3)..-1]
  end
  File.write(file, s)
end
{% endhighlight %}

これで記事についての補正は完了です。

# URL構成の設定

WordPressの時と同じURL構成にするために、*\_config.yml*ファイルに以下を追記しました。

{% highlight python %}
baseurl: "/yoichiro"
url: "https://www.eisbahn.jp"
permalink: /:year/:month/:title.html
{% endhighlight %}

# テンプレート構成

これでWordPressから「おさらば」できたので、あとはJekyllのテンプレートをいじっていじっていじり倒してデザインを入れていけばOKです。テンプレートは、*index.html*ファイル、*\_layouts*ディレクトリと*\_includes*ディレクトリの3つから構成されます。それぞれ以下のファイルを作りました。

* index.html - 記事インデックスページ
* \_layoutsディレクトリ
    * default.html - 全ての元となるテンプレート
    * post.html - 1つの記事のページ
* \_includesディレクトリ
    * head.html - headタグのテンプレート
    * header.html - bodyタグ内の冒頭部分のテンプレート
    * footer.html - bodyタグ内の終わりの部分のテンプレート
    * sidebar.html - 右側部分のテンプレート

これらのテンプレートが組み合わされて、全体のページが構成されます。今回Polymerを使ってみたのですが、例えばpost.htmlによって以下のようなタグ構成ができあがります。

{% highlight html %}
<html>
  <head>
    ...
  </head>
  <body>
    <paper-header-panel>
      <div class="paper-header">
        <div class="layout horizontal">
          <div class="flex main">
            <paper-material elevation="1">
              <paper-toolbar class="medium-tall">
                ...
                <div class="bottom title">{{ page.title }}</div>
              </paper-toolbar>
              <article>
                ...
              </article>
            </paper-material>
          </div>
          <div class="sidebar">
            <div class="layout vertical">
              <paper-card>
                <div class="card-content">
                  ...
                </div>
              </paper-card>
              ...
            </div>
          </div>
        </div>
      </div>
    </paper-header-panel>
  </body>
</html>
{% endhighlight %}

これでPCのChromeで見るとかっこいいんですけど、さっきNexus6で見たら非常に残念なことになってました。

![10583971_10153836236394539_4105311014366597681_n.jpg]({{ "/images/2016/01/10583971_10153836236394539_4105311014366597681_n.jpg" | prepend: site.baseurl }})

たぶんpaper-drawer-panelを使わないといけないと思うんだけど、paper-header-panelの中でpaper-drawer-panelを使えない（使うとpaper-header-panelが正しく機能しなくなる）って現象が出てしまいました。要修正です。

# まとめ

今年の冬休みの宿題にしてたブログリニューアルは、とりあえず最低限の状況まではできました。今後のんびりと改良していきたいと思ってます。
