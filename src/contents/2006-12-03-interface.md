---
layout: post
status: publish
published: true
title: interface命名にも発想の逆転を
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 275
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=275
date: '2006-12-03 10:46:17 +0900'
date_gmt: '2006-12-03 01:46:17 +0900'
categories:
- Java
---

例えば，何らかの値をセットすることができる，という性格を表現するinterfaceを考えてみる。セットすることができるんだから，

public interface SomethingSettable {
public void setSomething(int value);
}

というように，〜Settableという名前にすることが多かった。

「何かをセットできる」というのは，対象クラスを外から見たときの観点と言える。なので，「何かをセットできる性質」というのは，そのクラスの性質の説明として，どうもしっくりこない。しかし，代案がわからないまま，数年が経過してしまった。

しかし，最近では，以下のように命名している。

public interface SomethingHolder {
public void setSomething(int value);
・・・
}

「何かをセットできる」を「何かを持つことができる」というように発想の逆転をする。つまり「settable」から「holder」とすることで，僕の中では非常にすっきりした。「セットできる」というよりも，クラスの性格の説明としては正しい気がする。それに，「セットする」というだけの規定では通常役不足であり，セットされる値に付随する機能も同時に規定されることが望ましい。よって，holderという命名の方が自然と言えるのではないだろうか。

・・・ということを，JSFのEditableValueHolderインタフェースを見て思った。
