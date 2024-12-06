---
layout: post
status: publish
published: true
title: updated_atを自動でセットしないようにする方法
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 418
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=418
date: '2007-07-26 12:07:03 +0900'
date_gmt: '2007-07-26 03:07:03 +0900'
categories:
- Ruby on Rails
---

データベースに格納される情報は，「作成日時」や「更新日時」を付与することが多い。ActiveRecordでは，作成日時や更新日時について，暗黙的にセットする機構が標準で備わっている。例えば，

class CreateEmployees < ActiveRecord::Migration
def self.up
create_table :employees do |t|
・・・
t.column :created_at, :timestamp
t.column :updated_at, :timestamp
end
end
・・・
end

というように表を作成しておけば，created_atにinsertされたときの作成日時が，updated_atにはupdateがかかる度にその時の日時が更新日時としてセットされる。プログラマが改めて「emp.updated_at = Time.now」などとしなくても済むため，非常に便利である。

そんな便利なこの機構，テストの時に問題となる場合がある。例えば，Employeeモデルが以下のように定義されていたとする。

class Employee < ActiveRecord::Base
def self.last_updated_employee
find(:first, :order => 'updated_at DESC')
end
end

last_updated_employeeメソッドを呼び出すと，最後に更新されたEmployeeオブジェクトを1件取得できる。さて，これをテストすることを考えるのだが，ここではfixtureに初期データを記述するのではなく，テストメソッド内で初期データを生成するようにしてみる。

def test_last_updated_employee
emp1 = Employee.new(...)
assert emp1.save
emp2 = Employee.new(...)
assert emp2.save
result = Employee.last_updated_employee
assert_equal emp2.reload.id, result.id
end

それぞれsaveメソッドを呼び出してemployees表にinsertしている。ここで期待しているのは，last_updated_employeeで最後に登録されたemp2オブジェクトが結果として得られること。しかし，mysqlではTIMESTAMP型は秒までしか保持されないために，このように連続したsaveであれば，日時が一緒になってしまうことも十分考えられる(ほとんど一緒になると思う)。そうなると，emp1とemp2のどっちが結果として得られるかは，updated_atとは別の理由となってしまうために，上記のコードではテストにならない。

ではどうしたら良いかというと，自動的にupdated_atの値をセットしてもらうのではなく，テストコード内で明示的にセットしてあげれば良い。しかし，単に「emp1.updated_at = Time.now」としただけでは，saveメソッドの呼び出し時に暗黙的に上書きされてしまう。何とかしてupdated_atの自動更新処理をOFFにしないといけない。

ActiveRecordでは，このような要求に対応するために，ちゃんとOFFにする機構が提供されている。これは，

「
[Module: ActiveRecord::Timestamp](http://api.rubyonrails.com/classes/ActiveRecord/Timestamp.html)」

で説明されているとおり，record_timestampsをfalseにしてあげれば，updated_atの自動セットが行われなくなる。つまり，

def test_last_updated_employee
ActiveRecord::Base.record_timestamps = false
emp1 = Employee.new(...,
:updated_at => Time.now)
assert emp1.save
emp2 = Employee.new(...,
:updated_at => Time.now.tomorrow)
assert emp2.save
result = Employee.last_updated_employee
assert_equal emp2.reload.id, result.id
ActiveRecord::Base.record_timestamps = true
end

としてあげれば，明確にupdated_atが1日ずれて保存されるために，正しいテスト結果を得ることができるようになる。

ここで注意しなければならない点として，

* ActiveRecord::Baseに対して指定しているように，全てのモデルに対するupdated_atの暗黙的更新処理が行われなくなる。

* 後始末としてちゃんとフラグを元に戻してあげないと，他のテストメソッドの動作に影響が出てしまう。

があげられる。特に後者を忘れてしまうと，非常に原因としてわかりづらい問題として他のテストメソッドで発覚することになるので，フラグの上げ下げは対で最初に記述するのが良いだろう。
