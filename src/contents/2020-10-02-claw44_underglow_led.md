---
layout: post
title: Claw44の光らせ方
categories:
- keyboard
---

Claw44の現在のリビジョンでは、未サポートながら、Underglow LEDを取り付けることができるように、基板に端子が準備されています。作者のご厚意により、透明のアクリル板を譲り受けることができまして、さっそくUnderglow LEDの取り付けを行ってみました。


![]({{ "/images/2020/10/claw44-underglow-led-1.webp" | prepend: site.baseurl }})


現在では、透明のアクリルプレートの販売が始まっています。興味ある方は購入されてみてはいかがでしょうか？

[Claw44 パーツ - yfuku - BOOTH](https://yfuku.booth.pm/items/2421158)

このエントリでは、Underglow LEDの取り付け方を紹介してみます。もちろん、未サポートのことになりますので、自己責任で行いましょう。

# LEDテープの入手

まずは何はともあれ、光源となるLEDを入手します。最も手軽なものとして、マイコン制御可能なフルカラーLEDが直列に接続されているLEDテープがあります。僕も今回はそれを使いました。

僕が知る限りでは、WS2812Bが対応しているLEDですので、それが接続されているLEDテープを購入しました。遊舎工房で売ってる6連のものです。

[フルカラーシリアルLEDテープ 6個タイプ - 遊舎工房](https://yushakobo.jp/shop/a01sl-00/)

これを2本購入します。左右に1本ずつ基板に実装します。

# LEDテープを実装する場所

基板上でLEDテープを実装する場所は、下の写真の赤く囲った部分にある3つの端子です。


![]({{ "/images/2020/10/claw44-underglow-led-2.webp" | prepend: site.baseurl }})


もう少し拡大したのが、以下です。


![]({{ "/images/2020/10/claw44-underglow-led-3.webp" | prepend: site.baseurl }})


GND、Din、VCCと書かれているのがわかると思います。ここにLEDテープを実装します。

# LEDテープのハンダ付け

では、LEDテープをハンダ付けします。この際に、守るべきことが2つあります。

1つ目は、LEDテープには方向があるので、正しい方向で取り付けること。

もう一つは、基板の端子の種類と、LEDテープの端子の種類を、ちゃんと合わせてハンダ付けを行うこと。

LEDテープをよくみてみると、いくつか気がつくことがあるはずです。


![]({{ "/images/2020/10/claw44-underglow-led-4.webp" | prepend: site.baseurl }})


下向きの矢印がいくつも書かれています。この矢印の方向に、電流が流れていきます。つまり、基板に取り付けるのは、矢印の起点となる側です。これを逆さまに付けてしまうと、LEDは光りません。

そして、基板と同じように、3つの端子があって、それぞれに名称が書かれています。基板とLEDテープでそれぞれの端子は以下のように対応します。

* 基板: VCC = LED: +5V
* 基板: Din = LED: Din
* 基板: GND = LED: GND

上記のように対応することに注意して、ハンダ付けを行います。


![]({{ "/images/2020/10/claw44-underglow-led-5.webp" | prepend: site.baseurl }})


LEDテープがちょうど収まるように、基板上に何もないスペースがありますので、それに合わせてハンダ付けを行います。残念ながら基板上の端子とLEDテープの端子がきれいに揃わずに、若干斜めになる感じになると思います。接続が不十分にならないように、ちゃんとハンダで橋を作るようにします。


![]({{ "/images/2020/10/claw44-underglow-led-6.webp" | prepend: site.baseurl }})


この作業を両方の基板に対して行います。


![]({{ "/images/2020/10/claw44-underglow-led-7.webp" | prepend: site.baseurl }})


LEDテープは、アクリル板で挟まれることになると思いますので、実際には何もしなくても固定されるでしょう。僕はちょっと不安定になって別の端子に触れてしまうのは嫌だったので、マスキングテープで固定して、アクリル板を被せました。

以上でハードウェア側の作業は完了です。

# ファームウェアの更新

Underglow LEDを光らせるためには、QMK Firmwareの更新が必要となります。具体的には、RGB Light機能を有効にして、必要な設定を施します。

まず、 `rules.mk` ファイルにて、RGB Light 機能を有効にするために、以下を追記します。


```
RGBLIGHT_ENABLE = yes
```

次に、 `config.h` に対して、以下を追記します。これにより、QMK Firmware 側にLEDテープの実装状況を伝えます。


```
/* WS2812B RGB LED */
#define RGB_DI_PIN D3
#define RGBLIGHT_ANIMATIONS
#define RGBLED_NUM 12
#define RGBLIGHT_SPLIT
#define RGBLED_SPLIT { 6, 6 }    // Number of LEDs

// The LEDs on the slave half go in reverse order
#define RGBLIGHT_LED_MAP { 0,  1,  2,  3,  4,  5, \
                          11, 10,  9,  8,  7,  6 }
```

最後に、 `keymap.c` ファイルにて、Underglow LEDを制御するために必要となるキーの割り当てを行います。最低限として、以下はどこかのレイヤのキーに割り当てておくと良いでしょう。

* RGB_TOG - Underglow LEDのON/OFF切り替え
* RGB_MOD - Underglow LEDの点灯パターンの切り替え

コードの追記が終わったら、ProMicroにファームウェアを書き込みます。この際、左右両方のProMicroに書き込んでおくと良いでしょう。

# いざ、点灯！

準備は全て整いました。 `RGB_TOGG` キーコードを割り当てたキーを押してみてください。Underglow LEDが点灯すれば、成功です。


![]({{ "/images/2020/10/claw44-underglow-led-8.webp" | prepend: site.baseurl }})


更に、 `RGB_MOD` キーコードを割り当てたキーを押してみてください。押すごとに、LEDのアニメーションが変わっていくと思います。

見ているだけで、楽しい気分になりますし、これぞ自作キーボードだ！って感じることができるかと思います。

# まとめ

今回は、未サポートながら実装が可能なUnderglow LEDをClaw44に取り付ける手順を説明してみました。

特に透明のアクリルプレートであれば、他の光るキーボードたちに引けを取らないClaw44になると思います。実際にやってみて、大満足です。毎日昼間っからずっとLEDを光らせていますし、夜になればわざと部屋の証明を落として、光るClaw44を見て、ニヤニヤしてます。やばいですね。

もしClaw44をお持ちであれば、チャレンジして見る価値アリです。また、自作キーボードをなにか組み立ててみようとお考えの方には、「Claw44は光らないからなぁ」という諦めは間違っていますので、お気をつけくださいませ。

ご参考になれば幸いです。

