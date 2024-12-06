---
layout: post
status: publish
published: true
title: package-info.javaをコンパイルするために
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 770
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=770
date: '2010-01-07 21:04:37 +0900'
date_gmt: '2010-01-07 12:04:37 +0900'
categories:
- Java
---

Javaにおいて、パッケージにいくつかのアノテーションを指定することができます。これをするために、パッケージ定義のみを持つpackage-info.javaファイルを作成する必要があります。例えば、以下のような感じです。

```
/**
 * This package includes ... classes.
 * @author Yoichiro Tanaka
 */
package jp.eisbahn.foo.bar;
```

このpackage-info.javaファイルは、コンパイルすることができます。しかし、上記のケースでは、このファイルは不幸にもコンパイルされないでしょう。その理由は、それが実行時に必要とはjavacコマンドが判断しないからです。それ故に、もしpackage-info.classファイルが欲しい場合は、RUNTIME値を持つ何らかのアノテーションを配置する必要があります。

```
/**
 * This package includes ... classes.
 * @author Yoichiro Tanaka
 */
@RuntimeAnnotation
package jp.eisbahn.foo.bar;

import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Retention;

@Retention(RetentionPolicy.RUNTIME)
@interface RuntimeAnnotation {}
```

同じファイルの中で、RetentionPolicyクラスのRUNTIME属性を持つアノテーションを定義できます。そのアノテーションをパッケージに追加することで、そのパッケージは実行時に必要とされることになります。その結果として、そのファイルはjavacコマンドによってコンパイルされるでしょう。
