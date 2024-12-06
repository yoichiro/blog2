---
layout: post
status: publish
published: true
title: PluginやFeatureのファイルを自由に配置する
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 766
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=766
date: '2010-01-04 13:33:12 +0900'
date_gmt: '2010-01-04 04:33:12 +0900'
categories:
- Eclipse
---

基本的に、もしアップデートサイトにPluginやFeatureのJarファイルを配置したい場合、Eclipseアップデートサイトにて定義されたルールに従う必要があります。そのルールとは、site.xmlファイルを作成し、PluginのJarファイルを「plugins」という名前のサブディレクトリに置き、そしてFeatureのJarファイルを「features」という名前のサブディレクトリに置くことです。ユーザがこのアップデートサイトにアクセスしてプラグインをインストールする際に、EclipseはこのルールによってこれらのJarファイルを見つけることができます。

しかし、もしアップデートサイトを作りたいとき、何らかの理由でこれらのサブディレクトリを作れないかもしれません。そのとき、あなたは自由にこれらのファイルを配置したいと考えるでしょう。

このケースの場合、site.xmlファイル内で
タグを使用することができます。このタグの指定により、PluginのJarファイルを任意の場所に配置することができるようになります。もちろん、FeatureのJarファイルは、URLを記述することによって場所を指定できるので、任意の場所に配置することが可能です。

```
version="1.0" encoding="UTF-8"?

...
```

Featureが「jp.eisbahn.foo」という名前のPluginを持っているとき、Eclipseは「plugins」という名前のサブディレクトリからそのPluginを探そうとするでしょう。そのとき、PluginのJarファイルのパスは、「plugins/jp.eisbahn.foo_1.0.0.jar」となります（バージョン番号が1.0.0の例）。そして、
タグを使うことで、そのパスを任意のURLに置き換えることができます。上記の例では、そのパスは「http://www.eisbahn.jp/any/foo-plugin.jar」に置き換えられています。

このテクニックは、OSDEのアップデートサイトで使用されました。そのsite.xmlファイルは以下となります。

[http://opensocial-development-environment.googlecode.com/svn/update-site-candidate/site.xml](http://opensocial-development-environment.googlecode.com/svn/update-site-candidate/site.xml)
