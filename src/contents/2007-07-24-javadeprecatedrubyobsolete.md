---
layout: post
status: publish
published: true
title: JavaでいうdeprecatedはRubyではobsoleteというらしい
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 414
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=414
date: '2007-07-24 15:30:35 +0900'
date_gmt: '2007-07-24 06:30:35 +0900'
categories:
- Ruby on Rails
---

遥か昔に制定した法律や決め事が今日のご時世に合わずに支障を来しているように，プログラムにおいても，過去に作ったものについて現在使って欲しくないという状況がしばしば起きる。実際のプログラミング環境では，「使って欲しくない」という作者の意図が何らかの形でプログラマに伝わらなければならない。

Javaでは，ソースコードに"deprecated"というキーワードを埋めておくことで，プログラマに「このメソッドは非推奨だから使ってはいけません！」と意思表示することができる。例えば，こんな感じ。

/**
* @deprecated
* @see #getPreferredSize
*/
@Deprecated
public Dimension preferredSize() {
...
}

これによって，preferredSizeメソッドの使用が非推奨であることが，

* コンパイル時に警告メッセージとして表示される。

* ドキュメントにdeprecatedであることが書き込まれる。

という２つの効果としてプログラマに示されるようになる。

言語やライブラリの進化に伴い，どんなものでも使って欲しくない過去の遺物はどうしても生み出されてしまう。Rubyにおいても，そのようなクラスやメソッドがあるらしい。

「
[過去の遺物](http://www.ruby-lang.org/ja/man/?cmd=view;name=obsolete;em=obsolete)」- Rubyリファレンスマニュアル

Javaでは"deprecated"と言っていたが，Rubyでは"obsolete"(すたれた，時代遅れの)と呼んでいるらしい。Javaと同じように，Rubyにも使って欲しくない，すなわちobsoleteなことをプログラマに伝えるための予約語なり機構が備わっているのではないか？と思ってしまったが，良く考えると「Rubyにはコンパイルという工程がない」ため，Javaと同じように考えてはいけない(IDEサポートは別)。つまり，Rubyでは，obsoleteなことを，

* 実行時に警告メッセージとして表示される。

* ドキュメントにobsoletedであることを書き込む。

という２つの効果でプログラマに示すことになる。

具体的には，実行時にobsoletedなメソッドが呼び出された際に警告メッセージが表示されるように，warn組み込み関数でメソッドの先頭に警告メッセージの表示処理を仕込んでおき，rdocでobsoleteなことが書かれるようにコメント文に記述しておく，という手法となる。例えば，

# Component#preferred_size is *obsoleted* at 1.8.1. Use #get_preferred_size.
def preferred_size
warn 'Component#preferred_size is obsoleted. Use #get_preferred_size.'
...
end

という感じである。クラスの場合は実行コードを記述できないため，コメント文のみの記述となる。

Javaと違って，Rubyの場合は「ドキュメントを読む」か「動かしてみる」ことをしなければ，時代遅れのメソッドを使ってしまったかどうかわからない。また，動かしてみた際に，ログの出力内容を確認しなければ，時代遅れのメソッドを使ってしまったとしても気がつかないことになる。この辺はコンパイルという静的チェックがあるJavaの優位なところと言えるのかもしれない。まぁ，Rubyでの開発の場合は，ガンガン動かして動作確認をしながら開発を進めていくスタイルなので，"obsolete"という単語をログ監視してさえすれば，全体の開発効率は良いものとも言えるだろう。
