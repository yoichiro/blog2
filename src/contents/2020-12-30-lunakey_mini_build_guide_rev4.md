---
layout: post
title: Lunakey Mini ビルドガイド (Rev.4以降)
categories:
- keyboard
---

Lunakey Miniは、左右それぞれ3行 x 6列のキーに親指向けのキーが4個配列された、合計44個のキーを備えた左右分割型の40%キーボードです。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-1.webp" | prepend: site.baseurl }})


Lunakey Miniは、以下の特徴を備えています。

* 左右それぞれ3行 x 6列の40%キーボード。
* 指の長さにそれぞれフィットされたColumn Staggerd配列。
* 親指の自然な可動範囲に合わせた親指向けキー配列。
* Cherry MX互換だけでなく、Kailhロープロファイルも対応。
* Underglow LEDによる電飾効果。
* 圧電スピーカーによる音でのフィードバック効果。
* OLED（液晶パネル）を左右それぞれ搭載可能（オプション）。

この文章は、Lunakey Miniを組み立てるためのビルドガイドです。実際に組み立てを始める前に、ビルドガイドを最後までお読みいただき、組み立ての手順を事前に把握することをお勧めいたします。

※ このビルドガイドは Rev.4 以降の Lunakey Mini 向けです。Rev.2 をお持ちの方は、 [Lunakey Mini Build Guide Rev.2](https://www.eisbahn.jp/yoichiro/2020/11/lunakey_mini_build_guide.html) を参照ください。

注意点として、ファームウェアの容量の関係上、Underglow LED、圧電スピーカー、OLEDを3つ同時に有効にすることは難しいため、この中から2つを選択することになります。部品として3つとも取り付けることは可能ですので、ファームウェアにて必要に応じて有効にする機能を選択すると良いでしょう。

# 組み立て動画

[@Daihuku](https://twitter.com/Daihuku0015) さんが作成された Lunakey Mini の組み立て動画です。一通りの組み立て手順が紹介されていますので、組み立て前にご覧になると良いでしょう。もちろん、組み立てに際して細かな注意点がありますので、動画だけでなく以下で説明する記載についても十分に読み込んでいただければと思います。

<iframe width="560" height="315" src="https://www.youtube.com/embed/IIrRIkBH3-w" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

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
* キーソケット(Cherry MX互換): 44個 (*1)
* キーソケット(Kailh Choc V1): 44個 (*1)
* 圧電スピーカー: 1個
* 抵抗 1kΩ: 1個
* M2スペーサー 7.5mm: 10個
* M2スペーサー 10mm: 4個
* M2ネジ 5mm: 24個
* M2ネジ 4mm: 4個 (*2)
* ゴム足: 12個

(*1) 販売モデルによって、Cherry MX互換キーソケットとKailh Chocキーソケットが両方同梱されている場合と、選択した片方のみ同梱されている場合があります。

(*2) M2 ネジ 4mm の 4 個は、代わりに 5mm ネジが 4 個同梱されていることもありますので、その場合は 5mm ネジをお使いください。

いくつかの部品には、予備として数個余分に含まれています。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-2.webp" | prepend: site.baseurl }})


基板は、リバーシブルではなく、左右がそれぞれ決まっています。基板には、以下の4パターンの記載がされていて、右か左か、表か裏かがわかるようになっています。

* LEFT-FRONT
* LEFT-BACK
* RIGHT-FRONT
* RIGHT-BACK

下の写真は、LEFT-FRONT と RIGHT-FRONT 側です。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-3.webp" | prepend: site.baseurl }})


一方、以下の写真は、下の写真は、LEFT-BACK と RIGHT-BACK 側です。ハンダ付けを行うための銀色のランドが多く、またキーソケットを配置するための白色の部分が数多く描かれています。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-4.webp" | prepend: site.baseurl }})

# 別途購入する必要があるもの

Lunakey Miniを完成させるためには、以下の部品を別途購入してください。

* TRSケーブル、もしくはTRRSケーブル: 1本
* USB Micro Bケーブル: 1本
* キースイッチ（Cherry MX互換またはKailh Choc V1）: 44個
* キーキャップ（Cherry MX互換またはKailh Choc V1、1U）: 44個
* OLEDモジュール（ピンソケット付き）: 2個（オプション）

キースイッチとキーキャップは、トッププレートが1枚のために、Cherry MX互換とKailh Choc V1の混在はできませんので、ご注意ください。

参考までに、上記の部品は、以下より購入できます。

* [遊舎工房](https://yushakobo.jp/)
* [TALP KEYBOARD](https://talpkeyboard.stores.jp/)
* [ゆかりキーボードファクトリー](https://eucalyn.shop/)

組み立てにはハンダ付けが必要になりますので、ハンダごてやハンダなども準備をおこなってください。また、部品が正しく取り付けられているかどうかを確認するためのテスターがあると安心です。

同梱されているPro Microには、ファームウェアが書き込まれていません。そのため、ファームウェアを書き込むためのPCが必要ですので、ご準備ください。

同梱しているダイオードは、表面実装タイプとなります。もし組み立てに自信がない場合は、別途リード線を持つダイオードを購入して取り付けることも可能です。

もしキースイッチとしてKailh Choc（ロープロファイル）をメインで使うことを検討している場合は、同梱されている 7.5mm のスペーサーではなく、もっと低いスペーサーを利用することで、基板とボトムプレートとの距離を狭めることが可能です。これにより、キーボード全体の高さを低くすることができます。圧電スピーカーの取り付け具合にも依存しますが、5mm 〜 5.5mm のスペーサーを適用することが可能です。もし低めのスペーサーを入手したい場合は、以下の URL より別途購入をお願いいたします。

[金属スペーサー/丸型両メネジ/黄銅スペーサー(Ni)/M2 - ヒロスギネット](https://www.hirosugi-net.co.jp/shop/category/category.aspx?category=10141010)

# ダイオードの取り付け

基板のLEFT-BACKおよびRIGHT-BACK側に、ダイオードを取り付けます。キーの数だけ取り付けることになりますので、片側で22個、両方で44個取り付けます、同梱しているダイオードは、表面実装を行うタイプのダイオードですが、別途リード線を持つダイオードを購入して取り付けても大丈夫です。

以下の写真の赤く囲った部分に、ダイオードを取り付けていきます。その際、左右で1つずつ「抵抗（1kΩ）」を取り付ける場所がありますので、その場所に間違ってダイオードを取り付けないように気をつけましょう。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-5.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-6.webp" | prepend: site.baseurl }})


ダイオードには極性がありますので、取り付ける際には方向に気をつけてください。ダイオードには縦線のマーク（｜）が書かれています。この縦線を、ダイオードのスルーホールの四角い方に合わせて取り付けてください。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-7.webp" | prepend: site.baseurl }})


ハンダ付けのコツとして、まず基板の片方のランドに予備ハンダを乗せて、ピンセットなどでダイオードを持ち、予備ハンダを溶かしつつダイオードの足を滑り込ませる、という手順で行うと、スムーズに取り付けることができます。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-8.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-9.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-10.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-11.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-12.webp" | prepend: site.baseurl }})


# Pro Microの取り付け

Pro MicroのUSBコネクタが若干弱く、USBケーブルの抜き先の仕方によっては、USBコネクタが基板から外れてしまうことがあります。これを防ぐために、[エポキシ接着剤などでUSBコネクタを補強する](https://www.eisbahn.jp/yoichiro/2020/09/claw44_4.html) などすると良いでしょう。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-13.webp" | prepend: site.baseurl }})


Pro Microの取り付けには、故障時に交換しやすいように、コンスルーと呼ばれるピンを使って基板に取り付けを行います。Pro Microにコンスルーを取り付ける方法は、 [Helixのビルドガイド](https://github.com/MakotoKurauchi/helix/blob/master/Doc/buildguide_jp.md#pro-micro) を参考にして取り付けてください。ポイントは以下の2点となります。

* コンスルーの側面には、ピンごとに穴があります。その穴に近い方のピンを、Pro Microに挿してください。
* 一つのPro Microに2つのコンスルーを取り付けますが、その際に穴の向きをどちらも同じ方向にして取り付けてください。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-14.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-15.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-16.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-17.webp" | prepend: site.baseurl }})


コンスルーを取り付けた後に、基板のLEFT-FRONTおよびRIGHT-FRONT側からPro Microを基板に取り付けます。コンスルーと基板の間に隙間がなくなるまで押し込みます。基板とコンスルーをハンダ付けする必要はありません。

# リセットスイッチの取り付け

基板のLEFT-FRONTおよびRIGHT-FRONT側からタクトスイッチをリセットスイッチとして基板に差し込みます（方向はありません）。そして、基板の裏側から出ているタクトスイッチの足をハンダ付けします。1個のタクトスイッチにつき、2つの足に対してハンダ付けを行うことになります。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-18.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-19.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-20.webp" | prepend: site.baseurl }})


リセットスイッチを基板に挿しただけでおそらく固定はされるため、特にマスキングテープで固定などしなくてもハンダ付けはできるかと思います。もし不安定になる場合には、マスキングテープなどで固定した後にハンダ付けを行って、なるべく基板とタクトスイッチが離れないように取り付けてください。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-21.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-22.webp" | prepend: site.baseurl }})


# ファームウェアの書き込みと動作確認

この段階で、ダイオード、リセットスイッチ、そしてPro Microが正しく取り付けられているかどうかを確認しておくと良いでしょう。Pro Microとリセットスイッチを取り付けた状態にて、USBケーブルでPCに接続し、ファームウェアを書き込むことで、正しくキー入力が行われるかどうかを確認することができます。他の部品を接続する前にここで動作確認を行うことで、なにか問題があった際に問題の切り分けが容易になります。

Lunakey Miniは、オープンソースプロジェクトである [QMK Firmware](https://github.com/qmk/qmk_firmware) をファームウェアとして採用しています。Lunakey Miniを利用するためには、QMK FirmwareおよびLunakey Mini向けのコードをPro Microに書き込む必要があります。

ファームウェアの書き込みを行うための方法は、いくつか提供されています。その中で、QMK Toolboxを使う方法が最も手軽でしょう。QMK Toolboxは、以下の場所から入手することが可能です。

[QMK Toolbox Releases](https://github.com/qmk/qmk_toolbox/releases/)

各種OS向けに提供されていますので、適切なインストーラをダウンロードして、インストールを行ってください。

Pro MicroとPCを、USB Micro Bケーブルを使って接続します。そして、QMK Toolboxを起動します。

[Remap](https://remap-keys.app/) というキーマップを書き換えることが簡単になる便利ウェブアプリがありますので、キーマップの変更や LED をどう光らせるかを変更する程度のカスタマイズであれば、Remap を利用するのが良いでしょう。Remap については、以下の記事に詳しい紹介が書かれていますので、ご覧になってみてください。

[（初心者編）Remapを使ってキーマップを書き換えよう - 自作キーボード温泉街の歩き方](https://salicylic-acid3.hatenablog.com/entry/remap-manual)

Lunakey Miniのデフォルトキーマップを書き込んだビルド済みファイルは、以下からダウンロードすることができます。

* [Firmwares - Lunakey Mini - Remap](https://remap-keys.app/catalog/BnvcH8rfVvhoT34bsP4r/firmware)

注意点として、取り付けていない部品を有効にしたファームウェアを書き込んだ状態で USB Micro B ケーブルを挿して通電してしまうと、最悪の場合 Pro Micro が故障する可能性があります。ここでの動作確認では、上記の「デフォルトファームウェア」を使ってください。他のファームウェアは、実際に基板に部品が取り付けられた後に書き込むようにしてください。

QMK ToolboxのOpenボタンを押下して、ダウンロードした hex ファイルを選択します。そして、Auto-Flash チェックボックスにチェックを入れておきます。

基板にPro Microを表側から取り付け、リセットスイッチを1回押します。もし書き込みが始まらなかった場合は、リセットスイッチを2回連続で押してみてください。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-23.webp" | prepend: site.baseurl }})


QMK ToolboxはPro Microにファームウェアの書き込みを開始します。QMK Toolboxに出力されるメッセージの最後に Thank you と表示されれば、書き込みは完了です。

キー入力を確認するために、テキストエディタなどの文字入力が可能なアプリケーションや、入力を確認することができるウェブアプリ [QMK Confugulator](https://config.qmk.fm/#/test) などを開いておきます。そして、キーソケットのランドをピンセットなどでショートさせてください。これを、左右の基板およびもう一つのPro Microで行ってください。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-24.webp" | prepend: site.baseurl }})


それぞれのソケットでキー入力がされれば成功です。ただし、以下の2点に注意してください。

* 親指で押すことになるキーで左右一つずつ文字入力がされないキーがあります。これらは、レイヤの操作に割り当てられているため、PC側にキーコードが送られない状態が正常となります。
* 左右の基板とも、左手に割り当てられているキーコードが送信されます。右手側の基板であっても、左手側のキー割り当てのキーコードが送られる状態が正常となります。

上記のファームウェアにて適用されるキーマップは以下となりますので、動作確認の際の参考になさってください。 `KC_***` については、 [Keycodes Overview](https://docs.qmk.fm/#/keycodes) をご参照ください。


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

基板のLEFT-FRONTおよびRIGHT-FRONT側からTRRSジャックを基板に差し込みます。そして、基板の裏側に出ているTRRSジャックの足をハンダ付けします。1個のTRRSジャックにつき、3つの足に対してハンダ付けを行うことになります。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-25.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-26.webp" | prepend: site.baseurl }})


ハンダ付けのコツとして、TRRSジャックを基板に挿しただけでは固定されないため、基板とTRRSジャックを密着させた状態でハンダ付けを行うために、マスキングテープで固定した後にハンダ付けを行うと良いでしょう。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-27.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-28.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-29.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-30.webp" | prepend: site.baseurl }})


# 圧電スピーカーの取り付け

音を鳴らすための圧電スピーカーを取り付けます。圧電スピーカーは2つの部品が必要となります

* 圧電スピーカー
* 1kΩ 抵抗


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-31.webp" | prepend: site.baseurl }})


圧電スピーカーと抵抗は、キーボードとして左手の側に来る基板（正確に言うと、USBケーブルをPro Microに差し込むマスターとなる側の基板）のみに取り付けます。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-32.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-33.webp" | prepend: site.baseurl }})


まずは1kΩの抵抗を取り付けます。予め抵抗の足を折っておき、基板のLEFT-BACK側からスルーホールに差し込みます（方向はありません）。そして、抵抗の足を基板にハンダ付けしてください。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-34.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-35.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-36.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-37.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-38.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-39.webp" | prepend: site.baseurl }})


次に、圧電スピーカーを基板に取り付けます。基板のLEFT-BACK側の圧電スピーカーを取り付けるためのランド2つに、予備ハンダを乗せます。そして、圧電スピーカーを予備ハンダの上に乗せた状態（方向はありません）で、マスキングテープで固定します。最後に、左右からハンダ付けを行って取り付けを行います。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-40.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-41.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-42.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-43.webp" | prepend: site.baseurl }})


予備ハンダをせずにハンダ付けを行ってしまうと、基板のランドと圧電スピーカーの端子が接触していない状況になりますので、予備ハンダは必ず行ってください。

# Underglow LEDの取り付け

キーボードの背面に視覚効果を与えるためのUnderglow LEDを基板に取り付けます。LEDは、WS2812Bという型番の表面実装にて取り付けるタイプのLEDとなります。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-44.webp" | prepend: site.baseurl }})


左右それぞれ6個ずつ取り付けます。LEDは、基板上で直列に接続されています。下の写真に書かれた赤い矢印は、LEDの接続順を示しています。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-45.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-46.webp" | prepend: site.baseurl }})


Underglow LEDは、基板のLEFT-BACKおよびRIGHT-BACK側に取り付けます。取り付け場所は、正方形のマークが書かれている場所です。WS2812Bは方向があり、WS2812Bの四角の一つが三角形に欠けていて、その角を基板に書かれている三角形に合わせて取り付けます。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-47.webp" | prepend: site.baseurl }})


WS2812Bのハンダ付けは、熱に非常に弱いため、注意が必要です。ハンダ付けのコツは、一つのWS2812Bを一気に4カ所ハンダ付けをしないことです。具体的には、以下の手順で行うと良いでしょう。

1. WS2812Bを実装するランド（LED1つあたり4つ）に予備ハンダを乗せておく。
1. 片方の基板にWS2812B 6個を一つずつマスキングテープで固定する。
1. WS2812Bの4個あるうちの一つの端子のみをハンダ付けしていく。WS2812Bの6個それぞれで1カ所のみハンダ付けされている状態になる。
1. 前の手順を3回繰り返す。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-48.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-49.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-50.webp" | prepend: site.baseurl }})


上記の手順により、WS2812Bの温度上昇をできるだけ抑えた状態でハンダ付けを行うことができます。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-51.webp" | prepend: site.baseurl }})

もし「LEDが1つずつちゃんと点灯することを確認しながら取り付けたい」という場合は、急いでハンダ付けした結果熱でLEDが壊れた、とならないために十分にな時間と余裕を持って、接続されている順に一つずつ取り付けて、都度Pro MicroにUSBケーブルを挿して通電させていきながら点灯するかどうか確認すると良いでしょう。


# OLEDモジュールの取り付け

もしOLEDモジュールを別途購入済みであれば、この時点でOLEDモジュールの取り付けを行います。もちろん、現時点でお持ちではない場合でも、後からOLEDモジュールを取り付けることは可能ですが、その際にはカバープレートおよびボトムプレートを取り外すことが必要です。

最初に、OLEDモジュールを取り付けるためのソケットを基板に取り付けます。基板のLEFT-FRONTおよびRIGHT-FRONT側からOLEDソケットを基板に差し込みます（方向はありません）。そして、基板の裏側に出ているOLEDソケットの足をハンダ付けします。1個のOLEDソケットにつき、4つの足に対してハンダ付けを行うことになります。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-52.webp" | prepend: site.baseurl }})


ハンダ付けのコツとして、OLEDソケットを基板に挿しただけでは固定されないため、基板とOLEDソケットを密着させた状態でハンダ付けを行うために、マスキングテープで固定した後にハンダ付けを行うと良いでしょう。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-53.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-54.webp" | prepend: site.baseurl }})


次に、OLEDモジュールを取り付けます。OLEDモジュールにピンをハンダ付けします。この際、OLEDモジュールとピンを固定した状態でハンダ付けを行うために、マスキングテープなどを利用してOLEDモジュールとピンを密着させると良いでしょう。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-55.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-56.webp" | prepend: site.baseurl }})


ピンのハンダ付けが終わったら、予め基板に取り付けてあるソケットにピンを刺します。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-57.webp" | prepend: site.baseurl }})


Pro Microの上部に覆い被さるようにOLEDモジュールを配置しますが、Pro MicroとOLEDモジュールが接触している状態ですと、細かな振動により音が発生する可能性があります。OLEDモジュールをPro Microから浮かした状態を保つか、OLEDとPro Microの間にビニールテープなどの絶縁体を挟めるなどすると良いでしょう。

OLED 対応のファームウェアには、以下のコードを含めてビルドされていて、キーボード名とレイヤー名を表示するようにしてあります。何か独自に表示内容を変更したい場合は、ご自身でプログラムを修正して、ファームウェアをビルドすることが必要です。 `rules.mk` ファイルに `OLED_ENABLE = yes` を指定して、 `keymap.c` ファイルを編集してください。

```c
#ifdef OLED_ENABLE

void render_layer_state(void) {
    oled_write_P(PSTR("Layer: "), false);
    switch(get_highest_layer(layer_state)) {
        case _QWERTY:
            oled_write_P(PSTR("Default\n"), false);
            break;
        case _LOWER:
            oled_write_P(PSTR("Lower\n"), false);
            break;
        case _RAISE:
            oled_write_P(PSTR("Raise\n"), false);
            break;
        case _ADJUST:
            oled_write_P(PSTR("Adjust\n"), false);
            break;
    }
}

bool oled_task_user(void) {
    if (is_keyboard_master()) {
        render_layer_state();
    } else {
        oled_write_P(PSTR("Lunakey Mini\n"), false);
    }
    return false;
}

#endif
```

QMK Firmwareのビルド方法は、このビルドガイドの最後をお読みください。

# キーソケットの取り付け

キースイッチを交換可能にするためのキーソケットを取り付けます。Lunakey Mini Rev.4 では、Cherry MX互換のキースイッチと、Kailh Choc V1のキースイッチの両方をサポートしていて、両方のキーソケットが同梱されています。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-58.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-59.webp" | prepend: site.baseurl }})


基板にCherry MX互換のキーソケットとKailh Choc V1のキーソケットのどちらかを取り付けることも可能ですし、両方を取り付けてしまうことも可能です。両方を取り付けた際には、気分に応じてキースイッチを取り替えることもできるようになります。

キーソケットは、キーの個数分（片側22個、両方で44個）取り付けます、基板のLEFT-BACKおよびRIGHT-BACK側に、キーソケットを取り付けるためのマークがありますので、それに合わせて取り付けを行っていきます。

キーソケットは基板にできるだけ密着した状態で固定することが望ましいので、キーソケットを細く切ったマスキングテープで基板に固定してからハンダ付けをすると良いでしょう。キーソケットの両側にある端子と、基板上にあるランドをハンダ付けしていきます。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-62.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-63.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-64.webp" | prepend: site.baseurl }})


先の細いハンダごて先をお持ちであれば、端子の間に差し込んでハンダを流し込むと固定させることができます。もし太いハンダごて先しかない場合は、基板上のランドに予備ハンダを盛ってからキーソケットをハンダ付けすると良いでしょう。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-65.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-66.webp" | prepend: site.baseurl }})


Kailh Choc V1向けのキーソケットの取り付けですが、実用上はほとんど問題にはならないのですが、取り付けには方向が存在しています。

![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-90.webp" | prepend: site.baseurl }})


基板に描かれたシルク（キーソケット部品の形状を示した白色の部分）には角が欠けた側と角が丸い側が存在します。Rev.5 以降の基板は正しい方向でシルクが描かれていますので、シルクの形状に合わせて取り付けてください。

しかし、Rev.4 では逆になってしまっています。Rev.4 の場合、シルクに合わせて取り付けてしまうと、キースイッチの取り付けに少し力が必要となります。「シルクの角のマークと実際の部品の方向を逆にして取り付ける」ようにすると、正しい向きとなりますので、ご注意ください。

参考: [Kailh Choc ソケットの方向について - Self-Made Keyboards in Japan](https://scrapbox.io/self-made-kbds-ja/Kailh_Choc_%E3%82%BD%E3%82%B1%E3%83%83%E3%83%88%E3%81%AE%E6%96%B9%E5%90%91%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6)

以下の写真が、正しい取り付け方向となります。

![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-89.webp" | prepend: site.baseurl }})


# カバープレートの取り付け

Pro Micro（およびOLEDモジュール）が取り付けられている状態で、M2スペーサー（10mm）を片側2本ずつ、M2ネジ（4mm）を使って基板に取り付けます。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-67.webp" | prepend: site.baseurl }})


M2ネジを基板の裏側（先ほどキーソケットを取り付けた面と同じ側）から差し込んで、M2スペーサーをM2ネジに合わせてねじ込み、最後にドライバーを使って固定すると良いでしょう。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-68.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-69.webp" | prepend: site.baseurl }})


カバープレートには保護シートが接着されていますので、それを剥がします。剥がしにくい場合は、ぬるま湯につけると、糊が溶けて剥がしやすくなります。

M2スペーサーに、カバープレートをM2ネジ（5mm）を使って取り付けます。これにより、Pro Micro（およびOLEDモジュール）の上にアクリル板が覆い被さるように配置されれば大丈夫です。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-70.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-71.webp" | prepend: site.baseurl }})


# トッププレート、キースイッチの取り付け

トッププレートは、キースイッチを取り付けるための穴が22個空いているアクリル板です。キースイッチを固定するための板となります。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-72.webp" | prepend: site.baseurl }})


トッププレートには保護シートが接着されていますので、それを剥がします。剥がしにくい場合は、ぬるま湯につけると、糊が溶けて剥がしやすくなります。

トッププレートには、6つの小さな丸い穴が開いています。この穴に、M2スペーサー（7.5mm）をM2ネジ（5mm）を使って取り付けます。このM2スペーサーは、基板に予め開いている穴を通って、ボトムプレートに固定されます。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-73.webp" | prepend: site.baseurl }})


トッププレートの穴それぞれに、キースイッチを差し込みます。設計として、トッププレートにキースイッチがうまく固定されるようにしてありますので、トッププレートにキースイッチを差し込む際には、少し力が必要となるでしょう。

特に、Kailh Choc V1のキースイッチをトッププレートに取り付ける際には、強めにプレートにはめ込んでください。もしプレートが割れることを懸念される場合は、ヤスリなどで穴の大きさを調整してください。

基板のキーソケットの穴に合わせて、キースイッチのピンを基板のLEFT-FRONTおよびRIGHT-FRONT側から差し込みます。

キースイッチの取り付けのコツとしては、以下の2つの方法があります。

* トッププレートの全ての穴にキースイッチを取り付けてしまうのではなく、トッププレートの外周のいくつかにキースイッチを取り付けた状態で基板に差し込むと、後からキースイッチを取り付けていく際にスムーズな作業となります。
* トッププレートの全ての穴にキースイッチを取り付けてしまって、基板にトッププレートを乗せて位置を合わせます。その後、トッププレートの中央付近のキースイッチから基板にピンを押し込んでいきます。これを中央から外側に向かって取り付けていきます。

どちらか良いかは、キースイッチの種類によって変わってきますので、上記2つのうちスムーズに取り付けができる手順を選択してください。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-74.webp" | prepend: site.baseurl }})


注意点としては、キースイッチから2本出ている端子がまっすぐに伸びている状態で基板に差し込んでいただくことと、親指で押すことになる4つのキーについては上下が逆となっているために、ピンの方向を基板にちゃんと合わせることを確認してから差し込んでください。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-75.webp" | prepend: site.baseurl }})

# ボトムプレートの取り付け

基板の裏側（先ほどトッププレートを取り付けた面と反対側）の6つの穴から、トッププレートに取り付けたM2スペーサー（7.5mm）が見えていると思います。このM2スペーサーに合わせて、ボトムプレートを取り付けます。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-76.webp" | prepend: site.baseurl }})


ボトムプレートには保護シートが接着されていますので、それを剥がします。剥がしにくい場合は、ぬるま湯につけると、糊が溶けて剥がしやすくなります。

ボトムプレートを基板の形に合わせてM2スペーサーの上に乗せます。そして、M2ネジ（5mm）を使って固定します。

# ゴム足の取り付け

ボトムプレートの6カ所にそれぞれゴム足を取り付けます。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-77.webp" | prepend: site.baseurl }})


# キーキャップの取り付け

最後に、キースイッチにキーキャップを取り付けて、Lunakey Miniの完成です。お疲れさまでした。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-78.webp" | prepend: site.baseurl }})


上の写真では、Kailh Choc V1 対応のキーキャップを取り付けています。このキーキャップは、以下から購入することができます。

[MBK Choc Low Profile キーキャップ](https://kochikeyboard.stores.jp/items/5f7efd334b08395643f28486)


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-79.webp" | prepend: site.baseurl }})


上の写真では、無刻印のDSAプロファイルのキーキャップを取り付けています。このキーキャップは、以下から購入することができます。

[キーキャップ DSAプロファイル - TALP KEYBOARD](https://talpkeyboard.stores.jp/?category_id=59e2acfaed05e644fd004008)

# 使用方法

組み立てが完了したLunakey Miniを使用する際には、以下を行ってください。

* TRRSケーブルもしくはTRSケーブルを、左右のTRRSジャックにそれぞれ差し込んでください。
* USB Micro B ケーブルを、左手側のPro Microに差し込み、PCと接続してください。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-80.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-81.webp" | prepend: site.baseurl }})



![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-82.webp" | prepend: site.baseurl }})


これにより、Lunakey Miniをご利用できるようになります。組み立てお疲れさまでした。Lunakey Mini が素敵なPCライフにお役に立てることを期待しています！

もし片側のみ正常に利用でき、もう片側が一切使えない（何の反応もない）場合は、以下のエントリを参考にファームウェアの修正を行ってください。

[Lunakey Miniが片手だけ動かなかったときに確認すること](https://www.eisbahn.jp/yoichiro/2021/03/split_usb_detect.html)

# キーボードのカスタマイズ

Remap 対応のファームウェアを書き込んでいる場合は、キーマップの変更や Underglow LED の光らせ方の変更については、QMK Firmware のビルドを行うことなく Remap というウェブアプリを使って手軽に変更することが可能です。

Remap は Chrome または Edge のバージョン 89 以降で利用することができます。以下の URL にアクセスしてください。

[https://remap-keys.app/](https://remap-keys.app/)

Lunakey Mini を PC に接続して、「START REMAP FOR YOUR KEYBOARD」ボタンを押してください。Lunakey Mini が Remap に認識され、キーマップが表示されます。もし表示されていない場合は、「+KEYBOARD」ボタンを押して、Lunakey Mini へのアクセスを許可してください。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-85.webp" | prepend: site.baseurl }})


左側にある数字を切り替えることで、レイヤを切り替えることができます。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-86.webp" | prepend: site.baseurl }})


Remap の画面の下には、設定可能なキーが表示されています。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-87.webp" | prepend: site.baseurl }})


各キーを押すことで、割り当てるキーを変更することができます。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-91.webp" | prepend: site.baseurl }})


キーの割り当ての変更は、右上にある FLAH ボタンを押すことで、キーボードに反映されます。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-92.webp" | prepend: site.baseurl }})


LIGHTING アイコンをクリックすることで、Underglow LED をどう光らせるかを変更させることが可能です。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-88.webp" | prepend: site.baseurl }})


より詳しい Remap の操作方法は、以下をご覧になってください。

[（初心者編）Remapを使ってキーマップを書き換えよう - 自作キーボード温泉街の歩き方](https://salicylic-acid3.hatenablog.com/entry/remap-manual)

もし、Remap にてサポートされていない複雑なキー割り当てをしたり、Underglow LEDや圧電スピーカー、OLED対応を行うためには、QMK Firmwareをビルドする必要があります。以下の手順で行ってください。

1. [Setting Up Your QMK Environment](https://docs.qmk.fm/#/newbs_getting_started) を参考にして、QMK Firmwareのビルド環境を構築します。
1. `keyboards/yoichiro/lunakey_mini/keymaps/default` ディレクトリをコピーして、独自のキーマップファイル群を作成します（Ex: `keyboards/yoichiro/lunakey_mini/keymaps/customize` ）
1. `keymap.c` や `rules.mk` ファイルなどに対して、必要な修正を行います。
1. 次のコマンドにて、Lunakey Mini向けのファームウェアをビルドします: `make yoichiro/lunakey_mini:customize`
1. これにより、 `yoichiro_lunakey_mini_customize.hex` ファイルが生成されます。このファイルを QMK Toolbox を使って上記手順で Pro Micro に書き込みます。
