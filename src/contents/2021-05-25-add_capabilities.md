---
layout: post
title: AndroidアプリにCapabilityを追加する方法
categories:
- actions on google
---

2021年5月18日から行われたGoogle I/O 2021では、Googleアシスタントに関する新しい発表がいくつかありました。その中で、Androidアプリの機能をGoogleアシスタントから呼び出すことができるようになるApp Actions機能について、新しいフレームワークが発表されました。

従来は `actions.xml` リソースを定義して、インテントとディープリンクの関連を定義することでApp Actionsの組み込みが可能でした。これ自体は今後も継続して提供されますが、この度新しく「Capabilities」という機構が提供されることがアナウンスされています。Capabilities では、Androidのショートカットフレームワークを使って、 `shortcuts.xml` リソースを定義することになります。そのリソースの中で、インテントとアクティビティの関連付けを宣言することが可能になります。宣言された capability は、ショートカットに関連付けることができるようになります。

おそらく、以下の使い分けとなります。

* `actions.xml` - すでにAndroidアプリが何らかのディープリンクを提供していて、それをGoogleアシスタント対応したいとき。
* `shortcuts.xml` - Androidアプリが持つ機能やコンテンツへのショートカットを提供し、さらにGoogleアシスタントからもそれらを呼び出せるようにしたいとき。

以下は、Capabilities を利用する際に必要となる知識を説明した [Add capabilities](https://developer.android.com/guide/topics/ui/shortcuts/adding-capabilities) ドキュメントの日本語訳です。2021年5月25日時点ではこの機構はベータ版扱いなので、正式リリースまでに変更が入る可能性があります。ご注意ください。

----

# Add capabilities


`shortcuts.xml` にて Capabilities を使用すると、ユーザーがアプリを起動して特定のタスクの実行に直接ジャンプするために実行できるアクションの種類を宣言できます。たとえば、Googleアシスタントアプリアクションは、開発者がアプリ内の機能を [組み込みインテント](https://developers.google.com/assistant/app/intents) （BII）に拡張できるようにするために capabilities を使用して、ユーザーが音声コマンドを使用してこれらの機能をアクティブにして制御できるようにします。capability は、アクションの名前と、ユーザーの意図を解決するアプリ内の宛先をターゲットとする `intent` で構成されます。

## shortcuts.xmlでのcapabilitiesの定義


Androidアプリ開発プロジェクト内の `shortcuts.xml` リソースファイルにて、 `capability` 要素を定義します。 `capability` 要素を定義するために、以下を行います。

1. [Create static shortcuts](https://developer.android.com/guide/topics/ui/shortcuts/creating-shortcuts#static)  の指示に従って、 `shortcuts.xml` リソースを作ります。
2. 次に、定義したい capability にて必要となる以下の情報を要素に含めます。

* Capability name: アプリでサポートするアクション。capabilityの定義が必要とする機能については、コンポーネントのドキュメントを参照してください。 App Actionsの音声対応コマンドは、 [組み込みインテントリファレンス](https://developers.google.com/assistant/app/reference/built-in-intents) にある capability 名称に BII `Action ID` を使用します。たとえば、 [GET_THING](https://developers.google.com/assistant/app/reference/built-in-intents/common/get-thing) BII は、一覧内でその `Action ID` として `actions.intent.GET_THING` と掲載されています。
* App destination: ユーザーの要求を満たすためにアクションが起動するアプリ内の宛先。 `capability` 内にネストされた `intent` 要素を使用してアプリの宛先を定義します。
* Parameter mapping: それぞれの `intent` には、インテントの `extra` データとして渡されるパラメーターが含まれる場合があります。たとえば、各App Actions BIIには、BIIをトリガーするクエリでユーザーが時々提供する情報を表すフィールドが含まれています。

次の例は、 [actions.intent.START_EXERCISE](https://developers.google.com/assistant/app/reference/built-in-intents/health-and-fitness/start-exercise) の `shortcuts.xml` での capability 定義を示しています。これは、ユーザーがアシスタントで音声コマンドを使用してフィットネスアプリで運動を開始できるようにするBIIです。


```
<shortcuts xmlns:android="http://schemas.android.com/apk/res/android">
  <capability android:name="actions.intent.START_EXERCISE">
    <intent
      android:action="android.intent.action.VIEW"
      android:targetPackage="com.example.sampleApp"
      android:targetClass="com.example.sampleApp.ExerciseActivity">
      <parameter
        android:name="exercise.name"
        android:key="exerciseType"/>
    </intent>
  </capability>
</shortcuts>
```

上記の例では、 `<capability>` `android:name` 属性は `START_EXERCISE` BIIを参照しています。ユーザーがアシスタントに「Hey Google, start run in ExampleApp.」と尋ねて、このBIIを呼び出すと、アシスタントはネストされた `intent` 要素で提供される情報を使用してユーザーリクエストを処理します。このサンプルの `intent` は、次の詳細を定義しています。

* このインテントのために、対象のアプリケーションのパッケージが `android:targetPackage` にセットされています。
* `android:targetClass` フィールドには、 `com.example.sampleApp.ExerciseActivity` アクティビティが宛先として指定されています。
* インテントの `parameter` は、 [exercise.name](https://developers.google.com/assistant/app/reference/built-in-intents/health-and-fitness/start-exercise#recommended-fields) 組み込みインテントパラメータのサポートと、ユーザから収集されたパラメータ値を `intent` の追加データとして渡すための方法を宣言しています。

## capabilityをショートカットに関連付ける


capability を定義したら、静的または動的なショートカットを関連付けることで、その機能を拡張できます。ショートカットを `capability` にリンクする方法は、実装されている機能と、ユーザーの要求に含まれている実際の単語によって異なります。たとえば、ユーザーがアシスタントに「Hey Google, start a run in ExampleApp.」と尋ねることでフィットネス追跡アプリでランニングを開始するシナリオを想像してみてください。アシスタントはショートカットを使用して、 `exercise.name` パラメーターの "run" の有効なエクササイズエンティティを定義する `capability` のインスタンスを起動できます。

App Actionsにショートカットを関連付けする方法については、 [App Actions overview](https://developers.google.com/assistant/app/overview) をご覧ください。

