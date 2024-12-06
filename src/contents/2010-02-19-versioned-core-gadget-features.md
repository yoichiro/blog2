---
layout: post
status: publish
published: true
title: Versioned Core Gadget Features
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 781
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=781
date: '2010-02-19 22:50:54 +0900'
date_gmt: '2010-02-19 13:50:54 +0900'
categories:
- OpenSocial
---

"opensocial"や"minimessage"など、OpenSocialアプリケーション内で利用したいFeatureは、Require要素やOptional要素にて宣言します。その際に「どのバージョンのFeatureを利用したいか」といった記述方法は、今までかなり曖昧でした。明確にバージョン番号を指定できるように、Versioned Core Gadget Features仕様がOpenSocial 1.0にて追加されます。

以下、仕様の概要文です。

>Gadget XMLファイルの
または
要素として使われるOpenSocial featuresは、名前の一部としてバージョン番号を伴って定義されていました("opensocial-0.7", "opensocial-0.8"など)。

これは、後方互換を壊すことなしに、あるバージョンから次のバージョンへ、それらを進めさせ、そしてGadgetにそれらが要求するAPIのバージョンを明確に宣言させることを可能にします。

この慣例は、OpenSocial仕様によって説明される全てのFeatureのために定義されるバージョンにおいて、
および
上で明確なバージョン属性として正式なものとされるべきです。

特殊なケースとして、コアなGadget仕様のフォーマットおよびレンダリング処理は、Gadgetの要求する仕様の指定バージョンにおいて、ルート要素における属性を使ってバージョニングされるべきです。


具体的には、Gadget XMLファイルの書式として以下の2点が追加となります。

* Moduleルート要素へのspecificationVersion属性の追加

* RequireおよびOptional要素へのversion属性の追加

例えば、以下のような記述になります。

```
...
```

specificationVersionおよびversion属性がそれぞれ省略された場合には、1.0が指定されたと見なされます。各属性値は、

MajorVersion.MinorVersion.PatchVersion

というように、ピリオド区切りで3つの数値を並べることができます。PatchVersionおよびMinorVersionはそれぞれ省略可能です。上記の例では、specificationVersionとしてPatchVersionが省略されています。この各項目についての考え方は、一般的なバージョニングの考え方と同じです。

この仕様に基づき、OpenSocial 1.0仕様が公開されるタイミングにおいて、gadgets名前空間に属するFeature群は、基本的に全て1.0.0となります。

「opensocialは"opensocial-0.8"といったバージョン番号があるのに、osapiはないの？」といった不明瞭な点は、この仕様に基づいて明確に定義され直すことでしょう。
