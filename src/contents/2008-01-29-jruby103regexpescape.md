---
layout: post
status: publish
published: true
title: JRuby-1.0.3のRegexp#escapeメソッドは文字コードを指定できない
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 510
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=510
date: '2008-01-29 22:53:55 +0900'
date_gmt: '2008-01-29 13:53:55 +0900'
categories:
- Ruby on Rails
---

JRubyの現在の正式な最新バージョンは
[1.0.3](http://docs.codehaus.org/display/JRUBY/2007/12/15/JRuby+1.0.3+Released)である。しかし、
[1.1RC1](http://docs.codehaus.org/display/JRUBY/2008/01/08/The+JRuby+community+is+pleased+to+announce+the+release+of+JRuby+1.1+RC+1)も登場しているので、1.1の正式版が登場するのは時間の問題だろう。安定版ということで1.0.3を使いたくなるし、多くの場合は1.0.3で事足りるだろう。しかし、今日僕は1.0.3で小一時間ハマってしまった。

そろそろ
[こみゅすけ](http://commusuke.eisbahn.jp)の携帯版を作ろうと思い、
[jpmobile](http://jpmobile-rails.org/blog)というプラグインをRails 2.0にインストールしていろいろと遊んでみることを始めようとした。もちろんJRuby-1.0.3のgemでRails 2.0.2をインストールし、railsコマンドでプロジェクトファイル一式を生成、そしてscript/plugin installでjpmobileをインストールして、script/serverでいつものようにWEBrickを起動しようとした。

しかし、悲しいことに、エラーメッセージが表示されてしまい、WEBrickは起動しない。

>=> Booting WEBrick...
/Applications/java/jruby/jruby-1.0.3/lib/ruby/gems/1.8/gems/activesupport-2.0.2/lib/active_support/dependencies.rb:499:in `require': wrong number of arguments(2 for 1) (ArgumentError)
　　from /Applications/java/jruby/jruby-1.0.3/lib/ruby/gems/1.8/gems/rails-2.0.2/lib/commands/server.rb:39
　　from /Applications/java/jruby/jruby-1.0.3/lib/ruby/site_ruby/1.8/rubygems/custom_require.rb:27:in `require'
　　from /Applications/java/jruby/jruby-1.0.3/lib/ruby/site_ruby/1.8/rubygems/custom_require.rb:27:in `require'
　　from :1


調べてみると、vendor/plugins/jpmobile/lib/jpmobile/emoticon/z_combine.rbファイルの中でエラーが発生していることがわかった。具体的には、以下の行で引数の個数が合わない、というエラーが発生していた。

SJIS_REGEXP = Regexp.union(*SJIS_TO_UNICODE.keys.map{|s| Regexp.compile(Regexp.escape([s].pack('n'),"s"),nil,'s')})

この行のどこがエラーになっているのか、script/consoleを使って確かめてみる。すると、

>> p Regexp.escape('abc', 's')
ArgumentError: wrong number of arguments(2 for 1)
・・・
Maybe IRB bug!!
>>

というように、エラーを再現させることに成功。なんと、Regexpクラスのescapeメソッドが引数を２つ受け付けない、ということが判明した。確かに、JRubyのソースコードを追っていったところ、RubyRegexp.javaファイルにおいて、

regexpClass.getMetaClass().defineFastMethod("escape", callbackFactory.getFastSingletonMethod("quote", RubyString.class));

という記述しか見つからず、文字列一つを受け取るescapeメソッドしか定義されていない。

ここで、Regexpクラスのescapeメソッドは、quoteメソッドと等価であることに気がつく。では、quoteメソッドも同じなのか？

>> p Regexp.quote('abc', 's')
"abc"
=> nil
>> (irb):1 warning: JRuby supports only Unicode regexp.

一瞬「お、quoteメソッドは大丈夫じゃん」と思ったのもつかの間、なんとUnicodeしかサポートしないよ、と警告されてしまった。これじゃあ第2引数を渡しても、実質意味がない。

まとめると、JRuby-1.0.3では、

* Regexp#escapeメソッドでは、渡した正規表現の文字コードを指定できない。

* Regexp#quoteメソッドを使用して文字コードを指定しても、そもそもUnicodeしかサポートされない。

ということであり、自分でRubyRegexp.javaファイルをいじるなりしなければ「GAME OVER」という結論になってしまった。このメソッドが使用される局面は、普通のWebアプリケーションではそんなにないだろうけど、少なくともjpmobileを使った携帯サイトは、JRuby-1.0.3では構築できない。当然だけど、Rails 1.2.6でもNGであり、CRubyの1.8.6ではちゃんと動作する。

ちょっと悔しいので、JRuby-1.1RC1でもダメなのか？という検証を行ってみた。結論としては、上記の問題は解決されていて、Regexp#escapeメソッドとRegexp#quoteメソッドの両方とも、第2引数をちゃんと受け取るし、指定した文字コードが正しく処理に反映されているようだ。JRuby-1.1RC1、Rails 2.0.2、そしてjpmobileのtrunkという組み合わせで、ちゃんとWEBrickも起動するし、「mobile_filter :hankaku => true」として半角カナ変換も機能している。絵文字は試してないけど、たぶん大丈夫だろう。

というわけで、JRuby-1.0.3は、文字コードの扱いが非常に惜しいレベルであることが判明してしまった。が、JRubyコミュニティも1.1シリーズに今後力を注いでいくだろうから、もしJRubyでRailsするなら、すでに1.1系を使っていくことが現実解だということなのだろう。
