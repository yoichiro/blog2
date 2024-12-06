---
layout: post
status: publish
published: true
title: Google I/O 2013報告会に参加してきました、しかも名古屋！
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2369
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2369
date: '2013-05-31 09:12:39 +0900'
date_gmt: '2013-05-31 00:12:39 +0900'
categories:
- Google
---

今年は残念ながら参加できなかったGoogle I/O。しかし、Google Developers Expertとして、Google I/O報告会でレポートしなくちゃいけないという、なんとも嬉しいんだか悲しいんだかな感じで、しっかりとレポートをしてきました。今年も全国をHangoutsでつないで、各地で盛り上がりを見せた報告会になりました。昨年は東京会場でしたが、今年は名古屋会場にお邪魔してきました。

名古屋会場の場所は、名古屋ルーセントタワー32階にある株式会社エイチームの会議室でした。

![344005332864](http://www.eisbahn.jp/yoichiro/images/2013/05/344005332864.jpg)

会場はスクールタイプじゃなくて、4，5人で囲む机が点在してる感じ。きっちりしてなくて、これだと自然に雰囲気も良くなります。

![DSC_1666](http://www.eisbahn.jp/yoichiro/images/2013/05/DSC_1666.jpg)

僕の格好は、もちろん法被です。

![344074714060](http://www.eisbahn.jp/yoichiro/images/2013/05/344074714060.jpg)

さて、僕が担当したパートは2つ。Chrome&HTML5とSocialです（I/O参加してないのに・・・）。Chrome&HTML5のスライドは下のものになります。



この中で、WebPとrequestAutocomplete()について紹介をしました。WebPの話の要点は以下です。

* 既にChromeウェブストアで使われている。

* JPEGやPNGよりもサイズが小さくなることが利点。

* JPEGよりも圧縮や展開に時間がかかる。

* CPU処理時間よりも、ネットワークの帯域を優先した方が、金銭的に有利。

* 将来ハードウェアサポートが出てくれば、CPU時間は短縮される。

* Facebookでも実験的にこっそり使われてたりする。

質問としては、以下な感じでした。

Q: AndroidやiPhoneでWebPを扱うライブラリはあるの？
A: あります。セッション内でコード例が紹介されていました。

Q: WebPのアルゴリズムは？
A: 詳しくはわかりません。しかし、同じQualityで比較した場合、JPEGよりも細かな劣化があることが報告されています。

Q: WebPの読み方は「ウェブピー」ではなく「ウェッピー」じゃないの？
A: セッションでの発言は「ウェブピー」に近かったので、そう言いました。

ちなみに、WebPはWebMのサブセット的な位置づけなものです。つまり、WebMの動画の一コマがWebP。よって、VP8コーデックで使われている圧縮アルゴリズムになります。また、AndroidでWebPを扱うコードは、以下になります。

```
static {
    System.loadLibrary("webp");
}
private Bitmap webpToBitmap(byte[] encoded) {
    int[] width = new int[] { 0 };
    int[] height = new int[] { 0 };
    byte[] decoded = libwebp.WebPDecodeARGB(encoded, encoded.length, width, height);
    int[] pixels = new int[ decoded.length / 4 ];
    ByteBuffer.wrap(decoded).asIntBuffer().get(pixels);
    return Bitmap.createBitmap(pixels, width[0], height[0], Bitmap.Config.ARGB_8888);
}
```

iOS向けには、WebP.frameworkがあります。これを使ったコード例は以下になります。

```
int width = 0;
int height = 0;
WebPGetInfo([myData bytes], [myData length], &width, &height);
uint8_t *data = WebPDecodeRGBA([myData bytes], [myData length], &width, &height);
```

requestAutocomplete()の要点は以下です。

* インターネット上で何かを購入するとき、今まで数え切れないほどのカード情報や請求先住所などを入力してきたはず。

* 予め登録しておいた決済情報を選ぶだけで済めば、そんな楽なことはない。

* requestAutocomplete()は、Google Walletに登録されている決済情報をポップアップから選んで、決済情報の入力フォームに自動補完してくれる。

* フォームの各入力フィールドにautocomplete属性を書いておいて、JavaScriptでrequestAutocomplete()関数を呼ぶだけ。

* 決済情報を選択後にonautocompleteイベントハンドラが呼び出される。その時点で各決済情報項目は入力済み。あとはsubmit()するだけ。

* セッションの後半は、スピーカーが「如何に決済が簡単になることが世の中を進めるか」について熱く語ってた。

* Chrome Canary/Dev buildのWindows/Chrome OS版で試せる。

requestAutocomplete()についての質問は・・・たしか出ませんでした。

Chrome&HTML5の話を含む前半の動画を下に貼っておきます。



そしてSocialについてのスライドは以下になります。



ソーシャルというか、G+の中で特にG+ Sign-Inボタンについて話しました。要点は以下です。

* G+のセッション数はかなり増えた。一昨年なんて2個しかなかった。

* ただ、増えたとはいえ、その半分近くがG+ Sign-Inボタンについての話。

* G+ PlatformやG+ Sign-Inボタンの概要、各OS向けの設置方法、ベストプラクティス、といったストーリーでセッションが構成されていた。

* Google Play Game Servicesも、必須ではないとはいえ、G+ Sign-Inボタンを入り口にすることが推奨されている。

* Googleの多くのサービスの入り口としてG+ Sign-Inボタンを位置づけようとしている。

質問としては、以下が出ました。

Q: G+ Sign-Inボタンを入り口に、という話だったが、Androidでもそうなのか？AccountManagerとの棲み分けは？
A: 実はPlay Game ServicesのAndroid向けライブラリには認証機能が備わっているので、Android向けにはSign-Inボタンを使わずにそのライブラリの機能を使う方がきっと正しい。そのライブラリの中で、きっとAccountManagerにアクセスしていると思われる。

ちなみに、あとでGoogle+ Sign-In for Android Developersっていうセッションを聞いたところ、G+ Sign-In buttonのAndroid版は、すでにデバイスに登録されている認証情報を使うって言ってましたので、AccountManager経由かどうかわかりませんが、同じデバイスの認証情報を使ってログイン画面はほとんどスキップされる、という動作になると思われます。

Socialの話を含む後半の動画を以下に貼っておきます。



やっぱり報告会に来る参加者のレベルは高いです。質問の内容も鋭いものが多かった気がします。それにしても、やっぱりGoogle I/O報告会の雰囲気はいいですねー。地方をつないでワイワイガヤガヤだけど一体感もあるし、みんなで聞いてる感がホントに強い。いいイベントです。

昨年のI/O報告会は、福岡会場が一番盛り上がってたというか、ネタ満載でした。今年の名古屋会場は昨年の福岡会場に負けないように盛り上げたつもりです。Twitterでも「また名古屋か！」「名古屋ｗｗｗ」「名古屋キター」みたいな反応をかなりいただきまして、感無量です。

![DSC_1681](http://www.eisbahn.jp/yoichiro/images/2013/05/DSC_1681.jpg)

何かの機会に、またGDG Nagoyaにお邪魔したいと思います！
