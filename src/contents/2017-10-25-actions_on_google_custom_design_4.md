---
layout: actions_on_google
title: Design in Action "Design Walkthrough"を日本語訳しました
categories:
- actions on google
---
[設計原則と方法論](https://www.eisbahn.jp/yoichiro/2017/10/actions_on_google_custom_design_3.html)にて、会話型UIを設計するための指針について得ましたが、具体例がないと、なかなかイメージすることは難しいかもしれません。特に、「ダイアログ（対話）を書き出す」ということが何を意味しているのか、最初は想像できないと思います。[Crafting a Conversation](https://developers.google.com/actions/design/walkthrough)では、数字当てゲームを題材として、どのようなダイアログを設計すべきかについて、具体的な例を示して説明しています。ここでは、それを日本語訳してみました。

これを読むことで、Google Assistant向けのアプリをどう設計すれば良いか、そのアプローチを実感することができるかと思います。

---

# 会話を巧みに作る

このガイドでは、単純な数字推測ゲームを例にして、会話アクションを設計する方法を説明し、優れた経験を構築するために使用できる主要な概念とベストプラクティスについて説明します。

始める前に、推奨される[設計プロセスの方法論](https://www.eisbahn.jp/yoichiro/2017/10/actions_on_google_custom_design_3.html)を理解しておきましょう。[主な手順](https://www.eisbahn.jp/yoichiro/2017/10/actions_on_google_custom_design_3.html)は次のとおりです。

* **適切なユースケースを選びます。** 会話型インターフェイスでうまく機能するユースケースは、通常、シンプルで直感的であり、そしてほとんど複雑さを要求しません。
* **ペルソナを作成します。** 会話アクションが読み、機能する方法は、一貫性、ユニークなブランドの存在、性格を与えます。
* **ダイアログ（対話）を書きます。** これはダイアログ作成の大部分であり、ここに至るまでにベストプラクティスを提供します。
* **テストします。** 大きな声で話し、シミュレータツールでテストし、そして会話的に聞こえるかどうか確認します。
* **構築して反復します。** [Dialogflow](https://www.dialogflow.com/)で作成するか、Actions SDKを使用した独自のツールを使って、構築します。

## 正しいユースケースを選ぶ

ゲームは、タスクの完了に関してはリスクが低いですが、ユーザーが退屈に感じる閾値が低く、ゲームUIが関与することに対する高い期待を持っているという点で、扱いにくい場合があります。

数字を推測するゲームは、背景を必要としないドメインを活用する、直感的な会話のよい出発点です。

## ペルソナを作成する

ペルソナは、会話を設計し、書くのを助けてくれるので、正しい単語、構文、構造を簡単に選ぶことができるように、早めに選択してください。

![](https://developers.google.com/actions/images/number-genie-ic1.png)

私たちのゲームのペルソナは、次のような特徴を持っています。

* **アップビート**で**明るく**、そして**元気づける**
* ゲームを動かし続け、探検を奨励するために、**魅力的**で**軽妙**である
* ゲームを多くの年齢層やグループにアピールするために、**決して堅苦しくなく**、**簡単な言葉を使用する**

推測ゲームのジャンルについて、「魔法」の含みと本来の期待に対するユーザーの共通認識を利用して、さらにいっそうの個性をもたらすために、「Number Genie（数の魔神）」と呼ぶことにしましょう。

> **ヒント**
>
> たとえあなたが「個性」を持っているとは考えないとしても、単に会話を通してやり取りすることによって、あなたが計画するかどうかに関わらず、ユーザーは[「ペルソナ」を知覚する](https://developers.google.com/actions/design/principles#personas)ことになる、と覚えてください。そのため、私たちはあなたの考えを何か与えることを勧めます。ペルソナの作成に関する具体的なアドバイスについては、会話設計のヒントビデオをご覧ください。

## ダイアログを書く

ゲームのユースケースを決めて、ペルソナを決定したので、[サンプルダイアログ](https://developers.google.com/actions/design/principles#sample_dialogs)の形式でユーザーの道のりを書き始める準備が整いました。

**Number Genie**ゲームでは、次のようなダイアログを考え出しました。それぞれのダイアログが私たちの設計思想とベストプラクティスについて明らかにすることを理解するために、それらに踏み出しましょう。

### 道のり 1: ハッピー・パス

このダイアログは、3つの推測を伴う典型的なフローを示します。

<table style="border-spacing: 0; width: 100%;">
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">Hey Google, let me talk to Number Genie.</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Google Assistant</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Sure, here's Number Genie.</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>Welcome to Number Genie! I'm thinking of a number from 0 to 100. What's your first guess?</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">50</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>It's higher than 50. What's your next guess?</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">65</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>It's lower than 65. Next guess?</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">60</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>Congratulations, that's it! I was thinking of 60. Wanna play again?</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">No thanks.</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>Alright, talk to you later then.</i></td>
</tr>
</table>

> **それで？ コーディングを開始する時間ですか？**
>
> これまでのところ、とても良い、そうですか？残念ながら、ここで立ち止まって「ハッピー・パス」の構築を始めたら、それはかなりうんざりするようなゲームになるでしょう。ユーザーは、可能性としてこのダイアログを99回やり取りすることができてしまいますが（100以上の推測を数える場合は、もっと）、興味を増やし、ユーザとの関与を維持するための多くの機会が私たちにはあります。

### 道のり 2: 2回目のハッピーパス

このダイアログは、2回遊んで、より多くの推測を行うユーザーを表しています。

<table style="border-spacing: 0; width: 100%;">
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">Hey Google, let me talk to Number Genie.</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Google Assistant</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Sure, here's Number Genie.</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>Welcome to Number Genie! I'm thinking of a number from 0 to 100. What's your first guess?</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">30</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>It's higher than 30. What's your next guess?</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">47</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>It's lower than 47. Next guess?</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">45</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>Congratulations, that's it! I was thinking of 45. Wanna play again?</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">Sure.</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>Great! I'm thinking of a new number from 0 to 100. What's your guess?</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">99</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>It's lower than 99. Next guess?</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">98</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>It's lower than 98. Next guess?</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">92</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>You're getting warmer. It's also lower than 92. Have another guess?</i><br>[Persona addition: warmer when within 10]<br>(If too low) <i>Warmer. It's also higher than 92. Take another guess.</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">86</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>Close, but not quite!</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">85</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>You're piping hot! But it's still lower.</i><br>[Persona addition: hot when within 3]<br>(If too low) <i>You're hot as lava! Go higher.</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">82</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>Well done! It is indeed 82. Wanna play again?</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">No.</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>OK, I'm already thinking of a number for next time. Bye!</i></td>
</tr>
</table>

> **ペルソナの開発**
>
> このダイアログに個性がどのように織り交ぜているかに注目してください。なぜなら、以前のものよりも少し行ったり来たりを必要となります。これにより、ゲームの一意性を与えるだけでなく、これらの特殊なケースを処理するための開発要件も追加されます。

### 道のり 3: 探索的な推測

このダイアログでは、ランダムな推測で発言するユーザーを示し、そしてNumber Genieが正しい数（この場合は23）を推測させるヒントをユーザに提示します。

<table style="border-spacing: 0; width: 100%;">
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">Hey Google, let me talk to Number Genie.</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Google Assistant</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Sure, here's Number Genie.</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>Welcome to Number Genie! I'm thinking of a number from 0 to 100. What's your first guess?</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">50</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>It's lower than 50. Next guess?</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">99<br>[LOGIC: catch when they go against the hints, i.e. go higher when they were told lower]</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>Nice try, but it's still lower than 50.</i><br>(if too low) <i>Clever, but no. It's still higher than %s.</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">0</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">(if guess = 0) <i>I see what you did there. But no, it’s higher than 0.</i><br>(if guess = 100) <i>Oh, good strategy. Start at the top. But no, it's lower than a hundred.</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">99.<br>[LOGIC: cold if >75 away]</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>You're ice cold. It's way lower than 99.</i><br>(if too high) <i>You're freezing cold. It's a lot higher than %s.</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">25</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>It's lower than 25. Next guess?</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">24<br>[LOGIC: catch if they were 2 away and then 1 away in the same direction, e.g. 25 then 24 when it's 23 or 15 then 16 when it's 17]</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>Keep going.</i><br>[LOGIC: same prompt for both high and low]</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">23<br>[LOGIC: catch if they had a lot of turns (maybe more than 10) and use a "one more" round question instead of just asking to play again generically]</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>Yes! It’s 23. Nice job! How about one more round?.</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">No thanks.</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>Alright, talk to you later then.</i></td>
</tr>
</table>

> **ユーザーを軌道に乗せる**
>
> エッジの可能性を見極め、そしてそれを「感じる」方法として、ユーザはアクションの境界をテストしようと試みようとすることがあります。この道のりは、変わりやすさや触れ合いを加えつつ、数字を推測する最終目標に向かってユーザーを誘導する方法を示しています。

### 道のり 4: ゲームの文脈外での会話の修復

<table style="border-spacing: 0; width: 100%;">
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">OK Google, let me talk to Number Genie.</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Google Assistant</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Sure, Here's Number Genie.</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>Welcome to Number Genie! I'm thinking of a number from 0 to 100. What's your first guess?</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">50</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>It's higher than 50. What's your next guess?</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">How long is the Great Wall of China?</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>Are you done playing Number Genie?</i><br>[LOGIC: first nomatch with discernible context, listen for yes/no and number]</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">Yes.</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>OK, till next time. Bye!</i></td>
</tr>
</table>

> **「エラー」の修復**
>
> 詳細については、[Conversation Repair](https://developers.google.com/actions/design/be-cooperative#repair)を参照してください。

### 道のり 5: タイムアウトのための会話の修復

<table style="border-spacing: 0; width: 100%;">
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">OK Google, let me talk to Number Genie.</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Google Assistant</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Sure, Here's Number Genie.</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>Welcome to Number Genie! I'm thinking of a number from 0 to 100. What's your first guess?</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">[says nothing]</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>I didn't hear a number.</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">[silent or muffled]</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>If you're still there, what's your guess?</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">[silent]</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>We can stop here. Let's play again soon.</i></td>
</tr>
</table>

### 道のり 6: ユーザーが同じ番号を3回推測

<table style="border-spacing: 0; width: 100%;">
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">Hey Google, let me talk to Number Genie.</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Google Assistant</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Sure, here's Number Genie.</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>Welcome to Number Genie! I'm thinking of a number from 0 to 100. What's your first guess?</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">50</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>It's higher than 50. What's your next guess?</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">50<br>[LOGIC: catch same number]</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>It's still not 50. Guess higher.</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">50<br>[LOGIC: catch same number again]</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>Maybe it'll be 50 the next time. Let's play again soon.</i></td>
</tr>
</table>

> **「悪い」入力の処理**
>
> これはゲームなので、設計の一部としてユーザーをアクションの外に連れ出すことで、エッジケースを楽しい方法で処理できます。これらのエッジケースは、ターゲットユーザーがいずれにしても「境界を押し広げている」ものであるため、追求する価値があります。そして、他のタイプのアクションよりも多くの人に対応することができます。このエラーは、道のり 3と非常によく似ています。これらのダイアログをコードに移すときは、これに対する配慮をするために、コードを設計する方法を最適化できるかどうかを確認しながら、変動性をはっきりと表現させるまで、これらのタイプの類似点をメモします。

### 道のり 7: ゲームの終了。ユーザーはゲームをあきらめて終了します

<table style="border-spacing: 0; width: 100%;">
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">Hey Google, let me talk to Number Genie.</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Google Assistant</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Sure, here's Number Genie.</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>Welcome to Number Genie! I'm thinking of a number from 0 to 100. What's your first guess?</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">21</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>It's higher than 21. What's your next guess?</i></td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">User</td>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">I give up.<br>[LOGIC: catch ending intent]</td>
</tr>
<tr style="background-color: #fb8c00; border-top-color: #ffb74d;">
<td style="background-color: rgba(255,255,255,.75); padding: 5px;">Number Genie</td>
<td style="background-color: rgba(255,255,255,.75); padding: 5px;"><i>Sure, I’ll tell you the number anyway. It was 90.</i></td>
</tr>
</table>

## テストする

素敵なダイアログのグループができたので、大きな声でそれらを読んでみてください。[writing conversationally](https://developers.google.com/actions/design/principles#conversational)という原則で述べたように、書き英語として記載する傾向があります。そのため、各ダイアログで読むことを行うことで、都合の悪いダイアログを見つけることができます。大声で読むことに加えて、アクションシミュレーターを使用してダイアログを入力し、それらを話させることができます。シミュレータは、テキスト読み上げの発音方法をテストするのにも適しています。あなたは、その手順の後で、調整すべき言葉遣いを見つけることもあるかもしれません。

[アクションシミュレータの使用](https://developers.google.com/actions/tools/web-simulator)

## より多くのリソースを読む

会話をテストした後に、よりスムーズな会話のために、洗練したい、そして構築したいシナリオを見つけたくなるでしょう。自然で使いやすい会話型インタフェースを作成することは、最初に考えたよりも難しい場合があります。時間をかけて洗練し、ユーザーのために最高の体験を可能にします。ここに、始めるにあたってのいくつかの話題があります。

* [The Conversational UI and Why It Matters](https://developers.google.com/actions/design/)
* [Principles and Methodology](https://developers.google.com/actions/design/principles)
* [Understanding How Conversations Work: The Key to a Better UI](https://developers.google.com/actions/design/how-conversations-work)
* [Be Cooperative...Like Your Users](https://developers.google.com/actions/design/be-cooperative)
* [Unlocking the Power of Spoken Language](https://developers.google.com/actions/design/unlocking-the-power-of-spoken-language)
* [Instilling User Confidence Through Confirmations and Acknowledgements](https://developers.google.com/actions/design/instilling-user-confidence)
* [In Conversation, There Are No Errors](https://developers.google.com/actions/design/conversation-repair)

## サンプルの入手

Now that you have a good idea of how to design a great conversational interface, see the Number Genie sample and tutorial for more information on how to build the Number Genie game that we just designed.
あなたは、優れた会話型インターフェイスを設計する方法についての良いアイデアを得ました。ここで作成したNumber Genieゲームを構築する方法の詳細については、Number Genieの[サンプル](https://github.com/actions-on-google/dialogflow-number-genie-nodejs)と[チュートリアル](https://developers.google.com/actions/dialogflow/tutorials/number-genie)を参照してください。

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/design/walkthrough)
