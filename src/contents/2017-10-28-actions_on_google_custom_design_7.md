---
layout: actions_on_google
title: Best Practices "Be Cooperative...Like Your Users"を日本語訳しました
categories:
- actions on google
---
Actions on Googleのドキュメントサイトでは、会話型UIを設計するために役に立ついくつかのベストプラクティスが掲載されています。最初のベストプラクティスは、[Be Cooperative...Like Your Users](https://developers.google.com/actions/design/be-cooperative)です。ここでは、会話が協力的な原則で成り立っていることをベースとして、ユーザにとって自然な会話型UIを設計するためのポイントや具体例が説明されています。以下は、その日本語訳です。

---

# 協力的になれ... あなたのユーザのように

> [PDFをダウンロード](https://developers.google.com/actions/downloads/be-cooperative.pdf)

会話は、単なる情報の交換だけではありません。

会話では、トピックに関する当然の前提を共有します。私たちは、会話がどのように発展すべきかを知っています。私たちは、互いに行うべき貢献の質と量についての期待を持っています。それに加えて、私たちは、礼儀、一貫性、その他の自然な会話のルールを取り入れています。さらに加えて、不明瞭、または大げさであり、より深く言葉通りではない解釈を求めているならば、誰もが本能的に表面的な意味を_無視する_ことを知っています。私たちは皆それを自然にやっていますが、会話は実際にはかなり複雑なプロセスです。

言語学者の[Paul Grice](https://plato.stanford.edu/entries/grice/)は、理解するためには、人々は_協力して_話す必要があると語りました。彼は、これを協力的原則（Cooperative Principle）と呼びました。会話が著しく効率的になるように、協力の暗示があると仮定することによって、多くの情報を省略することができます。_"Do you ...?"_と尋ねることは、実際には_"Say 'yes or 'no'."_を意味しているわけではありません。むしろ、より具体的なものを尋ねる間接的で丁寧な方法です。

彼はまた、[Grice' Maxims](https://books.google.com/books?id=Ds-MdFG2hlMC&pg=PA10&lpg=PA10&dq=%22Grice%E2%80%99s+Maxims%22&source=bl&ots=os03UpLBg7&sig=SpRN-_W6SIQT2kP6Jly290Y8zy8&hl=en&sa=X&ved=0ahUKEwiqvqPJobrQAhWH14MKHbFqAoA4FBDoAQgaMAA#v=onepage&q=%22Grice%E2%80%99s%20Maxims%22&f=false)を作成して、協調的会話の基本ルールを定義しました。

* **品質** - 真実のことのみ言う
* **数量** - 必要以上に多くの情報を与えない
* **妥当性** - トピックに関連するものだけを言う
* **マナー** - 短く、要点を言い、曖昧さや難解さを避ける

言い換えれば、人々は状況が求めるように、真実で、有益で、関連性があり、明確でなければなりません。これは、ユーザーインターフェイスが有効であるために必要なものです。

## 論理と正確さが必ずしもルールではない

私たちの口頭のショートカットは、会話の非論理的、非数学的な性質を明らかにします。例えば、Sueに_5人_の子供がいる場合、「Sueには2人の子供がいる」というのは、技術的にも論理的にも正しい。しかし、この文は文脈情報が欠落しているため（この場合、Sueの他の3人の子供たち）、会話では誤解を招きます。

さらに、時には人々は意図的に非協力的であることもあります。いくつかのケースでは、彼らは優しく親切にしようとしているだけです。例えば、採用面接で見込みがどうだったかを聞いたとき、「彼は美しいネクタイを着ていた」と言って、否定的な返答を避けるかもしれません。

UIは、多くの人々が無意識に従うこれらの規則のすべてに対応しなければなりません。

## 認識文法のパフォーマンスと修復（エラー）指示の密接な関連

UI設計者は、認識文法（会話のある時点で人が答えるかもしれないすべてのこと）がどのように構築されるかだけでなく、人間の「エラー」の特定の種別を予想することもできなければなりません。例えば、航空券の購入のためのこの確認の指示を考えてみましょう。

<table style="background-color: #fb8c00; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>Alright, from Atlanta to Geneva on September 13th at 6 p.m. Is that right?</i><br>
<i>こんにちは、アトランタからジュネーブに9月13日午後6時です。正しいですか？</i>
</td>
</tr>
</table>

答えが「はい」の場合、人々は"Yes", "Yeah", "Correct", "That's right"などの短い答えが出てくる傾向にあります。しかし、答えが「いいえ」の場合、人々は概ね"no."とは言いません。代わりに、彼らは協力的になり、"No, not Geneva, I said 'Jamaica' （いいえ、ジュネーブではなく、ジャマイカと言いました）"や"No, not the 13th, the 30th, three zero. （いいえ、13日ではなく、30日、さんぜろ）"のようなレスポンスにて会話を前に進めます。あるいは、彼らは聞いたことの一部にしか反応しないかもしれません、"You got the time right, but the date's wrong. （時間は正しいですが、日付が間違っています。）"

技術的な制限により、あなたのUIがそのような協調的なやり取りを受け入れることが妨げられる場合、そのダイアログは、システムがわざとらしくそうすることができると期待するようユーザに促すべきではありません。もしそうする場合は、第一レベルの修理戦略は、「そして、それは正しいですか？単に"はい"または"いいえ"と言ってください」のような不自然な指示に限定されてしまいます。瞬時にユーザと距離が置かれ、UIの根本的な限界と人工的な個性の会話が露出され、それにより自然な印象を与える可能性が損なわれます。

UIが情報を修正、修飾、または追加するレスポンスを受け入れるように設計されていない場合、そのような自然で協調的な言語的行動を非協力的であると誤って伝える可能性があります。この言語的誤解は、あたかもその人が誤解したように（「それは有効なレスポンスではありませでした。」）、もしくは、見せかけの懸念のトーンで、あたかも物覚えが悪い人に話しているように（「すみません。聞いたレスポンスを理解しませんでした。」）、無愛想あるいは「エラー」プロンプトと呼ばれるような機械的なトーンにいつも現れます。

## さまざまなユーザーのレスポンスは機会であり、「エラー」ではない

不快感や乱心を起こすことなく会話を順調に保ちながら、修理の指示に備えて質問をどのように枠組みに入れることができるか考えてください。実際、そのような例は、ダイアログ内で別の意味のある方向に転換する機会になることがあります。

今では、人々は音声認識技術に対して悪い体験をしたことがあるため、誤ったUI設計によってしばしば更に悪化しています。文字通りの指示に従い、認識エラーを引き起こすのを避けるために、"自然で協調的"な追加情報を提供しない人もいます。UI設計は、これらのユーザーにも快適に対応する必要があります。

UIで日付と時刻の両方を収集する必要があるシナリオを考えてみましょう。これは、両方を得られるであろう一般的な指示を使って開始すべきです。

<table style="background-color: #fb8c00; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
User
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
Set an alarm. （アラームをセットしてください。）
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
UI
</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>Okay, for when? （オッケー、いつですか？）</i>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
User
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
Tuesday at 6 a.m. （火曜日の午前6時。）
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
UI
</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>Done. Your alarm's set for Tuesday at 6 a.m. （完了です。アラームは火曜日の午前6時にセットされました。）</i>
</td>
</tr>
</table>

しかし、より不確かで、またはそれほど情報を持っていないユーザーは、情報を分別する可能性があるため、UIはそれをエラーとして扱わずに、シンプルに次のように対応します。

<table style="background-color: #fb8c00; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
User
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
Set an alarm. （アラームをセットしてください。）
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
UI
</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>Alright, for when? （こんにちは、いつですか？）</i>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
User
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
Tuesday. （火曜日。）
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
UI
</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>And the time? （時間は？）</i>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
User
</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
6 a.m. （午前6時。）
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
UI
</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>There! Your alarm's set for Tuesday at 6 a.m. （それ！アラームは火曜日の午前6時にセットされました。）</i>
</td>
</tr>
</table>

上記のやり取りは、UIを協調させるために必要とされ、他の可能なユーザレスポンスのために準備される、隠されたロジックを露出していません。ユーザは、全く指示されずに、情報の片方または両方を与えることなく、一つの指令で全体の手順が完了したはずです。そして、不足している情報を解釈するためのUIに委ねられます（"Set an alarm for 6 a.m. Monday morning," "Wake me up in 6 hours," or "Set an alarm for 7 o'clock.,）。ユーザによって話されなかった要素を含めて全てとなります。

プロンプト "いつ、いつですか？" 1日か1時間のどちらかを念頭に置いている人や、両方で準備ができている人からの簡単な対応を容易にします。 この種のプロンプトは、協力原則を完全に促進します。
"こんにちは、いつですか？"の指示は、日付もしくは時間のいずれかを念頭に置いている人々や、両方の準備ができている人々からの気楽な対応を行いやすくします。この種類の指示は、協力原則を完璧に促進します。

## ジョークを言うように -- あなたがそれを説明しなければならない場合、あなたはそれを間違ってやっている

優れたUIは、どのようにコンピュータが「コマンド」を得るために調整されるかを示すのではなく、言語と意味の直感的な力に焦点を当てています。人々が最初に学び、最もよく知っているコミュニケーションシステムである日常会話を活用しています。私たちはすでに自分たちの言語に堪能であるので、ごく普通の英語（あるいはスペイン語、タガログ語、ヒンディー語）で期待される返事や命令をどのように伝えるべきか教えてもらう必要はありません。別の言い方をすると、命令自体は避けますが、会話を前に進めるために何が言えるのかを人々が理解できるようにする必要がある場合は、直感的なものを使用してください。

つまり、以下の代わりに、

<table style="background-color: #fb8c00; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>To hear the message again, say 'Repeat;' to reply to it, say 'Reply;' and to move on to the next one, say 'Next.'</i><br>
<i>メッセージをもう一度聞くには、'繰り返す'と言ってください。返事をするには、'返事をする'と言ってください。そして、次に進むためには、'次'と言ってください。</i>
</td>
</tr>
</table>

もっと直感的に考えてください。

<table style="background-color: #fb8c00; border-spacing: 0;">
<tr>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">
<i>Repeat, reply, or go on to the next one?</i><br>
<i>繰り返しますか？返事をしますか？または、次に進みますか？</i>
</td>
</tr>
</table>

## 自然な会話は時間をかけてテストされ、ユーザーが承認します

協力原則は、強力な共通知識ベースに基づいて構築された、効率的かつ社会的に適切な方法でコミュニケーションする能力を明白にしています。自然な会話の慣習を無視せずに活用することで、人々が使用方法を直感的に知り、心地よく感じる、より優れたUIを作成することができます。

> **ベストプラクティス**
>
> UIを作成する際の以下のガイドラインを思い出してください。
>
> * 認識文法と修復指示の理解
> * 多様なユーザの話すスタイルへの対応
> * 直感的に何が言えるかを人々に知らせる

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/design/be-cooperative)
