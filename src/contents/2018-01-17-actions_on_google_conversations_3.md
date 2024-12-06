---
layout: actions_on_google
title: Conversations "Responses"の日本語訳です
categories:
- actions on google
---
Googleアシスタント向けアプリがGoogleアシスタントに応答する際に、Google Homeを想像すると、文章のみを応答として返して
それがGoogle Homeによって発話される、となると思います。しかし、サーフェスとして音声だけでなく画面も想定されています。
つまり、アシスタント向けアプリの応答は、単純なテキストだけではなく、もっとリッチな応答も返すことができ、それはサーフェスと
して画面をサポートしていれば、視覚的にリッチな応答が「表示」されるということです。

Actions on Googleのドキュメントにある
[Conversations - Responses](https://developers.google.com/actions/assistant/responses)にて、
アシスタント向けアプリが返すことができる応答の種類が説明されています。以下はその日本語訳です。

---

# レスポンス

> **注:** あなたのUIのデザインに役立つスケッチテンプレートを[ダウンロード](https://developers.google.com/actions/downloads/3PStickerSheet_v1_sketch.zip)してください。

このドキュメントでは、オーディオコンポーネント、ビジュアルコンポーネント、またはその両方の組み合わせを持つことができるアクションを作成するときに、
ユーザーに提示できるさまざまなタイプのUIについて説明します。

## シンプルレスポンス

シンプルレスポンスは、オーディオのみ、画面のみ、または両方のサーフェスにて表現できます。それらは視覚的にチャットバブルの形をとり、
TTS/SSMLの音を出します。

TTSテキストはデフォルトでチャットバブルコンテンツとして使用されます。そして、これがうまく見える場合は、チャットバブルのための
いかなる表示テキストも指定する必要はありません。

**要件**

* `actions.capability.AUDIO_OUTPUT` および `actions.capability.SCREEN_OUTPUT` サーフェスでサポートされています。
* チャットバブルあたり640文字が制限です。制限より長い文字列は、640文字より前の最初の単語区切り（または空白）で切り捨てられます。
 > **推奨:** 各ダイアログ（対話）のターンごとに300文字未満が推奨です。これにより、ユーザーの関与を維持し、ビジュアルとオーディオの
 インターフェイスを明確かつ迅速に感じさせます。また、特にカードや他のインタラクティブなビジュアル要素とチャットバブルのペアリングを
 開始するときに、コンテンツが画面の折り目を超えてしまうのを防ぎます。
* チャットバブルコンテンツは、発音のサブセットまたはTTS/SSML出力の完全なトランスクリプトでなければなりません。これは、ユーザーが
さまざまな状況であなたが話していることをマッピングし、理解度を高めるのに役立ちます。
 > この例では、チャットバブルは "my favorite is 42" を省略しています。
* ターンごとに、最大2つのチャットバブルです。
* Googleに送信するチャットヘッド（ロゴ）は、192x192 ピクセルでなければならず、アニメーションできません。

**Audio (TTS/SSML)**

_Howdy, this is GeekNum. I can tell you fun facts about almost any number, my favorite is 42. What number do you have in mind?_

**Visual**

![](https://developers.google.com/actions/images/geeknum-simpleresponse.svg){:height="600px"}

### サンプルコード

**NODE.JS**

```js
function simpleResponse () {
  const app = new ActionsSdkApp({request, response});
  app.ask({
    speech: 'Howdy! I can tell you fun facts about ' +
    'almost any number, like 42. What do you have in mind?',
    displayText: 'Howdy! I can tell you fun facts about almost any ' +
    'number. What do you have in mind?'
  });
}
```

**JSON**

```json
{
  "conversationToken": "",
  "expectUserResponse": true,
  "expectedInputs": [
    {
      "inputPrompt": {
        "richInitialPrompt": {
          "items": [
            {
              "simpleResponse": {
                "textToSpeech": "Howdy! I can tell you fun facts about almost any number, like 42. What do you have in mind?",
                "displayText": "Howdy! I can tell you fun facts about almost any number. What do you have in mind?"
              }
            }
          ],
          "suggestions": []
        }
      },
      "possibleIntents": [
        {
          "intent": "actions.intent.TEXT"
        }
      ]
    }
  ]
}
```

### SSMLとサウンド

あなたのアプリでSSMLとサウンドを使用することで、より洗練されたものになり、ユーザー体験が向上します。次のコードスニペットは、SSML
を使用するレスポンスを作成する方法を示しています。

**NODE.JS**

```js
function saySSML () {
  const app = new ActionsSdkApp({request, response});
  const textToSpeech = '<speak>' +
    'Here are <say-as interpret-as="characters">SSML</say-as> samples. ' +
    'I can pause <break time="3" />. ' +
    'I can play a sound <audio src="https://www.example.com/MY_WAVE_FILE.wav">your wave file</audio>. ' +
    'I can speak in cardinals. Your position is <say-as interpret-as="cardinal">10</say-as> in line. ' +
    'Or I can speak in ordinals. You are <say-as interpret-as="ordinal">10</say-as> in line. ' +
    'Or I can even speak in digits. Your position in line is <say-as interpret-as="digits">10</say-as>. ' +
    'I can also substitute phrases, like the <sub alias="World Wide Web Consortium">W3C</sub>. ' +
    'Finally, I can speak a paragraph with two sentences. ' +
    '<p><s>This is sentence one.</s><s>This is sentence two.</s></p>' +
    '</speak>';
  app.ask(textToSpeech);
};
```

**JSON**

```json
{
  "conversationToken": "",
  "expectUserResponse": true,
  "expectedInputs": [
    {
      "inputPrompt": {
        "initialPrompts": [
          {
            "ssml": "<speak>Here are <say-as interpret-as=\"characters\">SSML</say-as> samples. I can pause <break time=\"3\" />. I can play a sound <audio src=\"https://www.example.com/MY_WAVE_FILE.wav\">your wave file</audio>. I can speak in cardinals. Your position is <say-as interpret-as=\"cardinal\">10</say-as> in line. Or I can speak in ordinals. You are <say-as interpret-as=\"ordinal\">10</say-as> in line. Or I can even speak in digits. Your position in line is <say-as interpret-as=\"digits\">10</say-as>. I can also substitute phrases, like the <sub alias=\"World Wide Web Consortium\">W3C</sub>. Finally, I can speak a paragraph with two sentences. <p><s>This is sentence one.</s><s>This is sentence two.</s></p></speak>"
          }
        ],
        "noInputPrompts": []
      },
      "possibleIntents": [
        {
          "intent": "actions.intent.TEXT"
        }
      ]
    }
  ]
}

```

より詳しい情報は、[SSML reference documentation](https://developers.google.com/actions/reference/ssml)をご覧ください。

### サウンドライブラリ

[サウンドライブラリ](https://developers.google.com/actions/tools/sound-library)には、さまざまな無料の短いサウンドが用意されています。
これらのサウンドは、あなたのためにホストされているので、SSMLにそれらを含めるだけです。

## リッチレスポンス

リッチレスポンスは、画面のみ、または音声と画面の体験にて表示できます。それらは以下のコンポーネントを含むことができます：

* 1つか2つのシンプルレスポンス（チャットバブル）
* オプションのBasic Card
* オプションのsuggestion chip
* オプションのlink-out chip
* オプションインタフェース（リストまたはカルーセル）

**要件**

* `actions.capability.SCREEN_OUTPUT` サーフェスでサポートされています
* リッチレスポンスの最初の項目は、シンプルレスポンスでなければなりません
* 多くても2つのシンプルレスポンス
* Basic Card、オプションインターフェース（リストまたはカルーセル）、または `StructuredResponse` （Basic Cardとオプションインターフェースの両方を同時に持つことはできません）
* 多くても8個のSuggestion chip
* Suggestion chipは、[FinalResponse](https://developers.google.com/actions/reference/rest/Shared.Types/AppResponse#FinalResponse)では使用できません

以下の例は、さまざまな種類のリッチレスポンスを作成する方法を示しています。

## Basic Card

![](https://developers.google.com/actions/images/geeknum-card.svg){:height="600px"}

Basic Cardには、以下を含む情報が表示されます。

* 画像
* タイトル
* サブタイトル
* テキスト本文
* リンクボタン
* ボーダー（境界）

主に表示のためにBasic Cardを使用してください。それらは、簡潔に、主要な（または要約の）情報をユーザーに提示し、ユーザーが（Webリンクを使用して）選択すればさらに多くのことを学べるように設計されています。

ほとんどの場合、カードの下にsuggestion chipを追加して、会話を続行するかピボットすべきです。

チャットバブルでカードに表示されている情報を何度も繰り返すことは避けてください。

**要件**

* `actions.capability.SCREEN_OUTPUT` サーフェスでサポートされています
* 書式設定されたテキスト（_画像がない場合に必要_）
  * デフォルトではプレーンテキストです。
  * リンクを含んではいけません。
  * 画像を伴う場合は10行制限、画像なしの場合は15行制限があります。これは、おおよそ500（画像あり）または750（画像なし）文字です。画面の小さい電話機は、大きな画面の電話機よりも、早くテキストを切り捨てます。テキストの行数が多すぎると、省略記号を使って、最後の単語の区切りで切り捨てられます。
  * Markdownの限定されたサブセットがサポートされています
    * 二重スペースの新しい行
    * `**bold**`
    * `*italics*`
* 画像（書式付きテキストがない場合に必要）
  * すべての画像は、192dpの高さに強制されます。
  * 画像のアスペクト比が画面と異なる場合、画像は垂直または水平のエッジにおいて灰色のバーで中央揃えされます。
  * 画像ソースはURLです。
  * モーションGIFを利用可能です。

**任意**

* タイトル
  * プレーンテキスト
  * フォントとサイズは固定
  * 最大で1行。余分な文字は切り捨てられます。
  * タイトルが指定されていない場合、カードの高さは折りたたまれます。
* サブタイトル
  * プレーンテキスト
  * フォントとサイズは固定
  * 最大で1行。余分な文字は切り捨てられます。
  * サブタイトルが指定されていない場合、カードの高さは折りたたまれます。
* リンクボタン
  * リンクタイトルは必須
  * 最大で一つのリンク
  * 開発者のドメイン外のサイトへのリンクは許可されています。
  * リンクテキストは誤解を招くものであってはなりません。これは承認プロセスでチェックされます。 
  * Basic Cardには、リンクなしでの対話機能はありません。リンクをタップすると、ユーザがリンクに送られ、カードのメイン本体は非アクティブのままです。
* 境界
  * カードと画像コンテナ間の境界を調整して、Basic Cardの見た目をカスタマイズすることができます。
  * 文字列をパラメータとして受け付ける `.setImageDisplay` メソッドを呼び出すことによって設定されます。
Parameter value options are DEFAULT, WHITE, or CROPPED.
  * パラメータ値の選択肢は、 `DEFAULT` 、 `WHITE` 、または `CROPPED` です。

### サンプルコード

**NODE.JS**

```js
function basicCard () {
  const app = new ActionsSdkApp({request, response});
  app.ask(app.buildRichResponse()
    // Create a basic card and add it to the rich response
    .addSimpleResponse('Math and prime numbers it is!')
    .addBasicCard(app.buildBasicCard('42 is an even composite number. It' +
      'is composed of three distinct prime numbers multiplied together. It' +
      'has a total of eight divisors. 42 is an abundant number, because the' +
      'sum of its proper divisors 54 is greater than itself. To count from' +
      '1 to 42 would take you about twenty-one…')
      .setTitle('Math & prime numbers')
      .addButton('Read more', 'https://example.google.com/mathandprimes')
      .setImage('https://example.google.com/42.png', 'Image alternate text')
      .setImageDisplay('CROPPED')
    )
  );
}
```

**JSON**

```json
{
  "conversationToken": "",
  "expectUserResponse": true,
  "expectedInputs": [
    {
      "inputPrompt": {
        "richInitialPrompt": {
          "items": [
            {
              "simpleResponse": {
                "textToSpeech": "Math and prime numbers it is!"
              }
            },
            {
              "basicCard": {
                "title": "Math & prime numbers",
                "formattedText": "42 is an even composite number. It\n    is composed of three distinct prime numbers multiplied together. It\n    has a total of eight divisors. 42 is an abundant number, because the\n    sum of its proper divisors 54 is greater than itself. To count from\n    1 to 42 would take you about twenty-one…",
                "image": {
                  "url": "https://example.google.com/42.png",
                  "accessibilityText": "Image alternate text"
                },
                "buttons": [
                  {
                    "title": "Read more",
                    "openUrlAction": {
                      "url": "https://example.google.com/mathandprimes"
                    }
                  }
                ],
                "imageDisplayOptions": "CROPPED"
              }
            }
          ],
          "suggestions": []
        }
      },
      "possibleIntents": [
        {
          "intent": "actions.intent.TEXT"
        }
      ]
    }
  ]
}
```

## List Selector

![](https://developers.google.com/actions/images/geeknum-list.svg){:height="600px"}

単一選択リストは、複数の項目の垂直リストをユーザに提示し、ユーザが単一項目を選択することを可能にします。
リストから項目を選択すると、リスト項目のタイトルを含むユーザクエリ（チャットバブル）が生成されます。

**要件**

* `actions.capability.SCREEN_OUTPUT` サーフェスでサポートされています
* リストタイトル（任意）
  * 固定のフォントとサイズ
  * 1行に制限されています。（余分な文字は切り捨てられます。）
  * プレーンテキストやMarkdownはサポートされていません。
  * タイトルが指定されていない場合、カードの高さは折りたたまれます。
* リスト項目
  * タイトル
    * 固定のフォントとフォントサイズ
    * 最大長: 1行（省略記号で切り捨てられます...）
    * 一意にする必要があります（音声選択をサポートするため）
  * 本文テキスト（任意）
    * 固定のフォントとフォントサイズ
    * 最大長: 2行（省略記号で切り捨てられます...）
  * 画像（任意）
    * サイズ: 48x48ピクセル
* ページネーション
  * 改ページコントロールは、2つの条件の下に表示されます
    * シンプルリスト: 5項目まで
    * 本文テキストまたはイメージのリスト: 3項目まで
  * 最大30項目
* インタラクション
  * 音声/テキスト
    * ユーザは、項目のタイトルをタップする代わりに、いつでもそれを言ったりタイプしたりできます。
  * スワイプ
    * リスト内のアイテムの数がページングコントロールを表示させるのに十分な大きさである場合、左右にスワイプすると異なるリスト項目が表示されます。

**ガイダンス**

選択肢を比較することが重要な場合（例: あなたが話す必要があるのは、どの"Peter"？ "Peter Jons"ですか？または"Peter Hans"ですか？）、
またはユーザーが一目でざっと見る必要のある選択肢の間で選ぶ必要がある場合には、リストが適しています。

Suggestion chipをリストの下に追加して、ユーザーが会話をピボットまたは展開できるようにすることをおすすめします。
リストに提示された選択肢をSuggestion chipとして繰り返さないでください。
このコンテキスト内のChipは、選択肢の選択ではなく、会話をピボットするために使用されます。

ここで示した例では、リストカードに付随するチャットバブルはオーディオ（TTS/SSML）のサブセットです。ここでのオーディオ（TTS/SSML）は、
最初にリストされた項目を統合します。リストからすべての要素を読むことはお勧めしません。トップアイテム（例えば、最も人気のあるもの、
最近購入されたもの、または最も話題のもの）について言及することが最善です。

### サンプルコード

**NODE.JS**

```js
function list () {
  const app = new ActionsSdkApp({request, response});
  app.askWithList('Alright! Here are a few things you can learn. Which sounds interesting?',
    // Build a list
    app.buildList('Things to learn about')
      // Add the first item to the list
      .addItems(app.buildOptionItem('MATH_AND_PRIME',
        ['math', 'math and prime', 'prime numbers', 'prime'])
        .setTitle('Math & prime numbers')
        .setDescription('42 is an abundant number because the sum of its ' +
        'proper divisors 54 is greater…')
        .setImage('http://example.com/math_and_prime.jpg', 'Math & prime numbers'))
      // Add the second item to the list
      .addItems(app.buildOptionItem('EGYPT',
        ['religion', 'egpyt', 'ancient egyptian'])
        .setTitle('Ancient Egyptian religion')
        .setDescription('42 gods who ruled on the fate of the dead in the ' +
        'afterworld. Throughout the under…')
        .setImage('http://example.com/egypt', 'Egypt')
      )
      // Add third item to the list
      .addItems(app.buildOptionItem('RECIPES',
        ['recipes', 'recipe', '42 recipes'])
        .setTitle('42 recipes with 42 ingredients')
        .setDescription('Here\'s a beautifully simple recipe that\'s full ' +
        'of flavor! All you need is some ginger and…')
        .setImage('http://example.com/recipe', 'Recipe')
      )
  );
}
```

**JSON**

```json
{
  "conversationToken": "",
  "expectUserResponse": true,
  "expectedInputs": [
    {
      "inputPrompt": {
        "initialPrompts": [
          {
            "textToSpeech": "Alright! Here are a few things you can learn. Which sounds interesting?"
          }
        ],
        "noInputPrompts": []
      },
      "possibleIntents": [
        {
          "intent": "actions.intent.OPTION",
          "inputValueData": {
            "@type": "type.googleapis.com/google.actions.v2.OptionValueSpec",
            "listSelect": {
              "title": "Things to learn about",
              "items": [
                {
                  "optionInfo": {
                    "key": "MATH_AND_PRIME",
                    "synonyms": [
                      "math",
                      "math and prime",
                      "prime numbers",
                      "prime"
                    ]
                  },
                  "title": "Math & prime numbers",
                  "description": "42 is an abundant number because the sum of its proper divisors 54 is greater…",
                  "image": {
                    "url": "http://example.com/math_and_prime.jpg",
                    "accessibilityText": "Math & prime numbers"
                  }
                },
                {
                  "optionInfo": {
                    "key": "EGYPT",
                    "synonyms": [
                      "religion",
                      "egpyt",
                      "ancient egyptian"
                    ]
                  },
                  "title": "Ancient Egyptian religion",
                  "description": "42 gods who ruled on the fate of the dead in the afterworld. Throughout the under…",
                  "image": {
                    "url": "http://example.com/egypt",
                    "accessibilityText": "Egypt"
                  }
                },
                {
                  "optionInfo": {
                    "key": "RECIPES",
                    "synonyms": [
                      "recipes",
                      "recipe",
                      "42 recipes"
                    ]
                  },
                  "title": "42 recipes with 42 ingredients",
                  "description": "Here's a beautifully simple recipe that's full of flavor! All you need is some ginger and…",
                  "image": {
                    "url": "http://example.com/recipe",
                    "accessibilityText": "Recipe"
                  }
                }
              ]
            }
          }
        }
      ]
    }
  ]
}
```

### 選択された項目のハンドリング

ユーザーが項目を選択すると、選択した項目の値が引数として渡されます。クライアントライブラリを使用して、
app.getSelectedOption() を呼び出して値を読み取ることができます。戻り値では、選択した項目のキー識別子が取得されます。

**NODE.JS**

```js
const app = new ActionsSdkApp({request, response});
let actionMap = new Map();
actionMap.set(app.StandardIntents.OPTION, () => {
  const param = app.getSelectedOption();
  if (!param) {
    app.ask('You did not select any item from the list or carousel');
  } else if (param === 'MATH_AND_PRIME') {
    app.ask('42 is an abundant number because the sum of its…');
  } else if (param === 'EGYPT') {
    app.ask('42 gods who ruled on the fate of the dead in the...');
  } else if (param === 'RECIPES') {
    app.ask('Here\'s a beautifully simple recipe that\'s full...');
  } else {
    app.ask('You selected an unknown item from the list or carousel');
  }
});
app.handleRequest(actionMap);
```

**JSON**

```json
{
  "user": {
    "userId": "123456abcde",
    "locale": "en-US"
  },
  "conversation": {
    "conversationId": "123456",
    "type": "ACTIVE",
    "conversationToken": ""
  },
  "inputs": [
    {
      "intent": "actions.intent.OPTION",
      "rawInputs": [
        {
          "inputType": "VOICE",
          "query": "42 recipes with 42 ingredients"
        }
      ],
      "arguments": [
        {
          "name": "OPTION",
          "textValue": "RECIPES"
        }
      ]
    }
  ],
  "surface": {
    "capabilities": [
      {
        "name": "actions.capability.AUDIO_OUTPUT"
      },
      {
        "name": "actions.capability.SCREEN_OUTPUT"
      }
    ]
  },
  "isInSandbox": true,
  "availableSurfaces": [
    {
      "capabilities": [
        {
          "name": "actions.capability.AUDIO_OUTPUT"
        },
        {
          "name": "actions.capability.SCREEN_OUTPUT"
        }
      ]
    }
  ]
}
```

## Carousel

![](https://developers.google.com/actions/images/geeknum-carousel.svg){:height="600px"}

カルーセルは水平にスクロールし、1つの項目を選択することができます。List Selectorと比較すると、大きなタイルがあり、
豊富なコンテンツが可能です。カルーセルを構成するタイルは、画像付きのBasic Cardと似ています。カルーセルから項目を選択すると、
List Selectorのように、単にチャットバブルが応答として生成されます。

> **注:** 視覚的には魅力的ですが、カルーセルはマルチモーダルインターフェースでの使用が制限されています。これは、
音声インターフェイスとしての相互作用が難しいためです（そのために、私たちはリストを好みます）。詳細については、
カルーセルのガイドラインのセクションを参照してください。

**要件**

* `actions.capability.SCREEN_OUTPUT` サーフェスでサポートされています
* カルーセル
  * 最大 _＃_ タイル数: 10
  * 最小 _#_ タイル数: 2
  * プレーンテキスト、Markdownはサポートされていません。
* カルーセルタイル
  * 画像（任意）
    * 画像は、高さ 128dp x 幅 232dp に強制されます
    * イメージのアスペクト比がイメージの境界矩形と一致しない場合は、イメージはいずれかの側のバーでセンタリングされます
    * イメージリンクが壊れている場合は、代わりにプレースホルダイメージが使用されます
  * タイトル（必須）
    * Basic Text Cardと同じ
    * タイトルは一意でなければなりません（音声選択をサポートするため）
  * 本文テキスト（任意）
    * Basic Text Cardと同じ書式設定オプション
    * 最大4行
* インタラクション
  * 左右にスワイプ: カルーセルをスライドさせて別のカードを表示します。
  * タップカード: アイテムをタップするだけで、要素のタイトルと同じテキストのチャットバブルが生成されます。
    * `actions_intent_OPTION` イベントを処理するタッチ入力のインテントが必要です。
  * 音声/キーボード: カードタイトル（指定されている場合）を返信することは、そのアイテムを選択することと同じ働きをします。

**ガイダンス**

カルーセルは、さまざまな選択肢がユーザーに提示されている場合に適していますが、（リストと比較して）直接比較する必要はありません。
一般的に、カルーセルよりもリストが優先されるのは、リストを視覚的にスキャンして音声でやりとりする方が簡単だからです。

> **注:** もしあなたがウェブページへリンクするためのアイテムを持つカルーセルを構築したい場合は、代わりにブラウジングカルーセルを構築したく
なるでしょう。

ユーザーが会話をピボットまたは展開できるように、カルーセルの下にSuggestion chipを追加することをおすすめします。
**リストに提示された選択肢をSuggestion chipとして繰り返さないでください。このコンテキスト内のChipは、選択肢の選択ではなく、
会話をピボットするために使用されます。**

リストと同じように、カルーセルカードに付随するチャットバブルは、オーディオのサブセット（TTS/SSML）です。
ここでの音声（TTS/SSML）は、カルーセルの最初のタイルを統合します。カルーセルからすべての要素を読み取ることも強く反対します。
最初のアイテムとそのアイテムが存在する理由（たとえば、最も人気があり、最近購入したもの、最も話したものなど）を言及することが最善です。

> **警告:** カルーセルの選択肢をタップして、意図した応答ではなくフォールバックメッセージが返される場合、タッチ入力を処理するインテント
が設定されていない可能性があります。カルーセルの選択肢をタップすると、 **actions_intent_OPTION** イベントが発生します。
そのため、アプリはタッチ入力に対してこのイベントを処理するインテントを含める必要があります。

### サンプルコード

**NODE.JS**

```js
function carousel (app) {
  app.askWithCarousel('Alright! Here are a few things you can learn. Which sounds interesting?',
    // Build a carousel
    app.buildCarousel()
    // Add the first item to the carousel
    .addItems(app.buildOptionItem('MATH_AND_PRIME',
      ['math', 'math and prime', 'prime numbers', 'prime'])
      .setTitle('Math & prime numbers')
      .setDescription('42 is an abundant number because the sum of its ' +
        'proper divisors 54 is greater…')
      .setImage('http://example.com/math_and_prime.jpg', 'Math & prime numbers'))
    // Add the second item to the carousel
    .addItems(app.buildOptionItem('EGYPT',
      ['religion', 'egpyt', 'ancient egyptian'])
      .setTitle('Ancient Egyptian religion')
      .setDescription('42 gods who ruled on the fate of the dead in the ' +
        'afterworld. Throughout the under…')
      .setImage('http://example.com/egypt', 'Egypt')
    )
    // Add third item to the carousel
    .addItems(app.buildOptionItem('RECIPES',
      ['recipes', 'recipe', '42 recipes'])
      .setTitle('42 recipes with 42 ingredients')
      .setDescription('Here\'s a beautifully simple recipe that\'s full ' +
        'of flavor! All you need is some ginger and…')
      .setImage('http://example.com/recipe', 'Recipe')
    )
  );
}
```

**JSON**

```json
{
  "conversationToken": "",
  "expectUserResponse": true,
  "expectedInputs": [
    {
      "inputPrompt": {
        "initialPrompts": [
          {
            "textToSpeech": "Alright! Here are a few things you can learn. Which sounds interesting?"
          }
        ],
        "noInputPrompts": []
      },
      "possibleIntents": [
        {
          "intent": "actions.intent.OPTION",
          "inputValueData": {
            "@type": "type.googleapis.com/google.actions.v2.OptionValueSpec",
            "carouselSelect": {
              "items": [
                {
                  "optionInfo": {
                    "key": "MATH_AND_PRIME",
                    "synonyms": [
                      "math",
                      "math and prime",
                      "prime numbers",
                      "prime"
                    ]
                  },
                  "title": "Math & prime numbers",
                  "description": "42 is an abundant number because the sum of its proper divisors 54 is greater…",
                  "image": {
                    "url": "http://example.com/math_and_prime.jpg",
                    "accessibilityText": "Math & prime numbers"
                  }
                },
                {
                  "optionInfo": {
                    "key": "EGYPT",
                    "synonyms": [
                      "religion",
                      "egpyt",
                      "ancient egyptian"
                    ]
                  },
                  "title": "Ancient Egyptian religion",
                  "description": "42 gods who ruled on the fate of the dead in the afterworld. Throughout the under…",
                  "image": {
                    "url": "http://example.com/egypt",
                    "accessibilityText": "Egypt"
                  }
                },
                {
                  "optionInfo": {
                    "key": "RECIPES",
                    "synonyms": [
                      "recipes",
                      "recipe",
                      "42 recipes"
                    ]
                  },
                  "title": "42 recipes with 42 ingredients",
                  "description": "Here's a beautifully simple recipe that's full of flavor! All you need is some ginger and…",
                  "image": {
                    "url": "http://example.com/recipe",
                    "accessibilityText": "Recipe"
                  }
                }
              ]
            }
          }
        }
      ]
    }
  ]
}
```

### 選択した項目の処理

ユーザーが項目を選択すると、選択した項目の値が引数として渡されます。クライアントライブラリを使用して、app.getSelectedOption()
を呼び出して値を読み取ることができます。戻り値では、選択した項目のキー識別子が取得されます。

**NODE.JS**

```js
const app = new ActionsSdkApp({request, response});
let actionMap = new Map();
actionMap.set(app.StandardIntents.OPTION, () => {
  const param = app.getSelectedOption();
  if (!param) {
    app.ask('You did not select any item from the list or carousel');
  } else if (param === 'MATH_AND_PRIME') {
    app.ask('42 is an abundant number because the sum of its…');
  } else if (param === 'EGYPT') {
    app.ask('42 gods who ruled on the fate of the dead in the...');
  } else if (param === 'RECIPES') {
    app.ask('Here\'s a beautifully simple recipe that\'s full...');
  } else {
    app.ask('You selected an unknown item from the list or carousel');
  }
});
app.handleRequest(actionMap);
```

**JSON**

```json
{
  "user": {
    "userId": "123456abcde",
    "locale": "en-US"
  },
  "conversation": {
    "conversationId": "123456",
    "type": "ACTIVE",
    "conversationToken": ""
  },
  "inputs": [
    {
      "intent": "actions.intent.OPTION",
      "rawInputs": [
        {
          "inputType": "VOICE",
          "query": "42 recipes with 42 ingredients"
        }
      ],
      "arguments": [
        {
          "name": "OPTION",
          "textValue": "RECIPES"
        }
      ]
    }
  ],
  "surface": {
    "capabilities": [
      {
        "name": "actions.capability.AUDIO_OUTPUT"
      },
      {
        "name": "actions.capability.SCREEN_OUTPUT"
      }
    ]
  },
  "isInSandbox": true,
  "availableSurfaces": [
    {
      "capabilities": [
        {
          "name": "actions.capability.AUDIO_OUTPUT"
        },
        {
          "name": "actions.capability.SCREEN_OUTPUT"
        }
      ]
    }
  ]
}
```

## Browsing Carousel

![](https://developers.google.com/actions/images/browsing-carousel-example.png){:height="600px"}

ブラウジングカルーセルは、リッチレスポンスであり、水平にスクロールすることや、タイトルをユーザに選択させる点において、カルーセルレスポンスに似ています。ブラウジングカルーセルは、特にウェブコンテンツのためにデザインされました。ウェブコンテンツは、ウェブブラウザ（または、もし全てのタイルがAMP可能だった場合は、AMPブラウザ）内で選択されたタイルが開かれます。ブラウジングカルーセルはまた、後で閲覧するために、ユーザのアシスタントサーフェスに留まります。

**要件**

* `actions.capability.SCREEN_OUTPUT` サーフェスでサポートされています
* ブラウジングカルーセル
  * 最大 _＃_ タイル数: 10
  * 最小 _#_ タイル数: 2
  * カルーセル内のタイルは、ウェブコンテンツに全てリンクされていなければなりません（AMPコンテンツが推奨されます）。
    * ユーザがAMPビューアで得られるように、AMPコンテンツの `UrlHintType` は "AMP_CONTENT" がセットされていなければなりません。
* ブラウジングカルーセルタイル
  * タイルの一貫性（必須）
    * ブラウジングカルーセル内の全てのタイルは、同じコンポーネントを持っていなければなりません。例えば、もしあるタイルがimageフィールドを持っている場合は、そのカルーセルの残りの全てのタイルもimageフィールドを持っていなければなりません。
  * 画像（任意）
    * 画像は、高さ 128dp x 幅 232dp に強制されます
    * イメージのアスペクト比がイメージの境界矩形と一致しない場合は、イメージはいずれかの側のバーでセンタリングされます
    * イメージリンクが壊れている場合は、代わりにプレースホルダイメージが使用されます
    * 代替テキスト（Alt-text）がimageに必要です。
  * タイトル（必須）
    * Basic Text Cardと同じ書式オプション
    * タイトルは一意でなければなりません（音声選択をサポートするため）
    * 最大2行
    * フォントサイズは16sp
  * 説明（任意）
    * Basic Text Cardと同じ書式設定オプション
    * 最大4行
    * (...) にて省略されます
    * フォントサイズは14sp、グレー色
  * フッター（任意）
    * 固定されたフォントとサイズ
    * 最大1行
    * (...) にて省略されます
    * 下に固定されます。そして、本文テキストを数行持つタイルは、サブテキストの上に空白を持つかも知れません。
    * フォントサイズは14sp、グレー色
* インタラクション
  * 左右にスワイプ: カルーセルをスライドさせて別のカードを表示します。
  * タップカード: アイテムをタップするだけで、要素のタイトルと同じテキストのチャットバブルが生成されます。
* 音声入力
  * マイクの振る舞い
    * ブラウジングカルーセルがユーザに送信される際は、マイクは再オープンしません。
    * ユーザは依然としてマイクをタップすることができます。また、マイクを再オープンするためにアシスタントを（"Ok, Google"）呼び出すことができます。
    
**ガイダンス**

デフォルトでは、ブラウズカルーセルが送信された後もマイクは閉じたままです。その後もユーザーが会話を続けることができるように、カルーセルの下にSuggestion chipを追加することをおすすめします。 **リストに提示された選択肢をSuggestion chipとして繰り返さないでください。このコンテキスト内のChipは、選択肢の選択ではなく、
会話をピボットするために使用されます。**

> **注:** もしカルーセルの下にSuggestion chipsを提供しない場合は、ブラウジングカルーセルを返した後に、会話を終了することを考慮すべきです。

リストと同じように、カルーセルカードに付随するチャットバブルは、オーディオのサブセット（TTS/SSML）です。
ここでの音声（TTS/SSML）は、カルーセルの最初のタイルを統合します。カルーセルからすべての要素を読み取ることも強く反対します。
最初のアイテムとそのアイテムが存在する理由（たとえば、最も人気があり、最近購入したもの、最も話したものなど）を言及することが最善です。

**サンプルコード**

下のJSONはActions SDKを使ったWebhookのレスポンスを説明していることに注意してください。

**NODE.JS**

```js
function carousel (app) {
  const response = app.buildRichResponse()
    // Introduce the carousel
    .addSimpleResponse('Alright! Here are a few web pages you might want to check out.')
    .addBrowseCarousel(
      app.buildBrowseCarousel()
      // Add the items to the carousel
        .addItems([
          app.buildBrowseItem("Title of item 1", "https://github.com")
            .setDescription("Description of item 1")
            .setFooter("Item 1 footer")
            .setImage('https://www.gstatic.com/mobilesdk/170329_assistant/assistant_color_96dp.png', 'Google Assistant Bubbles'),
          app.buildBrowseItem("Title of item 2", "https://google.com")
            .setDescription("Description of item 2")
            .setFooter("Item 2 footer")
            .setImage('https://www.gstatic.com/mobilesdk/170329_assistant/assistant_color_96dp.png', 'Google Assistant Bubbles')
        ])
    );
}
```

**JSON**

```json
{
  "conversationToken": "[\"_actions_on_google_\"]",
  "expectUserResponse": true,
  "expectedInputs": [
    {
      "inputPrompt": {
        "richInitialPrompt": {
          "items": [
            {
              "simpleResponse": {
                "textToSpeech": "Alright! Here are a few web pages you might want to check out."
              }
            },
            {
              "carouselBrowse": {
                "items": [
                  {
                    "title": "Title of item 1",
                    "description": "Description of item 1",
                    "footer": "Item 1 footer",
                    "image": {
                      "url": "https://www.gstatic.com/mobilesdk/170329_assistant/assistant_color_96dp.png",
                      "accessibilityText": "Google Assistant Bubbles"
                    },
                    "openUrlAction": {
                      "url": "https://github.com"
                    }
                  },
                  {
                    "title": "Title of item 2",
                    "description": "Description of item 2",
                    "footer": "Item 2 footer",
                    "image": {
                      "url": "https://www.gstatic.com/mobilesdk/170329_assistant/assistant_color_96dp.png",
                      "accessibilityText": "Google Assistant Bubbles"
                    },
                    "openUrlAction": {
                      "url": "https://google.com"
                    }
                  }
                ]
              }
            }
          ]
        }
      },
      "possibleIntents": [
        {
          "intent": "assistant.intent.action.TEXT"
        }
      ]
    }
  ],
  "responseMetadata": {
    "status": {},
    "queryMatchInfo": {
      "queryMatched": true,
      "intent": "74c61f1c-8d79-4c07-a2c5-84e20ebd3d16"
    }
  }
}
```

### 選択した項目の処理

ブラウジングカルーセルの項目を使ったユーザの操作に対しては、フォローアップするフルフィルメントは必要ありません。カルーセルは、ブラウザに処理を引き継ぎます。ブラウジングカルーセルの項目を伴うユーザの操作の後は、マイクは再オープンされないことに気をつけてください。そしてあなたは、会話を終了するか、上のガイダンスごとにレスポンスにて[Suggestion chips](https://developers.google.com/actions/assistant/responses#suggestion_chip)を含めるべきです。


## Suggestion Chip

![](https://developers.google.com/actions/images/geeknum-chip.svg){:height="600px"}

**要件**

* `actions.capability.SCREEN_OUTPUT` サーフェスでサポートされます。
* 最大Chip数: 8
* 最大テキスト長: 25文字
* プレーンテキストのみサポート

> あなたのSuggestion chipが（ **addSuggestionLink** または **linkOutSuggestion** を使用して）外部のウェブサイトにリンクしている場合は、
リンク先サイトは検証される必要があります。

**ガイダンス**

Suggestion chipを使用して、応答にヒントを提供して会話を続行するか、会話をピボットします。
会話中にアクションの主な呼びかけがある場合は、それを最初のSuggestion chipとして列挙することを考慮してください。

可能な限り、チャットバブルの一部として1つの重要な提案を組み込む必要がありますが、応答またはチャットの会話が自然であると感じる場合にのみ
行うようにしてください。

### サンプルコード

**NODE.JS**

```js
function suggestionChips () {
  const app = new ActionsSdkApp({request, response});
  app.ask(app.buildRichResponse()
    .addSimpleResponse({speech: 'Howdy! I can tell you fun facts about ' +
        'almost any number like 0, 42, or 100. What number do you have ' +
        'in mind?',
      displayText: 'Howdy! I can tell you fun facts about almost any ' +
        'number. What number do you have in mind?'})
    .addSuggestions(['0', '42', '100', 'Never mind'])
    .addSuggestionLink('Suggestion Link', 'https://assistant.google.com/')
  );
}
```

**JSON**

```json
{
  "conversationToken": "",
  "expectUserResponse": true,
  "expectedInputs": [
    {
      "inputPrompt": {
        "richInitialPrompt": {
          "items": [
            {
              "simpleResponse": {
                "textToSpeech": "Howdy! I can tell you fun facts about almost any number like 0, 42, or 100. What number do you have in mind?",
                "displayText": "Howdy! I can tell you fun facts about almost any number. What number do you have in mind?"
              }
            }
          ],
          "suggestions": [
            {
              "title": "0"
            },
            {
              "title": "42"
            },
            {
              "title": "100"
            },
            {
              "title": "Never mind"
            }
          ],
          "linkOutSuggestion": {
            "destinationName": "Suggestion Link",
            "url": "https://assistant.google.com/"
          }
        }
      },
      "possibleIntents": [
        {
          "intent": "actions.intent.TEXT"
        }
      ]
    }
  ]
}
```

## UIチェックリスト

次のチェックリストは、ユーザーがあなたのアクションを体験しているサーフェス上に、応答が適切に現れることを確認するためにできる、
一般的なことをハイライトしています。

<table style="background-color: #d81b60;">
<tr style="color: white;">
<td style="padding: 5px;">カードと選択肢</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;"><input type="checkbox"><b>カードと選択肢を使用する</b>
<p>カードと選択肢を使用すると、より豊かでカスタマイズ可能な形式で情報を表示できます。</p>
<ul>
<li>Basic card - ユーザーに多くのテキストを提示する必要がある場合は、Basic Cardを使用します。カードには最大15行のテキストを表示し、さらに読むためにウェブサイトにリンクすることができます。チャットバブルとは異なり、カードはテキストの書式設定をサポートしています。画像とリストまたはカルーセルを追加して、選択肢を表示することもできます。</li>
<li>リスト - 選択肢のリストから選択するようにユーザーに要求する場合は、チャットバブルでリストを書き込む代わりにリストを使用することを検討してください。</li>
<li>カルーセル - ユーザーが大きな画像に焦点を合わせて選択肢のリストから選択したい場合、カルーセルを使用します。カルーセルの項目は8個の制限があります。</li>
</ul>
</td>
</tr>
</table>

<p></p>

<table style="background-color: #3949ab;">
<tr style="color: white;">
<td style="padding: 5px;">Suggestion Chips</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;"><input type="checkbox"><b>ほとんどのターンの後に使用する</b>
<p>画面付きの端末でアプリの使い勝手を向上させるためにできる最良のことは、Chipを追加することです。そして、音声やキーボードの使用に加えて、すばやくタップして応答することができます。たとえば、はい/いいえの質問には **はい** と **いいえ** のSuggestion chipが必要です。</p>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;"><input type="checkbox"><b>いくつかの選択肢があるとき...</b>
<p>ユーザーに少数の選択肢（8つ以下）を提供するときは、各選択肢にSuggestionを追加することができます（TTSと同じ順序で、同じ用語を使用して提示します）。</p>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;"><input type="checkbox"><b>多くの選択肢があるとき...</b>
<p>広い範囲の回答があり得る質問をする場合は、最も一般的な回答のいくつかを提示してください。</p>
</td>
</tr>
</table>

<p></p>

<table style="background-color: #00acc1;">
<tr style="color: white;">
<td style="padding: 5px;">チャットバブル</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;"><input type="checkbox"><b>正しい大文字の使用と句読点</b>
<p>あなたのTTS文字列がチャットバブルとして表示されるようになる際には、大文字の使用と句読点が間違っていないかチェックしてください。</p>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;"><input type="checkbox"><b>発音の綴りを修正する</b>
<p>発音の問題を手助けするために、TTSによって音声で何かを綴ると、その音声のスペルミスがチャットバブルに表示されます。画面のあるデバイスでチャットバブルのために正しいスペルを使用するには、別の表示テキストを使用します。</p>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;"><input type="checkbox"><b>切り捨てを避ける</b>
<p>チャットバブルは640文字に制限されており、その制限の後に切り捨てられます（ただし、一般的なデザインガイドラインとして約300を推奨します）。 それ以上のものがあれば、次のことができます。</p>
<ul>
<li><b>2つ目のチャットバブルを使用する</b> - ターンごとに最大2つのチャットバブルが許可されるため、自然なブレークポイントを見つけて2つ目のチャットバブルを作成します。</li>
<li><b>すべてを表示しない</b> - 長いTTSコンテンツを提供する場合は、ちょっとした紹介など、TTSコンテンツのサブセットのみをチャットバブルに表示することを検討してください。この場合、TTSテキストより短い表示テキストを使用できます。</li>
</ul>
</td>
</tr>
</table>

<p></p>

<table style="background-color: #fb8c00;">
<tr style="color: white;">
<td style="padding: 5px;">録音されたオーディオ</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;"><input type="checkbox"><b>チャットバブルから&lt;audio&gt;テキストを削除</b>
<p>SSMLの&lt;audio&gt;タグ内にテキストがある場合、それは対応するチャットバブルに表示されます。たとえば、あなたのSSMLが、</p>
{% highlight html %}<speak>
  Here's that song.
  <audio src="...">song audio</audio>
</speak>{% endhighlight %}
<p>の場合、あなたのチャットバブルテキストは、"Here's that song. song audio"と表示されます。</p>
<p>代わりに、&lt;desc&gt;要素を&lt;audio&gt;要素の中に追加します。&lt;desc&gt;内のテキストはすべて表示され、オーディオソースファイルをロードできない場合は、&lt;audio&gt;以外のテキストが代替テキストとして使用されます。例えば、</p>
{% highlight html %}<speak>
  Here's that song.
  <audio src="bad_url"><desc></desc>song audio</audio>
</speak>{% endhighlight %}
<p>結果として、音声出力は"Here's that song. song audio"になり、チャットバブルテキストは"Here's that song"になります。</p>
<p>あるいは、&lt;audio&gt;タグからテキストを削除するだけでも、SSMLの&lt;sub&gt;タグを使用してもかまいません。</p>
</td>
</tr>
<tr>
<td style="background-color: rgba(255,255,255,.95); padding: 5px;"><input type="checkbox"><b>空のチャットバブルをなくす</b>
<p>すべてのダイアログ（対話）のターンには、少なくとも1つのチャットバブルが必要です。アクションにストリーミングオーディオ（TTSなし）だけで構成されたダイアログがある場合、チャットバブルテキストが欠落してアプリが失敗します。このような場合は、録音されたオーディオの単語または紹介に一致する表示テキストを追加します。</p>
</td>
</tr>
</table>

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/assistant/responses)
