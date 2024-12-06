---
layout: post
status: publish
published: true
title: mixiAndroidSDKWrapperが全APIをサポートしました！
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1172
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1172
date: '2011-11-09 23:17:11 +0900'
date_gmt: '2011-11-09 14:17:11 +0900'
categories:
- Other
---

6月からコツコツと開発を進めてきたmixiAndroidSDKWrapper、遂にVersion 1.6にてmixi Platformが提供する現時点で利用可能な全APIをサポートしました。

[
[mixiAndroidWrapper](https://github.com/yoichiro/mixiAndroidSDKWrapper)] - Github

mixiAndroidSDKWrapperは、mixi Platformが提供するmixiアプリやmixi Graph APIを使ったスマートフォンアプリを開発しやすくするためのライブラリです。mixi API SDK for Androidを拡張する形で利用します。多くのAPIがmixi Platformから提供されていますが、mixiAndroidSDKWrapperを使うことで、以下のようにJava言語らしいコーディングにて各種APIにアクセスし結果を得ることが可能となります。

```
mixiWrapper.getPeopleAPI().getFriends(
                new GetPeopleCallbackHandler() {
    protected void handleResult(boolean canceled) {
        for (Person friend : getPeople()) {
            // do something...
        }
    }
});
```

もしmixi Platformを利用するスマートフォンアプリの開発をお考えであれば、ぜひmixiAndroidSDKWrapperの使用をご検討ください！
