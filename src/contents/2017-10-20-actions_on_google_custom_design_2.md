---
layout: actions_on_google
title: Design Overview "How Conversations Work"を日本語訳しました
categories:
- actions on google
---
[会話型UIとそれが重要な理由](https://www.eisbahn.jp/yoichiro/2017/10/actions_on_google_custom_design_1.html)に続いて、ここでは[Understanding How Conversations Work](https://developers.google.com/actions/design/how-conversations-work)の日本語訳を紹介します。会話型UIの重要性から、会話の仕組みを理解することで、アプリを設計するための指針となる事項が数多く出てきます。どれも日々の会話の中で全く意識していないことなのですが、改めて考えてみると、どれも納得な重要な要素ばかりです。

---

# 会話の仕組みの理解: より良いUIへの鍵

[PDFをダウンロード](https://developers.google.com/actions/downloads/how-conversations-work.pdf)

> 今から25年後、誰もドロップダウンメニューをクリックすることはありませんが、誰もがまだ地図を指差し、お互いの文章を修正しているでしょう。それは基本的なものです。優れた情報ソフトウェアは、コンピュータではなく人間がどのように情報を扱うかを反映しています。
>
> [Bret Victor, Magic Ink](http://worrydream.com/MagicInk/MagicInk.pdf)

さあ、直視しましょう。現時点では、ほとんどのユーザーインターフェイスは、人工知能や、巧みなロボットやスマート機器との楽しい会話に囲まれた、SF小説によって約束された未来には足りません。

では、どうやってそこにたどり着くのでしょうか？

最初の一手として、私たちは人間と話をすることを機械に教えなければならないのであって、その逆ではありません。

これを考えてみましょう。会話は、私たちの文明を今日までに進化させました。すべての人間の発明は、私たちが話し言葉で伝えるアイデアから生まれました。私たちが非常に長い間進化させた能力です。実際には、10万年以上です。およそ5千年前の執筆の初期段階と比較してください。ましてやコンピューティングなどなおさらです。

だから、人々は、当然ながら、すぐに彼らがどのように話すかを変えることはないでしょう。そして、会話の仕組みについて、彼らの無意識の期待は消え去ることはありません。

私たちがそれを認識しているかどうかに関わらず、私たちはすべて、話すときに特定の規則と慣例に従います。もし人間の良い会話のために何ができるかを解くことができれば、より良いUIを構築する方法を見つけ出すことができるかもしれません。

<table style="border-spacing: 0; width: 100%;">
<tr style="background-color: #3949ab;">
<td style="color: white; padding: 5px;">会話の6つのステップ</td>
</tr>
<tr style="background-color: #3949ab;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">会話の基本的な仕組みは、次の6つの簡単なステップに分解することができます。</td>
</tr>
<tr style="background-color: #3949ab;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">1）共通の場を設定するチャンネルを開く - スピーカーAはスピーカーBにメッセージを送信する</td>
</tr>
<tr style="background-color: #3949ab;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">2）関与することを約束する - BはAとの会話をコミットする</td>
</tr>
<tr style="background-color: #3949ab;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">3）構築の意味付け - AとBは、一連の構造化されたアイデアと（しばしば暗黙の）文脈を結びつける</td>
</tr>
<tr style="background-color: #3949ab;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">4）発展する - AまたはB（またはその両方）が、会話の相互作用に基づいて何かを学んだり、得る</td>
</tr>
<tr style="background-color: #3949ab;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">5）合意に収束 - すべてがうまくいくならば、AとBは合意に達した。もしそうでなければ、両者は状況を修復するために動くかもしれない</td>
</tr>
<tr style="background-color: #3949ab;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">6）行動するか相互作用する - 会話の結果として機能的な行動が続くことがある、または無意識なゴールに達する可能性がある（孤独ではない）</td>
</tr>
</table>

## ターンテイキング（交互に話す）

いくらか明白で重要な会話の手段は、私たちが当然と思っている微妙な信号を伴うターン・テイキングです。_シンタックス（文の構成規則）_は、聞き手が応答するチャンスが来ることの予測を助け、_韻律_（ペース、音量、ピッチ、および沈黙の組み合わせ）は、推移のポイントが来ていることを知らせます。人々は互いに、これらの合図を使用して会話のバトンを前後に渡します。効果的なターン・テイキングがなければ、お互いに話すか、会話が同期しなくなります。

## 協力原理

人工知能にも熱中していた言語学者の[Paul Grice](https://plato.stanford.edu/entries/grice/)は、理解するためには、人々は協力して話す必要があると言いました。彼は、[Grice' Maxims](https://books.google.com/books?id=Ds-MdFG2hlMC&pg=PA10&lpg=PA10&dq=%22Grice%E2%80%99s+Maxims%22&source=bl&ots=os03UpLBg7&sig=SpRN-_W6SIQT2kP6Jly290Y8zy8&hl=en&sa=X&ved=0ahUKEwiqvqPJobrQAhWH14MKHbFqAoA4FBDoAQgaMAA#v=onepage&q=%22Grice%E2%80%99s%20Maxims%22&f=false)と呼ばれる協力的な会話のいくつかの基本的なルールを思いつき、状況が求める際に、人々が真実で、有益で、関連性があり、明確でなければならないと指摘しました。協力的な会話について、[Be Cooperative...Like Your Users](https://developers.google.com/actions/design/be-cooperative)を読んでください。

## 合意と文脈

会話の意味は、その文脈によって異なります。しかし、通常の会話では、私たちが言わずに放棄したものは、しばしば意味をも持ちます。

あなたは友人に「土曜日にパーティに行くの？」と尋ね、彼女は「私は夕方のシフトの仕事をしています」と返答します。あなたの友人が同時に2つの場所にいることができないことを暗示しているので、あなたは彼女がパーティーに来ることはないと推測します。

また別の文脈では、予約する人数を尋ねられたときに、「ああ、私と夫だけ」と言うと、あなたが2人でパーティーを予約するのだと他の人が推測することを期待しています。

私たちがこれらの前提と原則をバックグラウンドで行っていない場合、われわれの会話は理解するためには超逐語的である必要があります。


## スレッディング

会話のすべての要素は、一貫性のあるスレッドで結合される必要があります。良いノックノックジョークを高く評価する人は、会話の各ターンのコンテキストをスレッディングすることで、関連性が維持され、全体的なやり取りがより良くなることを、良いノックノックジョークを正しく理解する人は知っています。

これを成功させるには、会話が展開するにつれて、デザイナーは会話の各ターンのペアを認識するようにしなければなりません（隣接ペアと呼ばれます）。

<table style="border-spacing: 0; width: 300px;">
<tr style="background-color: #3949ab;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Q What time is it?</td>
</tr>
<tr style="background-color: #3949ab;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">A It's exactly noon.</td>
</tr>
</table>

質問と回答のペアである必要はありません。いずれかでもあり得ます。聴取者の合図は、隣接関係を形成します。

<table style="border-spacing: 0; width: 300px;">
<tr style="background-color: #3949ab;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">A You won't believe what happened!</td>
</tr>
<tr style="background-color: #3949ab;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">B What??</td>
</tr>
</table>

ちょうど言ったことの承認または否定をする時。

<table style="border-spacing: 0; width: 300px;">
<tr style="background-color: #3949ab;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">A This is the worst line ever!!</td>
</tr>
<tr style="background-color: #3949ab;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">B I know!</td>
</tr>
</table>

もし束ねられた隣接関係のペアで満たされている適切にスレッド化されたやり取りをUIが産み出さなかった場合、会話はすぐに壊れてしまうか、単純に面白くない会話になります。このように、スレッディングは、このゲームの例のように、ユーザーがより魅力的な体験をするための不可欠なツールです。

<table style="border-spacing: 0; width: 100%;">
<tr style="background-color: #fb8c00;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">UI</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Welcome to Number Genie! I'm thinking of a number from 0 to 100. What's your first guess?</td>
</tr>
<tr style="background-color: #fb8c00;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">50</td>
</tr>
<tr style="background-color: #fb8c00;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">UI</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">It's lower than 50. Next guess?</td>
</tr>
<tr style="background-color: #fb8c00;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">How about 9.</td>
</tr>
<tr style="background-color: #fb8c00;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">UI</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Piping hot! Go lower.</td>
</tr>
<tr style="background-color: #fb8c00;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">Okay...is it 8?</td>
</tr>
<tr style="background-color: #fb8c00;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">UI</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Keep going.</td>
</tr>
<tr style="background-color: #fb8c00;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">7!</td>
</tr>
<tr style="background-color: #fb8c00;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">UI</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Yes! It's 7. How about another round?</td>
</tr>
<tr style="background-color: #fb8c00;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">No thanks.</td>
</tr>
<tr style="background-color: #fb8c00;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">UI</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Alright, talk to you later then.</td>
</tr>
</table>

この簡単な数の推測ゲームにベストプラクティスを適用する方法の詳細は、[design walkthrough](https://developers.google.com/actions/design/walkthrough)をご覧ください。

## 修復

壊れた会話は、共通の見方がないことからも生じる可能性があります。Griceの協力的会話のルールに違反する不適切な貢献によって、意味が崩壊し始める可能性もあります。たとえば、ある人が「あなたは誰がパーティに行くのか知っていますか？」と尋ねられ、彼らは単に「はい」と答えた場合、それは非協力的で不自然なので、修理するのが厄介です。

機能している会話内であっても、様式や内容が不正確、不適切、または無意味な場合があり、事態を正しい方向に戻すために修復が求められるでしょう。いずれの当事者も修復を始めることができますが、ターンの出入りにおいて、一般的な優先順位があり、話し手は通常、自分の間違いを発見し修復します。UIは、やり取りの流れや性質に基づいて、会話を修復できる必要があります。

修復の戦略については、[Be Cooperative...Like Your Users](https://developers.google.com/actions/design/be-cooperative)、[Unlocking the Power of Spoken Language](https://developers.google.com/actions/design/unlocking-the-power-of-spoken-language)、そして[In Conversation, There Are No Errors](https://developers.google.com/actions/design/conversation-repair)を読んでください。

## 要点: 会話はあなたのUIの基礎です

会話は、協力と交渉の原則的な相互プロセスです。関与するすべての当事者は、意味を創造し、合意し、そして豊かな背景と、微妙な違いに対して行動します。これを理解することで、独自の会話型UIを設計するための理論的なモデルを得ることができます。

> **ベストプラクティス**
>
> UIエクスペリエンスを作成する際には、以下を覚えてください。
>
> * 会話の6つのステップ
> * ターンテイキングの役割
> * 協力原理
> * 合意と文脈
> * スレッディング
> * 修復

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/design/how-conversations-work)
