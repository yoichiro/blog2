---
layout: post
title: Lunakey Mini ビルドガイド (Rev.2)
categories:
- keyboard
---

Lunakey Miniは、左右それぞれ3行 x 6列のキーに親指向けのキーが4個配列された、合計44個のキーを備えた左右分割型の40%キーボードです。


![]({{ "/images/2020/11/lunakey-mini-build-guide-1.webp" | prepend: site.baseurl }})


Lunakey Miniは、以下の特徴を備えています。

* 左右それぞれ3行 x 6列の40%キーボード。
* 指の長さにそれぞれフィットされたColumn Staggerd配列。
* 親指の自然な可動範囲に合わせた親指向けキー配列。
* Underglow LEDによる電飾効果。
* 圧電スピーカーによる音でのフィードバック効果。
* OLED（液晶パネル）を左右それぞれ搭載可能（オプション）。

この文章は、Lunakey Miniを組み立てるためのビルドガイドです。実際に組み立てを始める前に、ビルドガイドを最後までお読みいただき、組み立ての手順を事前に把握することをお勧めいたします。

※ このビルドガイドは Rev.2 向けです。Rev.4 をお持ちの方は、 [Lunakey Mini Build Guide Rev.4](https://www.eisbahn.jp/yoichiro/2020/12/lunakey_mini_build_guide_rev4.html) を参照ください。

# 同梱品の確認

Lunakey Miniには、以下の部品群が含まれています。全て含まれているかどうか確認してください。

* 基板: 2枚
* トッププレート: 2枚
* ボトムプレート: 2枚
* カバープレート: 2枚
* ProMicro: 2個
* コンスルー: 4個
* TRRSジャック: 2個
* タクトスイッチ: 2個
* ダイオード: 44個
* LED (WS2812B): 12個
* キーソケット: 44個
* 圧電スピーカー: 1個
* 抵抗 1kΩ: 1個
* M2スペーサー 7.5mm: 10個
* M2スペーサー 10mm: 4個
* M2ネジ 5mm: 24個
* M2ネジ 3mm: 2個
* ゴム足: 12個

いくつかの部品には、予備として数個余分に含まれています。


![]({{ "/images/2020/11/lunakey-mini-build-guide-2.webp" | prepend: site.baseurl }})


基板は、リバーシブルではなく、左右がそれぞれ決まっています。以下の写真は、実際に使う際の基板の配置、つまり表側となります。ハンダ付けを行うためのランドが少ない側が表側、と覚えればよいでしょう。


![]({{ "/images/2020/11/lunakey-mini-build-guide-3.webp" | prepend: site.baseurl }})


一方、以下の写真は、基板の裏側となります。ハンダ付けを行うための銀色のランドが多く、またキーソケットを配置するための白色の部分が数多く描かれている面が裏側、と覚えてください。


![]({{ "/images/2020/11/lunakey-mini-build-guide-4.webp" | prepend: site.baseurl }})

# 別途購入する必要があるもの

Lunakey Miniを完成させるためには、以下の部品を別途購入してください。

* TRSケーブル、もしくはTRRSケーブル: 1本
* USB Micro Bケーブル: 1本
* キースイッチ（Cherry MX互換）: 44個
* キーキャップ（Cherry MX互換、1U）: 44個
* OLEDモジュール（ピンソケット付き）: 2個（オプション）

参考までに、上記の部品は、以下より購入できます。

* [Kochi Keyboard](https://kochikeyboard.stores.jp/)
* [遊舎工房](https://yushakobo.jp/)
* [TALP KEYBOARD](https://talpkeyboard.stores.jp/)
* [ゆかりキーボードファクトリー](https://eucalyn.shop/)

組み立てにはハンダ付けが必要になりますので、ハンダごてやハンダなども準備をおこなってください。また、部品が正しく取り付けられているかどうかを確認するためのテスターがあると安心です。

同梱されているPro Microには、ファームウェアが書き込まれていません。そのため、ファームウェアを書き込むためのPCが必要ですので、ご準備ください。

同梱しているダイオードは、表面実装タイプとなります。もし組み立てに自信がない場合は、別途リード線を持つダイオードを購入して取り付けることも可能です。

# ダイオードの取り付け

基板の「裏側」に、ダイオードを取り付けます。キーの数だけ取り付けることになりますので、片側で22個、両方で44個取り付けます、同梱しているダイオードは、表面実装を行うタイプのダイオードですが、別途リード線を持つダイオードを購入して取り付けても大丈夫です。

以下の写真の赤く囲った部分に、ダイオードを取り付けていきます。その際、左右で1つずつ「抵抗（1kΩ）」を取り付ける場所がありますので、その場所に間違ってダイオードを取り付けないように気をつけましょう。


![]({{ "/images/2020/11/lunakey-mini-build-guide-5.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-6.webp" | prepend: site.baseurl }})


ダイオードには極性がありますので、取り付ける際には方向に気をつけてください。ダイオードには縦線のマーク（｜）が書かれています。この縦線を、ダイオードのスルーホールの四角い方に合わせて取り付けてください。


![]({{ "/images/2020/11/lunakey-mini-build-guide-7.webp" | prepend: site.baseurl }})


ハンダ付けのコツとして、まず基板の片方のランドに予備ハンダを乗せて、ピンセットなどでダイオードを持ち、予備ハンダを溶かしつつダイオードの足を滑り込ませる、という手順で行うと、スムーズに取り付けることができます。


![]({{ "/images/2020/11/lunakey-mini-build-guide-8.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-9.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-10.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-11.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-12.webp" | prepend: site.baseurl }})


# Pro Microの取り付け

Pro MicroのUSBコネクタが若干弱く、USBケーブルの抜き差しの仕方によっては、USBコネクタが基板から外れてしまうことがあります。これを防ぐために、[エポキシ接着剤などでUSBコネクタを補強する](https://www.eisbahn.jp/yoichiro/2020/09/claw44_4.html) などすると良いでしょう。


![]({{ "/images/2020/11/lunakey-mini-build-guide-13.webp" | prepend: site.baseurl }})


Pro Microの取り付けには、故障時に交換しやすいように、コンスルーと呼ばれるピンを使って基板に取り付けを行います。Pro Microにコンスルーを取り付ける方法は、 [Helixのビルドガイド](https://github.com/MakotoKurauchi/helix/blob/master/Doc/buildguide_jp.md#pro-micro) を参考にして取り付けてください。ポイントは以下の2点となります。

* コンスルーの側面には、ピンごとに穴があります。その穴に近い方のピンを、Pro Microに挿してください。
* 一つのPro Microに2つのコンスルーを取り付けますが、その際に穴の向きをどちらも同じ方向にして取り付けてください。


![]({{ "/images/2020/11/lunakey-mini-build-guide-14.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-15.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-16.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-17.webp" | prepend: site.baseurl }})


コンスルーを取り付けた後に、基板の表側（先ほどダイオードを取り付けた面と反対側）からPro Microを基板に取り付けます。コンスルーと基板の間に隙間がなくなるまで押し込みます。基板とコンスルーをハンダ付けする必要はありません。

# リセットスイッチの取り付け

基板の表側（先ほどPro Microを取り付けた面と同じ側）からタクトスイッチをリセットスイッチとして基板に差し込みます（方向はありません）。そして、基板の裏側から出ているタクトスイッチの足をハンダ付けします。1個のタクトスイッチにつき、2つの足に対してハンダ付けを行うことになります。


![]({{ "/images/2020/11/lunakey-mini-build-guide-18.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-19.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-20.webp" | prepend: site.baseurl }})


リセットスイッチを基板に挿しただけでおそらく固定されるため、特にマスキングテープで固定などしなくてもハンダ付けはできるかと思います。もし不安定になる場合には、マスキングテープなどで固定した後にハンダ付けを行って、なるべく基板とタクトスイッチが離れないように取り付けてください。


![]({{ "/images/2020/11/lunakey-mini-build-guide-21.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-22.webp" | prepend: site.baseurl }})


# ファームウェアの書き込みと動作確認

この段階で、ダイオード、リセットスイッチ、そしてPro Microが正しく取り付けられているかどうかを確認しておくと良いでしょう。Pro Microとリセットスイッチを取り付けた状態にて、USBケーブルでPCに接続し、ファームウェアを書き込むことで、正しくキー入力が行われるかどうかを確認することができます。他の部品を接続する前にここで動作確認を行うことで、なにか問題があった際に問題の切り分けが容易になります。

Lunakey Miniは、オープンソースプロジェクトである [QMK Firmware](https://github.com/qmk/qmk_firmware) をファームウェアとして採用しています。Lunakey Miniを利用するためには、QMK FirmwareおよびLunakey Mini向けのコードをPro Microに書き込む必要があります。

ファームウェアの書き込みを行うための方法は、いくつか提供されています。その中で、QMK Toolboxを使う方法が最も手軽でしょう。QMK Toolboxは、以下の場所から入手することが可能です。

[QMK Toolbox Releases](https://github.com/qmk/qmk_toolbox/releases/)

各種OS向けに提供されていますので、適切なインストーラをダウンロードして、インストールを行ってください。

Pro MicroとPCを、USB Micro Bケーブルを使って接続します。そして、QMK Toolboxを起動します。

[VIA](https://caniusevia.com/) というキーマップを書き換えることが簡単になる便利ツールがありますので、キーマップの変更や LED をどう光らせるかを変更する程度のカスタマイズであれば、VIA を利用するのが良いでしょう。VIA については、以下の記事に詳しい紹介が書かれていますので、ご覧になってみてください。

[（初心者編）VIAを使ってキーマップを書き換えよう - 自作キーボード温泉街の歩き方](https://salicylic-acid3.hatenablog.com/entry/via-manual)

Lunakey Miniのデフォルトキーマップを書き込んだビルド済みファイルは、以下からダウンロードすることができます。

**VIA 対応ファームウェア**

* <a href="{{ '/images/2020/11/yoichiro_lunakey_mini_via.hex' | prepend: site.baseurl }}" download>Underglow LED を有効にしたファームウェア</a>
* <a href="{{ '/images/2020/11/yoichiro_lunakey_mini_oled_via.hex' | prepend: site.baseurl }}" download>Underglow LED, OLED を有効にしたファームウェア</a>
* <a href="{{ '/images/2020/11/yoichiro_lunakey_mini_audio_via.hex' | prepend: site.baseurl }}" download>Underglow LED, Speaker を有効にしたファームウェア</a>

**VIA 非対応ファームウェア**

* <a href="{{ '/images/2020/11/yoichiro_lunakey_mini_default.hex' | prepend: site.baseurl }}" download>デフォルトファームウェア</a>
* <a href="{{ '/images/2020/11/yoichiro_lunakey_mini_default_led.hex' | prepend: site.baseurl }}" download>Underglow LED を有効にしたファームウェア</a>
* <a href="{{ '/images/2020/11/yoichiro_lunakey_mini_default_speaker.hex' | prepend: site.baseurl }}" download>Speaker を有効にしたファームウェア</a>
* <a href="{{ '/images/2020/11/yoichiro_lunakey_mini_default_led_speaker.hex' | prepend: site.baseurl }}" download>Underglow LED, Speaker を有効にしたファームウェア</a>
* <a href="{{ '/images/2020/11/yoichiro_lunakey_mini_default_oled.hex' | prepend: site.baseurl }}" download>OLED を有効にしたファームウェア</a>
* <a href="{{ '/images/2020/11/yoichiro_lunakey_mini_default_oled_led.hex' | prepend: site.baseurl }}" download>Underglow LED, OLED を有効にしたファームウェア</a>
* <a href="{{ '/images/2020/11/yoichiro_lunakey_mini_default_oled_speaker.hex' | prepend: site.baseurl }}" download>Speaker, OLED を有効にしたファームウェア</a>
* <a href="{{ '/images/2020/11/yoichiro_lunakey_mini_default_oled_led_speaker.hex' | prepend: site.baseurl }}" download>Underglow LED, Speaker, OLED を有効にしたファームウェア</a>

注意点として、取り付けていない部品を有効にしたファームウェアを書き込んだ状態で USB Micro B ケーブルを挿して通電してしまうと、最悪の場合 Pro Micro が故障する可能性があります。ここでの動作確認では、上記の「デフォルトファームウェア」を使ってください。他のファームウェアは、実際に基板に部品が取り付けられた後に書き込むようにしてください。

QMK ToolboxのOpenボタンを押下して、ダウンロードした hex ファイルを選択します。そして、Auto-Flash チェックボックスにチェックを入れておきます。

基板にPro Microを表側から取り付け、リセットスイッチを1回押します。もし書き込みが始まらなかった場合は、リセットスイッチを2回連続で押してみてください。


![]({{ "/images/2020/11/lunakey-mini-build-guide-23.webp" | prepend: site.baseurl }})


QMK ToolboxはPro Microにファームウェアの書き込みを開始します。QMK Toolboxに出力されるメッセージの最後に Thank you と表示されれば、書き込みは完了です。

キー入力を確認するために、テキストエディタなどの文字入力が可能なアプリケーションや、入力を確認することができるウェブアプリ [QMK Confugulator](https://config.qmk.fm/#/test) などを開いておきます。そして、キーソケットのランドをピンセットなどでショートさせてください。これを、左右の基板およびもう一つのPro Microで行ってください。


![]({{ "/images/2020/11/lunakey-mini-build-guide-24.webp" | prepend: site.baseurl }})


それぞれのソケットでキー入力がされれば成功です。ただし、以下の2点に注意してください。

* 親指で押すことになるキーで左右一つずつ文字入力がされないキーがあります。これらは、レイヤの操作に割り当てられているため、PC側にキーコードが送られない状態が正常となります。
* 左右の基板とも、左手に割り当てられているキーコードが送信されます。右手側の基板であっても、左手側のキー割り当てのキーコードが送られる状態が正常となります。

上記のファームウェアにて適用されるキーマップは以下となりますので、動作確認の際の参考になさってください。`KC_***` については、 [Keycodes Overview](https://docs.qmk.fm/#/keycodes) をご参照ください。

```
[デフォルトレイアウト]
//+--------+--------+--------+--------+--------+--------+                 +--------+--------+--------+--------+--------+--------+
   KC_TAB,  KC_Q,    KC_W,    KC_E,    KC_R,    KC_T,                      KC_Y,    KC_U,    KC_I,    KC_O,    KC_P,    KC_BSPC,
//+--------+--------+--------+--------+--------+--------+                 +--------+--------+--------+--------+--------+--------+
   KC_LCTL, KC_A,    KC_S,    KC_D,    KC_F,    KC_G,                      KC_H,    KC_J,    KC_K,    KC_L,    KC_SCLN, KC_QUOT,
//+--------+--------+--------+--------+--------+--------+                 +--------+--------+--------+--------+--------+--------+
   KC_LSFT, KC_Z,    KC_X,    KC_C,    KC_V,    KC_B,                      KC_N,    KC_M,    KC_COMM, KC_DOT,  KC_SLSH, KC_RSFT,
//+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+
                              KC_LGUI, KC_LALT, LOWER,   KC_SPC,  KC_ENT,  RAISE,   KC_RALT, KC_RGUI
//+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+
  ),

[LOWERキーと同時押し]
//+--------+--------+--------+--------+--------+--------+                 +--------+--------+--------+--------+--------+--------+
   KC_ESC,  KC_1,    KC_2,    KC_3,    KC_4,    KC_5,                      KC_6,    KC_7,    KC_8,    KC_9,    KC_0,    KC_BSPC,
//+--------+--------+--------+--------+--------+--------+                 +--------+--------+--------+--------+--------+--------+
   KC_LCTL, _______, _______, _______, _______, _______,                   KC_LEFT, KC_DOWN, KC_UP,   KC_RIGHT,_______, _______,
//+--------+--------+--------+--------+--------+--------+                 +--------+--------+--------+--------+--------+--------+
   KC_LSFT, _______, _______, _______, _______, _______,                   _______, _______, _______, _______, _______, _______,
//+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+
                              KC_LGUI, KC_LALT, LOWER,   KC_SPC,  KC_ENT,  RAISE,   KC_RALT, KC_RGUI
//+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+
  ),

[RAISEキーと同時押し]
//+--------+--------+--------+--------+--------+--------+                 +--------+--------+--------+--------+--------+--------+
   KC_ESC,  KC_EXLM, KC_AT,   KC_HASH, KC_DLR,  KC_PERC,                   KC_CIRC, KC_AMPR, KC_ASTR, KC_LPRN, KC_RPRN, KC_BSPC,
//+--------+--------+--------+--------+--------+--------+                 +--------+--------+--------+--------+--------+--------+
   KC_LCTL, _______, _______, _______, _______, _______,                   KC_MINS, KC_EQL,  KC_LCBR, KC_RCBR, KC_PIPE, KC_GRV,
//+--------+--------+--------+--------+--------+--------+                 +--------+--------+--------+--------+--------+--------+
   KC_LSFT, KC_TILD, _______, _______, _______, _______,                   KC_UNDS, KC_PLUS, KC_LBRC, KC_RBRC, KC_BSLS, KC_TILD,
//+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+
                              KC_LGUI, KC_LALT, LOWER,   KC_SPC,  KC_ENT,  RAISE,   KC_RALT, KC_RGUI
//+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+
  ),

[LOWERキーおよびRAISEキーと同時押し]
//+--------+--------+--------+--------+--------+--------+                 +--------+--------+--------+--------+--------+--------+
   RESET,   _______, _______, _______, _______, _______,                   KC_F1,   KC_F2,   KC_F3,   KC_F4,   KC_F5,   KC_F6,
//+--------+--------+--------+--------+--------+--------+                 +--------+--------+--------+--------+--------+--------+
   RGB_TOG, RGB_HUI, RGB_SAI, RGB_VAI, _______, _______,                   KC_F7,   KC_F8,   KC_F9,   KC_F10,  KC_F11,  KC_F12,
//+--------+--------+--------+--------+--------+--------+                 +--------+--------+--------+--------+--------+--------+
   RGB_MOD, RGB_HUD, RGB_SAD, RGB_VAD, _______, _______,                   _______, _______, _______, _______, _______, _______,
//+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+
                              KC_LGUI, KC_LALT, LOWER,   KC_SPC,  KC_ENT,  RAISE,   KC_RALT, KC_RGUI
//+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+
```

# TRRSジャックの取り付け

基板の表側（先ほどリセットスイッチを取り付けた面と同じ側）からTRRSジャックを基板に差し込みます。そして、基板の裏側に出ているTRRSジャックの足をハンダ付けします。1個のTRRSジャックにつき、3つの足に対してハンダ付けを行うことになります。


![]({{ "/images/2020/11/lunakey-mini-build-guide-25.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-26.webp" | prepend: site.baseurl }})


ハンダ付けのコツとして、TRRSジャックを基板に挿しただけでは固定されないため、基板とTRRSジャックを密着させた状態でハンダ付けを行うために、マスキングテープで固定した後にハンダ付けを行うと良いでしょう。


![]({{ "/images/2020/11/lunakey-mini-build-guide-27.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-28.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-29.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-30.webp" | prepend: site.baseurl }})


# 圧電スピーカーの取り付け

音を鳴らすための圧電スピーカーを取り付けます。圧電スピーカーは2つの部品が必要となります

* 圧電スピーカー
* 1kΩ 抵抗


![]({{ "/images/2020/11/lunakey-mini-build-guide-31.webp" | prepend: site.baseurl }})


圧電スピーカーと抵抗は、キーボードとして左手の側に来る基板（正確に言うと、USBケーブルをPro Microに差し込むマスターとなる側の基板）のみに取り付けます。


![]({{ "/images/2020/11/lunakey-mini-build-guide-32.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-33.webp" | prepend: site.baseurl }})


まずは1kΩの抵抗を取り付けます。予め抵抗の足を折っておき、基板の裏側（先ほどTRRSジャックを取り付けた面と反対側）からスルーホールに差し込みます（方向はありません）。そして、抵抗の足を基板にハンダ付けしてください。


![]({{ "/images/2020/11/lunakey-mini-build-guide-34.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-35.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-36.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-37.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-38.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-39.webp" | prepend: site.baseurl }})


次に、圧電スピーカーを基板に取り付けます。基板の裏側（先ほど抵抗を挿した面と同じ側）の圧電スピーカーを取り付けるためのランド2つに、予備ハンダを乗せます。そして、圧電スピーカーを予備ハンダの上に乗せた状態（方向はありません）で、マスキングテープで固定します。最後に、左右からハンダ付けを行って取り付けを行います。


![]({{ "/images/2020/11/lunakey-mini-build-guide-40.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-41.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-42.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-43.webp" | prepend: site.baseurl }})


予備ハンダをせずにハンダ付けを行ってしまうと、基板のランドと圧電スピーカーの端子が接触していない状況になりますので、予備ハンダは必ず行ってください。

# Underglow LEDの取り付け

キーボードの背面に視覚効果を与えるためのUnderglow LEDを基板に取り付けます。LEDは、WS2812Bという型番の表面実装にて取り付けるタイプのLEDとなります。


![]({{ "/images/2020/11/lunakey-mini-build-guide-44.webp" | prepend: site.baseurl }})


左右それぞれ6個ずつ取り付けます。


![]({{ "/images/2020/11/lunakey-mini-build-guide-45.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-46.webp" | prepend: site.baseurl }})


Underglow LEDは、基板の裏側（先ほど圧電スピーカーを取り付けた面と同じ側）に取り付けます。取り付け場所は、正方形のマークが書かれている場所です。WS2812Bは方向があり、WS2812Bの四角の一つが三角形に欠けていて、その角を基板に書かれている三角形に合わせて取り付けます。


![]({{ "/images/2020/11/lunakey-mini-build-guide-47.webp" | prepend: site.baseurl }})


WS2812Bのハンダ付けは、熱に非常に弱いため、注意が必要です。ハンダ付けのコツは、一つのWS2812Bを一気に4カ所ハンダ付けをしないことです。具体的には、以下の手順で行うと良いでしょう。

1. 片方の基板にWS2812B 6個を一つずつマスキングテープで固定する。
1. WS2812Bの4個あるうちの一つの端子のみをハンダ付けしていく。WS2812Bの6個それぞれで1カ所のみハンダ付けされている状態になる。
1. 前の手順を3回繰り返す。


![]({{ "/images/2020/11/lunakey-mini-build-guide-48.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-49.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-50.webp" | prepend: site.baseurl }})


上記の手順により、WS2812Bの温度上昇をできるだけ抑えた状態でハンダ付けを行うことができます。


![]({{ "/images/2020/11/lunakey-mini-build-guide-51.webp" | prepend: site.baseurl }})


# OLEDモジュールの取り付け

もしOLEDモジュールを別途購入済みであれば、この時点でOLEDモジュールの取り付けを行います。もちろん、現時点でお持ちではない場合でも、後からOLEDモジュールを取り付けることは可能ですが、その際にはカバープレートおよびボトムプレートを取り外すことが必要です。

最初に、OLEDモジュールを取り付けるためのソケットを基板に取り付けます。基板の表側（先ほどUnderglow LEDを取り付けた面と反対側）からOLEDソケットを基板に差し込みます（方向はありません）。そして、基板の裏側に出ているOLEDソケットの足をハンダ付けします。1個のOLEDソケットにつき、4つの足に対してハンダ付けを行うことになります。


![]({{ "/images/2020/11/lunakey-mini-build-guide-52.webp" | prepend: site.baseurl }})


ハンダ付けのコツとして、OLEDソケットを基板に挿しただけでは固定されないため、基板とOLEDソケットを密着させた状態でハンダ付けを行うために、マスキングテープで固定した後にハンダ付けを行うと良いでしょう。


![]({{ "/images/2020/11/lunakey-mini-build-guide-53.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-54.webp" | prepend: site.baseurl }})


次に、OLEDモジュールを取り付けます。OLEDモジュールにピンをハンダ付けします。この際、OLEDモジュールとピンを固定した状態でハンダ付けを行うために、マスキングテープなどを利用してOLEDモジュールとピンを密着させると良いでしょう。


![]({{ "/images/2020/11/lunakey-mini-build-guide-55.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-56.webp" | prepend: site.baseurl }})


ピンのハンダ付けが終わったら、予め基板に取り付けてあるソケットにピンを刺します。


![]({{ "/images/2020/11/lunakey-mini-build-guide-57.webp" | prepend: site.baseurl }})


Pro Microの上部に覆い被さるようにOLEDモジュールを配置しますが、Pro MicroとOLEDモジュールが接触している状態ですと、細かな振動により音が発生する可能性があります。OLEDモジュールをPro Microから浮かした状態を保つか、OLEDとPro Microの間にビニールテープなどの絶縁体を挟むなどすると良いでしょう。

# キーソケットの取り付け

キースイッチを交換可能にするためのキーソケットを取り付けます。


![]({{ "/images/2020/11/lunakey-mini-build-guide-58.webp" | prepend: site.baseurl }})


キーソケットは、キーの個数分（片側22個、両方で44個）取り付けます、基板の裏側（先ほどWS2812Bを取り付けた面と同じ側）に、キーソケットを取り付けるためのマークがありますので、それに合わせて取り付けを行っていきます。


![]({{ "/images/2020/11/lunakey-mini-build-guide-59.webp" | prepend: site.baseurl }})


キーソケットは基板にできるだけ密着した状態で固定することが望ましいので、キーソケットを細く切ったマスキングテープで基板に固定してからハンダ付けをすると良いでしょう。キーソケットの両側にある端子と、基板上にあるランドをハンダ付けしていきます。


![]({{ "/images/2020/11/lunakey-mini-build-guide-60.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-61.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-62.webp" | prepend: site.baseurl }})


先の細いハンダごて先をお持ちであれば、端子の間に差し込んでハンダを流し込むと固定させることができます。もし太いハンダごて先しかない場合は、基板上のランドに予備ハンダを盛ってからキーソケットをハンダ付けすると良いでしょう。


![]({{ "/images/2020/11/lunakey-mini-build-guide-63.webp" | prepend: site.baseurl }})


# カバープレートの取り付け

Pro Micro（およびOLEDモジュール）が取り付けられている状態で、M2スペーサー（10mm）を片側2本ずつ、M2ネジ（3mm）を使って基板に取り付けます。


![]({{ "/images/2020/11/lunakey-mini-build-guide-64.webp" | prepend: site.baseurl }})


M2ネジを基板の裏側（先ほどキーソケットを取り付けた面と同じ側）から差し込んで、M2スペーサーをM2ネジに合わせてねじ込み、最後にドライバーを使って固定すると良いでしょう。


![]({{ "/images/2020/11/lunakey-mini-build-guide-65.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-66.webp" | prepend: site.baseurl }})


カバープレートには保護シートが接着されていますので、それを剥がします。剥がしにくい場合は、ぬるま湯につけると、糊が溶けて剥がしやすくなります。

M2スペーサーに、カバープレートをM2ネジ（5mm）を使って取り付けます。これにより、Pro Micro（およびOLEDモジュール）の上にアクリル板が覆い被さるように配置されれば大丈夫です。


![]({{ "/images/2020/11/lunakey-mini-build-guide-67.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-68.webp" | prepend: site.baseurl }})


# トッププレート、キースイッチの取り付け

トッププレートは、キースイッチを取り付けるための穴が22個空いているアクリル板です。キースイッチを固定するための板となります。


![]({{ "/images/2020/11/lunakey-mini-build-guide-69.webp" | prepend: site.baseurl }})


トッププレートには保護シートが接着されていますので、それを剥がします。剥がしにくい場合は、ぬるま湯につけると、糊が溶けて剥がしやすくなります。

トッププレートには、5つの小さな丸い穴が開いています。この穴に、M2スペーサー（7.5mm）をM2ネジ（5mm）を使って取り付けます。このM2スペーサーは、基板に予め開いている穴を通って、ボトムプレートに固定されます。


![]({{ "/images/2020/11/lunakey-mini-build-guide-70.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-71.webp" | prepend: site.baseurl }})


トッププレートの穴それぞれに、キースイッチを差し込みます。設計として、トッププレートにキースイッチがうまく固定されるようにしてありますので、トッププレートにキースイッチを差し込む際には、少し力が必要となるでしょう。そして、基板のキーソケットの穴に合わせて、キースイッチのピンを基板の表側（先ほどカバープレートを取り付けた面と同じ側）から差し込みます。

コツとしては、トッププレートの全ての穴にキースイッチを取り付けてしまうのではなく、トッププレートの外周のいくつかにキースイッチを取り付けた状態で基板に差し込むと、後からキースイッチを取り付けていく際にスムーズな作業となるでしょう。


![]({{ "/images/2020/11/lunakey-mini-build-guide-72.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/11/lunakey-mini-build-guide-73.webp" | prepend: site.baseurl }})


注意点としては、キースイッチから2本出ている端子がまっすぐに伸びている状態で基板に差し込んでいただくことと、親指で押すことになる4つのキーについては上下が逆となっているために、ピンの方向を基板にちゃんと合わせることを確認してから差し込んでください。


![]({{ "/images/2020/11/lunakey-mini-build-guide-74.webp" | prepend: site.baseurl }})


# ボトムプレートの取り付け

基板の裏側（先ほどトッププレートを取り付けた面と反対側）の5つの穴から、トッププレートに取り付けたM2スペーサー（7.5mm）が見えていると思います。このM2スペーサーに合わせて、ボトムプレートを取り付けます。


![]({{ "/images/2020/11/lunakey-mini-build-guide-75.webp" | prepend: site.baseurl }})


ボトムプレートには保護シートが接着されていますので、それを剥がします。剥がしにくい場合は、ぬるま湯につけると、糊が溶けて剥がしやすくなります。

ボトムプレートを基板の形に合わせてM2スペーサーの上に乗せます。そして、M2ネジ（5mm）を使って固定します。


![]({{ "/images/2020/11/lunakey-mini-build-guide-76.webp" | prepend: site.baseurl }})


# ゴム足の取り付け

ボトムプレートの6カ所にそれぞれゴム足を取り付けます。


![]({{ "/images/2020/11/lunakey-mini-build-guide-77.webp" | prepend: site.baseurl }})


# キーキャップの取り付け

最後に、キースイッチにキーキャップを取り付けて、Lunakey Miniの完成です。お疲れさまでした。


![]({{ "/images/2020/11/lunakey-mini-build-guide-78.webp" | prepend: site.baseurl }})

ちなみに、上の写真では、無刻印のDSAプロファイルのキーキャップを取り付けています。このキーキャップは、以下から購入することができます。

[キーキャップ DSAプロファイル - TALP KEYBOARD](https://talpkeyboard.stores.jp/?category_id=59e2acfaed05e644fd004008)

# 使用方法

組み立てが完了したLunakey Miniを使用する際には、以下を行ってください。

* TRRSケーブルもしくはTRSケーブルを、左右のTRRSジャックにそれぞれ差し込んでください。
* USB Micro B ケーブルを、左手側のPro Microに差し込み、PCと接続してください。

![]({{ "/images/2020/11/lunakey-mini-build-guide-79.webp" | prepend: site.baseurl }})

![]({{ "/images/2020/11/lunakey-mini-build-guide-80.webp" | prepend: site.baseurl }})

![]({{ "/images/2020/11/lunakey-mini-build-guide-81.webp" | prepend: site.baseurl }})

これにより、Lunakey Miniをご利用できるようになります。組み立てお疲れさまでした。Lunakey Mini が素敵なPCライフにお役に立てることを期待しています！

# キーボードのカスタマイズ

VIA 対応のファームウェアを書き込んでいる場合は、キーマップの変更や Underglow LED の光らせ方の変更については、QMK Firmware のビルドを行うことなく VIA というアプリケーションを使って手軽に変更することが可能です。

以下よりお使いの OS に合わせて VIA のインストーラをダウンロードして、インストールを行ってください。

[Releases · the-via/releases](https://github.com/the-via/releases/releases)

Lunakey Mini を PC に接続して、VIA を起動してください。Lunakey Mini が VIA に認識され、キーマップが表示されます。

![]({{ "/images/2020/11/lunakey-mini-build-guide-84.webp" | prepend: site.baseurl }})

LAYER という箇所にて、レイヤを切り替えることができます。

![]({{ "/images/2020/11/lunakey-mini-build-guide-85.webp" | prepend: site.baseurl }})

VIA の画面の下には、設定可能なキーが表示されています。

![]({{ "/images/2020/11/lunakey-mini-build-guide-86.webp" | prepend: site.baseurl }})

LIGHTING という箇所をクリックすることで、Underglow LED をどう光らせるかを変更させることが可能です。

![]({{ "/images/2020/11/lunakey-mini-build-guide-87.webp" | prepend: site.baseurl }})

より詳しい VIA の操作方法は、以下をご覧になってください。

[（初心者編）VIAを使ってキーマップを書き換えよう - 自作キーボード温泉街の歩き方](https://salicylic-acid3.hatenablog.com/entry/via-manual)

もし、VIA にてサポートされていない複雑なキー割り当てをしたり、Underglow LEDや圧電スピーカー、OLED対応を行うためには、QMK Firmwareをビルドする必要があります。以下の手順で行ってください。

1. [Setting Up Your QMK Environment](https://docs.qmk.fm/#/newbs_getting_started) を参考にして、QMK Firmwareのビルド環境を構築します。
1. `keyboards/yoichiro/lunakey_mini/keymaps/default` ディレクトリをコピーして、独自のキーマップファイル群を作成します（Ex: `keyboards/yoichiro/lunakey_mini/keymaps/customize` ）
1. `keymap.c` や `rules.mk` ファイルなどに対して、必要な修正を行います。
1. 次のコマンドにて、Lunakey Mini向けのファームウェアをビルドします: `make yoichiro/lunakey_mini:customize`
1. これにより、 `yoichiro_lunakey_mini_customize.hex` ファイルが生成されます。このファイルを QMK Toolbox を使って上記手順で Pro Micro に書き込みます。
