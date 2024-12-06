---
layout: actions_on_google
title: Design in Action "Design Checklist"を日本語訳しました
categories:
- actions on google
---
Design in Actionの[設計原則と方法論](https://www.eisbahn.jp/yoichiro/2017/10/actions_on_google_custom_design_3.html)と[会話を巧みに作る](https://www.eisbahn.jp/yoichiro/2017/10/actions_on_google_custom_design_4.html)を読むことで、Googleアシスタント向けのアプリにてどのように会話型UIを組み立てていけば良いかをイメージすることができるようになります。これに加えて、正しい会話型UIとして対話が設計されているかどうかを確認するためのチェックリストがあると、とても役に立ちます。

ここでは、Actions on Googleのドキュメントに掲載されている、会話型UIとして正しく設計されたかどうかを確認するための[Design Checklist](https://developers.google.com/actions/design/checklist)の日本語訳を紹介します。設計後にこのチェックリストを使って確認し、すべての項目においてチェックが付けられていれば、会話型UIとしての品質は良く、一般公開する際の承認を得られる可能性も高いと考えて良いでしょう。

---

# 設計チェックリスト

[PDFをダウンロード](https://developers.google.com/actions/downloads/actionsongoogle_designchecklist.pdf)

このチェックリストは、アクションをユーザーに[配布](https://developers.google.com/actions/distribute/)する準備が整ったことを確実にする迅速な方法を提供し、承認に向けてアクションを評価するのに役立ちます。あなたが着手する際に、この[設計プロセス](https://developers.google.com/actions/design/principles)に従うことをお勧めします。

<table style="background-color: #3949ab;">
<tr style="color: white;">
<td style="padding: 5px;">挨拶と別れの言葉</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;"><input type="checkbox"><b>あなたが誰かをユーザに伝える</b>
<p>Googleアシスタントは会話のアクションにユーザー体験を会話アクションにパスするので、ユーザーがその体験に入っていることを必ず伝えるようにしてください。</p>
<ul>
<li>あなたのペルソナは、話している相手をユーザーに伝えますか？</li>
<li>Googleアシスタントからアクションへの明確な移行はありますか？また、ユーザーは現在の位置を知ることができますか？</li>
</ul>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;"><input type="checkbox"><b>適切な情報量を与える</b>
<p>アクションに熟知している度合いに応じて、挨拶をユーザーに提供してください。</p>
<ul>
<li>ブランドの新しいユーザーは、アクションが何であるかを理解していますか？最初の挨拶は、ユーザを困惑させない有益なものでしょうか？</li>
<li>あなたの挨拶は体験済みのユーザーに繰り返されますか？戻ってきたユーザー向けに、より短くて親しみやすい挨拶がありますか？</li>
</ul>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;"><input type="checkbox"><b>会話を適切に終了する</b>
<p>ユーザーが意図を満たした際に、何か他のことをする機会を与えたり、続きを進めることができます。妨げにならない出口への道がありますか？「留意」や「いいえ、ありがとう」のような単純な取り消し要求は、ユーザにとって意味のある文脈として尊重されますか？</p>
</td>
</tr>
</table>

<p></p>

<table style="background-color: #fb8c00;">
<tr style="color: white;">
<td style="padding: 5px;"><strong>ペルソナ</strong></td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<input type="checkbox"><b>あなたのユニークなブランドとアイデンティティを反映する</b>
<p>あなたが予定しているかどうかに関わらず、ユーザはペルソナを認識します。従って、あなたのブランド向けに定義されているペルソナがまだない場合は、<a href="https://developers.google.com/actions/design/principles#personas">作成してください</a>！私たちのデザインヒント<a href="https://youtu.be/MSUPVbbhIGA?t=1m35s">ビデオ</a>で詳細をご覧ください。</p>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<input type="checkbox"><b>ユーザが戻ってくることを維持する</b>
<p>あなたのペルソナを本当の人物とみなし、多くのやり取りを行った後であっても、あなたとあなたのユーザーがその人物とやり取りをしたいかどうかを確かめてください。</p>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<input type="checkbox"><b>一貫性を保つ</b>
<p>ユーザーが「複数の人物と話しているように感じる」という厄介な、または混乱するダイアログを経験することがないように、会話全体を通してペルソナを維持してください。</p>
</td>
</tr>
</table>

<p></p>

<table style="background-color: #00acc1;">
<tr style="color: white;">
<td style="padding: 5px;">会話型のダイアログ</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;"><input type="checkbox"><b>交代する</b>
<p>良い会話のパートナーは、適切な合図を与える方法を知っています。</p>
<ul>
<li><b>行うこと</b> あなたがターンを明け渡す度に、応答できるための十分な文脈をユーザに与える。 <b>行わないこと</b> 曖昧な文を作ってマイクを渡すだけ。</li>
<li><b>行うこと</b> ユーザーに質問を行い、会話を相手に引き継ぐきっかけを与える。 <b>行わないこと</b> 質問をした後に話し続ける。</li>
</ul>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<input type="checkbox"><b>自然な印象</b>
<p>あなたが書くすべてのダイアログについて、<b>声を出して</b>それを確かめてください。</p>
<ul>
<li>あなたが望んだペルソナは何と言いますか？</li>
<li>何か他の媒体（変換されたモバイルアプリやウェブサイトなど）のコピーではなく、<a href="https://developers.google.com/actions/design/principles#methodology">会話を念頭に置いて</a>書かれていますか？</li>
</ul>
</td>
</tr>
</table>

<p></p>

<table style="background-color: #8e24aa;">
<tr style="color: white;">
<td style="padding: 5px;">会話の修復 (エラーハンドリング)</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<input type="checkbox">
<b>バリエーションを期待したエラーの抑止</b>
<p>数多くの異なる言い回しで表現された入力を理解します: yes, yeah, sure, it does, it sure does, of course, definitely</p>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<input type="checkbox">
<b>役に立つ再指示や他の質問への転換</b>
<p>あなたのアクションが理解できないことをユーザが言うとき、あるいはユーザが何も言わないとき（2つはとても異なる文脈です！）、ユーザへの質問を組み立て直してください。</p>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<input type="checkbox"><b>いつでも助けを準備しておく</b>
<p>ユーザーは会話のどの時点ででも助けを求めることができます（"What can I do?"）。そのために、ヘルプを準備して、ユーザに再指示をしたり、明示的なヘルプのダイアログを提供したりしてください。ヒント: <a href="https://developers.google.com/actions/design/unlocking-the-power-of-spoken-language">直感的</a>なコマンドを使って混乱を防ぐ。</p>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<input type="checkbox">
<b>ユーザに情報のリプレイをさせる</b>
<p>"what?" "repeat" "say that again" などのユーザー入力を認識し、適切に対応します。</p>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;">
<input type="checkbox">
<b>優雅な失敗</b>
<p>ユーザーがレスポンスを提供しない場合、または2回または3回試行した後でも認識できないレスポンスをユーザが返す場合は、適切なメッセージで終了します。</p>
</td>
</tr>
</table>

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/design/checklist)
