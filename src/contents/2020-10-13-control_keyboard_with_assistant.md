---
layout: post
title: 手元のキーボードを声で操作するという未来を実現してみました
categories:
- actions on google
---

最近は自作キーボードのキットを2つほど作って、今まで使ってきた80個以上キーがあるキーボードは収納にしまってしまって、40個ほどしかないClaw44というキーボードを普段使っています。「いくらなんでも、いきなり40％キーボードを買っても使いこなせないだろう」と思っていたので、最初に組み立てたものは、ErgoDashという70個のキーが配置されたキーボードでした。


![]({{ "/images/2020/10/control-keyboard-with-assistant-1.webp" | prepend: site.baseurl }})


このキーボードに搭載されているマイコンには、オープンソースで開発が進められている「QMK Firmware」というファームウェアが書き込まれています。もちろん、自分でも書き込むことができますので、独自のキー配列、独自のキーバインドを定義して利用することはもちろん、LEDの制御などもコードを書きさえすれば自由自在です。

更に、 [Raw HID](https://docs.qmk.fm/#/feature_rawhid) というAPIがあって、キーボードからPCではなく、PCからキーボード側に情報を送信して何かをさせる、といったことも可能なのです。

これ、すごくないですか？いろいろ利用できそうじゃないですか。

というわけで、Googleアシスタントに声でお願いすると、キーボードがそれに反応して何かをしてくれる、というものを作ってみました。以下がその動画です。

<iframe width="560" height="315" src="https://www.youtube.com/embed/fBMJDVm8hZE" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

# 仕組み

処理の流れとしては、以下のようになってます。


![]({{ "/images/2020/10/control-keyboard-with-assistant-2.webp" | prepend: site.baseurl }})


6月に公開された新しいActions Builderという機能を使って、会話型のアクションを作りました。


![]({{ "/images/2020/10/control-keyboard-with-assistant-3.webp" | prepend: site.baseurl }})


最初にアクションが呼び出されたときは、まず挨拶を返します。そして、何をするかをユーザに問い合わせる `UseFeature` シーンに移行します。


![]({{ "/images/2020/10/control-keyboard-with-assistant-4.webp" | prepend: site.baseurl }})


UseFeature シーンでは、いくつかのインテントを割り当ててあります。具体的には、以下のインテントです。

* LED_TURN_OFF - 「LEDを消して」
* LED_TURN_ON - 「LEDをつけて」
* LED_XMAS - 「クリスマスです」

各インテントでは、Webhookでフルフィルメントコードを呼び出すようにしています。


![]({{ "/images/2020/10/control-keyboard-with-assistant-5.webp" | prepend: site.baseurl }})


手元のPCのコードを実行しないといけないので（キーボードがネットにつながる時代はまだなので）、今回はデモとして、ngrok を使って、Actions on Googleから直接手元のPCにWebhookリクエストを送信しています。

Webhookで呼び出されるコードは、NodeJSのexpressを使ってHTTPリクエストを受け付けるようにしています。そして、 [Actions SDK Node.js Fulfillment Library](https://github.com/actions-on-google/assistant-conversation-nodejs) を使ってリスエストの解釈とレスポンス処理を行っています。

以下は、package.json ファイルです。


```
{
  "name": "my-keyboard",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "dependencies": {
    "@assistant/conversation": "^3.1.0",
    "express": "^4.17.1"
  }
}
```

キーボードなどの入力デバイスは、Linuxの場合、 `/etc/hidraw3` といったパスでマッピングされます。これに情報を送信することで、キーボードにPCから何かをお願いすることができるわけです。以下の index.js ファイルの中では、各ハンドラメソッド内で、"rc" といった文字列をキーボードに送信していることがわかります。


```
const express = require('express');
const bodyParser = require('body-parser');
const { conversation } = require('@assistant/conversation');
const execSync = require('child_process').execSync;

const app = conversation();

app.handle('led_turn_on', conv => {
  const result =  execSync('echo -n "rt" > /dev/hidraw3');
  console.log(result);
  conv.add('LEDを点灯しました。');
});

app.handle('led_turn_off', conv => {
  const result =  execSync('echo -n "rf" > /dev/hidraw3');
  console.log(result);
  conv.add('LEDを消しました。');
});

app.handle('led_xmas', conv => {
  const result =  execSync('echo -n "rc" > /dev/hidraw3');
  console.log(result);
  conv.add('メリークリスマス！って、ちょっと早すぎませんか？');
});

const expressApp = express().use(bodyParser.json());

expressApp.post('/fulfillment', app);

expressApp.listen(3000);
```

QMK Firmware側のコードですが、使っているキーボードのキーマップを定義している `keymap.c` というC言語で書かれたコードがあるはずです。これに、Raw HIDで送られてきた際のハンドラ関数を追加して、情報を受けとります。

まず、 `rules.mk` ファイルにて、Raw HID 機能を有効にします。


```
RAW_ENABLE = yes
```

次に、keymap.c ファイルにハンドラ関数を追加します。


```
void raw_hid_receive(uint8_t *data, uint8_t length) {
  uint8_t operation = data[0];
  uint8_t option = data[1];
  if (operation == 'r') {
    if (option == 't') {
      rgblight_enable();
    } else if (option == 'f') {
      rgblight_disable();
    } else if (option == 'c') {
      rgblight_mode(RGBLIGHT_MODE_CHRISTMAS);
    }
  }
}
```

送られてきた情報が "r" で始まる場合に、続いて送られてきた文字に応じて、LEDのON、OFFを制御しています。また、"rc" だった場合は、LEDの点灯モードをクリスマス調に変更しています。

この追記をした後に、ビルドして、マイコンに書き込みます。すると、Googleアシスタントに話しかけることで、手元のキーボードが反応する、という動作が行われます。

# まとめ

今回はキーボードのLEDを制御するデモを作ってみました。これ、もっといろいろできるはずです。

例えば、Googleアシスタントに話しかけたテキスト（英語のアルファベットの並び）をファームウェアから `SEND_STRING()` してあげることで、キーを叩かなくても、キーを叩いたことにできます。つまり、声で入力をすることが可能（PCからしたらキーボードを叩かれたことと区別がつかない）になります。

他には、キー数の少ないキーボードですと、例えば Fn キーに代表されるレイヤーを切り替えながら入力を行っていくことになるのですが、これが基本的には「複数のキーを同時に押す」必要があるので、キーバインドによっては指が結構つらいことになります。でも、声で「テンキーモードにして」ということでキーボードのレイヤーが自動的に切り替わったら、便利そうですよね。

今回は会話型のアクションとして作りましたので、本当に広く使われるアクションにするためには、ホームアクションにするとか、認証認可が必要とか、いろいろ考えなければならないことがあります。が、ちょっとしたコードを書くだけで、なにか未来が手元に来たようなことができた感じがして、個人的にはワクワクでした。

もしキーボードをより便利にしてみたい！と思った方は、ぜひチャレンジしてみてください。




