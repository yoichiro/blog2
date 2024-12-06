---
layout: actions_on_google
title: Build with Dialogflow "Design a Conversation"の日本語訳です
categories:
- actions on google
---
Dialogflowを使ってGoogleアシスタント向けのアプリを開発する最初のステップは、Dialogflowを使わずに、まず会話を設計することです。ユースケースを選び、ペルソナを作成して、アプリ名を考え、そしてダイアログ（対話: ユーザとアプリでやり取りされる具体的な会話の内容）を書き出しています。以下は、[Build with Dialogflow - Design a Conversation](https://developers.google.com/actions/dialogflow/design-conversation)にて掲載されている上記の手順に関する日本語訳です。

---

# 会話を設計する

Googleアシスタントの機能を拡張する時は、会話型のユーザーインターフェイスを通じてユーザーが物事を達成することができるアクションを作成します。会話型インターフェイスは設計にとっては斬新なものである可能性が高いため、アプリを構築する際には、私たちの設計プロセスとベストプラクティスを実施することを強くお勧めします。これはまた、承認のために提出したときに、あなたのアプリが承認される一番のチャンスをあなたに与えます。

<iframe class="devsite-embedded-youtube-video" data-autohide="1" data-showinfo="0" frameborder="0" allowfullscreen="" id="ytplayer0" data-enablejsapi="1" src="//www.youtube.com/embed/MSUPVbbhIGA?autohide=1&amp;showinfo=0&amp;enablejsapi=1" origin="https://developers.google.com" style="border: 0;"></iframe>

アクションの構築を始める最善の方法は、私たちの[設計原則](https://www.eisbahn.jp/yoichiro/2017/10/actions_on_google_custom_design_3.html)によって定義されていますが、ここでは原則をどのように実践するかについて簡単に説明します。

## ユースケースを選ぶ

会話型のインタフェースは、以下の一般的な分類に落とし込まれるユースケースに対して最も機能します。

* **人々がパッと思いついて答えることができること。** 航空券の予約のような、時間や日付のような使い慣れた入力で達成できるアクション。
* **迅速に、しかし説得力のある有益な行為。** これらは、通常、商品をすばやく注文するなど、ごくわずかな時間で多くのメリットをユーザに提供します。
* **本質的に声に適したアクション。** これらは、通常、料理中にレシピを聞くことや、運転中に記憶に留めるなど、ハンズフリーでやりたいことです。

## ペルソナを作成する

ペルソナはあなたの会話をデザインし、記述するのを手伝ってくれるので、正しい単語、構文、構造を簡単に選ぶことができるように、早めに選択してください。ペルソナは、幸せ、悲しみ、自己嫌悪感、フォーマル、そして何かの中間にあるものまで、多岐にわたります。

覚えておいてください。あなたが1つ以上計画しているかどうかに関わらず、ユーザはペルソナを認識するので、偶然に任せず、あなたが認識させたいと思うような方法で体験を構築することをお勧めします。

**TTSの声。** _あなたのアクションをデプロイするときには、あなたのアクションに合わせてTTSの声を選ぶこともできます。 これらの音を聞いてください！_

<table>
  <tbody><tr>
    <th>声</th>
    <th>サンプル</th>
  </tr>
  <tr>
    <td>男性 1</td>
    <td>
      <audio controls="">
        <source src="https://developers.google.com/actions/downloads/tts-voices/male1-demo1.mp3" type="audio/mp3">
        <p>Your user agent does not support the HTML5 Audio element.</p>
      </audio>
      </td>
  </tr>
  <tr>
    <td>男性 2</td>
    <td>
      <audio controls="">
        <source src="https://developers.google.com/actions/downloads/tts-voices/male2-demo1.mp3" type="audio/mp3">
        <p>Your user agent does not support the HTML5 Audio element.</p>
      </audio>
     </td>
  </tr>
  <tr>
    <td>女性 1</td>
    <td>
      <audio controls="">
        <source src="https://developers.google.com/actions/downloads/tts-voices/female1-demo1.mp3" type="audio/mp3">
        <p>Your user agent does not support the HTML5 Audio element.</p>
      </audio>
    </td>
  </tr>
  <tr>
    <td>女性 2</td>
    <td>
      <audio controls="">
        <source src="https://developers.google.com/actions/downloads/tts-voices/female2-demo1.mp3" type="audio/mp3">
        <p>Your user agent does not support the HTML5 Audio element.</p>
      </audio>
      </td>
  </tr>
</tbody></table>

## アプリ名を考える

あなたのアプリの名前は、ユーザーがあなたのアプリを呼び出す主要な方法です。そのため、良い名称を選ぶことが非常に重要です。アプリ名が発音しにくい、他の単語のように聞こえる、Googleでうまく認識できない場合は、ユーザーがアプリを起動するのが難しくなる可能性があります。あなたのペルソナとブランドを反映する名前を考え始めることを、ガイドラインでは支持しています。

> **注:** _実際にアプリを承認レビューのために送信する前では、Actions on Googleでアプリ名の予約が保証されるわけではありません。これにより、実際にビルドすることなく、ユーザーが不当占拠して予約することを防ぎます。_

## ダイアログ（対話）を書く

いくつかのユースケースを選択してペルソナを決定した今、開発に飛びつくよう誘惑されているかもしれませんが、その衝動に耐えてください！代わりに、あなたの会話を構成するダイアログを鉛筆と紙で、またはすばやく書ける何かで、書き留めることから始めます。

オーディオサポート、ディスプレイサポート、またはその両方を備えたデバイスで機能する必要のあるUIをデザインする場合は、最初に音声会話を書き留めておくことをおすすめします。音声会話を書くことで、オーディオのみの体験に必要なすべてのダイアログが与えられ、そこからカード、リスト、その他のアフォーダンスなどの視覚的な機能を補完することで、画面上の体験を向上させることができます。

サンプルダイアログを書く場合は、次のシナリオを考慮に入れてください。

* あまり複雑ではなく、最も簡単な方法でアクションを達成する「ハッピー・パス」。
* ユーザーが「ハッピー・パス」と同じ結末になるために必要な追加パス。
* ユーザーが予期せぬことを行う会話の修復シナリオ。たとえば、サポートしていないものや理解できないものを行うよう要求するなどです。
* ユーザーが会話の途中で終了するダイアログ、または必要な操作を行ったときにユーザーが終了するダイアログ。
* あなたのアプリが呼び出されたときに、ユーザを迎える方法のバリエーション。

## 例

この例では、Googleアシスタントで今すぐ試すことができるNumber Genie（数値予測）ゲームのハッピーパスダイアログがあります。このダイアログは、ゲームの典型的なハッピーパスを示しています。ここでは、ユーザーが3回の推測でラウンドに成功します。

<table style="background-color: #fb8c00; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>User</b>
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
Hey Google, let me talk to Number Genie. （ねえ、Google、Number Genieと話す。）
</td>
</tr>
<tr style="background: blue;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>Developer Note:</b> この最初のリクエストでは、ユーザーがアプリをその名前（この場合はNumber Genie）で呼び出す方法が説明されています。これは、アプリを呼び出すためのインテントを作動させます。
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<b>Googleアシスタント</b>
</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
Sure, here's Number Genie. （はい、ここにNumber Genieがいます。）
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<b>Number Genie</b>
</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>Welcome to Number Genie! I'm thinking of a number from 0 to 100. What's your first guess? （ようこそNumber Genieに！私は0から100までの数を考えています。あなたの最初の推測は何ですか？）</i>
</td>
</tr>
<tr style="background: blue;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>Developer Note:</b> これは、アクションのWelcomeレスポンスであり、作動された一つ前のインテントを処理します。
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>User</b>
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
50
</td>
</tr>
<tr style="background: blue;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>Developer Note:</b> そのユーザは数を言い、Dialogflowによって解釈されます。関連するインテントが作動され、あなたのフルフィルメントが呼ばれます。次の2つのユーザの応答は、同じインテントを作動させます。何故なら、文法が同じだからです。
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<b>Number Genie</b>
</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>It's higher than 50. What's your next guess? （50よりも大きいです。あなたの次の推測は何ですか？）</i>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>User</b>
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
65
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<b>Number Genie</b>
</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>It's lower than 65. Next guess? （65よりも小さいです。次の推測は？）</i>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>User</b>
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
60
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<b>Number Genie</b>
</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>Congratulations, that's it! I was thinking of 60. Wanna play again? （おめでとうございます、それです！私は60を考えていました。もう一度遊びますか？）</i>
</td>
</tr>
<tr style="background: blue;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>Developer Note:</b> フルフィルメントは最終的に勝利の数を検知して、ユーザに再び遊ぶかどうかを問い合わせます。この質問が回答された時、ユーザは異なるインテントを作動させます。
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>User</b>
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
No thanks. （いいえ、ありがとう。）
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<b>Number Genie</b>
</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>Alright, talk to you later then. （わかりました。後で話しましょう。）</i>
</td>
</tr>
<tr style="background: blue;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>Developer Note:</b> フルフィルメントは、ユーザがゲームを抜けて会話を終わらせたいことを検知します。
</td>
</tr>
</table>

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/dialogflow/design-conversation)
