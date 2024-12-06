---
layout: actions_on_google
title: Best Practices "Instilling User Confidence Through Confirmations and Acknowledgements"を日本語訳しました
categories:
- actions on google
---
誰かと会話をしている際に、相手が本当に自分が言ったことを理解してくれているのか、とても気になると思います。理解したことを相手が自分に繰り返してくれれば、理解されたんだな、と確認することができます。また、適度に「はい」「オッケー」「わかりました」と、自分が言ったことを相手が肯定してくれれば、さらに会話はスムーズに進んでいくはずです。

Actions on GoogleのBest Practicesの[Instilling User Confidence Through Confirmations and Acknowledgements](https://developers.google.com/actions/design/instilling-user-confidence)では、Googleアシスタント向けのアプリの会話型UIについて、ユーザが言ったことをアプリが理解できたことを示す「確認」と、さらに理解したことを自然な形でユーザに安心させるための「肯定応答」について説明がされていましたので、日本語訳してみました。会話型UIの設計について、これらは非常に重要ですので、ぜひ覚えておいて設計に反映して欲しいと思います。

---

# 確認と肯定応答を通じてユーザーの信頼を植え付ける

[PDFをダウンロード](https://developers.google.com/actions/downloads/instilling-user-confidence.pdf)

ユーザーインターフェースの設計者にとっての基本的な課題は、誰が問題を抱えていることを知ることです。しかし、最初の地点における問題を回避する良いスタートは、システムが聞いていることを人々に知らせることです。設計者には、これを支援する2つのツールがあります。 _確認_ と _肯定応答_ です。

_確認_ は、UIが質問、命令、または応答が正しく理解されたことを人々に知らせるために、UIがチェックインする方法です。 _肯定応答_ とは、UIが関与していることを示す単語または句（ _OK_ や _Alright_ など）です。

## 確認戦略が必要な理由

確認は、ユーザーに信頼を植え付けることによって、会話の体験を改善します。確認戦略を実施していない場合は、入力が誤って解釈されて間違った経路に落ちるリスクがあります。例えば、"オークランド（Oakland）の天気は何ですか？"と聞いた人が、代わりにニュージーランドのオークランド（Auckland）の予報を得ます。

リスクの重大性は、間違った株を1,000株購入する、または単に間違った天気予報を取得する、といった文脈に依存します。知識を持ったUI設計者は、認識を確認し、間違った経路になってしまうことを避けるために、与えられた文脈において、どの戦略が最も適切かを知っています。

通常、 _暗黙的_ にまたは _明示的_ に確認するかの間での選択です。

## 戦略#1: 明示的な確認

明示的な確認によって、UIはユーザーに譲歩し、進む前に口頭での合意を求めます。2つの例があります。

<table style="background-color: #fb8c00; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>Alright, from Atlanta to Geneva on September 13th at 6 p.m. Is that right?</i><br>
<i>That was ____, right? （それは____ですよね？）</i>
</td>
</tr>
</table>

<p></p>

<table style="background-color: #fb8c00; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>Alright, from Atlanta to Geneva on September 13th at 6 p.m. Is that right?</i><br>
<i>Okay, to confirm, I got ___. Is that right? （オッケー、確認のために、私は___を得ました。それは正しいですか？）</i>
</td>
</tr>
</table>

いくつかのアイテムが概念的にトランザクションに関係している時は、それらはしばしば一緒に確認することができます。

<table style="background-color: #fb8c00; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>Alright, from Atlanta to Geneva on September 13th at 6 p.m. Is that right?</i><br>
<i>Okay, that's a table for 6 at Quartino, on Friday at 7. Is that right? （オッケー、Quartinoの6テーブル、金曜日は7テーブルです。それは正しいですか？）</i>
</td>
</tr>
</table>

明示的な確認は、UIが取り消すことが困難または不可能な何らかのアクションを行うためのパスにあるときに適切です。ビジネスルールまたは法律が消費者からの明確な口頭の同意を必要とする場合（例えば、送金を確定する前）、またはシステムのパフォーマンスが悪い場合などです。

## 戦略#2: 暗黙的な確認

暗黙の確認によって、何が理解されたかを明確にするために、UIはユーザーの発言の重要な要素をレスポンスに組み込みます。ここに例があります。

<table style="background-color: #fb8c00; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
User
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
What's the state flower of Nebraska? （ネブラスカ州の花は何ですか？）
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
UI
</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>The state flower of Nebraska is goldenrod. （ネブラスカ州の花はアキノキリンソウです。）</i>
</td>
</tr>
</table>

UIが大事な要素を繰り返した、もしくは言い換えたため、システムが認識していることをその人はすぐに知ることができるので、その確認は暗黙的になります。

繰り返される要素は、それほど正確である必要はありません。この例を考えてみましょう。

<table style="background-color: #fb8c00; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
User
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
What kind of news can I listen to? （聞くことができるニュースの種類は何ですか？）
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
UI
</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>In addition to general news, you can ask for almost any news topic, such as technology or sports news. （一般的なニュースに加えて、テクノロジやスポーツのニュースなど、ほとんど全てのニューストピックを尋ねることができます。）</i>
</td>
</tr>
</table>

疑いという暗黙の利点が「一般的な」ニュースによってユーザに与えられ、それによって、ユーザが既に試みた可能性や、単純に問い合わせを上達させたり利用可能な機能をテストするために尋ねるであろう明確さが避けられます。

正確さの確信度が中〜高程度の場合や、悪い結果が得られる可能性が低い場合に、暗黙的な確認は適切です。その利点は、スピードです。欠点は、多くの場合、人々がしばしばダイアログを軌道に戻す方法を知らないことです。これらの要因を念頭に置き、文脈に合わせて戦略を選択してください。

## ユーザーに返信しない

多くのUIは、簡単な「戻る」命令を使って、暗黙的な確認に従います。（「もし正しくない場合は、「戻る」と言ってください。）私たちは、このアプローチをお勧めしません。その代わりに、あなたの認識文法を構築して、ダイアログを軌道に戻すための人々の協調的な試みに対応してください（[Be Cooperative...Like Your Users](https://www.eisbahn.jp/yoichiro/2017/10/actions_on_google_custom_design_7.html)を参照してください）。または、「アウト」が本当に必要な場合は、明示的に確認してください。

最終的に、確認が必要でない場合もあります。例えば、懐中電灯をオンにするように頼んだ場合、ライトはすぐに点灯するはずです。結果がすぐに知覚できるならば、"Just-do-it"（行動あるのみ）アプローチが最も効果的です。

確認する時期と使用する戦略のタイプを決めるときは、正確さや、認識エラーに対する人へのコスト（お金、時間、いらだち）などの要因を考慮します。

## 肯定応答

> **肯定応答について**
>
> 肯定応答は、聞いたことを人々に安心させて、ダイアログに流動性と自然さに役立つ、OK、Sure、Thanks、Got itなどの単語やフレーズです。
>
> * 受け入れ、拒否、確認、訂正、および主題を変える前に通知するために、肯定応答を使います。
> * 繰り返すことは慎重になってください。つまり、肯定応答を過度に使用しないでください。

正しい会話型のUIは、やり取りの自然な流れを促進します。ペルソナは発話者に注意深く、必要に応じてさらなるダイアログを待つことを伝えます。単純な肯定応答は、前のターンの受諾を示し、会話の成功と進展にとって重要です。それらは、製品、サービス、ブランド、または企業価値に対して次々と好ましい印象を与える会話を追跡していることを示します。

肯定応答がなければ、ペルソナが全てを聞いたかどうか、人々は気になります。UIは、進む前にその人にカジュアルに肯定応答することで、この困惑をなくすことができます（"Sure. For what time? （はい、何時ですか？）"）。それはまた、対話のマーク（Next, And, So, Actually）などの他のテクニックを使って、まとまりのある会話として残りのダイアログをまとめる良い脈略への機会を切り開きます。

以下の2つの例を考えてみましょう。両方とも同じ情報を収集しますが、2つ目は確認戦略と肯定応答の組み合わせを使用します。

<table style="background-color: #fb8c00; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px; text-align: center;">
<b>A</b>
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px; text-align: center;">
<b>B</b>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<b>User</b> Put an appointment on my calendar （私のカレンダーに予定を入れて）
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>User</b> Put an appointment on my calendar （私のカレンダーに予定を入れて）
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<b>UI</b> What's the date? （日付はいつですか？）
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>UI</b> Sure. When for? （了解です。いつですか？）
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<b>User</b> Saturday （土曜日）
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>User</b> Saturday at 2 （土曜日の2時）
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<b>UI</b> What's the time? （何時ですか？）
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>UI</b> OK, Saturday at 2 p.m. What should I call this? （オッケー、土曜日の午後2時ですね。これは何と呼ぶべきでしょうか？）
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<b>User</b> 2 o'clock （2時）
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>User</b> Sascha's party （サーシャのパーティ）
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<b>UI</b> What's the subject? （題名は何ですか？）
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<b>UI</b> Got it. Sascha's party is all set. （わかりました。サーシャのパーティの設定を終えました。）
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<b>User</b> Sascha's party （サーシャのパーティ）
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<br>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<b>UI</b> Sascha's party. Saturday, September 10th, at 2 p.m. is saved. （サーシャのパーティ。9月10日、土曜日、午後2時で保存されました。）
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<br>
</td>
</tr>
</table>

## 実用的な考慮事項

肯定応答は、文脈、ブランド、タスクの種類、およびダイアログのその他の詳細（例えば、人がエラーのないパスを下っているのか、問題が理解されているのかなど）に対して適切でなければなりません。

現実の会話では、"Alrighty then,"、"You got it!"、"Doh!"のような肯定応答が聞こえるかもしれません。しかし、例えば、あなたがその経験を提供するために作り出したペルソナの一部であれば、残りのUIと一致する場合にのみ、これらを使用してください。

肯定応答を慎重に、そして制限して使用し手ください。つまり、すべての文がそれで始まるわけではありません。対話がサンプルのダイアログとして事前に計画またはレイアウトされている場合（推奨設計プロセスを参照）、やり取りの最中に使用される肯定応答の数とフローを簡単に確認できます。

明示的な人の要求の繰り返し - 「マイクをオフにする」 「マイクは今オフになっています。」 - これは肯定応答の一形態でもあり、聞いたことのある聞き手を安心させます。流動性と自然さに役立たせるために、今後も肯定応答を使用することができます。

単調性とギミックを避ける方法の1つは、ランダム化です。その人物が聞いたことがある肯定応答だけでなく、その要求が満たされたことを確認します。

新鮮かつ多様なことを保つために、特定の肯定応答にランダム化を適用することもできます。Done、Okay、Alright、Sure、などです。レコーディングスタジオでは、声優も非常に創造的であることがわかります。それらを1つの単語に限定しても、トーンに基づく複数のバリエーションを提供するための小さな変化を表現します。

## シームレスな会話へのあなたのやり方での確認そして肯定応答

UIツールキットのすべてのリソースのうち、確認と肯定応答が最も重要です。それらは、人間味のない前後のやり取りの連続していない脈略が自然な会話としてつながるのを助けます。文脈を活用し、流れにテンポや流動性を与え、理解を深め、現在のやりとりだけでなく、技術全体として、広くユーザーに信頼を吹き込みます。

> **ベストプラクティス**
>
> 確認や肯定応答を最大限に活用する:
>
> * リスクの高いリクエストを明確にするために明示的な確認を使用する
> * シンプルなリクエストの速度を暗黙的に確認する
> * 「戻る」手順を避ける
> * 肯定応答を活用して、聞いた人を安心させる助けにする
> * 単調性とギミックを避けるために肯定応答をランダム化する

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/design/instilling-user-confidence)
