---
layout: post
title: Google Assiatant対応AI BotをActions on Google作る
categories:
- Bot
---
今年はGoogleからAlloアプリが登場し、さらにGoogle AssistantというAIを中心としたサービス展開が行われることが表明されました。もちろんGoogleはそれをプラットフォームとして広く使えるようにするだろうと言われていましたが、遂に<a href="https://developers.google.com/actions/">Actions on Google</a>という名前でGoogle Assistantと連携可能なBotを作るための環境が公開されました。

![actions_on_google_1.png]({{ "/images/2016/12/actions_on_google_1.png" | prepend: site.baseurl }})

Google Assistantは、Alloアプリ内で@googleという名前のアカウントとして登場しています。また、Google Homeもその裏にはGoogle Assistantがいます。Google Assistantが@googleアカウントやGoogle Homeへのユーザからの問い合わせを理解して、適切なアクションを行い、その結果を返すことになります。そのアクションを自由に作ることができる仕組みが、Actions on Googleです。

Actions on Googleでは、会話アクションとダイレクトアクションの2つがあって、会話アクションはSDKを使ってアクションを実装する方法と、API.AIを利用してアクションを実装する方法の2つが現在提供されています。API.AIを利用することで、簡単に会話アクションを試しに作ってみることができます。Google Homeのシミュレータもあるので、アクションが実際にどう呼び出されるか、実体験することも可能です。

ここでは、<a href="https://developers.google.com/actions/develop/apiai/tutorials/getting-started">API.AIを使った会話アクションを作るGetting Started</a>に書かれている内容を紹介したいと思います。

# どのようなアクションを作るか

テキストにすると、以下のような会話内容になります。

{% highlight txt %}
Silly Name Maker: Hi! Welcome to Silly Name Maker! Let's get started. What is your lucky number?
User: 22
Silly Name Maker: What is your favorite color?
User: Blue
Silly Name Maker: Alright, your silly name is Blue 22. I hope you like it! See you next time!
{% endhighlight %}

ラッキーナンバーと好きな色を聞いて、くだらない(?)名前を作って答える、というシンプルな会話型のアクションです。

# API.AIエージェントの作成

最初に、API.AIに新規にエージェントを作成します。

1. <a href="https://console.api.ai/api-client/#/newAgent" target="_blank">新しいエージェントを作成するための画面</a>を開きます。
1. 以下のように入力します。
  * Agent name: "SillyNameMaker" と入力。
  * Agent Type: Private を選択。
  * Description: "Use this action to come up with your silly name!" と入力。
  * Add Sample Data: 空欄のままでOK。
  * Language: "English" を選択。
  * Default Time Zone: "(GMT+9:00) Asia/Tokyo" を選択。
1. "SAVE" ボタンを押します。

![actions_on_google_2.png]({{ "/images/2016/12/actions_on_google_2.png" | prepend: site.baseurl }})

新規に SillyNameMaker というエージェントが作られます。

![actions_on_google_3.png]({{ "/images/2016/12/actions_on_google_3.png" | prepend: site.baseurl }})

# Default Welcome Intentの編集

作成された SillyNameMaker エージェトには、予め Default Welcome Intent が作られています。このインテントは、ユーザがアクションを呼び出した際の WELCOME イベントによって最初に起動されます。このインテントが適切に歓迎をユーザに伝えることで、Googleからの応答ではないことを明確にすることが求められます。

歓迎のメッセージを変更してみましょう。

左のメニューから "Intents" をクリックして、Default Welcome Intent を選択します。

![actions_on_google_4.png]({{ "/images/2016/12/actions_on_google_4.png" | prepend: site.baseurl }})

次に、Text Response の右にあるゴミ箱ボタンを押して、事前に登録されている内容を全て削除します。

![actions_on_google_5.png]({{ "/images/2016/12/actions_on_google_5.png" | prepend: site.baseurl }})

そして、ADD MESSAGE CONTENT ボタンを押して Text Response を選択し、"Hi! Welcome to Silly Name Maker! Let's get started. What is your lucky number?" と入力します。

![actions_on_google_6.png]({{ "/images/2016/12/actions_on_google_6.png" | prepend: site.baseurl }})

その後、SAVE ボタンを押します。

# 愚かな名前を作成するインテントの追加

このエージェントがユーザに挨拶を送った後、別のインテントが起動されます。ここでは、馬鹿げた名前を作成するインテントを新規に追加します。

まず、左の Intents メニューの右にある追加（＋）ボタンを押します。

![actions_on_google_7.png]({{ "/images/2016/12/actions_on_google_7.png" | prepend: site.baseurl }})

そして、インテントの名前として "make_name" と入力し、SAVE ボタンを押します。

![actions_on_google_8.png]({{ "/images/2016/12/actions_on_google_8.png" | prepend: site.baseurl }})

# User says Phaseの定義

新しく追加した make_name インテントが起動されるきっかけとなる、User says phase を定義します。User says フィールドに、以下の2つを入力します。

* 23
* My lucky number is 23

![actions_on_google_9.png]({{ "/images/2016/12/actions_on_google_9.png" | prepend: site.baseurl }})

