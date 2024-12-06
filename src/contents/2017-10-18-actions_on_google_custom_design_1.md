---
layout: actions_on_google
title: Design Overview "Conversational UI and Why It Matters"を日本語訳しました
categories:
- actions on google
---
Google AssistantへのアプリによるUIの提供は、会話型になります。つまり、GUIのような「画面の操作」ではないので、アプリの開発者はどのように会話を組み立てれば良いのか、悩むはずです。Actions on Googleのドキュメントでは、アプリのデザインの概要として、[Conversational UI and Why It Matters](https://developers.google.com/actions/design/)が掲載されています。以下はその日本語訳になります。原文でも難しめの記載になってるため、日本語訳も微妙な感じになってますが、キーワードだけでも知っていると役に立つと思います。

---

# 会話型UIとそれが重要な理由

> [PDFをダウンロード](https://developers.google.com/actions/downloads/get-started.pdf)

機械学習と人工知能の進歩によって、会話インターフェースや自然言語処理に関心が高まり、そして技術を使った新しい対話形式としての会話の可能性を生み出すコンピューティングの有望な新しい時代に、私たちは入っています。

<iframe class="devsite-embedded-youtube-video" data-autohide="1" data-showinfo="0" frameborder="0" allowfullscreen="" id="ytplayer0" data-enablejsapi="1" src="//www.youtube.com/embed/ulAKc2gezfU?autohide=1&amp;showinfo=0&amp;enablejsapi=1" origin="https://developers.google.com" style="border: 0;"></iframe>

ほとんどの場合、話し言葉での入力の認識に関する問題は大きく解決され、自然な人間の会話をモデルとしたユーザーエクスペリエンスを構築する新しい挑戦が始まっています。

このサイトでは、会話の基本的な仕組みを概説し、設計するための基本原則を紹介し、そして実際のUIツールキットを提示して、エンゲージ、喜び、本当に役立つ会話体験の作成を開始します。

## 会話の基礎

私たちは、自然な人間の会話ではほとんど気づいていない規則や慣習を解体するだけで、良い会話のための重要な要素を発見することができます。会話を成功させるためのビルディングブロックは、以下を含みます。

### ターンテイキング（交互に話す）

我々は、前後に挟まる微妙な信号に基づいて、会話を交互に行います。効果的なターン・テイクがなければ、お互いに話し出すかもしれませんし、会話が同期しなくなり、会話に従うことが難しくなります。

### スレッディング（脈略）

自然言語では、会話のすべての要素は、通常、文脈と会話が時間とともに進化する方法を含む一貫したスレッドでまとめられています。 スレッディングは、会話フローを追跡するのに役立ちます。

### 言語の固有効率性を活用する

人々は、何を言われているのか直感的に理解しているため、しばしば言葉のショートカットを使います。基本的には、私たちは会話の中で「行間を読む」ことができ、話さなくてもいくつかのことが残ることがあります。しかし、ソフトウェアベースのシステムとの会話の意味合いは、人間の言語の一見非論理的で非数学的な性質を補う必要があります。

### 変わりやすいユーザー行動の予測

人々は、以前の経験に基づいた文脈の状況や会話の予測に依存して、同じことを言うためにさまざまな言葉やスタイルを使用します。そのため、すべてのユーザーが摩擦がない体験をするように、UIはそれらのバリエーションをサポートする必要があります。

いわゆる「ハッピー・パス」にのみ焦点を当てる代わりに、設計者は「エラー」のようなシナリオであっても、すべてのシナリオで堅牢なエクスペリエンスを作成できます。何かの会話でうまくいかなかったとしても、人間が普通に自分の間違いを見つけて修復するのと同じように、設計者はやりとりの流れや性質に基づいて会話を修復することもできなければなりません。

詳しくは、[Understanding How Conversations Work: The Key to a Better UI](https://developers.google.com/actions/design/how-conversations-work)の会話の基本をご覧ください。


## 協調行動の理解

ターンテイキング、文脈、スレッディングは、全て協調的な会話の一部であり、言語学者の[Paul Grice](https://plato.stanford.edu/entries/grice/)によって一般化された考えです。Griceは、これを協調の原理と呼びました。彼はまた、Grice's Maximを開発して、彼が観察した本質的な会話のルールを定義しました。つまり、お互いに話し合うときに、人々はできるだけ正直で情報に富み、関連性があり、そして明確でなければなりません。

UIは、これらの固有の協力ルールにも従うべきであり、過去に他の会話型インターフェースとの間に悪い経験をしている用心深いユーザーをサポートする準備があるべきです。

Cooperative Principleについては、[Be Cooperative...Like Your Users](https://developers.google.com/actions/design/be-cooperative)を読んでください。


## 話し言葉の力を解き放つ

良いUIは、古くなったスクリプトに従わず、そしてユーザーに限られたパスを強いる古いタッチトーン電話システムに基づいてはいけません。また、いわゆる「ハッピー・パス」から逸れることから守るために、人々が言うことを「教える」ことは、試みるべきではありません。

代わりに、言語と意味の直感的な力に焦点を当て、日常の言語を使用してユーザーとコミュニケーションをとる必要があります。UIは、明白なことを述べたり、ユーザを説き伏せることは避けるべきです。人々は、彼らよりも利口だと思うように聞こえるデバイスには、感謝しません。

直感的なUIを構築する方法の詳細については、[Unlocking the Power of Language](https://developers.google.com/actions/design/unlocking-the-power-of-spoken-language)を参照してください。

## ユーザーの信頼を向上させる

良いUIとは、彼らの信用を得て、そして信頼を向上させるために、ユーザーの入力を検証し、期待を管理することも意味します。

誰かがリクエストを行うと、UIは「OK」「Sure」「Alright」「Thanks」「Got it」のような単語やフレーズの肯定応答を使うことで、UIが関与し聞いていることを示します。肯定応答を無作為にすると、より流動的で自然な気分を体験させることを助けることができます。

確認応答後、システムは、理解した明示的または暗黙的な確認を要求することができます。明示的な確認（航空チケットを購入するなど、大きな何かがかかっている時に通常使用される）により、UIは先に進む前にユーザーに言葉の同意を求めます。

暗黙の確認（歌をストリーミングするなどの低リスクの状況で使用される）では、UIは、ユーザーの信頼性を検証し獲得するために、ユーザーの要求の重要な要素をレスポンスに含めますが、それ自体は口頭での承認を必要としません。

[Instilling User Confidence Through Confirmations and Acknowledgements](https://developers.google.com/actions/design/instilling-user-confidence)を読んでください。

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/design/)
