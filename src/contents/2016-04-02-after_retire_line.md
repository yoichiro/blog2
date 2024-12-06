---
layout: post
title: 退職エントリの反響
categories:
- Other
---
3月31日に「[LINEを退職します](https://www.eisbahn.jp/yoichiro/2016/03/retire_line_corp.html)」というエントリを書きましたが、「すごい反響ですね〜」と言われるくらいな反響だった模様です。[3年前にも同じようなエントリを書いた](https://www.eisbahn.jp/yoichiro/2013/02/retire-from-mixi.html)のですが、その時と今回でどっちが反響大きかったのか、比べてみました。

以下が、ミクシィ社を辞めたときのユーザセッションのグラフです。

![after_retire_line_1.png]({{ "/images/2016/04/after_retire_line_1.png" | prepend: site.baseurl }})

次が、今回のLINE社を辞めたときのユーザセッションのグラフです。

![after_retire_line_2.png]({{ "/images/2016/04/after_retire_line_2.png" | prepend: site.baseurl }})

結果は、今回の方が反響が大きかった模様です。このブログのアクセス数的にも、記録更新って感じですね。

3年前は、このブログは「Apache + mod_php + WordPress + MySQL」という構成でした。しかも、Apacheにキャッシュ設定とかもしてなかったので、全てのアクセスがダイレクトにWordPress側に行ってたという感じでした。

それに比べて、このブログは既にJekyllを使った静的生成に移行しているのと、nginx + キャッシュ設定を施していたので、前回以上のアクセスが来ても余裕な感じだな、と思ってました。実測したりは今回してないですけど、裏側の仕組み的にも今回は特に心配は何もありませんでしたって感じです。
