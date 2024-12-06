---
layout: post
status: publish
published: true
title: "替わりのオブジェクトをシリアライズするwriteReplace()/readResolve()"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 355
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=355
date: '2007-04-16 23:10:18 +0900'
date_gmt: '2007-04-16 14:10:18 +0900'
categories:
- Java
---

今回は「
[動的プロキシとシリアライズ](http://www.eisbahn.jp/yoichiro/2007/04/post_141.html)」エントリで取り上げた通り，writeReplace()メソッドとreadResolve()メソッドの使い方について紹介してみようと思う。

あるオブジェクトをシリアライズしたくなったとする。しかし，時として，そのオブジェクトをそのままシリアライズすることができない理由が出てきたりする。そのままシリアライズしてしまうとサイズが非常に大きくなってしまう，などがすぐに思い付く代表例だろう。その場合，シリアライズしたい情報を自分で決定し，さらに情報を加工するなどして，自前の形で永続化を行いたくなる。

そのようなニーズに応えるのが，writeReplace()/readResolve()メソッドの機構である。これは，シリアライズ時およびデシリアライズ時に自動的に呼び出されるメソッドであり，これらのメソッドを使うことによって，シリアライズの際に対象のオブジェクトそのものではなく，別のクラスのオブジェクトをシリアライズ対象にすり替えてしまう，という芸当が可能になる。

writeReplace()/readResolve()メソッドは，特に何らかのインタフェースで規定されたメソッドではないので，以下のシグネチャを持つメソッドを対象のクラスに定義してあげれば良い。もちろん，Serializableインタフェースが実装されたクラスが対象なことは言うまでもない。

ANY-MODIFIER Object writeReplace() throws ObjectStreamException {}
ANY-MODIFIER Object readResolve() throws ObjectStreamException {}

実際に例を取り上げよう。まず，以下のようなクラスがあったとする。

package test;
public class Target implements java.io.Serializable {
private int value;
public int getValue() { return value; }
public void setValue(int value) { this.value = value; }
}

valueプロパティを持つ簡単なJavaBeansクラスである。さて，このクラスをTargetクラス以外のクラスのオブジェクトとして，わざわざシリアライズさせてみよう。以下が，替わりとなるオブジェクトのクラスである。

package test;
public class SerializedObject implements java.io.Serializable {
private String className;
private int value;
public SerializedObject(String className, int value) {
this.className = className;
this.value = value;
}
public String getClassName() { return className; }
public int getValue() { return value; }
}

永続化したいvalueフィールドの値を保持すると同時に，クラス名を保持するようにしている。Targetクラスのオブジェクトとしてシリアライズする替わりに，SerializedObjectクラスのオブジェクトをシリアライズするためには，Targetクラスに以下のメソッドを追加すれば良い。

public void writeReplace() throws ObjectStreamException {
SerializedObject obj = new SerializedObject(value, "test.Target");
return obj;
}

SerializedObjectクラスのインスタンスを生成し，value値とクラス名を引数に与えて，その結果を返却している。これにより，以下のようにシリアライズを行った際にwriteReplace()メソッドが呼び出されて，返却したSerializedObjectオブジェクトがシリアライズされる。

Target t = new Target();
t.setValue(5);
FileOutputStream fos = new FileOutputStream("./test.ser");
ObjectOutputStream out = new ObjectOutputStream(fos);
out.writeObject(t);

さて，実はこれでは"./test.ser"の内容をデシリアライズする際に失敗してしまう。替わりのオブジェクトを永続化したことに対応して，ちゃんと復元ができるようにSerializedObjectクラスに処理を記述してあげないといけない。それが，readResolve()メソッドである。SerializedObjectクラスに，以下のようにメソッドを追加する（本来は例外処理が必要だが，ここでは割愛）。

public Object readResolve() throws ObjectStreamException {
Class clazz = Class.forName(className);
Target t = (Target)clazz.newInstance();
t.setValue(value);
return t;
}

これにより，SerializedObjectオブジェクトがデシリアライズされる際にreadResolve()メソッドが自動的に呼び出される。そして，保持しておいたクラス名よりインスタンスを生成し（Targetクラスで明示的にキャストしちゃってるので意味はないのだが），保持しておいたvalue値を生成したインスタンスにセットしてあげて，そのインスタンスをデシリアライズ結果として返却している。

以下のように"./test.ser"ファイルの内容を使ってデシリアライズしてあげることで，readResolve()メソッドが呼び出され，元々のTargetオブジェクトと同じ内容のインスタンスを復元することができる。

FileInputStream fis = new FileInputStream("./test.ser");
ObjectInputStream in = new ObjectInputStream(fis);
Target t = (Target)in.readObject();

今回取り上げた例では，writeReplace()/readResolve()メソッドによってもたらされる恩恵が伝わりにくいかも知れない。次回は「
[Javassistを使った動的プロキシの作成](http://www.eisbahn.jp/yoichiro/2007/04/javassist.html)」エントリの内容も踏まえて，動的プロキシとシリアライズについての解説を行う予定である。
