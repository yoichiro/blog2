---
layout: post
title: 数当てゲームを支えるVUI設計と手順
categories:
- actions on google
---
Googleアシスタント向けアプリを設計する上で、VUI（Voice User Interface）の理解がとても重要になります。GUIのように数多くのUIが組み合わされて
UXが決まっていくことに対して、VUIはあくまで「対話」がUXを決定します。基本的には視覚的な情報が一切ない状態で、自然言語を使った会話によって目的を
達成しなければならないため、GUIとは全く違った設計手法が必要になってきます。

以前、Googleアシスタント向けアプリに数当てゲームアプリを開発して公開しました。その際に使ったノウハウを以下の資料にまとめてみましたので、
ぜひ参考にしていただければと思っています。

<iframe src="https://docs.google.com/presentation/d/e/2PACX-1vRk_DEdHLQXeQSxROVJWaDY4MlPmGMcn_4VAiHh3MfnqddvK5qFswfnKrCMXiYxkwF1MqzoKdDB9zmo/embed?start=false&loop=false&delayms=3000" frameborder="0" width="960" height="569" allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true"></iframe>

構成的には大きく2つに分けられます。

* 前半はVUIにおいて気をつけるべき事項について。
* 後半はVUIの設計から実装に落とし込むまでにどのようなアプローチを取ったか、について。

今までVUIアプリについての設計から実装に落とすまでの具体的な手法については、なかなか文献がなかったかと思います。上記資料がVUIアプリ設計の助けに
なれば嬉しいです。