注意点として、上記の2つを入力する際に、ENTER キーを押さずに、TAB キーで次の行に移動させてください。ENTER キーを押してしまうと、自動的に Action が作られてしまいます。

# アクションの定義

次に、make_name インテントが起動した際のアクションを定義します。

1. Action エリアの "Enter action name ..." の部分に、"make_name" と入力する。
1. 以下の2つのパラメータを入力する。
  * Number -  Required: チェックを入れる、Parameter name: "number"、Entity: "@sys.number"、Value: "$number"
  * Color -  Required: チェックを入れる、Parameter name: "color"、Entity: "@sys.color"、Value: "$color"

Number パラメータを入力した後は、Color パラメータを入力するために、"+ New paraeter" をクリックします。

![actions_on_google_10.png]({{ "/images/2016/12/actions_on_google_10.png" | prepend: site.baseurl }})

そして、それぞれの "Define prompts" をクリックして、ユーザへの質問文を入力します。

* Number: "What is your lucky number?"
* Color: "What is your favorite color?"

![actions_on_google_11.png]({{ "/images/2016/12/actions_on_google_11.png" | prepend: site.baseurl }})

![actions_on_google_12.png]({{ "/images/2016/12/actions_on_google_12.png" | prepend: site.baseurl }})

# 返事の定義

Number と Color の入力がユーザから行われた後は、入力された値を使って文章を返します。Text Response に "Alright, your silly name is $color $number. I hope you like it! See you next time!" と入力し、SAVE ボタンを押します。

![actions_on_google_13.png]({{ "/images/2016/12/actions_on_google_13.png" | prepend: site.baseurl }})

# デプロイ

ここまでくれば、あとはもう少しで動作確認することができるようになります。動作確認は、Google Home のシミュレータになります。もちろん、音声入力して、結果がやはり音声出力されますので、まさに Google Home 感覚です。

まず、左のメニューから Integrations をクリックして、Actions on Google のスライダーをクリックして有効にします。

![actions_on_google_14.png]({{ "/images/2016/12/actions_on_google_14.png" | prepend: site.baseurl }})

すると、自動的にダイアログが表示されます。以下の内容で入力し、AUTHORIZE ボタンを押します。認証ウィンドウが表示された時は、同意しましょう。ボタンが AUTHORIZE から PREVIEW に変わりますので、PREVIEW ボタンを押します。

* Invocation name for testing: "Silly Name Maker" と入力。
* Google Project ID: 空欄のままでOK。
* TTS voice: 好きな声を選択。
* Welcome Intent: "make_name" を選択。
* Sign in required for welcome intent: チェックは入れない。
* Additional triggering intents: 空欄のままでOK。

![actions_on_google_16.png]({{ "/images/2016/12/actions_on_google_16.png" | prepend: site.baseurl }})

上手くいけば、Google Home Web Simulator で確認することができたことを示すメッセージが表示されます。CLOSE ボタンを押してダイアログを閉じます。

![actions_on_google_17.png]({{ "/images/2016/12/actions_on_google_17.png" | prepend: site.baseurl }})

その後、もう一度左のメニューの Intents から make_name インテントを選択します。そして、下の方にある Actions on Google 項目を展開して、End conversation にチェックを入れて、SAVE ボタンを押しておきます。

![actions_on_google_18.png]({{ "/images/2016/12/actions_on_google_18.png" | prepend: site.baseurl }})

# テスト

お待たせいたしました。Google Home Web Simulator で動作確認してみましょう。まずは、<a href="https://developers.google.com/actions/tools/web-simulator" target="_blank">Google Home Web Simulator</a>を開きます。

![actions_on_google_19.png]({{ "/images/2016/12/actions_on_google_19.png" | prepend: site.baseurl }})

START ボタンを押してください。

![actions_on_google_20.png]({{ "/images/2016/12/actions_on_google_20.png" | prepend: site.baseurl }})

マイクのボタンを押して音声入力をしてもいいですし、テキストで入力しても構いません。まず、アクションを起動するために、"talk to silly name maker" と入力しましょう。

![actions_on_google_21.png]({{ "/images/2016/12/actions_on_google_21.png" | prepend: site.baseurl }})

ラッキーナンバーを聞いてきますので、適当に数値で答えます。その後、好きな色も聞かれますので、適当に答えましょう。

![actions_on_google_22.png]({{ "/images/2016/12/actions_on_google_22.png" | prepend: site.baseurl }})

"Alright, your silly name is Green 41. I hope you like it! See you next time!" というように、無事に "Green 41" という名前を授かることができました。

# 最後に

今回は、API.AI の機能を使って、プログラミングすることなく、会話形式のアクションを Google Assistant 向けに作るチュートリアルを紹介してみました。Actions on Google では、会話形式のアクションを SDK を使って作ることもできますし、食べ物の注文やIoT機器の操作、音楽やニュース、旅行の予約などを行うための Direct Action も将来開発することができるようになるようです。

もちろん、開発したアクションを実際に一般公開して Google Home から実際に利用できるようにするためには、Googleの審査が必要になります。しかし、シミュレータで試すだけであれば上記のように手軽にできますので、ぜひ時間を見つけて試してみてください。
