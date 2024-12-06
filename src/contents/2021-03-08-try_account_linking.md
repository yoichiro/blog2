---
layout: post
title: Account LinkingをActions Builderから試してみました
categories:
- actions on google
---
  
今日は久々にActions on GoogleのAccount Linking 機能を試してみたいと思います。Actions Builderになって開発の仕方が以前に比べてガラリと変わりました。Account Linkingについても、Actions Builderから利用しやすいようになっています。

# Account Linkingを有効にする


まず、空っぽのプロジェクトを新規に作成します。そして、画面上部にある「Develop」タブを選択して、左にある「Account linking」メニューをクリックします。


![]({{ "/images/2021/03/try-account-linking-1.webp" | prepend: site.baseurl }})


Account linking と書かれた隣りにあるトグルスイッチをONにします。そして、声で新しいアカウントをサインアップすることをユーザに許可するかどうかという選択肢には、ここでは「Yes」を選択します。その後、「Next」ボタンを押します。


![]({{ "/images/2021/03/try-account-linking-2.webp" | prepend: site.baseurl }})


Linking type は「Google Sign in」を選択して、「Next」ボタンを押します。

![]({{ "/images/2021/03/try-account-linking-3.webp" | prepend: site.baseurl }})


そして、「Google Sign In Client Information」と書かれている部分をクリックすると、「Client ID issued by Google to your Actions」という値が発行されるので、どこかにメモしておきましょう。


![]({{ "/images/2021/03/try-account-linking-4.webp" | prepend: site.baseurl }})


最後に SAVE ボタンを押します。これで、下ごしらえは完了です。

# 会話の中でAccount Linkingを呼び出す

有効にしたAccount Linkingを、会話の中で呼び出します。呼び出しタイミングは、会話の中で必要になったタイミングで呼び出します（説明なく会話の冒頭でAccunt Linkingを始めることは推奨されていません）。しかし今回はAccount Linkingを試したいだけなので、アクションが呼び出された直後にやってしまいます。

まずは、シーンを追加して、すでにAccount Linkingが済んでいるユーザかどうかをチェックします。シーン追加のために、左にある「Scenes」メニューをクリックします。そして、「+」アイコンを押して、新しいシーンを追加します。


![]({{ "/images/2021/03/try-account-linking-5.webp" | prepend: site.baseurl }})


シーン名は、"AccountLinkingOrigin" とでもしておきましょう。作成後、Condition の隣りにある「+」アイコンをクリックします。右に条件設定のためにUIが表示されますので、if のところに `user.verificationStatus != "VERIFIED"` と入力します。これは、Account Linking が可能なユーザかどうかを判断するための条件となります。VERIFIED ではないユーザに対して Account Linking は実施できないので、ここでは Transition に「End conversion」を選択して会話を終えます。


![]({{ "/images/2021/03/try-account-linking-6.webp" | prepend: site.baseurl }})


次に、Condition の右にある「+」アイコンをクリックします。そして「else if」の欄に `user.verificationStatus == "VERIFIED"` と入力します。VERIFIED の場合は Account Linking を呼び出すことができますので、Transition として「Account linking」を選択します。その後、SAVEボタンを押して保存します。


![]({{ "/images/2021/03/try-account-linking-7.webp" | prepend: site.baseurl }})


# Account Linkingシーンのカスタマイズ

先ほどのAccountLinkingOriginシーンを保存した直後に、「AccountLinkingOrigin_AccountLinking」シーンが自動的に作成されます。次はこれをカスタマイズしていきましょう。

左のメニューから「AccountLinkingOrigin_AccountLinking」シーンをクリックします。


![]({{ "/images/2021/03/try-account-linking-8.webp" | prepend: site.baseurl }})


右にある AccountLinkingOrigin_AccountLinking シーンの Send prompt フィールドをクリックします。右にカスタマイズするためのUIが表示されますので、"To keep using <your action>" を消して、"To save preferences" と変えましょう。その後 SAVE ボタンを押して保存します。


![]({{ "/images/2021/03/try-account-linking-9.webp" | prepend: site.baseurl }})


次に、Account Linkingが正常に完了したときの処理を定義します。Conditions の中にある「If user successfully completes account linking」をクリックします。右に表示されるUIの中から、Transitionを「End conversation」にして、ここでは会話を終えてしまいましょう。本来であれば、Call your webhook にチェックを入れて何らかの処理を行い、別のシーンに遷移して会話を継続させるべきです。SAVE ボタンを押して保存します。


