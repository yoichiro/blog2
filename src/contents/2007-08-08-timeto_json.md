---
layout: post
status: publish
published: true
title: Timeオブジェクトに対するto_jsonメソッド呼び出し
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 428
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=428
date: '2007-08-08 06:23:05 +0900'
date_gmt: '2007-08-07 21:23:05 +0900'
categories:
- Ruby on Rails
---

Ajaxを多用したアプリケーションにおいても，Ruby on Railsの採用が今後どんどん増えていくと考えられる。Ajaxと言えば，言葉の通りXMLを連想するかもしれない。しかし，サーバから受け取った後の処理効率を考えると，JSONでやり取りを行ったほうがトータルで都合が良い。近年では，XMLではなくJSONを利用する機会が多いと考えられる。

Ruby on Railsの1.2から，全オブジェクトに対してto_jsonメソッドを呼び出すことができるようになった。これは，active_supportが提供してくれる機能である。例えば，

employee = {:name => 'Yoichiro', :age => 32}
json = employee.to_json

としてあげれば，"{name: \"Yoichiro\", age: 32}"という結果を得ることができる。非常に便利である(速度に問題があるらしいけど)。

さて，これを書いている時点での最新であるRails 1.2.3では，to_jsonメソッドが全てにおいて万能かというと，残念ながらそうではない。実は，to_jsonメソッドを呼び出した結果を正しく受け取れるレシーバのクラスに制限があるのだ。これは，activesupport-1.4.2/lib/active_support/json/encodersディレクトリにあるcore.rbファイルの中身を見てみると良い。

module ActiveSupport
module JSON #:nodoc:
module Encoders #:nodoc:
define_encoder Object do |object| ・・・ end
define_encoder TrueClass do ・・・ end
define_encoder FalseClass do ・・・ end
define_encoder NilClass do ・・・ end
define_encoder String do |string| ・・・ end
define_encoder Numeric do |numeric| ・・・ end
define_encoder Symbol do |symbol| ・・・ end
define_encoder Enumerable do |enumerable| ・・・ end
define_encoder Hash do |hash| ・・・ end
define_encoder Regexp do |regexp| ・・・ end
end
end
end

正しく結果が得られるのは，TrueClass, FalseClass, NilClass, String, Numeric, Symbol, Enumerable, Hash, Regexpのみである。一般的なアプリケーションなら，これ以外にも多用しているクラスがあるはずだ。例えば，Timeクラスのオブジェクト。これはサポート外である。つまり，

Time.now.to_json

としても，得られる結果は"{}"である。なんとも悲しい。

この問題は，RailsのTracにしっかりと登録されていて，既にパッチが提供されている(id:tonocchiさん，
[情報](http://twitter.com/tonocchi/statuses/191360232)さんきゅです！)。このチケットがcloseになったのは，たった3ヶ月前。Railsの次のバージョンになればきっとTimeクラスに対するサポートも含まれることだろう。しかし，現状ではそのパッチをあてるか，動的になんとかするしかない。

[Ticket #5140 (closed defect: fixed)](http://dev.rubyonrails.org/ticket/5140) - Rails Trac

というわけで，パッチをあてることなく，Timeクラスのオブジェクトに対してto_jsonメソッドが正しい結果を返すようにするコードが以下である。

ActiveSupport::JSON::Encoders::define_encoder(Time) do |time|
time.to_i.to_json
end

先のcore.rbファイルでも使われているdefine_encoderメソッドを使って，自分でエンコード処理を追加登録している。上記のコードを，例えばapplication.rbファイルの中に書いておけば良いだろう。例えばHashオブジェクトの中にTimeクラスのオブジェクトがいるときにも，登録されたTimeクラスに対する処理(time.to_i.to_json)が実行され，日時を示す値がJSON文字列に格納されるようになる。

このテクニックはTimeクラスに限ったものではなく，ほかのクラスに関しても適用することができる。独自にJSON文字列の構築を処理したい場合などに活用すると良いだろう。
