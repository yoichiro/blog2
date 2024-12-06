---
layout: post
status: publish
published: true
title: Content Rewriter Feature (0.9)
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 645
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=645
date: '2009-05-14 20:46:47 +0900'
date_gmt: '2009-05-14 11:46:47 +0900'
categories:
- OpenSocial
---

OpenSocial v0.9からちゃんと規定された「Content Rewriter Feature」について紹介します。実は多くのSNSで、v0.8.1から使えていた機能です。

---

[Content Rewriter Feature]

OpenSocialでは、ユーザからの細かく、しかも多量なリクエストを効率よく捌くために、OpenSocialコンテナによって積極的にキャッシュが行われます。ただし、OpenSocialアプリケーションによっては、何でもかんでもキャッシュされては困ることも十分考えられます。Content Rewriter Featureは、OpenSocialアプリケーションがキャッシュの振る舞いを制御できるようにするための機能となります。

Conent Rewriter Featureは、開発されたガジェットのコンテンツをRewriteする機構のサポートの追加であり、新しい標準的なオプショナルガジェットFeature「content-rewrite」を導入することで、開発者にRewriterの振る舞いをどうするかを制御させることができます。content-rewrite featureは、コンテナがコンテンツのレンダリングおよびプロキシ時に行うことができるRewriteのオペレーションのセットであり、コンテンツがRewriteされる際のルールを定義します。

つまり、content-rewrite featureによって記述されたルールに基づいて、コンテナが積極的に外部リソースのキャッシュを行うように指定することもできるし、その逆も可能ということです。

定義:

Rewriter featureは、一般的なフォームを持ちます。

86400



excluded

moreexcluded

true

true

true

各パラメータは以下のように定義されています。

* expires - RewriteされたURLを経由してプロキシから取得されたコンテンツの最小HTTPキャッシュ時間として効果するための時間(秒)です。初期値は、86400です。

* include-url - 大文字小文字を区別する文字列としてこのパラメータ値を含む任意のURLは、Rewriteすることが考慮されます。リテラル文字列"*"は、全てのURLを意味する特別なケースです。もし値を伴うこのエントリが指定されなかった場合は、"*"が指定されたとみなします。このパラメータは繰り返し書くことができます。

* exclude-url - 大文字小文字を区別する文字列としてこのパラメータ値を含む任意のURLは、Rewriteすることから除外されます。リテラル文字列"*"は、全てのURLを意味し、全てのRewriteを無効にします。このパラメータは繰り返し書くことができます。

* minify-css - コンテナがstyleタグや参照されているcssファイルの中のcssを最小化することを試みるかどうか制御します。有効な値は"true"または"false"です。初期値は"true"です。

* minify-js - コンテナがstyleタグや参照されているJSファイルの中のJSを最小化することを試みるかどうか制御します。有効な値は"true"または"false"です。初期値は"true"です。

* minify-html - コンテナがHTMLコンテンツを最小化することを試みるかどうか制御します。有効な値は"true"または"false"です。初期値は"true"です。

"exclude-url"によるマッチングは、"include-url"によるマッチングよりも優先されます。

全てのURLを意味する"*"の特別な使用は、文字列のGLOBまたはRegExマッチングによるサポートとして解釈されるべきではないことに注意してください。

コンテナは、以下を含むリンクをRewriteする際に追加の最適化を行うことは自由であり、制限されません:

* styleタグから@importディレクティブを展開し、それらをHTMLコンテンツに含まれるheadタグの中のlinkタグとして変換すること。

* 独立したURLからフェッチされたコンテンツをProxyによってまとめるために、連続したlinkタグから一つのlinkタグへ複数のCSSをマージすること。

* 連続した
