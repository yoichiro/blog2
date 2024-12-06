---
layout: post
title: Converse AIを使ったGoogleアシスタント向けアクションの開発
categories:
- actions on google
---

世の中AI全盛な昨今ですが、チャットボットや会話型アプリを開発するためのツールやウェブサービスが次々と登場しています。その中には、
一般的に難しいとされるプログラミングを行うことなく、GUIをいろいろ操作していけば開発できますよ、というものも少なくありません。
[Converse AI](http://www.converse.ai/) は、そんなウェブサービスの中の一つです。ここでは、Converse AIを使ってGoogleアシスタント
向けアクションを開発するための手順を紹介したいと思います。

# アクション作成手順が以前と変わってる

Actions on GoogleのActions Consoleは、5月に行われたGoogle I/O 2018に合わせて、UIが一新されました。そのためか、いくつかの機能が
統廃合されています。Converse AIもその一つのようで、昔は以下のようにActions on GoogleからConverse AIを使ったアクションの作成が
できたのですが、

![]({{ "/images/2018/07/converse_ai_01.webp" | prepend: site.baseurl }})

現在はそのUIはなくなってしまっています。そのため、Actions on Googleから直接Converse AIのアクションを作成するのではなく、以下の手順
を踏むことになります。

1. Actions on Googleにて、何もないプロジェクトを作成する。
1. Converse AIにて、テンプレートを作成する。
1. Converse AIのChannelsからActions on Googleのプロジェクトに同期を行う。
1. Actions Simulatorにてテストする。

Dialogflowも上記のようなアプローチですので、Actions Consoleのリニューアル後は、外部ツールからActions on Google側に同期を行って
アクション作成などの反映を行う、という手順となっています。

では、実際の手順を紹介していきましょう。

# Actions on Google プロジェクトの作成

Actions Consoleにて、Actions on Googleプロジェクトの新規作成を行います。新規作成のためのポップアップにて、適当にプロジェクト名を
入力して、Englishが選択されたまま、"CREATE PROJECT" ボタンをクリックします。

![]({{ "/images/2018/07/converse_ai_02.webp" | prepend: site.baseurl }})

その後出てくるひな形の選択では、右上にある "SKIP" をクリックします。

![]({{ "/images/2018/07/converse_ai_03.webp" | prepend: site.baseurl }})

これにより、からっぽのプロジェクトができあがります。

![]({{ "/images/2018/07/converse_ai_04.webp" | prepend: site.baseurl }})

# Converse AIテンプレートの作成

Actions on Googleは一旦放置して、ここからはConverse AI側の操作を行っていきます。Converse AIのサインアップが終わった後、新規の
ワークスペースを一つ持っているはずです。これに、会話の定義を行っていくテンプレートを追加します。右下にある "NEW TEMPLATE" ボタンを
クリックします。

![]({{ "/images/2018/07/converse_ai_05.webp" | prepend: site.baseurl }})

名前を聞かれますので、適当に名前を付けます。ここでは "hello world" としました。入力後、右下の "CONVERSATION" をクリックします。

![]({{ "/images/2018/07/converse_ai_06.webp" | prepend: site.baseurl }})

もしConverse AIを使うのが初めての場合は、各UIの説明を吹き出しでいろいろ説明してくれると思いますので、ぜひお読みください。

テンプレート作成直後は、以下のように "startstate" が一つだけある画面が表示されます。ここで、右上にある "Trigger phrases" ボタンを
クリックします。

![]({{ "/images/2018/07/converse_ai_07.webp" | prepend: site.baseurl }})

右からにょきっとUIが出てくると思います。"Phrase #1" のところに、このテンプレートで定義される会話を呼び出すきっかけとなるフレーズを
入力します。ここでは "hello" としました。入力後、"Save changes" ボタンを押して反映させます。

![]({{ "/images/2018/07/converse_ai_08.webp" | prepend: site.baseurl }})

もう一度右上にある "Trigger phrases" ボタンを押して、UIを消します。次に、"hello" と呼びかけられた後の応答フレーズを設定します。
画面真ん中にある "startstate" をクリックしてください。設定UIが表示されます。

![]({{ "/images/2018/07/converse_ai_09.webp" | prepend: site.baseurl }})

その設定UIの中に "Answer" とい入力フィールドがあるので、適当に応答フレーズを入れます。ここでは "hello, world!" としました。
入力後、"Save changes" を押して反映するのをお忘れなく。そして、右上にある "Exit" ボタンをクリックして、テンプレートの編集から抜けます。

![]({{ "/images/2018/07/converse_ai_10.webp" | prepend: site.baseurl }})

# Actions on Googleとの同期

作成したテンプレートの内容をActions on Google側に同期することで、Actions on Google側のプロジェクトにアクションが作成されます。
これを行ってみましょう。まず、左にある "Channels" をクリックします。

![]({{ "/images/2018/07/converse_ai_11.webp" | prepend: site.baseurl }})

連携先のサービスがいっぱい表示されますが、もちろんここでは "Actions on Google" をクリックします。

![]({{ "/images/2018/07/converse_ai_12.webp" | prepend: site.baseurl }})

連携先のActions on Googleプロジェクトを決定するために、まずはGoogleサインインを求められます。"Sign in with Google" をクリック
します。

![]({{ "/images/2018/07/converse_ai_13.webp" | prepend: site.baseurl }})

Googleアカウントを複数持っている場合は、どのGoogleアカウントを使用するかを聞かれるので、選択しましょう。

![]({{ "/images/2018/07/converse_ai_14.webp" | prepend: site.baseurl }})

次に認可画面が表示されます。Converse AIにActions on Google関連の操作を許可するかどうかを聞かれていますので、問題なければ許可して
ください。

![]({{ "/images/2018/07/converse_ai_15.webp" | prepend: site.baseurl }})

許可すると、Converse AIに戻ってきます。同時に、Actions on Google側と同期するための設定UIが表示されているのがわかると思います。
まず、Welcome Messageを何か入れておきましょう。ここでは "Welcome to Converse AI!" としました。入力後、右下にある "SAVE" ボタン
を押して反映させます。

![]({{ "/images/2018/07/converse_ai_16.webp" | prepend: site.baseurl }})

次に、"DEPLOYMENTS" タブをクリックします。そして、"Project Id" の選択肢から、最初に作成したActions on Google側のプロジェクト名
を選択します。その後、"UPDATE" ボタンを押してください。

![]({{ "/images/2018/07/converse_ai_17.webp" | prepend: site.baseurl }})

同期に成功すれば、右上に成功したことを示す通知が表示されます。その通知の中にある "DEVELOPER CONSOLE" をクリックしてみましょう。

![]({{ "/images/2018/07/converse_ai_18.webp" | prepend: site.baseurl }})

すると、Actions Consoleが表示されますので、左にある "Actions" をクリックします。Converse AIで定義した内容が同期され、アクションが
新規追加されていることがわかると思います。

![]({{ "/images/2018/07/converse_ai_19.webp" | prepend: site.baseurl }})

# 作った会話をActions Simulatorでテストする

では、実際に作った会話をActions Simulatorにてテストしてみましょう。Converse AI側の画面は、Actions on Googleに同期をとった
ままだと思います。そのときの "UPDATE" ボタンの隣に "Test" がありますので、それをクリックします。

![]({{ "/images/2018/07/converse_ai_20.webp" | prepend: site.baseurl }})

ここでもActions on Google側との同期が行われて、テスト準備が完了すると、右上に通知が表示されます。そこにある "SIMULATOR" を
クリックします。

![]({{ "/images/2018/07/converse_ai_21.webp" | prepend: site.baseurl }})

すると、Actions Simulator画面が表示されますので、会話を始めてみましょう。

![]({{ "/images/2018/07/converse_ai_22.webp" | prepend: site.baseurl }})

Converse AI側で設定した Welcome Message が発話されていることがわかります。では、"hello" と呼びかけてみましょう。

![]({{ "/images/2018/07/converse_ai_23.webp" | prepend: site.baseurl }})

"hello, world!" と Answer に指定したフレーズが返ってきました。あとは、Converse AI側のUIを使って、会話を組み立てていき、
Actions on Google側と同期をとっていけば、アクションを開発していくことができます。

# まとめ

ここでは、Converse AI を使ったGoogleアシスタント向けアクションの開発の始め方を紹介しました。Converse AI以外のツールや
ウェブサービスに関しても、基本的に上記のような流れでアクション開発をすることができるはずです。ぜひチャレンジしてみてください！