![]({{ "/images/2021/03/try-account-linking-10.webp" | prepend: site.baseurl }})


ここでは、他の条件についても、全部会話を終えるように設定します。

# 実際に試してみる

以上で会話の設計は完了です。全て「End conversation」にしてしまったので、会話らしい会話はありません。挙動としては、以下となることを期待しています。

* Account Linkingをする前で会話を呼び出した場合には、Account Linkingの会話フローに遷移して、Account Linkingの完了後に会話が終了される。
* Account Linking済みで会話を呼び出した場合には、会話が即終了される。

非常にわかりやすい流れとなっています。試してみましょう。Androidスマートフォンから「Talk to my test app」と話しかけてみます。ちなみに、今回は英語環境で試しています。


![]({{ "/images/2021/03/try-account-linking-11.webp" | prepend: site.baseurl }})



Welcomeメッセージはプロジェクト作成時の初期設定そのままです。次がポイントで、シーンに設定した「To save preferences」が文章に入っていることがわかります。Account Linkingするかどうか聞いてきているので、Yes を選択します。

その後、アカウントを作成するかどうかを聞いてくるので、それに対してもYesと答えます。このアカウント作成は、Googleアカウントを新規に作成することではないことに注意です。先ほどAccount creationにYesと設定したので、Googleアカウントが聞いてきています。


![]({{ "/images/2021/03/try-account-linking-12.webp" | prepend: site.baseurl }})


その後、新規にアカウントがセットアップされたことを伝えて、会話が終了します。


![]({{ "/images/2021/03/try-account-linking-13.webp" | prepend: site.baseurl }})


実際にメールが来ているのがわかると思います。


![]({{ "/images/2021/03/try-account-linking-14.webp" | prepend: site.baseurl }})


さて、もう一度会話を始めてみましょう。


![]({{ "/images/2021/03/try-account-linking-15.webp" | prepend: site.baseurl }})


即座に会話が終了しました。これも期待通りですね。Account Linkingが完了している場合に、Account Linkingのフローに行かないことがこれで確認できました。

あとは、会話を終えずに別のシーンに遷移したり、WebhookでAccount Linkingの結果特定されたユーザアカウントの情報を利用したりして、アクションの動作を作り込んでいくと良いでしょう。

# Account Linkingを解除するには

一旦Account Linkingをしてしまうと、基本的にはずっと関連付けたアカウントでアクションが起動することになります。このAccount Linkingを解除するには、どうしたら良いでしょうか？

まず、アクションの開発者であれば、Actions Consoleから解除することができます。画面上部にある Test タブを選択して、Actions Simulator を表示します。その後、右上にある「Settings」ボタンを押すと、各種設定項目がポップアップで表示されます。その中の最初にAccount Linking の項目があって、「Unlink」ボタンを押すと、解除することができます。


![]({{ "/images/2021/03/try-account-linking-16.webp" | prepend: site.baseurl }})


では、ユーザはどこから解除すれば良いでしょうか？正解は「Actions Directory」のページから解除することになります。

まず、デスクトップPCの場合は、以下のページから Actions Directory のページを表示します。

https://assistant.google.com/explore?hl=ja_jp

その後、リンクを解除したアクションを検索などで探して、そのページを表示します。すると、画面上部に「リンクを解除」するためのリンクがありますので、そこから解除することが可能です。


![]({{ "/images/2021/03/try-account-linking-17.webp" | prepend: site.baseurl }})


次にAndroidスマートフォンからの手順ですが、これが結構難解です。まず、Googleアカウントを呼び出します。そして、左にあるアイコンをタップします。


![]({{ "/images/2021/03/try-account-linking-18.webp" | prepend: site.baseurl }})


次に、右上に出てくるプロフィール画像のアイコンをタップします。


![]({{ "/images/2021/03/try-account-linking-19.webp" | prepend: site.baseurl }})


次の画面では真ん中あたりまで下にスクロールしていくと、「使い方・ヒント」という項目があるので、それをタップします。これが、Actions Directory への入り口になっています。


![]({{ "/images/2021/03/try-account-linking-20.webp" | prepend: site.baseurl }})



Account Linkingを解除したいアクションを名前などで検索します。そのアクションのページに「リンクを解除」ボタンがありますので、そのボタンをタップすることでAccount Linkingを解除できます。


![]({{ "/images/2021/03/try-account-linking-21.webp" | prepend: site.baseurl }})



ユーザに案内するのがちょっと面倒かもしれませんが、Account Linking の解除はセキュリティ上大事なことになりますので、案内を頑張って作り込みましょう。

