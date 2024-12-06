---
layout: actions_on_google
title: Invocation and Discovery "Explicit Invocation"の日本語訳です
categories:
- actions on google
---
Googleアシスタント向けアプリのアプリ名をユーザが知っている場合は、ユーザはそのアプリ名を使ってそのアプリを呼び出すことができます。
これを明示的な呼び出し（Explicit Invocation）と呼びます。その際、例えば
「オッケーグーグル、マイレシピアプリで **今日のスープレシピについて教えて** 」というように、アプリに対して呼び出しと意図を同時に
ユーザが伝え、そのことをアプリが受け取ることができます。こうすることで、ユーザがアプリを利用するための対話の数を削減できます。

Actions on Googleのドキュメントにある
[Invocation and Discovery - Explicit Invocation](https://developers.google.com/actions/discovery/explicit)にて、
明示的にアプリを呼び出す方法が説明されています。以下はその日本語訳です。

---
# 明示的な呼び出し（Explicit Invocation）

![](https://developers.google.com/actions/images/explicit-invocation.png){:width="600px"}

明示的な呼び出しは、ユーザーがアプリを名前で使用するようにGoogleアシスタントに指示したときに発生します。
必要に応じて、ユーザーは呼び出しの最後にアクション呼び出しフレーズを含めて、要求している関数を直接呼び出すことができます。

> **注:** ユーザーは通常、[アシスタントディレクトリ](https://developers.google.com/actions/distribute/directory)
（Googleアシスタント上のアプリのためのウェブストアのようなもの）からアプリについて学びます。このディレクトリは、
アプリを発見するための主要なソースですので、良いアプリリストを作成するための開発時間を費やしてください。

## 明示的な呼び出しのコンポーネント

次の例は、明示的な呼び出しコマンドの可能なさまざまなコンポーネントを示しています。

![](https://developers.google.com/actions/images/explicit-invocation-grammar.png){:width="600px"}

### トリガーフレーズ

これらのフレーズは、明示的な呼び出しを開始します。これらは、Actions on Googleによって定義されます。

* _"Ok Google, talk to..."_
* _"Ok Google, speak to..."_
* _"Ok Google, I want to speak to..."_
* _"Ok Google, ask..."_

### アプリ名（アプリ呼び出し）

これは、"Personal Chef"のような、あなたのアプリの名前です。 _"Ok Google, let me talk to Personal Chef."_ のように、ユーザは、アプリ名によって明示的にアプリを呼び出すために、これとトリガーフレーズを組み合わせます。基本的な明示的な呼び出しのこの種別は、アプリ呼び出しと呼ばれます。

> **注:** アプリの名前を決めるときには、呼び出しについて気にすることが重要です。
巧みなアプリ名を持っていてもGoogleがそれを認識しない場合、ユーザーはあなたのアプリを呼び出す時にトラブルとなるでしょう。
詳細については、[アプリの命名ポリシー](https://developers.google.com/actions/policies/general-policies#naming_directory_listing_and_promotion)と[ベストプラクティス](https://developers.google.com/actions/discovery/checklist)を参照してください。

### アクション呼び出しフレーズ（任意）

アクション呼び出しフレーズは、アプリの発見可能性を高め、ユーザーが実行するアクションを提供することでアプリにディープリンクする方法を提供する貴重なメカニズムです。これらのフレーズは、ユーザーがアプリの機能をリクエストするさまざまな方法を示しているため、Googleアシスタントはアプリの機能をより良く理解し、アプリのアクションとユーザーのリクエストを一致させることができます。

* _"find me recipes"_
* _"recommend a wine"_
* _"book a ride"_
* _"play a game"_
* _"want to meditate"_

ユーザーは、アプリ名とアクション呼び出しフレーズを組み合わせて、 _"Ok Google, talk to Personal Chef to find me recipes."_ などの特定のアクションを明示的に呼び出すことができます。このタイプのタスク固有の明示的な呼び出しは、アクション呼び出しと呼ばれます。

> **注:** アクション呼び出しフレーズがあなたのアプリに固有であり、実際のユースケースを記述していることを確認してください。
よくある間違いは、あなたのアプリの目的が示されるにはあまりにも一般的なアクション呼び出しフレーズを定義することです。
これは、あなたのフレーズがあまり役に立たず、アプリの発見を妨げます。詳細については、[暗黙的な呼び出し](https://developers.google.com/actions/discovery/implicit#writing_useful_action_invocation_phrases)と[ベストプラクティス](https://developers.google.com/actions/discovery/checklist)を参照してください。

## 明示的な呼び出しのインテント

あなたのアプリがアクション呼び出しフレーズなしで明示的に呼び出された場合（ _"Ok Google, talk to MyRecipeApp"_ など）、アプリにおいて `actions.intent.MAIN` インテントがトリガーされます。アプリには、このインテントを処理するデフォルトアクションが1つだけ含まれている必要があります。

アプリが明示的に名前とアクション呼び出しフレーズを伴って呼び出された場合（たとえば、 _"Ok Google, talk to MyRecipeApp for today's soup recipe"_ ）、そのアクション呼び出しフレーズに対応するインテントが呼び出されます。

詳細については、[インテント](https://developers.google.com/actions/reference/rest/intents)に関するドキュメントを参照してください。

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/discovery/explicit)
