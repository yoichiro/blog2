---
layout: post
status: publish
published: true
title: options_form_collection_for_selectの初期選択が効かない
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 413
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=413
date: '2007-07-24 08:49:28 +0900'
date_gmt: '2007-07-23 23:49:28 +0900'
categories:
- Ruby on Rails
---

アプリケーションでは，データベース内に格納された各種マスタの一覧から１つ選択する，という行為が良く行われる。Webアプリケーションにおいて，マスタの内容を一覧表示する際は，selectタグとoptionタグの組み合わせが多く使われる。Ruby on Railsのactionpackに含まれるaction_viewヘルパーには，あるモデルの集合からoptionタグのセットを生成してくれる便利な機能が提供されている。それが，options_from_collection_for_selectメソッドである。

options_from_collection_for_selectメソッド(やたらと長い)の基本的な使い方として，例えばnameとuser_idという両方とも文字列の属性を持つUserクラスのインスタンスの集合@usersがあったとき，



とすることで，name属性を表示文字列に，user_id属性をvalue値として，optionタグを作ってくれる。さらに，リクエストパラメータ:user_id値で初期選択をさせておきたければ，



というように，options_from_collection_for_selectメソッドに引数を加えれば，その値をvalue値に持つoptionタグにselected属性を付与してくれるようになる。

さて，昨日1時間以上はまったことなのだが，options_from_collection_for_selectメソッドでActiveRecordを使ったモデルの集合を表示する際，なぜか初期選択が有効にならない事象に出くわした。

ActiveRecordのモデルはid属性を持つため，options_from_collection_for_selectメソッドを使用する場合にもid属性値をvalue属性値として採用することがほとんどだろう。つまり，ActiveRecord::Baseクラスを継承し，id属性とname属性を持つCategoryモデルクラスがあったとき，このモデルのインスタンスの集合@categoriesをoptions_from_collection_for_selectメソッド(長い！)を使って表示させようとすると，



としたくなる。もちろんこれでoptionタグが生成されるのだが，何らかの選択を行ったとしても，params[:category_id]と指定した初期選択は有効にならず，1つ目の選択肢が選択された状態に戻ってしまうだろう。

この原因，実は型の違いが影響している。params[:category_id]で得られる値は文字列型なのだが，ActiveRecord::Baseを継承したCategoryモデルのid属性は数値型なのだ。options_from_collection_for_selectメソッドの中では基本的に「element == selected」で比較が行われるため，数値型と文字列型の==演算の結果がfalseとなってしまい初期選択がされなくなる，という原理だった。

上記の例が正しく動作するためには，params[:category_id]値を数値型に変換してoptions_from_collection_for_selectメソッドに渡してあげれば良い。



この原因がわかったのは，options_from_collection_for_selectメソッドのソースコードを見たからだ。うーん，Javaでも結局ライブラリのソースコードを追いまくることがほとんどだけど，Ruby on Railsも一緒ということか。ま，他人が作った良質(かどうか判断できないけど)のソースコードを見るのは勉強にもなるし，楽しいものだ。
