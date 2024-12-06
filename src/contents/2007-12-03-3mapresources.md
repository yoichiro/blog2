---
layout: post
status: publish
published: true
title: "リソースが3段以上ネストしたときのmap.resourcesの書き方"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 476
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=476
date: '2007-12-03 22:31:56 +0900'
date_gmt: '2007-12-03 13:31:56 +0900'
categories:
- Ruby on Rails
---

RESTfulなサービスを実装するとして、対象リソースがその他のリソースとの従属（親子）関係があるときに、Ruby on Railsではmap.resourcesをネストして記述することが可能になっている。例えば、部署(Division)に所属する社員(Employee)のリソース定義であれば、

map.resources :divisions, :path_prefix => '/api' do |divisions|
divisions.resources :employees, :controller => 'api/employees'
end

とすることで、「GET http://localhost:3000/api/divisions/1/employees/2」というURLにApi::EmployeesControllerクラスをマッピングすることができる。この場合、show()メソッドが呼び出され、部署IDと社員IDは、

# 部署ID
div_id = params[:division_id]  # => 1
# 社員ID
emp_id = params
