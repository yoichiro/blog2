---
layout: post
status: publish
published: true
title: JSFアプリケーションの単体テスト with Shale Test Framework (1)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 289
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=289
date: '2006-12-19 09:32:07 +0900'
date_gmt: '2006-12-19 00:32:07 +0900'
categories:
- Shale
---

JSFの機能を補完することで，より生産性の高さを求めることができる
[Apache Shale](http://shale.apache.org/)。もちろんJSFのテストの領域にも足を踏み入れている。今回から数回，JSFの単体テストを楽にしてくれる「Shale Test Framework」について紹介してみようと思う。
JSFの大きな特徴として「ManagedBeanをPOJOで作ることができる」ということが真っ先にあげられる。Strutsやその他の普及したWebアプリケーションフレームワークでは，フレームワークが提供するinterfaceや抽象クラスを継承して業務的なクラスを作成することが基本だった。もちろんそれが悪いことではなく，実装者に縛りを強いることで，クラス粒度が揃えられたり，業務的な共通機構が作りやすくなったり，というような一定の効果をもたらしてくれた。ただし，その代償として，単体テストの敷居を一気に高めてしまったことは，否定できない事実だろう。これはWebアプリケーションフレームワークというよりは，そもそもServlet APIが原因だとも言える。つまり，単体テストを行うためには，サーブレットコンテナを強烈に意識しなくてはならないわけだ。
そこでJSFでは，バリューバインディングやメソッドバインディングという仕組みを設けることで，Servlet APIからの独立を実現した。特殊なインタフェースや抽象クラスが提供するメソッドに頼ることなく，JavaBeans規約に沿った手法（つまりPOJO）にて，情報を受け取って処理したり情報を外部に提供したりすることができるようになった。ManagedBeanは，能動的に外部から渡ってくる値を取りに行くことはせず，プロパティのsetterメソッドにて値が渡されることを待っていれば良い。これは，DIの考えにも通じている。そのおかげで，ManagedBeanに対する単体テストコードが非常に書きやすくなったことは，言うまでもないだろう。
簡単なアプリケーションを，JSFの標準実装が提供する機能を普通に使って開発していれば，バリューバインディングやメソッドバインディングといった機構のみで十分であり，ManagedBeanに対する単体テストコードも苦労することなく作れるだろう。
しかし，時代はそれを許してはくれない。
Ajaxの普及によって，HTMLとJavaScriptによるWebブラウザサイドでのリッチなUIが一気に主流になってしまった。その結果，従来のページ更新による画面書き換えよりも，処理の流れは複雑になる。つまり，画面を構成する個々のUIコンポーネントのイベント処理を非同期通信でサーバに依頼し，その結果を元に画面の一部を書き換える，というパターンで実装を行うことになる。これは，JSFの仕様策定時点では全くメジャーでなかった考えであり，個人的には，新参者な割にトレンドに乗り切れていない感がJSFにあることの理由となっていると思っている。
しかし，JSFのスペックリードだったCraigによって，その印象は払拭されようとしている。Apache Shaleによって，JSFは技術のトレンドにマッチしたものになる。特にShale Remotingはその代表格であり，JSFが目指すコンポーネント・イベント指向について，より進化させてくれる。これについては，「
[JSF+AjaxによるUIコンポーネント開発 with Shale Remoting](http://www.eisbahn.jp/yoichiro/2006/12/jsfajaxui_with_shale_remoting_15.html)」という一連のエントリを是非読んでみて欲しい。
ただし，やはり仕様策定時点でAjaxが見据えられていないJSFは，Shale Remotingを使ったとしても，弱点が見え隠れしてしまう。ManagedBeanをPOJOで作成できることがJSFの強みだったが，残念ながらShale Remotingによって呼び出されるManagedBeanは，JSFのAPIに依存したコードが現時点では必要になってしまう。具体的には，Webブラウザから送られたパラメータ値の取得や，処理結果のストリームへの送出処理についてが対象となる。つまり，Shale Remoting対応のManagedBeanに関する単体テストは，実行時の環境をシミュレートしてあげないといけない。
UIコンポーネントを自作した場合についても，そのコードはJSFに依存した内容となる。ServletやJSPの単体テストがCactusを必要としたのと同じように，JSFに関しても実行時の環境を再現してくれる単体テストソリューションが必要になるのだ。
この問題を解決してくれるのが，
[Shale Test Framework](http://shale.apache.org/shale-test/index.html)である。長いので，以後STFと略すことにしよう。STFでは，JUnitでの単体テストコードの記述を楽にしてくれる抽象クラスが提供されている。

* AbstractJsfTestCase - STFが提供するモッククラスを使用してJSF機構の各オブジェクトが提供される。

* AbstractJmockJsfTestCase - JMockを使用してJSF機構の各オブジェクトが提供される。

* AbstractHtmlUnitTestCase - HtmlUnitを統合した抽象テストケース。

* AbstractViewControllerTestCase - ViewControllerを実装したManagedBeanクラスをサポートする抽象テストケース。
最も良く使われるのは，一番上のAbstractJsfTestCaseクラスだろう。

![abstract-jsf-test-case-layer.jpg](http://www.eisbahn.jp/yoichiro/images/abstract-jsf-test-case-layer.jpg)
このクラスのsetUp()メソッドにて，ApplicationやFacesContextなどのJSFが提供するオブジェクトや，HttpServletRequestやHttpSessionなどのServlet APIに対するオブジェクトを，モックとして利用可能な状態にしてくれる。テストメソッドの中で適切にこれらのモックオブジェクトに対していろいろと前準備を施してあげることで，実行時のJSF環境がシミュレートされるというわけである。
次回のエントリでは，STFを適用する題材を取り上げる予定である。
