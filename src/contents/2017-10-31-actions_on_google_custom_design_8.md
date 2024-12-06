---
layout: actions_on_google
title: Best Practices "Unlocking the Power of Spoken Language"を日本語訳しました
categories:
- actions on google
---
普段の会話と比べて、自動音声応答に対して「機械的だなぁ」と思ってしまったことは誰しもあることだと思います。これは、ちょっとしたことに気をつけるだけで、避けることができます。どうしてもコンピュータ相手には「命令をする」というイメージで考えがちですが、会話型UIでは、あくまで相手はコンピュータかどうかではなく、「会話相手」として自然に感じるような設計が必要です。

Actions on GoogleのBest Practicesに[Unlocking the Power of Spoken Language](https://developers.google.com/actions/design/unlocking-the-power-of-spoken-language)というページがあり、そこでまさに自然な受け答えをするために注意すべき点が掲載されていました。以下は、その日本語訳です。ここに書かれたことを意識して設計するだけで、Googleアシスタント向けのアプリとして、とても良い会話をしてくれるようになるはずです。

---

# 話し言葉の力のロックを解除する

[PDFをダウンロードする](https://developers.google.com/actions/downloads/unlocking-the-power-of-spoken-language.pdf)

会話型UIの利点は、人々がすでに話す方法を知っていることです。ビジュアルインターフェイスのボタンやタッチトーン電話システムのキーの意味とは異なり、よく設計されたユーザーインターフェイスは直感的であり、コマンドを教える必要はありません。それでもなお、助けを求めているか、進め方がわからない（特に新しいユーザーの場合）人々のために、何が言えるのかを人々に知らせなければならないことがあります。

ここでは、直感的な話し言葉の性質を利用して、より良い会話を自然に実現できるUIを構築するためのいくつかの指針を示します。

## 何が理解されたかを伝える

人が質問をしたり、タスクやアクションを実行する方法を尋ねる場合、システムが聞いたことを知ることで会話技術への信頼を高めるために、UIはシステム（"認識装置"）が理解した（または「解析した」）ことを伝えるべきです。私たちがユーザーの意図の[暗黙的な確認](https://developers.google.com/actions/design/instilling-user-confidence#implicit_confirmations)と呼ぶことは、次のような簡単なことです。

<table style="background-color: #fb8c00; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
User
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
Who made the statue David? （ダビデ像を作ったのは誰ですか？）
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
UI
</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>David was created by Michelangelo. （ダビデ像はミケランジェロによって作られました。）</i>
</td>
</tr>
</table>

または、より込み入った答えの一部になります。例えば以下です。

<table style="background-color: #fb8c00; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
User
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
How do I cancel an alarm? （どのようにすればアラームをキャンセルできますか？）
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
UI
</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>If you have only one alarm and you want to cancel it, just say "Cancel my alarm." Otherwise, try saying things like "Cancel my 7 a.m. alarm" or "Cancel all my alarms." （もしアラームが一つだけの場合にそのアラームをキャンセルしたい場合は、「アラームをキャンセル」と言うだけです。そうではない場合は、「午前7時のアラームをキャンセル」または「全てのアラームをキャンセル」のように言うことを試してください。）</i>
</td>
</tr>
</table>

覚えてください、音声のみのインターフェイスの直進的な性質のために、人々は先に飛ぶことができません。彼らは一つの単語を聞く必要があります。前。聞く。その次。これと、画面にレスポンスを表示して一度に返す方法とを対比させると、「Michelangelo, artist （ミケランジェロ、彫刻家）」（おそらく画像が付いている可能性が高い）と表示してその場を切り抜けることができます。一方のUIは、システムが聞いていることが実際に彼らが求めたものであることをユーザーに知らせる必要があります。別の言い方をすれば、新しい情報が最後に提示されます。これは、エンドフォーカスの原則として知られています。

## 人々が何をどのように言うことができるかを示す例を提供する

UIはまた、問い合わせや以下のやり取りのような複雑さのレベルを示す例を言い表すことができる様々な方法を人々に提供すべきです。

<table style="background-color: #fb8c00; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
User
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
How do I set an alarm? （どのようにすればアラームをセットできますか？）
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
UI
</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>To set an alarm, try saying things like "Set an alarm for 6 a.m.," or "Wake me up in two hours," or "Set a 'medication' alarm for everyday at 7 p.m." （アラームをセットするために、「午前6時にアラームをセット」や「2時間後に起こして」もしくは「毎日午前7時に"薬"アラームをセット」のようなことを言ってみてください。）</i>
</td>
</tr>
</table>

## 明白なことを避ける

情報が不十分なメッセージは、あなたのペルソナ（あなたのブランドでもあります）を不快で深みがないようにさせてしまいます。人々は、ユーザの知性を過小評価しているデバイスに感謝しません。「どうすればニュースを入手できますか？」と尋ねる人の例を見てみましょう。

まったく役に立たないものを考えてみます。

<table style="background-color: #78909c; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>To get the news, say "Get the news." （ニュースを得るために、"ニュースを得る"と言ってください。）</i>
</td>
</tr>
</table>

そして、変化のために単語を入れ替えても価値はありません。

<table style="background-color: #78909c; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>To listen to the news, say "Hear the news." （ニュースを聞くために、"ニュースを聞く"と言ってください。）</i>
</td>
</tr>
</table>

その同じ人は、以前の問い合わせの組み立てを知っていたか、試しています。人々は、おそらく他に何ができるかを探そうとしています。その代わりに、より広い範囲の可能性を推論するための直感的な指示を作成してください。

<table style="background-color: #fb8c00; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>If you're interested in recent headlines, you can say, for instance, "Tell me the latest news." Or try asking for a specific category, like technology or sports news. （最近の見出しに興味がある場合は、例えば「最新のニュースを教えてください」と言うことができます。または、テクノロジーやスポーツのニュースなど、特定の分類を尋ねてみてください。）</i>
</td>
</tr>
</table>

それでも、UIは、明示的に助けを求める人がいなくても、何を言うべきかを人に伝える必要がしばしばあります。ここでも、明白なことを避けるべきです。

例えば、次のような文字通りの命令を使う代わりに、

<table style="background-color: #78909c; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>Editing your shopping list is easy. To add an item, just say "Add," followed by the item you'd like to add. To remove an item, just say "Remove," followed by the item you'd like to remove. （ショッピングリストを編集するのは簡単です。商品を追加するには、追加したい商品に続いて「追加」と言ってください。商品を削除するには、削除したい商品に続いて「削除」と言ってください。）</i>
</td>
</tr>
</table>

代わりとなる直感的で分かりやすくすることを検討してください。

<table style="background-color: #fb8c00; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>By the way, to edit your list, you can say something like "Add toothpaste" or "Take off the ice cream." （ちなみに、あなたのリストを編集するには、「歯磨き粉を追加する」または「アイスクリームを外す」のように言うことができます。）</i>
</td>
</tr>
</table>

## ユーザーに信用を与え、必要な人に追加の案内をしなくて済むようにする

UIは、いわゆる「ハッピー・パス」から逸れることを防ぐために、人々が何を言うかを「教える」ようにすべきではありません。命令は、問題を抱えていない人にとっては無関係です。あなたが直感的なUIを設計していれば、ほとんどの人がそうなるはずです。その代わりに、次の例のように、予備のパスと修復（エラー）指示を与えます。このようにして、助けを必要としない人のための関連性を最適化し、誰かがき詰まってしまったような際に助けを提供します。

望むものを求める方法を誰もが知っておく必要がある、と仮定しないでください。

<table style="background-color: #fb8c00; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
User
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
Start a metronome. （メトロノームを開始して。）
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
UI
</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>What tempo did you want to start with? You can say, for instance, "110 beats per minute." Or you can give me a tempo like "Allegro" or "Moderately fast." (We can always speed it up or slow it down later.) （どのテンポから始めたいですか？例えば、「1分あたり110ビート」と言うことができます。または、私に「アレグロ」や「やや速く」のようにテンポを与えることができます。（私たちはいつでもそれをスピードアップしたり遅くしたりすることができます。））</i>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
User
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
92 beats per minute. （1分あたり92ビート。）
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
UI
</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>Okay, 92 beats per minute. Here you go. （オッケー、1分あたり92ビートです。はい、どうぞ。）</i>
</td>
</tr>
</table>

これは、UIの一部として、ユーザにターンを渡すことなく、質問を尋ねた後直ちに話し続ける点において、悪い振る舞いです。また、このアプローチでは、長い初期メッセージが終了するのを待つか、割り込む（割り込みが有効になっている場合）ことが必要であり、それらは不適切な会話の参加者となってしまう理由になります。

その代わりに、連続した、時間のかかる会話の性質を考慮し、会話をユーザに戻します。

<table style="background-color: #fb8c00; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
User
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
Start a metronome. （メトロノームを開始して。）
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
UI
</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>Sure, what tempo? （わかりました。テンポは何ですか？）</i>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
User
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
[No reply （返事なし）]
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
UI
</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>You can say, for instance, "110 beats per minute." Or you can give me a tempo like "Allegro" or "Moderately fast." (We can always speed it up or slow it down later.) （例えば、「1分あたり110ビート」と言うことができます。または、私に「アレグロ」や「やや速く」のようにテンポを与えることができます。（私たちはいつでもそれをスピードアップしたり遅くしたりすることができます。））</i>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
User
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
92 beats per minute. （1分あたり92ビート。）
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
UI
</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>Okay, 92 beats per minute. Here you go. （オッケー、1分あたり92ビートです。はい、どうぞ。）</i>
</td>
</tr>
</table>

明らかに、すぐに提案を提供するより少ないステップのように思えます。しかし、実際には時間がかかりますが、ユーザーは初心者であると仮定していて、おそらく当てはまりません。

人々は望むものを知っていることを思い出してください。助けに飛び込む前に、彼らにチャンスを与えてください。

> **ベストプラクティス**
>
> 会話の体験を作成するときは、次のガイドラインを思い出してください。
>
> * システムが理解したことを伝える
> * 人々に何が言えるのかを知らせる際には、意味のある例を提供する
> * 明白なことを避ける
> * 必要な場合にのみ指示を与える

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/design/unlocking-the-power-of-spoken-language)
