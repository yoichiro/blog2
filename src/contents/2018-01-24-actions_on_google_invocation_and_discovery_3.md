---
layout: actions_on_google
title: Invocation and Discovery "Implicit Invocation"の日本語訳です
categories:
- actions on google
---
Googleアシスタント向けアプリの明示的な呼び出しには、ユーザは事前にそのアプリ名を知っていなければなりません。もちろん、既に
そのアプリを利用した経験があって名前を知っていれば、アプリ名を使って直接呼び出すことができるため、非常に明確です。しかし、
ユーザがまだアプリ名を知らない際には、ユーザができることは「何をしたいのか」を表明することだけです。そこで、アシスタント向けアプリ
では、「何ができるのか」を予めActions on Googleに教えておくことで、ユーザが表明した意図に対して、それを使えるアプリをユーザに
提案してくれるようになります。これは、暗黙的な呼び出し（Implicit Invocation）と呼ばれています。

Actions on Googleのドキュメントにある
[Invocation and Discovery - Implicit Invocation](https://developers.google.com/actions/discovery/implicit)にて、
暗黙的にアプリを呼び出す方法が説明されています。以下はその日本語訳です。

---
# 暗黙的な呼び出し（Implicit Invocation）

![](https://developers.google.com/actions/images/implicit-invocation.png){:width="600px"}

暗黙的な呼び出し（Implicit Invocation）は、ユーザーがアプリの名前を使用せずにアプリを呼び出すときに発生します。
このタイプの呼び出しは、設定されたインテントのアクション呼び出しフレーズに似た何かを行うようにユーザーがGoogleアシスタントに指示したとき、
またはあなたのアプリの適切なコンテキストにユーザーがいるときに発生します。

## アクションディスカバリー

アクションディスカバリーは、ユーザーの前でアプリを入手するための非常に強力なツールです。
ユーザーがタスクを達成したいと思っていて、あなたのアプリがユーザの仕事に役立つアクションを持っている場合、
Googleアシスタントはあなたのアプリをそのユーザーに推薦する可能性があります。

この相互作用は次のように起こります。

1. ユーザーがGoogleアシスタントにタスクの実行を依頼します。
1. 推奨アルゴリズムは、あなたのアプリがユーザーのタスクを完了できるアクションを持っているかどうか判断します。
1. アシスタントは、あなたのアプリをユーザーに推薦します。

このインタラクションは、ユーザーの選択した単語とアプリのインテントとのギャップを効果的に橋渡しするアクション呼び出しフレーズをアプリが使用する場合に、はるかに可能性が高くなります。実際には、あなたのアプリは、実際のユーザーがGoogleアシスタントにこのような方法で発見されるように頼んでいる特定の有用な目的を果たさなければなりません。

推薦アルゴリズムの性質が進化しているため、Googleは、あなたのアプリがアクションディスカバリーによって推薦されることを保証することはできません。 アプリを設計する際には、アプリを推奨する機会を増やすため、次のベストプラクティスを念頭に置いてください。

* **会話エラーを減らす**<br>
  あなたのアプリの会話の中での行き止まりを探して、あなたのアプリのエラーの回数を減らしたり、ユーザーを強制終了させたりしてください。
* **アカウントリンクを使用してフローをブロックしない**<br>
  あなたのアプリがそのトリガーインテントで最初に行うこととして、アカウントにリンクするプロンプトの要求をユーザーに行わないでください。 認証されていないユーザーにゲストのフローを与えてアクションの価値を見せてから、続行する必要がある場合にのみアカウントのリンクを求めてください。
* **自由な質問を避ける**
  _「どんなことをすれば私はあなたを助けることができますか？」_ のような質問は、どのように進行するかについて、ユーザにコンテキストを与えません。 _「ビーチの名前を教えてくれれば、サーフレポートを送ることができます」_ といった特定の質問は、会話を進めることを助けます。
* **有用なアクション呼び出しフレーズを書く**<br>
  もしアクション呼び出しフレーズを、関係するさまざまなユーザーのクエリに関連付けできない場合、アプリは関連性があると認識されません。 詳細については、[Writing useful action invocation phrases](https://developers.google.com/actions/discovery/implicit#writing_useful_action_invocation_phrases)セクションを参照してください。

### ディスカバリー分析

[Actions Console](https://console.actions.google.com/)の **Analytics > Discovery** セクションでは、
どのようなフレーズがGoogleアシスタントにアプリのおすすめを促したか、またどのアプリのアクションがプロンプトに一致したかについての便利な情報が表示されます。

詳細は、[analytics and health](https://developers.google.com/actions/console/analytics#discovery)に関するドキュメンテーションをご覧ください。


## 有用なアクション呼び出しフレーズの作成

アクション呼び出しフレーズは、ユーザーにとって便利な呼び出しと発見ツールを提供しますが、注意深く選択する必要があります。
アクション呼び出しフレーズを設計するときは、次のベストプラクティスを念頭に置いてください。

* **アプリのユースケースに特有のアクション呼び出しフレーズを保つ**<br>
  あなたのアプリは特定の目的を果たすべきですので、アクション呼び出しフレーズが実際にあなたのアプリが何をするのかを記述してください。たとえば、アプリがフライトの予約に使用される場合は、 _"$location から $location に移動するにはどうすればよいですか"_ を使用しないでください。より良い選択肢は、 _"$location から $location へのフライトの予約"_ です。

* **動詞と目的語の両方を含むフレーズを作成する**<br>
  呼び出しは完全な文で構成されます。つまり、あなたのアクション呼び出しフレーズは、言語的に自然であるために、動詞-目的語の対でなければなりません。たとえば、 _"簡単なレシピ"_ は、完全な呼び出しが _"Ok Google, 簡単なレシピ"_ になるため、不適切なアクション呼び出しフレーズになります。より良い選択肢は、 _"Ok Google, 簡単なレシピを得るにはどうすればよいですか"_ によって呼び出される _"簡単なレシピを得るにはどうすればよいですか"_ です。

悪いアクションと良いアクションのフレーズの例を考えてみましょう。

**悪いアクション呼び出しフレーズ**

* 動詞の欠如 - "the number five（数字の5）"
* 一般的すぎる - "travel to New York（ニューヨークに旅行する）"

**良いアクション呼び出しフレーズ**

* 明確な動詞と目的語を持つ - "hear a dad joke（父のジョークを聞く）"
* 特定のリクエストを提供する
  * "what is my horoscope for today（今日の私の星占いは何ですか？）"
  * "hear a fun fact（ちょっと楽しい情報を聞く）"
  * "give me a 5 minute workout（仕事外で5分ください）"
  * "what should I wear today（今日は私は何を着るべき？）"
  * "get me a rental car（レンタカーを借りる）"
  * "I need a hotel room（ホテルの部屋が必要です）"
  * "buy concert tickets（コンサートのチケットを買う）"
  * "coupons that expire today（今日失効するクーポン）"

もちろん、これらのアクション呼び出しフレーズは、あなたのアプリにとって一意ではないため、アシスタントはユーザーに提案するアプリを決定します。

### アクション呼び出しフレーズの追加

**Dialogflow**

Dialogflowでは、あなたのアクションとその呼び出しフレーズは、ユーザーがDialogflow内のGoogleアシスタントのインテグレーションで設定できるトリガー可能なインテントのための **User Says** 表現として定義されます。

![](https://developers.google.com/actions/images/triggering-intents.png){:width="565px"}

より詳しい情報は、[Google Assistant integration](https://dialogflow.com/docs/integrations/google-assistant#triggering-intents)
のDialogflow文書を参照ください。

**Actions SDK**

Actions SDKでは、アクションと呼び出しフレーズは、クエリーパターンにて定義されます。詳しい情報は、
[Actions SDK docs](https://developers.google.com/actions/sdk/define-actions)を参照ください。

**Actions Console**

[Actions console](https://console.actions.google.com/)では、**Action discovery and re-engagement** の下の **Overview** ページであなたのアクションと呼び出しフレーズを見ることができます。

> **注:** Dialogflowを使用していて、このページにあなたのアプリのアクションが掲載されていない場合、それらの行方不明アクションは、追加のトリガーインテントとして設定されていない可能性が非常に高いです。Dialogflowでインテントを正しく設定するためには、 **Add Actions from Dialogflow** ボタンをクリックしてください。

![](https://developers.google.com/actions/images/discovery-console-1.png){:width="752px"}

表示されているアクションのいずれかをクリックすると、呼び出しフレーズを表示できます。Dialogflowを使用している場合、Dialogflowのアプリのインテントに直接リンクする **Add more phrases** オプションがあります。

![](https://developers.google.com/actions/images/discovery-console-2.png){:width="700px"}

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/discovery/implicit)
