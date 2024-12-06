---
layout: post
status: publish
published: true
title: "デバッグ放棄"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 823
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=823
date: '2010-09-21 21:57:17 +0900'
date_gmt: '2010-09-21 12:57:17 +0900'
categories:
- Java
---

お願いだから、良い子はこんなコードを書かないでください。

```
try {
    ...
} catch(RuntimeException e) {
    System.out.println(e.getMessage());
}
```
せめてLogger.debug("error", e)を、そうでなくともせめてe.printStackTrace()としましょうよ。もちろん、ちゃんとした対処も記述されるべきです。
