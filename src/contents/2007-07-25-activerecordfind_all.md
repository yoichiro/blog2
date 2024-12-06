---
layout: post
status: publish
published: true
title: ActiveRecordに置き換えられたfind_allメソッド
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 416
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=416
date: '2007-07-25 16:20:55 +0900'
date_gmt: '2007-07-25 07:20:55 +0900'
categories:
- Ruby on Rails
---

ActiveRecordは，Rubyの動的な性質を存分に使って作られた，非常に利便性の高いライブラリだと思う。ActiveRecordをJavaにコピーすることは，きっとできないだろう。しかし，時に初心者(今の僕)は，その動的な特徴故に，期待した通りの動きにならずに苦戦することが多い。

ActiveRecordを使えば，モデル間の関連とそれに関する操作を数行で定義することができる。例えば，部署を表すDivisionモデルと，部署に所属する社員を表すEmployeeモデルがあった場合，

class Division < ActiveRecord::Base
has_many :employees
end
class Employee < ActiveRecord::Base
belongs_to :division
end

としてあげれば，

div = Division.find(1)
div.employees.each do |emp|
...
end

というように，id:1の部署に所属する社員のEmployeeモデル群を簡単に取得し，走査することが可能である。

さて，上記のように取得したEmployeeモデル群の中で，例えば退職した社員のみを抽出したいとする。まず，div.employeesが何者なのかを確認してみる。

div = Division.find(1)
p div.employees.class

結果は，

>Array


となり，Arrayオブジェクトであることがわかる。そうとなれば，各Employeeオブジェクトのretireの値を見て抽出すればいいと考えた。

div = Division.find(1)
retires = div.employees.find_all {|emp| emp.retire == true}

上記のコード，実はこれでは期待した結果にならない。抽出は行われず，employeesで得られるEmployeeオブジェクト群がそのまま結果となる。つまり，「retires = div.employees」とされたに過ぎない。

では，find_allメソッドが何者なのかを見てみたくなり，

div = Division.find(1)
m = div.employees.method(:find_all)
p m.inspect

としたが，その結果は，

>"#
"


となり，Enumerable#find_allメソッドが使われているように思える。なんでー！？と思い，別の手段を探すと，Enumerable#find_allメソッドは，Enumerable#selectメソッドを使っても同様の処理結果が得られるはず。実際に，

div = Division.find(1)
retires = div.employees.select {|emp| emp.retire == true}

とやってみると，見事に期待通りの結果を得ることができた。

さて，なんでこんなことになってしまうのか，それはActiveRecordのドキュメントを見ることで判明した。

「
[Module: ActiveRecord::Associations::ClassMethods](http://techno.hippy.jp/apidoc/classes/ActiveRecord/Associations/ClassMethods.html)」

>  class Project < ActiveRecord::Base
    belongs_to :portfolio
    has_one :project_manager
    has_many :milestones
    has_and_belongs_to_many :categories
  end

プロジェクトクラスはこれで、以下のメソッド (とその他) を持つ事になり、関連を簡単に走査したり処理したりできます:

  * ・・・
  * ・・・, Project#milestones.find_all(conditions), 
  * ・・・


ActiveRecordが提供するhas_manyメソッドの処理結果として，動的にfind_allメソッドが追加される。つまり，既存のEnumerable#find_allメソッドは，ActiveRecordによって置き換えられていたのである。そのために，「div.employees.find_all {...}」の呼び出しが期待通りの動きにならなかった，ということだったのだ。

例えば，素直にActiveRecord提供のfind_allメソッドを使うとすれば，

div = Division.find(1)
retires = div.employees.find_all('retire = 1')

というふうにすれば良い。ただし，もちろんこの場合はデータベースに問い合わせに行ってしまう(キャッシュが効くだろうけど)。

Rubyの動的な特徴を見せつけられたような気分になった。とっても面白いけど，はまってしまうと原因がわかるまで訳わからん。。。
