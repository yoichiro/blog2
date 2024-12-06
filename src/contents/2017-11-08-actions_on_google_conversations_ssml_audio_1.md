---
layout: actions_on_google
title: Conversations SSML and Audio "SSML"の日本語訳です
categories:
- actions on google
---
Googleアシスタント向けのアプリでは、基本的にユーザにはテキストから音声合成された結果を「聞かせる」ことになるのですが、時としてもっと異なるものを聞かせたくなることがあります。音声合成を細かく制御するために、またオーディオファイルの再生を指示するために、SSML（Speech Synthesis Markup Language）のサブセットを利用することができます。Actions on Googleの [Conversations SSML and Audio - SSML](https://developers.google.com/actions/reference/ssml) にて、サポートされるSSMLの内容が説明されています。以下は、その日本語訳です。

---

# SSML

Googleアシスタントへの返信を返すとき、あなたの回答に音声合成マークアップ言語（SSML）のサブセットを使用することができます。SSMLを使用することで、エージェントの応答をもっと生き生きとした感じにすることができます。以下は、SSMLマークアップの例と、それがGoogleアシスタントによってどのように読み取られるかを示しています。

**マークアップ**

```html
<speak>
  Here are <say-as interpret-as="characters">SSML</say-as> samples.
  I can pause <break time="3s"/>.
  I can play a sound
  <audio src="https://www.example.com/MY_MP3_FILE.mp3">didn't get your MP3 audio file</audio>.
  I can speak in cardinals. Your number is <say-as interpret-as="cardinal">10</say-as>.
  Or I can speak in ordinals. You are <say-as interpret-as="ordinal">10</say-as> in line.
  Or I can even speak in digits. The digits for ten are <say-as interpret-as="characters">10</say-as>.
  I can also substitute phrases, like the <sub alias="World Wide Web Consortium">W3C</sub>.
  Finally, I can speak a paragraph with two sentences.
  <p><s>This is sentence one.</s><s>This is sentence two.</s></p>
</speak>
```

```text
Here are S S M L samples. I can pause [3秒間停止]. I can play a sound [オーディオファイル再生].
I can speak in cardinals. Your number is ten.
Or I can speak in ordinals. You are tenth in line.
Or I can even speak in digits. The digits for ten are one oh.
I can also substitute phrases, like the World Wide Web Consortium.
Finally, I can speak a paragraph with two sentences. This is sentence one. This is sentence two.
```

SSMLの出力にクライアントライブラリを使用する方法については、[SSMLリファレンスガイド](https://developers.google.com/actions/reference/ssml)を参照してください。注: SSMLは[アクションシミュレータ](https://developers.google.com/actions/tools/web-simulator)ではサポートされていますが、Dialogflowシミュレータではサポートされていません。

## サポートされるSSML要素

以下の表は、あなたが使用することができるSSML要素を説明しています。

**&lt;speak&gt;**

SSMLレスポンスのルート要素。必須の<b>xml:lang</b>属性は、ルートドキュメントの言語を指定します。
以下は、<b>&lt;speak&gt;</b>要素の使用例です。
{% highlight html %}
<speak>
my SSML content
</speak>
{% endhighlight %}
より詳しい情報は、<a href="https://www.w3.org/TR/speech-synthesis/#edef_speak"><b>speak</b> Element</a>を参照してください。

**&lt;break&gt;**

単語間の一時停止やその他の境界を制御する空の要素です。任意の単語のペアの間に&lt;break&t;を使用することは、任意です。
以下の例は、2つの手順の間で一時停止する<b>&lt;break&gt;</b>要素の使用例です。<br>
{% highlight html %}
<speak>
Step 1, take a deep breath. <break time="2s" />
Step 2, exhale.
</speak>
{% endhighlight %}
より詳しい情報は、<a href="https://www.w3.org/TR/speech-synthesis/#S3.2.3"><b>break</b> Element</a>を参照してください。

**&lt;say-as&gt;**

要素内に含まれるテキスト構造の種別に関する情報を指定します。また、含まれているテキストのレンダリングの詳細レベルを指定するのに役立ちます。<br>
<b>&lt;say-as&gt;</b>要素には、値の発声方法を決定する必須の属性である、<b>interpret-as</b>があります。任意の属性の<b>format</b>と<b>detail</b>は、特定の<b>interpret-as</b>値に依存して使用されるかもしれません。<b>interpret-as</b>属性は、次の値をサポートします。<br>
<ul>
<li>
<b>cardinal</b><br>
次の例は、"Twelve thousand three hundred forty five（アメリカ英語）" または "Twelve thousand three hundred and forty five（イギリス英語）" として発声されます。<br>
{% highlight html %}
<speak>
  <say-as interpret-as="cardinal">12345</say-as>
</speak>
{% endhighlight %}
</li>
<li>
<b>ordinal</b><br>
次の例は、"First"として発声されます。<br>
{% highlight html %}
<speak>
  <say-as interpret-as="ordinal">1</say-as>
</speak>
{% endhighlight %}
</li>
<li>
<b>ordinal</b><br>
次の例は、"see ay en"として発声されます。<br>
{% highlight html %}
<speak>
  <say-as interpret-as="characters">can</say-as>
</speak>
{% endhighlight %}
</li>
<li>
<b>date</b><br>
次の例は、"September ten nineteen hundred sixty"として発声されます。<br>
{% highlight html %}
<speak>
  <say-as interpret-as="date" format="ymd">1960-09-10</say-as>
</speak>
{% endhighlight %}
<b>format</b>属性は、日付フィールドの文字コードのシーケンスです。<b>format</b>でサポートされているフィールド文字コードは、それぞれ年、月、（その月の）日の{<b>y</b>、<b>m</b>、<b>d</b>}です。フィールドコードが年、月、または日について1文字指定される場合、予想される桁数はそれぞれ4,2および2です。もしフィールドコードが繰り返される場合、期待される数字の桁は、コードが繰り返される回数になります。日付テキストのフィールドは、句読点やスペースで区切ることができます。<br>
<b>detail</b>属性は、日付の発声形式を制御します。<b>detail='1'</b>の場合、日フィールドと月フィールドまたは年フィールドのいずれか1つだけが必要ですが、両方とも提供されることもあります。これは、3つのフィールドすべてよりも少ない指定がされた場合のデフォルトです。発声形式は "The {順序の日} of {月}, {年}" です。<br>
以下の例は、"The tenth of September, nineteen sixty"と発声されます。<br>
{% highlight html %}
<speak>
  <say-as interpret-as="date" format="yyyymmdd" detail="1">
    1960-09-10
  </say-as>
</speak>
{% endhighlight %}
次の例は、"The tenth of September"と発声されます。
{% highlight html %}
<speak>
  <say-as interpret-as="date" format="dm">10-9</say-as>
</speak>
{% endhighlight %}
<b>detail='2'</b>の場合は、日、月、年のフィールドが必要であり、3つのフィールドがすべて指定されている場合は、これがデフォルトです。発声形式は "{月} {日付序数}, {年}" です。<br>
以下の例は、"September tenth, nineteen sixty" と発声されます。
{% highlight html %}
<speak>
  <say-as interpret-as="date" format="dmy" detail="2">
    10-9-1960
  </say-as>
</speak>
{% endhighlight %}
</li>
<li>
<b>time</b><br>
次の例は、"Two thirty P.M."として発声されます。<br>
{% highlight html %}
<speak>
  <say-as interpret-as="time" format="hms12">2:30pm</say-as>
</speak>
{% endhighlight %}
<b>format</b>属性は、時間フィールドの文字コードのシーケンスです。時、（その時の）分、（その分の）秒、タイムゾーン、12時間制、24時間制のフォーマットでサポートされているフィールド文字コードはそれぞれ {<b>h</b>, <b>m</b>, <b>s</b>, <b>Z</b>, <b>12</b>, <b>24</b>} です。時、分または秒のフィールドコードが1回指定される場合は、予想される数字の桁数はそれぞれ1,2および2です。フィールドコードが繰り返される場合、期待される数字の桁は、コードが繰り返される回数となります。時間テキストのフィールドは、句読点やスペースで区切ることができます。時、分、秒がformatで指定されていないか、桁が一致しない場合、フィールドはゼロ値として扱われます。デフォルトの<b>format</b>は "hms12" です。<br>
<b>detail</b>属性は、時間の音声形式が12時間制か24時間制かを制御します。<b>detail='1'</b>の場合、または<b>detail</b>が省略されていて時刻の形式が24時間制の場合、音声形式は24時間制です。<b>detail='2'</b>の場合、または<b>detail</b>が省略されていて時刻の形式が12時間制の場合、音声形式は12時間制になります。
</li>
<li>
<b>telephone</b><br>
See the interpret-as='telephone' description in the W3C SSML 1.0 say-as attribute values WG note.
W3C SSML 1.0 <a href="https://www.w3.org/TR/ssml-sayas/#S3.3">say-as attribute valuse</a> WG noteの中の<b>interpret-as='telephone'</b>を参照ください。
</li>
</ul>
より詳しい情報は、<a href="https://www.w3.org/TR/speech-synthesis/#S3.1.8">say-as Element</a>を参照してください。


**&lt;audio&gt;**

合成された音声出力と共に、記録されたオーディオファイルの挿入と他のオーディオフォーマットの挿入をサポートします。<br>
以下は、現在サポートされているオーディオの設定です。
<ul>
<li>
フォーマット: MP3 (MPEG v2)
<ul>
<li>毎秒24Kサンプル</li>
<li>毎秒24K〜96Kビット、固定レート</li>
</ul>
</li>
<li>
フォーマット: OggのOpus
<ul>
<li>毎秒24Kサンプル（スーパーワイドバンド）</li>
<li>毎秒24K〜96Kビット、固定レート</li>
</ul>
</li>
<li>
フォーマット: (廃止予定) WAV (RIFF)
<ul>
<li>PCM 16ビット符号付き、リトルエンディアン</li>
<li>毎秒24Kサンプル</li>
</ul>
</li>
<li>
全てのフォーマット向け:
<ul>
<li>シングルチャンネルが好ましいが、ステレオでも良い。</li>
<li>最大持続時間は120秒。</li>
<li>ファイルサイズ制限は5MB。</li>
<li>ソースURLはHTTPSプロトコルが必須。</li>
<li>オーディオを取得する際のUserAgentは、"Google-Speech-Actions"。</li>
</ul>
</li>
</ul>
以下の例は、<b>src</b> URLで格納された音を出力します。
{% highlight html %}
<speak>
  <audio src="https://.../meow.mp3">
    a cat meowing
  </audio>
</speak>
{% endhighlight %}
<b>&lt;audio&gt;</b>要素の内容は任意であり、オーディオファイルを再生できない場合、または出力デバイスがオーディオをサポートしていない場合に使用されます。<br>
The src URL must also be an https URL (Google Cloud Storage can host your audio files on an https URL).
また、<b>src</b> URLは、https URLでなければなりません（<a href="https://cloud.google.com/storage/docs/creating-buckets">Google Cloud Storage</a>は、あなたのオーディオファイルをhttps URLでホストすることが可能です）。<br>
より詳しい情報は、<a href="https://www.w3.org/TR/speech-synthesis/#S3.3.1">audio Elements</a>を参照ください。


**&lt;p&gt;, &lt;s&gt;**

文および段落の要素です。
{% highlight html %}
<p><s>This is sentence one.</s><s>This is sentence two.</s></p>
{% endhighlight %}
より詳しい情報は、<a href="https://www.w3.org/TR/speech-synthesis/#edef_paragraph"><b>p and s</b> Element</a>を参照してください。


**&lt;sub&gt;**

alias属性値のテキストが、発音のために含まれているテキストに置き換えることを示します。
{% highlight html %}
<sub alias="World Wide Web Consortium">W3C</sub>
{% endhighlight %}
より詳しい情報は、<a href="https://www.w3.org/TR/speech-synthesis/#edef_sub"><b>sub</b> Element</a>を参照してください。


---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/reference/ssml)
