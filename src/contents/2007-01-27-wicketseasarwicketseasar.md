---
layout: post
status: publish
published: true
title: WicketとSeasarを統合するwicket-seasarを公開！
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 313
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=313
date: '2007-01-27 23:46:33 +0900'
date_gmt: '2007-01-27 14:46:33 +0900'
categories:
- Wicket
---

Wicketは，POHPソリューションの代表として今後広く普及するであろうフレームワークである。そして，WicketでDIコンテナの恩恵を受けるために，Spring Frameworkとの統合を行う拡張ライブラリ(wicket-spring)も提供されている。
最近，僕の回りではSpring Frameworkよりも，Seasar2をDIコンテナとして採用する事例がとても多い。さらに，以下のようなエントリを見つけてしまった。
「
[Webアプリ作成前に考えたこと](http://mesolabs.com/2006/11/19/web%E3%82%A2%E3%83%97%E3%83%AA%E4%BD%9C%E6%88%90%E5%89%8D%E3%81%AB%E8%80%83%E3%81%88%E3%81%9F%E3%81%93%E3%81%A8/)」 - めそらぼ - mesolabs.com

>プレゼン層でJSFに決まっていれば、EJB 3.0かSeasar 2.4の一騎打ちだったのですが、WicketになったのでWicketとの親和性を考えてSpring 2.0に決めました。

選択肢が狭いということは，とても悲しいことだ。オープンソースプロダクトの利点は，数多いソフトウェアを自分なりに組み合わせられる，ということなはずだ。
というわけで，WicketとSeasar2を統合するためのwicket-seasar拡張ライブラリを開発した。以下のURLがプロジェクトのページである。
S2Wicket - Wicket Seasar Integration

http://www.eisbahn.jp/wicket-seasar/

[http://s2wicket.sandbox.seasar.org/](http://s2wicket.sandbox.seasar.org/)
もしWicketとSeasar2との親和性を求めている方には，ぜひ利用してみていただきたい。ファーストバージョンということで最低限の機能しか実装していないが，今後機能追加や性能向上を行っていくつもりである。
WIcketは，先日下記のエントリによって，開発者にWicketの良さが広まった。
「
[Wicketはライトウェイトなフレームワークじゃない。でも気持ちがいい。](http://d.hatena.ne.jp/t_yano/20070116/1168972384)」 - 矢野勉のはてな日記

>Wicketはオブジェクト指向での開発をプログラマに与えるため、非常にJavaっぽい重厚なオブジェクト階層を作り上げ（その重厚さはほとんどSwing級です）つつも、部品の再利用を実現するためコンポーネントをベースとし、オブジェクト指向フレームワークらしく開発者が拡張できる拡張ポイントを大量に用意しています。コードが汚くなればリファクタリングにより改善できます。あらゆるシーンであなたの知ってるデザインパターンを適用できます。

ただし，

>とはいえ、Wicketが提供するのはビューとコントローラまでですね。モデルはどうしましょうか。

と言及されているように，Wicketはあくまでフロントエンドのフレームワークである。ビジネスロジックについては，オブジェクト指向原理主義を適用するよりは，情報と振る舞いをクラスレベルで分離して開発する方が都合が良いだろう。更にRDBMSとのやり取りに関しても，Wicketにモデルを供給する観点ではO/Rマッピングツールが非常に重要になってくる。そうなると，何らかのDIコンテナの適用がやはり自然な解決策となる。そして，S2Daoなどの魅力的なプロダクトを使うためにも，wicket-seasarが切り口となると思っている。
個人的にSeasar2の使用経験はほとんどない(というかSpringに慣れている)のだが，Wicketが持つサクサク感とSeasar2が持つサクサク感が合わさることによる絶妙なハーモニーを，あなたのWebアプリケーションにも取り入れて欲しいと願っている。
追記: 2007/02/02
wicket-seasarは，
[S2Wicket](http://s2wicket.sandbox.seasar.org)として下記サイトで再公開としたため，wicket-seasarのサイトは閉鎖した。
