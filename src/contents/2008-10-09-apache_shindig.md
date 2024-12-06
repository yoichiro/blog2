---
layout: post
status: publish
published: true
title: Apache Shindigにコードをコントリビュートしてみました
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 588
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=588
date: '2008-10-09 20:10:29 +0900'
date_gmt: '2008-10-09 11:10:29 +0900'
categories:
- OpenSocial
---

OpenSocialコンテナとして多くのSNSが採用しているApache Shindig。このShindigが今後のOpenSocialの広がりに関して重要なキーパーツであることは、絶対的に明白なことである。OpenSocialの中心的なメンバーのほとんどが、Shindigの開発に関わっており、その人たちがコミットしている一文字一文字が、OpenSocialを支えている。ただし、Shindigの開発は慢性的な人手不足のようで、一部の開発者がものすごい勢いでコミットしているのが残念ながら今の現状である。
そんなShindigの開発に何とか僕も貢献したい、そう思い続けて早数ヶ月。今回ちょうど機能を追加したくなったので、OpenSocial and Gadgets Specification DiscussionグループにProposalを出しつつ、並行して実装も行い、パッチを送ってみた。
[Proposal: checking view type]

[http://groups.google.com/group/opensocial-and-gadgets-spec/browse_thread/thread/e4896bbdf6e5626](http://groups.google.com/group/opensocial-and-gadgets-spec/browse_thread/thread/e4896bbdf6e5626)
[Adding gadgets.views.View#checkType() function]

[https://issues.apache.org/jira/browse/SHINDIG-647](https://issues.apache.org/jira/browse/SHINDIG-647)
OpenSocialコンテナがサポートする各ビューの情報は、gadgets.views.Viewオブジェクトによって取得できる。そして、OpenSocialでは、予めビューが4つほど想定されていて、gadgets.views.ViewTypeクラスの定数的な感じで定義されている。具体的には、以下のようになる。

>var view = gadgets.views.getCurrentView();
var viewType = view.getName(); // => "canvas"

var canvasType = gadgets.views.ViewType.CANVAS; // => "CANVAS"

var f = (viewType == canvasType); // => false!!

というわけで、せっかくViewTypeが定義されているのに、対象のViewオブジェクトがどのビューなのかViewTypeの定数使って判断できないじゃん、という問題があって、規定されたAPIが使えない。同じCanvasビューでも、その名前はFULL_PAGEだったりcanvasだったりCANVASだったりする（Aliases）ので、これらは全てViewType.CANVASなんだぞ、と判断できる何かがないといけない。
そんなわけで、

>var f = view.checkType(gadgets.views.ViewType.CANVAS); // => true

的な関数を追加してみませんか、という提案と実装をこのほど行ってみた、という話である。
採用されるといいな。
