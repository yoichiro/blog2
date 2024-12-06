---
layout: post
status: publish
published: true
title: ActiveResourceで拡張子なしのURIを発行する方法
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 513
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=513
date: '2008-01-31 21:21:59 +0900'
date_gmt: '2008-01-31 12:21:59 +0900'
categories:
- Ruby on Rails
---

Ruby on Rails 2.0から標準搭載されたActiveResource。これを使うと、RESTful APIをActiveRecordのように叩くことができるようになる。自分でURIを作ってopenすることもせず、レスポンスを自分でパースしてオブジェクトを作ることもせず、数行の記述でRESTfulなサービスを利用することが可能になる優れものだ。ActiveResourceの詳細は、
[ここ](http://www.eisbahn.jp/yoichiro/2007/11/active_resourcereadme.html)を見て欲しい。

さて、現状のActiveResourceは、僕にとって一つ気になる点がある。それは「URIが必ず拡張子付きで発行される」という仕様。例えば、

class Community < ActiveResource::Base
self.site = 'http://commusuke.eisbahn.jp/api/'
end

として、「Community.find(1)」と実行すると、

>GET http://commusuke.eisbahn.jp/api/communities/1.xml


というURIでRESTfulサービスにアクセスしにいく。つまり、接尾語として「.xml」が自動的に付加される。これにより「レスポンスはXML形式で返してね」という意思表示になる。ちなみにこの拡張子は、format=メソッドを使って変更することができる。例えば、

class Community < ActiveResource::Base
self.site = 'http://commusuke.eisbahn.jp/api/'
self.format = :json
end

としてあげて「Community.find(1)」と実行してあげれば、

>GET http://commusuke.eisbahn.jp/api/communities/1.json


というURIが発行され、「JSON形式で返してね」という意思表示を行うことができる、という機構。

では「.xml」も「.json」も付けず、

>GET http://commusuke.eisbahn.jp/api/communities/1


というURIでアクセスを行いたいとする。もちろんRESTfulサービスの中には、拡張子付きのリクエストを受け付けず、固定でXML形式のレスポンスを返してくる、という局面も存在するわけである。しかし、現状のActiveResourceでは、必ず何らかの拡張子を付けてURIを組み立てる、というコーディングになっていて、つまり拡張子付きを受け付けないRESTfulサービスを利用することはできない、ということになっているのだ。ちなみに、「self.format = nil」とかやってもダメ。

では、拡張子がないURIに対応するためのパッチを書くことができるかどうか、やってみることにした。結論から言うと、拡張子をつけないようにするこことができた。以下に、そのためのコードを紹介してみる。

まず、「self.format = :json」というように指定した結果、ActiveResource::Formatsモジュールに属しているフォーマッタモジュールが実際には適用される。XML形式とJSON形式が標準で搭載されていて、

* XML形式 - ActiveResource::Formats::XmlFormat

* JSON形式 - ActiveResource::Formats::JsonFormat

という対応になっている。上記のフォーマッタモジュールでは、下記のメソッドがそれぞれ定義されている。

* extension - 拡張子を返す。

* mime_type - MIME種別を返す。

* encode - 受け取ったオブジェクトをエンコード(XMLやJSONに変換)する。

* decode - 受け取った文字列をデコード(ハッシュに変換)する。

まずは、XML形式なんだけど拡張子はなし、というモジュールを自作する。XmlFormatモジュールを継承(?)して作ってみる。XmlFormatのメソッドはそのままに、extensionメソッドのみ再定義してnilを返却するようにしている。これは、例えば「app/controller/application.rb」ファイルの最後に書いておけば良いだろう。

module ActiveResource
module Formats
module XmlNotExtensionFormat
include ActiveResource::Formats::XmlFormat
def extension
nil
end
extend self
end
end
end

format=メソッドに渡すシンボル(:xmlとか:jsonとか)は、camelizeされたものに「Format」が付けられた名前(:xmlならXml+Format)のモジュールが適用されるようになっている。つまり、上記のXmlNotExtensionFormatモジュールを使用したい場合は、

class Community < ActiveResource::Base
self.site = 'http://commusuke.eisbahn.jp/api/'
self.format = :xml_not_extension
end

というように指定してあげれば良い。これにより、拡張子が取り除かれたURIが作られる。

・・・と思いきや、世の中そんなに甘くなかった。extensionメソッドがnilを返したことによって「xml」という文字は取り除かれるようになるのだが、「.」が残ってしまう。つまり、

>GET http://commusuke.eisbahn.jp/api/communities/1.


となってしまうのだ。惜しい。実に惜しい。

URIを構築しているのは、ActiveResource::Baseクラスに定義されているelement_pathおよびcollection_pathという2つの特異メソッドである。例えばelement_pathメソッドのコードを見てみると、非常に残念なコードであることがわかる。

def element_path(id, prefix_options = {}, query_options = nil)
prefix_options, query_options = split_options(prefix_options) if query_options.nil?
"#{prefix(prefix_options)}#{collection_name}/#{id}.#{format.extension}#{query_string(query_options)}"
end

これでは「.」を取り除くことがフォーマッタモジュールの頑張りだけでできないことがわかってもらえるだろう。解決策としては、これら2つのメソッドを置き換えるしかない。具体的には、

ActiveResource::Base.class_eval do
class << self
def element_path(id, prefix_options = {}, query_options = nil)
prefix_options, query_options = split_options(prefix_options) if query_options.nil?
"#{prefix(prefix_options)}#{collection_name}/#{id}.#{format.extension}#{query_string(query_options)}" if format.extension
"#{prefix(prefix_options)}#{collection_name}/#{id}#{query_string(query_options)}" if !format.extension
end
def collection_path(prefix_options = {}, query_options = nil)
prefix_options, query_options = split_options(prefix_options) if query_options.nil?
"#{prefix(prefix_options)}#{collection_name}.#{format.extension}#{query_string(query_options)}" if format.extension
"#{prefix(prefix_options)}#{collection_name}#{query_string(query_options)}" if !format.extension
end
end
end

というコードを、例えば先ほどと同じように「app/controller/application.rb」ファイルの最後に記述してあげれば良い。これにより、適用されたフォーマッタモジュールのextensionメソッドがnilを返したときは、拡張子も「.」もないURIが発行されるようになる。めでたしめでたし。

もし「.xmlとか.json付けられないのにActiveResourceでアクセスしなきゃならねーんだよ」的なことになったら、上記のコードを利用してみて欲しい。
