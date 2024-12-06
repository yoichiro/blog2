---
layout: post
title: 新しい会話構築IDE「Actions Builder」が登場しました！
categories:
- actions on google
---

昨日行われたオンラインイベント「 [VOICE Global](https://www.voicesummit.ai/global) 」にて、Googleより大きな発表がありました！具体的には、以下となります。

* 新しい会話構築IDE「 [Actions Builder](https://developers.googleblog.com/2020/06/announcing-actions-builder-actions-sdk.html) 」の公開開始
* Home Graph に接続されたデバイスに共有ストレージを提供する「 [Home Storage](https://developers.google.com/assistant/conversational/storage-home) 」
* より長い形式のメディアセッションをサポートする、更新された「 [Media API](https://developers.google.com/assistant/conversational/prompts-media) 」
* マイクの状態変化を待たずに会話体験を組み立てられる「Continuous Match Mode」
* 「 [AMP](https://developers.google.com/amp) 」コンテンツのスマートディスプレイへの対応

## Actions Builderによる新しい会話型アクション開発

今まで会話型アクションを組み立てる時は、Actions Console にてアクションプロジェクトを登録した後に、ほとんどの時間を [Dialogflow](https://cloud.google.com/dialogflow?hl=ja) に費やしてきたと思います。Dialogflow は、今でも強力な NLU エンジンおよび会話組み立てのためのサービスです。ただ、Actions Console と Dialogflow との行ったり来たりがあったり、どうもアクションプロジェクト内で Dialogflow とどう連携されているかがブラックボックスだったりして、気持ち悪さがどうしてもあったかと思います。

今回登場した Actions Builder は、Actions Console に完全に統合された、全く新しい開発環境となります。

![]({{ "/images/2020/06/actions-builder-01.webp" | prepend: site.baseurl }})

Dialogflow の場合は、インテントを定義していくことが会話の組み立ての中心でした。この考え方が、Actions Builder だと更に進化していて、「シーン」を定義していくことが会話構築の中心となります。

![]({{ "/images/2020/06/actions-builder-02.webp" | prepend: site.baseurl }})

シーンは、Google アシスタント向けアクションの開発のベストプラクティスから生み出された考え方です。シーンの中で、今までお馴染みのインテントやタイプ（Dialogflow におけるエンティティ）を組み合わせて会話体験を作っていきます。特徴的なこととしては、例えば「はい、もしくは、いいえ、でお答えください」というトレーニングフレーズが同じになるようなインテントを、複数の異なるシーンで再利用していくことが可能になります。そのため、Dialogflow に慣れている開発者は、ちょっと発送の転換が必要になるかもしれませんが、個人的にはすぐに慣れることができると思います。

「とはいえ、なんか難しそうだなおぃ！」と思ってしまう方も多いでしょう。そんなあなたに、以下のコードラボをオススメします。

* [Build Actions for Google Assistant using Actions Builder (Level 1)](https://codelabs.developers.google.com/codelabs/actions-builder-1/#0)
* [Build Actions for Google Assistant using Actions Builder (Level 2)](https://codelabs.developers.google.com/codelabs/actions-builder-2/#0)

何も考えずに、まずはこれら2つのコードラボをやってみてください！終わった時には、新しい Actions Builder でどのようにシーンを組み立てて会話型アクションを作ればよいか、把握している状態になっているはずです。

日本語訳を早急に準備するつもりですので、もし英語が苦手な方は、ちょっと待っててくださいね。

「コードラボやるのも面倒だよ！」って方は、うーん、仕方ない、これでどうでしょうか？

<iframe width="560" height="315" src="https://www.youtube.com/embed/Z1hxvniJ18s" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Actions Builder の使い方の雰囲気がわかるかと思います。

この Actions Builder に対応したフルフィルメントのコードは、従来の Dialogflow に対応したコードとはかなり違います。Actions on Google SDK も [@assistant/conversation](https://www.npmjs.com/package/@assistant/conversation) ([GitHub](https://github.com/actions-on-google/assistant-conversation-nodejs)) に変更となっていますので、ご注意ください。 [公式ドキュメント](https://developers.google.com/assistant/) も Actions Builder 前提に書き換えられています。

## Home Storage

今まではユーザ個人に対するストレージ（[User storage](https://developers.google.com/assistant/conversational/storage-user)）が提供されていましたが、例えば家族で共有可能なストレージが登場しました。名前は、 [Home Storage](https://developers.google.com/assistant/conversational/storage-home) です。

これにより、Home Graph に接続されたデバイス間で、情報の共有が可能になります。ただし、モバイルデバイスは Home Graph には接続されていませんので、共有の対象外です。Home Storage を使用するには、Actions Console にて機能を有効にします。

![]({{ "/images/2020/06/actions-builder-03.webp" | prepend: site.baseurl }})

Home Storage にアクセスするには、フルフィルメントのコード内で `conv.home.params` を利用します（ `conv.home.params.foobar = 'abc'` のようにセットします）。また、プロンプトにて `$home.params.foobar` と参照するすることが可能です。

例えば、スマートディスプレイで提供されるゲームにて、家族で力を合わせてクリアしよう！、といったことが実現可能です。皆さんさまざまなアイディアを考えてみてください。

## まとめ

今回の発表は、かなり衝撃というか、会話型アクションの開発方法が今までと大きく変わってくることがわかると思います。7月初旬に、この新しい Actions Builder の解説オンラインイベントを行おうと企画していますので、準備でき次第お伝えいたします。楽しみに待っていてくださいね！
