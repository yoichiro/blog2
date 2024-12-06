---
layout: post
status: publish
published: true
title: Javassistを使った動的プロキシの作成
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 354
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=354
date: '2007-04-16 10:57:31 +0900'
date_gmt: '2007-04-16 01:57:31 +0900'
categories:
- Java
---

前回の「
[動的プロキシとシリアライズ](http://www.eisbahn.jp/yoichiro/2007/04/post_141.html)」エントリで紹介した通り，今回はその第一歩として，Javassistを使って動的プロキシを作成するための方法を紹介してみようと思う。

[Javassist](http://www.csg.is.titech.ac.jp/~chiba/javassist/)は，動的プロキシを作成するためのライブラリというよりは，バイトコードを操作するためのライブラリである。とは言っても，バイトコードに対する低レベルな処理は，Javassistに完全にお任せで大丈夫である。JavassistのAPIを使っている限りでは，バイトコードに関する知識はほぼ０で良い。Javassistを使うと，クラスファイルを作り出すこともできるし，ファイルとして生成しなくても，その場でClassオブジェクトとして利用することが可能である。生成したいクラスについて，親クラスやインタフェースを実装させることもできるし，フィールドやメソッドを追加することもできる。

では，さっそく例を見ていこう。まず，次のような抽象クラスがあるとする。

```
package test;
pubic abstract class Component {
    private int cnt;
    public abstract void onClick();
    public void output() {
        System.out.println(cnt++);
    }
}
```

onClick()という抽象メソッドがあり，またoutput()メソッドを呼び出すことで，cntフィールドの値が表示され，さらに+1される，という抽象クラスである。

では，このクラスを継承した動的プロキシクラスを作成してみよう。

```
ClassPool cp = ClassPool.getDefault();
CtClass componentCC = cp.get("test.Component");
CtClass proxyCC = cp.makeClass("Proxy", componentCC);
CtClass serializeCC = cp.get("java.io.Serializable");
proxyCC.addInterface(serializeCC);
Class proxyClazz = proxyCC.toClass();
```

ClassPoolクラスのgetDefault()メソッドによりClassPoolオブジェクトを取得する。ClassPoolクラスが，Javassistを使う上での起点となる。その名の通り，クラスがプールされていて，取り出したり新規に生成を依頼したりすることができる。

ClassPool#get(String)メソッドを利用することにより，引数で渡したクラス名（FQCN）のクラスの情報を持つCtClassオブジェクトを得ることができる。そして，ClassPool#makeClass(String, Class)メソッドを利用することにより，新しいクラスを作成するためのCtClassオブジェクトを生成することができる。第１引数にはクラス名を，第２引数には継承したいクラスのCtClassオブジェクトを指定する。

上記の例では，親クラスとしたいtest.ComponentクラスのCtClassオブジェクトを取得し，それを第２引数に指定することで，それを継承した新しいクラスのCtClassオブジェクトをProxyというクラス名で生成している。さらに，java.io.SerializeインタフェースのCtClassオブジェクトを取得して，CtClass#addInterface(CtClass)メソッドに渡すことで，新しいクラスのCtClassオブジェクトにSerializeインタフェースを実装することを指示している。

あとはCtClass#toClass()メソッドを呼び出せば，Proxyというクラス名のClassオブジェクトを得ることができる。あとは，

```
Component c = (Component)proxyClazz.newInstance();
```

とすることで，インスタンスが生成される。

さて，早速生成したインスタンスに対して，メソッドを呼び出して確認してみよう。

```
c.onClick();
```

実行結果は無残にも，

>java.lang.AbstractMethodError: test.Component.onClick()V


となってしまうだろう。ま，よく考えれば当然で，Component#onClick()メソッドは抽象クラスであり，実装を持っていない。しかし，ProxyクラスもonClick()メソッドに関することは何もしていないので，abstractなまま。よって実行時にチェックが走って，エラーとなったというわけだ。しかし，abstractなメソッドを実装しないままでインスタンスを生成できることは驚きである。

では，ProxyクラスにてonClick()メソッドの実装を定義してあげよう。proxyCC.toClass()メソッドを呼び出す前に，以下のコードを追記する。

```
CtMethod onClickMethod = CtMethod.make(
     "public void onClick() {"
  + "    output();"
  + "}", proxyCC);
proxyCC.addMethod(onClickMethod);
```

CtMethod#make(String)メソッドを使って，追加したいメソッドの情報を持つCtMethodオブジェクトを生成し，CtClass#addMethod(CtMethod)メソッドにより，クラスにメソッドを追加している。驚くべきことは，追加したいメソッドを，ソースコードそのままの文字列で渡している点だろう。Javassistによって渡されたコードは実行時にコンパイルされる。つまり，メソッドの本体をプログラムで組み立てることができるため，条件によって内容を変更することも可能になるということである。

上記の例では，Component#output()メソッドの呼び出しを，オーバーライドしたonClick()メソッド内で実行するようにしている。もう一度コード全体の実行確認を行うことで，例えば「c.onClick();」を2回連続で呼び出せば，

>0
1


という実行結果を得ることができる。

上記の例では，追加するメソッドの本体全てを文字列で記述したが，自由な反面，コンパイラチェックが効かなくなるなどのデメリットが発生するため，極力文字列で書くことは避け，何らかのクラスが持つstaticなメソッドを呼び出すだけにするなどの工夫をしておくと良いだろう。
