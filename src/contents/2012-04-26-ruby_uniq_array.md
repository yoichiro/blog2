---
layout: post
status: publish
published: true
title: Rubyで重複しない結果を得るための効率的な方法
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1413
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1413
date: '2012-04-26 09:57:48 +0900'
date_gmt: '2012-04-26 00:57:48 +0900'
categories:
- Ruby on Rails
---

今まで「Rubyのuniqは遅い」という思い込みをしていました。ある配列から重複を取り除いた配列を作り出すためにはいくつかの方法が考えられますが、何が効率的なのか、実測してみました。

試した方法は以下の4つです。

* Array#uniq

* Array#uniq!

* Set

* Hash

それぞれのコードは最後に掲載するとして、さっそく実測値です。使ったマシンはmacbook air 11'。CPUは1.6GHz Intel Core 2 Duo。メモリは4GBです。rubyのバージョンは以下になります。

>yoichiro$ ruby -v
ruby 1.9.2p180 (2011-02-18 revision 30909) [x86_64-darwin11.3.0]


それぞれ5回実行して、その平均を比べてみました。0〜99までの数値を100万個持つ配列を作り、それから重複を排除した配列を求めます。

![](http://www.eisbahn.jp/yoichiro/images/2012/04/table.png)

最後の行が平均値です。それをグラフで並べると、差ははっきり見て取れます。

![](http://www.eisbahn.jp/yoichiro/images/2012/04/graph.png)

uniqが遅いなんて言ってごめんなさいごめんなさい。今では、ある配列から重複を取り除いた配列を得たい場合は、素直にuniqを使うべきですね。

追記： 2012/05/28
コメントにあるように、確かにhashに有利なデータセットでのみの試験となってしまってました。深く反省いたします。。。

実際に測ってくれた結果をグラフにしたのが以下となります。

![](http://www.eisbahn.jp/yoichiro/images/2012/04/graph1.png)

これからわかることは、要素数(上記の場合は100,000個)に占める重複の割合が1割を下回ってる場合はuniqメソッドの方が速く、1割を超えている場合はhashを使ったほうが速い、という結果でした。

追記ここまで

最後にそれぞれのコードを掲載します。もしフェアな比較になってないなどあれば、コメントください。

uniq
```
source = Array.new
1000000.times do |i|
  source << rand(100)
end

# Start
s = Time.now

result = source.uniq

# End
e = Time.now

puts e - s
puts result.length
```

uniq!
```
source = Array.new
1000000.times do |i|
  source << rand(100)
end

# Start
s = Time.now

source.uniq!

# End
e = Time.now

puts e - s
puts source.length
```

set
```
require 'set'

source = Array.new
1000000.times do |i|
  source << rand(100)
end

# Start
s = Time.now

result = Set.new
source.each do |i|
  result << i
end

# End
e = Time.now

puts e - s
puts result.length
```

hash
```

source = Array.new
1000000.times do |i|
  source << rand(100)
end

# Start
s = Time.now

hash = {}
source.each do |i|
  hash[i] = 1
end
result = hash.keys

# End
e = Time.now

puts e - s
puts result.length
```
