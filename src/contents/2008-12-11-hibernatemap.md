---
layout: post
status: publish
published: true
title: HibernateでMap<String,String>なプロパティを扱う方法
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 600
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=600
date: '2008-12-11 17:06:44 +0900'
date_gmt: '2008-12-11 08:06:44 +0900'
categories:
- Java
---

Hibernateを使っていて、ふと困った状況に遭遇。

@Entity
@Table(name="...")
public class Hoge {
・・・
protected Map
desc;
・・・
}

Hogeエンティティクラスのdescプロパティを永続化させるにはどうしたらいいのだろう？もし、descプロパティが、

protected Map
desc;

というように、Mapの値が別のエンティティだった場合は、特に問題はない。しかし上記の場合はMapの値がString。これは困った。

Interceptorを自分で作って、onSave()やonFlushDirty()などを実装してstateを入れ替えてみたりして試行錯誤を繰り返すも、残念ながらうまくいかない。そこで、HibernateのForumをいろいろと検索していく中で、同じ話題を発見できた。

[Topic: Map of primitives using CollectionOfElements]

[http://forum.hibernate.org/viewtopic.php?t=953403](http://forum.hibernate.org/viewtopic.php?t=953403)

このトピックの最後に掲載されている投稿に正解が書いてあった。具体的には、

@CollectionOfElements
@JoinTable(name="hoge_desc", joinColumns=@JoinColumn(name="hoge_id")
@MapKey(columns=@Column(name="desc_key"))
@Column(name="desc_value", nullable=false)
protected Map
desc;

としてあげることで、descプロパティのためにhoge_desc表が生成され、hogeエンティティとの関連にhoge_id列が、descプロパティのMapのkey値がdesc_key列に、value値がdesc_value値に、それぞれマッピングされて永続化されることになる。

HibernateのReferenceやJavadocには残念ながらズバリの解答は載ってなかったため、半ば諦めかけていた。Hibernate Forum++。
