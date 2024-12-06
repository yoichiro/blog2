---
layout: post
status: publish
published: true
title: nullに対するUn-boxing
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 104
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=104
date: '2005-08-11 01:08:16 +0900'
date_gmt: '2005-08-10 16:08:16 +0900'
categories:
- Java
---

前のエントリで疑問に思った，nullに対するUn-boxingをした場合にどうなるのかを試してみた。

>public class Test {


　　public static void main(String[] args) {


　　　　Integer obj = null;


　　　　int i = obj;


　　　　System.out.println(i);


　　}


}


実行結果は以下のとおり。

>Exception in thread "main" java.lang.NullPointerException


　　at Test.main(Test.java:4)


あー，なるほど。ぬるぽになるのか。よくよく考えてみれば当然で，コンパイラは内部でintValue()を呼ぶコードを生成しているはずだから，intValue()を呼び出す対象がnullなのでNullPointerException例外発生っていう理屈だ。

僕の私見としては，オブジェクトに対するnullチェックは明確にプログラマが行う必要があるから，チェック処理の記述を促すためにも，Un-boxingは余計なお世話かな，と思う。特に，

>class A {


　　Integer getValue() {...}


}


class B {


　　void setValue(int i) {...}


}





A a = ...;


B b = ...;


b.setValue(a.getValue());


という場合は，Un-boxingが行われる箇所で型名が一切出てこないために，プログラマは「b.getValue()」の戻り値がnullかどうかを気にするきっかけがなくなってしまう(JDK1.4ではコンパイルエラーになるから気にすることができる)。

うーん，ちょっと考えものだなぁ・・・。
