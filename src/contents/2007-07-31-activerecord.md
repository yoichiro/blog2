---
layout: post
status: publish
published: true
title: ActiveRecordの属性に「?」で問い合わせる
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 425
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=425
date: '2007-07-31 22:11:16 +0900'
date_gmt: '2007-07-31 13:11:16 +0900'
categories:
- Ruby on Rails
---

ActiveRecordオブジェクトは，

class Employee < ActiveRecord::Base
end

とするだけで，上記で言えばemployees表が持つ各種列に対応する属性が利用可能になる。例えば，employees表にname列が定義されていたとすれば，

employee.name = 'Yoichiro'
p employee.name

というように，nameアクセッサを利用することができる。とても簡単だ。では，

employee.name?

とemployeeオブジェクトにメッセージを投げることが可能である人は，どれだけいるだろうか？もちろん僕も，rails-mlに「
[[rails:2302] ActiveRecordオブジェクトの属性にクエスチョンをつけると・・・？](http://www.fdiary.net/ml/rails/msg/2302)」という投稿があるまで，知らなかった。

では，「#{attr_name}?」で問い合わせた場合，その仕様はどうなっているのだろうか？そしてその実装コードはどこにあるのだろうか？ActiveRecordのコードを追ってみた。

これ関係が処理されているのは，「activerecord-1.15.3/lib/active_record/base.rb」ファイルの中である。base.rbファイルの1842行目付近にあるmethod_missingメソッドが起点となる。このメソッドの最初で，渡されたメッセージ(=メソッド名)の最後が"?"で終わっていた場合は，query_attributeメソッドを呼び出している。具体的には，

def method_missing(method_id, *args, &block)
method_name = method_id.to_s
if @attributes.include?(method_name) or
(md = /\?$/.match(method_name) and
@attributes.include?(query_method_name = md.pre_match) and
method_name = query_method_name)
define_read_methods if self.class.read_methods.empty? && self.class.generate_read_methods
md ? query_attribute(method_name) : read_attribute(method_name)
elsif ・・・

というコードである。もうちょっと詳しく説明すると，

* ActiveRecord::Base::generate_read_methodsクラス変数がfalseだった場合は，「#{attr_name}?」呼び出しの都度，上記のmethod_missingメソッドが呼び出されて，結果query_attributeメソッドが呼び出される。

* ActiveRecord::Base::generate_read_methodsクラス変数がtrueだった場合は，初回の「#{attr_name}?」呼び出しの際に，define_read_methodsメソッドの実行により「#{attr_name}?」メソッドが動的に追加され，その後にquery_attributeメソッドが実行される。2回目以降の呼び出しの際には，method_missingメソッドが呼び出されずに，動的に追加された「#{attr_name}?」メソッドが直接実行される。

というような動作になる。後者の場合に動的に追加されたメソッドは，例えば#{attr_name}がnameだった場合は，

def name?
query_attrbiute('name')
end

というメソッドが追加されることになる。

さて，最終的に呼び出されるquey_attributeメソッドは，同じくbase.rbファイルに記述されている。

def query_attribute(attr_name)
attribute = @attributes[attr_name]
if attribute.kind_of?(Fixnum) && attribute == 0
false
elsif attribute.kind_of?(String) && attribute == "0"
false
elsif attribute.kind_of?(String) && attribute.empty?
false
elsif attribute.nil?
false
elsif attribute == false
false
elsif attribute == "f"
false
elsif attribute == "false"
false
else
true
end
end

これを見れば，「#{attr_name}?」メソッドがblank?メソッドやempty?メソッドとは違う挙動なことがわかるだろう。例えば，"0"という属性値だった場合にfalseが返却されるのは特徴的である。このルールをまとめると，

* 属性値がFixnumまたはそのサブクラスのインスタンスでしかも0だった場合は，false

* 属性値がStringまたはそのサブクラスのインスタンスでしかも"0"だった場合は，false

* 属性値がStringまたはそのサブクラスのインスタンスでしかも長さが0だった場合は，false

* 属性値がnilだった場合は，false

* 属性値が真偽値でfalseだった場合は，false

* 属性値が文字列で"f"だった場合は，false

* 属性値が文字列で"false"だった場合は，false

* 上記のどれにも当てはまらなかった場合は，true

となる。

Railsのコード群を追うことで，Rubyの動的な性質を生かした非常に面白いコードを目にすることができる。今までJavaをやってきた僕にとっては，かなり新鮮な世界だ。そして，Ruby on Railsを初めて1ヶ月も経たない僕が，ライブラリのコードまで追えるようになっている点も，Rubyの文法が人に優しいものであることを示していると思う。
