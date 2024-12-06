---
layout: actions_on_google
title: Invocation and Discovery "Checklist"の日本語訳です
categories:
- actions on google
---
Googleアシスタント向けアプリを呼び出すための方法は、明示的な呼び出し（Explicit Invocation）と、暗黙的な呼び出し（Implicit Invocation）
の2種類があります。「ユーザがどのようにコンタクトを取ってくるか」を十分に考えることで、これらの呼び出しに関する設計も洗練されてくるはずです。

Actions on Googleのドキュメントにある
[Invocation and Discovery - Checklist](https://developers.google.com/actions/discovery/checklist)にて、
アプリの呼び出しと発見に関するチェックリストが掲載されています。以下はその日本語訳です。

---

# 呼び出しと発見のチェックリスト

<table style="background-color: #00acc1;">
<tr style="color: white;">
<td style="padding: 5px;">アプリ名</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;"><input type="checkbox"><b>複数の発音を持つ単語を避ける</b>
<p>これは、ユーザーとGoogleアシスタントの両方があなたのアプリ名の発音を知るために必要です。たとえば、ユーザーとアシスタントが「en-vel-ope」や「ahn-vel-ope」のどちらを宣言するか分からないため、アプリ名に「Envelope」という単語を含めることはお勧めできません。他の例には、"tear"、"bow"、および "bass" があります。</p>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;"><input type="checkbox"><b>Googleがあなたのアプリ名を認識していることを確認する</b>
<ul>
<li>デベロッパーコンソールのマイク入力を使用して、タイプするのではなく、アプリ名を話してください。音声認識機能を使用すると、アプリ名が正しく認識される可能性が高くなります。また、正確に一貫して認識されることを確認するために、何度か試してみてください。音声入力は現在Chromeブラウザでのみ利用可能です。</li>
<li>Actions Simulatorのマイク入力で、あなたのアプリ名をテストしてください。テキスト出力があなたのアプリ名のものと一致するかどうかを確認してください。</li>
<li>音声認識可能なスピーカーで、あなたのアクションをテストしてください。音声認識可能なスピーカーで、あなたのアクションをプレビューし、アプリを呼び出してみてください。</li>
<li>さまざまな性別やアクセントの人々と一緒に試してみてください。誰がそれを言っても、あなたのアプリ名が認識されることを目指します。</li>
</ul>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;"><input type="checkbox"><b>簡単だけどユニークな名前を選択する</b>
<p>ユーザーが簡単にあなたのアクションを使用できるようにします。ユーザが名前を言うのに苦労しなければならない場合、または名前を簡単に覚えていない場合は、あなたのアクションを使用し続ける可能性は、かなりに低いです。</p>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;"><input type="checkbox"><b>Googleのアプリ名ポリシーに準拠する</b>
<p>アプリ名のガイドラインと制限の詳細については、<a href="https://developers.google.com/actions/policies/general-policies#name_requirements">app naming policies</a>をご覧ください。</p>
<p>Googleのポリシーに違反するビジネス向けのアプリ名が必要な場合や、アシスタントが正しく認識できない場合は、<a href="https://developers.google.com/actions/support">サポート</a>に連絡して、ケースバイケースで評価する例外的なサポートを受けるために連絡をしてください。</p>
</td>
</tr>
</table>

<p></p>

<table style="background-color: #fb8c00;">
<tr style="color: white;">
<td style="padding: 5px;">アクション呼び出しフレーズ</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;"><input type="checkbox"><b>アプリのユースケースの設計</b>
<p><b>DO</b> あなたのアプリの<a href="https://developers.google.com/actions/design/principles#optimize_for_conversation_pick_the_right_use_cases">特定のタスクと実際のユースケース</a>に関係するあなたのアクション呼び出しフレーズを設計してください。</p>
<p><b>DON'T</b> あまりにも一般的なフレーズや、アプリの目的を具体的に説明していないフレーズは使用しないでください。</p>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;"><input type="checkbox"><b>明確な動詞と目的語のペアを指定する</b>
<p>最も有用なアクション呼び出しフレーズは、通常、動詞-目的語のペアで構成されます。これらのフレーズは、覚えやすく、ほとんどのユーザーの問い合わせとうまく適合します。</p>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;"><input type="checkbox"><b>質の高いアクション呼び出しフレーズを幅広く提供する</b>
<p>ユーザーの選択した単語に関係なく、あなたのアクションが呼び出されるようにする必要があります。各アクションに少なくとも10の異なる呼び出しフレーズを提供してください。</p>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;"><input type="checkbox"><b>良い、そして悪いアクション呼び出しの例をレビューする</b>
<p>アクション呼び出しフレーズは、あなたのアプリが暗黙的な呼び出しによって自然に発見されるために重要です。そのため、それらがうまく設計されていることを確認する時間が必要です。<a href="https://developers.google.com/actions/discovery/implicit#writing_useful_action_invocation_phrases">有用なアクション呼び出しフレーズを書く</a>ための推奨事項を見直して、あなたのアプリがこの権利を得られるようにしてください。</p>
</td>
</tr>
</table>

<p></p>

<table style="background-color: #3949ab;">
<tr style="color: white;">
<td style="padding: 5px;">アシスタントディレクトリ</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;"><input type="checkbox"><b>アシスタントディレクトリのWebリンクを使用する</b>
<p>アシスタントディレクトリのWebリンクを使用してアプリを宣伝してください。Webisteやその他のマーケティング資料にこのリンクを提供して、ユーザーにあなたのアクションを促すことができます。</p>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;"><input type="checkbox"><b>良いアプリの説明文を書く</b>
<p>説明文が、テキストと画像を使って、あなたのアプリのユーティリティ、メリット、および最高の機能を促進することを確認してください。</p>
<p>アシスタントがあなたのアプリをユーザーに推薦し、さらに詳細を尋ねる場合、アシスタントはあなたのアプリの説明文をユーザーに読んでくれます。最初に良い印象を与えるために、あなたの説明文がこの文脈で良く聞こえるようにしてください。</p>
</td>
</tr>
</table>

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/discovery/checklist)
