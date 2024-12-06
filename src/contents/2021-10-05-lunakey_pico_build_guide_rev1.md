---
layout: post
title: Lunakey Pico ビルドガイド (Rev.1以降)
categories:
- keyboard
---

Lunakey Picoは、左右それぞれ3行 x 6列のキーに親指向けのキーが4個配列された、合計44個のキーを備えた左右分割型の40%キーボードです。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-1.webp" | prepend: site.baseurl }})


Lunakey Picoは、以下の特徴を備えています。

* 左右それぞれ3行 x 6列の40%キーボード。
* 指の長さにそれぞれフィットされたColumn Staggerd配列。
* 親指の自然な可動範囲に合わせた親指向けキー配列。
* Cherry MX互換だけでなく、Kailhロープロファイルも対応。
* Underglow LEDによる電飾効果。
* 圧電スピーカーによる音でのフィードバック効果。

この文章は、Lunakey Picoを組み立てるためのビルドガイドです。実際に組み立てを始める前に、ビルドガイドを最後までお読みいただき、組み立ての手順を事前に把握することをお勧めいたします。

# 同梱品の確認

Lunakey Picoには、以下の部品群が含まれています。全て含まれているかどうか確認してください。

* 基板: 2枚
* トッププレート: 2枚
* ボトムプレート: 2枚
* カバープレート: 2枚
* Raspberry Pi Pico: 2個
* コンスルーまたはピンヘッダ: 4個
* TRRSジャック: 2個
* タクトスイッチ: 2個
* ダイオード: 44個
* LED (WS2812B): 12個
* キーソケット(Cherry MX互換): 44個
* キーソケット(Kailh Choc V1): 44個
* 圧電スピーカー: 1個
* 抵抗 1kΩ: 1個
* M2スペーサー 7.5mm: 14個
* M2ネジ 5mm: 32個
* ゴム足: 12個

コンスルーが入手困難なために、2022年9月以降はピンヘッダが同梱されています。

いくつかの部品には、予備として数個余分に含まれています。

基板は、リバーシブルではなく、左右がそれぞれ決まっています。基板には、以下の4パターンの記載がされていて、右か左か、表か裏かがわかるようになっています。

* LEFT-FRONT
* LEFT-BACK
* RIGHT-FRONT
* RIGHT-BACK

下の写真は、LEFT-FRONT と RIGHT-FRONT 側です。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-2.webp" | prepend: site.baseurl }})


一方、以下の写真は、下の写真は、LEFT-BACK と RIGHT-BACK 側です。ハンダ付けを行うための銀色のランドが多く、またキーソケットを配置するための白色の部分が数多く描かれています。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-3.webp" | prepend: site.baseurl }})


# 別途購入する必要があるもの


Lunakey Picoを完成させるためには、以下の部品を別途購入してください。

* TRRSケーブル: 1本
* USB Micro Bケーブル: 1本
* キースイッチ（Cherry MX互換またはKailh Choc V1）: 44個
* キーキャップ（Cherry MX互換またはKailh Choc V1、1U）: 44個

キースイッチとキーキャップは、トッププレートが1枚のために、Cherry MX互換とKailh Choc V1の混在はできませんので、ご注意ください。

TRRSケーブルとは、4極のオーディオケーブルのことです。3極のTRSケーブルでは左右の通信ができなくなりますので、購入の際にはご注意ください。

参考までに、上記の部品は、以下より購入できます。

* [遊舎工房 – 自作キーボード＆レーザーカット](https://yushakobo.jp/)
* [TALPKEYBOARD - 自作キーボードとパーツのショップ -](https://talpkeyboard.net/)
* [Lilakey](https://lilakey.com/)

組み立てにはハンダ付けが必要になりますので、ハンダごてやハンダなども準備をおこなってください。また、部品が正しく取り付けられているかどうかを確認するためのテスターがあると安心です。

同梱されているRaspberry Pi Picoには、ファームウェアが書き込まれていません。そのため、ファームウェアを書き込むためのPCが必要ですので、ご準備ください。

同梱しているダイオードは、表面実装タイプとなります。もし組み立てに自信がない場合は、別途リード線を持つダイオードを購入して取り付けることも可能です。

もしキースイッチとしてKailh Choc（ロープロファイル）をメインで使うことを検討している場合は、同梱されている 7.5mm のスペーサーではなく、もっと低いスペーサーを利用することで、基板とボトムプレートとの距離を狭めることが可能です。これにより、キーボード全体の高さを低くすることができます。圧電スピーカーの取り付け具合にも依存しますが、5mm 〜 5.5mm のスペーサーを適用することが可能です。もし低めのスペーサーを入手したい場合は、以下の URL より別途購入をお願いいたします。

[金属スペーサー/丸型両メネジ/黄銅スペーサー(Ni)/M1.4 - ヒロスギネット - スペーサー・ピンヘッダー・ワッシャー通販](https://www.hirosugi-net.co.jp/shop/category/category.aspx?category=10141010)

# ダイオードの取り付け

基板のLEFT-BACKおよびRIGHT-BACK側に、ダイオードを取り付けます。キーの数だけ取り付けることになりますので、片側で22個、両方で44個取り付けます、同梱しているダイオードは、表面実装を行うタイプのダイオードですが、別途リード線を持つダイオードを購入して取り付けても大丈夫です。

以下の写真の赤く囲った部分に、ダイオードを取り付けていきます。その際、左右で1つずつ「抵抗（1kΩ）」を取り付ける場所がありますので、その場所に間違ってダイオードを取り付けないように気をつけましょう。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-4.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-5.webp" | prepend: site.baseurl }})


ダイオードには極性がありますので、取り付ける際には方向に気をつけてください。ダイオードには縦線のマーク（｜）が書かれています。この縦線を、ダイオードのスルーホールの四角い方に合わせて取り付けてください。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-6.webp" | prepend: site.baseurl }})


ハンダ付けのコツとして、まず基板の片方のランドに予備ハンダを乗せて、ピンセットなどでダイオードを持ち、予備ハンダを溶かしつつダイオードの足を滑り込ませる、という手順で行うと、スムーズに取り付けることができます。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-7.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-8.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-9.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-10.webp" | prepend: site.baseurl }})


# Raspberry Pi Pico の取り付け

Raspberry Pi Pico の取り付けは、コンスルーとピンヘッダでそれぞれ手順が異なります。

## コンスルーの場合

Raspberry Pi Picoの取り付けには、故障時に交換しやすいように、コンスルーと呼ばれるピンを使って基板に取り付けを行います。Raspberry Pi Picoにコンスルーを取り付ける方法は、 [Helixのビルドガイド](https://github.com/MakotoKurauchi/helix/blob/master/Doc/buildguide_jp.md#pro-micro) を参考にして取り付けてください。ポイントは以下の2点となります。

* コンスルーの側面には、ピンごとに穴があります。その穴に近い方のピンを、Raspberry Pi Picoに挿してください。
* 一つのRaspberry Pi Picoに2つのコンスルーを取り付けますが、その際に穴の向きをどちらも同じ方向にして取り付けてください。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-11.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-12.webp" | prepend: site.baseurl }})


コンスルーを取り付けた後に、基板のLEFT-FRONTおよびRIGHT-FRONT側からRaspberry Pi Picoを基板に取り付けます。コンスルーと基板の間に隙間がなくなるまで押し込みます。基板とコンスルーをハンダ付けする必要はありません。

## ピンヘッダの場合

Raspberry Pi Pico の左右に、それぞれピンヘッダを差し込み、基板側にも差し込みます。この際、Raspberry Pi Pico の BOOTSEL ボタン（白いボタン）が基板と反対側を向くようにして、ボタンを押せるようにしてください。基板側に BOOLSEL ボタンを向けてしまうと、そのボタンが押せなくなるだけでなく、配線としても間違った接続になり、Lunakey Pico が機能しませんので、ご注意ください。

Raspberry Pi Pico 側と、基板側の両方をはんだ付けします。ピンヘッダと Raspberry Pi Pico および基板が浮かないように、マスキングテープなどで固定してからはんだ付けを行ってください。

# リセットスイッチの取り付け

基板のLEFT-FRONTおよびRIGHT-FRONT側からタクトスイッチをリセットスイッチとして基板に差し込みます（方向はありません）。そして、基板の裏側から出ているタクトスイッチの足をハンダ付けします。1個のタクトスイッチにつき、2つの足に対してハンダ付けを行うことになります。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-13.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-14.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-15.webp" | prepend: site.baseurl }})


リセットスイッチを基板に挿しただけでおそらく固定はされるため、特にマスキングテープで固定などしなくてもハンダ付けはできるかと思います。もし不安定になる場合には、マスキングテープなどで固定した後にハンダ付けを行って、なるべく基板とタクトスイッチが離れないように取り付けてください。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-16.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-17.webp" | prepend: site.baseurl }})


# TRRSジャックの取り付け

基板のLEFT-FRONTおよびRIGHT-FRONT側からTRRSジャックを基板に差し込みます。そして、基板の裏側に出ているTRRSジャックの足をハンダ付けします。1個のTRRSジャックにつき、3つの足に対してハンダ付けを行うことになります。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-18.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-19.webp" | prepend: site.baseurl }})


ハンダ付けのコツとして、TRRSジャックを基板に挿しただけでは固定されないため、基板とTRRSジャックを密着させた状態でハンダ付けを行うために、マスキングテープで固定した後にハンダ付けを行うと良いでしょう。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-20.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-21.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-22.webp" | prepend: site.baseurl }})




# ファームウェアの書き込みと動作確認

この段階で、ダイオード、リセットスイッチ、TRRSジャック、そしてRaspberry Pi Picoが正しく取り付けられているかどうかを確認しておくと良いでしょう。USBケーブルでPCとRaspberry Pi Picoを接続し、ファームウェアを書き込むことで、正しくキー入力が行われるかどうかを確認することができます。他の部品を接続する前にここで動作確認を行うことで、なにか問題があった際に問題の切り分けが容易になります。

Lunakey Picoでは、いくつかのファームウェアを使うことができます。ここでは、 [QMK Firmware](https://github.com/qmk/qmk_firmware) と [KMK Firmware](https://github.com/KMKfw/kmk_firmware) 、 [PRK Firmware](https://github.com/picoruby/prk_firmware) を使ったファームウェアのセットアップ方法を紹介いたします。

## QMK Firmwareの導入方法

まず、必要なファイルを入手しましょう。

* [ここ](https://remap-keys.app/catalog/Kg5ww7zULE3TTt9PSQzw/firmware) から、QMK Firmware の最新バージョンの uf2 ファイルをダウンロードして、任意の場所に保存します。

次に、QMK Firmwareの uf2 ファイルを Raspberry Pi Pico に書き込みます。まず、左手側の基板に差し込んでいる Raspberry Pi Pico の緑色の基板上にある白いボタン（BOOTSEL）を押しっぱなしにした状態で、USB ケーブルを差し込みます。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-23.webp" | prepend: site.baseurl }})


これにより、OS 側で `RPI-RP2` という名前のリブーバブルディスクとして認識されます。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-24.webp" | prepend: site.baseurl }})

この RPI-RP2 に、先程ダウンロードした QMK Firmware の uf2 ファイルをドラッグアンドドロップでコピーします。コピーが完了すると、自動的に uf2 が Raspberry Pi Pico に書き込まれます。その後、自動的に RPI-RP2 ディスクは接続解除されます。

右手側の Raspberry Pi Pico についても、同様の操作を行います。

以上が完了したら、USB ケーブルを一度抜き、左右の TRRS ジャックを TRRS ケーブルで接続した状態で、左手側の Raspberry Pi Pico に USB ケーブルを差し込んでください。これで最低限の準備が完了し、キーボードとして文字入力ができるようになります。

キー入力を確認するために、テキストエディタなどの文字入力が可能なアプリケーションや、入力を確認することができるウェブアプリ [QMK Confugulator](https://config.qmk.fm/#/test) などを開いておきます。そして、キーソケットのランドをピンセットなどでショートさせてください。これを、左右の基板で行ってください。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-27.webp" | prepend: site.baseurl }})


それぞれのソケットでキー入力がされれば成功です。ただし、親指で押すことになるキーで左右一つずつ文字入力がされないキーがあります。これらは、レイヤの操作に割り当てられているため、PC側にキーコードが送られない状態が正常となります。

上記のファームウェアにて適用されるキーマップは以下となりますので、動作確認の際の参考になさってください。 KC_*** については、 [Keycodes Overview](https://docs.qmk.fm/#/keycodes) をご参照ください。

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


## PRK Firmwareの導入方法

まず、必要なファイルを入手しましょう。

* [ここ](https://github.com/picoruby/prk_firmware/tags) から、PRK Firmwareの最新バージョンの uf2 ファイルの zip ファイルをダウンロードして、任意の場所に展開します。
* [ここ](https://raw.githubusercontent.com/yoichiro/prk_lunakey_pico/main/keymap.rb)  から、keymap.rb ファイルをダウンロードします。
* [ここ](https://raw.githubusercontent.com/yoichiro/prk_lunakey_pico/main/prk-conf.txt)  から、prk-conf.txt ファイルをダウンロードします。

次に、PRK Firmwareの uf2 ファイルを Raspberry Pi Pico に書き込みます。まず、左手側の基板に差し込んでいる Raspberry Pi Pico の緑色の基板上にある白いボタン（BOOTSEL）を押しっぱなしにした状態で、USB ケーブルを差し込みます。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-23.webp" | prepend: site.baseurl }})


これにより、OS 側で `RPI-RP2` という名前のリブーバブルディスクとして認識されます。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-24.webp" | prepend: site.baseurl }})

この RPI-RP2 に、先程ダウンロードした PRK Firmware の uf2 ファイルをドラッグアンドドロップでコピーします。コピーが完了すると、自動的に uf2 が Raspberry Pi Pico に書き込まれます。その後、自動的に RPI-RP2 ディスクは接続解除され、その直後に `PRKFirmware` という名前で再接続されます。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-25.webp" | prepend: site.baseurl }})


この PRKFirmware ドライブに、先程ダウンロードした keymap.rb ファイルと prk-conf.txt ファイルをドラッグアンドドロップしてコピーします。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-26.webp" | prepend: site.baseurl }})


右手側の Raspberry Pi Pico についても同様の操作を行います。

以上が完了したら、USB ケーブルを一度抜き、左右の TRRS ジャックを TRRS ケーブルで接続した状態で、左手側の Raspberry Pi Pico に USB ケーブルを差し込んでください。これで最低限の準備が完了し、キーボードとして文字入力ができるようになります。

キー入力を確認するために、テキストエディタなどの文字入力が可能なアプリケーションや、入力を確認することができるウェブアプリ [QMK Confugulator](https://config.qmk.fm/#/test) などを開いておきます。そして、キーソケットのランドをピンセットなどでショートさせてください。これを、左右の基板で行ってください。



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-27.webp" | prepend: site.baseurl }})


それぞれのソケットでキー入力がされれば成功です。ただし、親指で押すことになるキーで左右一つずつ文字入力がされないキーがあります。これらは、レイヤの操作に割り当てられているため、PC側にキーコードが送られない状態が正常となります。

上記のファームウェアにて適用されるキーマップは以下となりますので、動作確認の際の参考になさってください。 KC_*** については、 [Keycodes Overview](https://docs.qmk.fm/#/keycodes) をご参照ください。


```
kbd.add_layer :default, [
  :KC_ESC,  :KC_Q,    :KC_W,    :KC_E,    :KC_R,    :KC_T,     :KC_Y,    :KC_U,    :KC_I,    :KC_O,    :KC_P,     :KC_BSPC,
  :CT_TAB,  :KC_A,    :KC_S,    :KC_D,    :KC_F,    :KC_G,     :KC_H,    :KC_J,    :KC_K,    :KC_L,    :KC_SCOLON,:CT_QUOT,
  :KC_LSFT, :KC_Z,    :KC_X,    :KC_C,    :KC_V,    :KC_B,     :KC_N,    :KC_M,    :KC_COMM, :KC_DOT,  :KC_SLSH,  :SF_MINS,
  :KC_NO,   :KC_NO,   :KC_LALT, :KC_LANG2,:LOWER,   :KC_SPC,   :KC_ENT,  :RAISE,   :KC_LANG1,:KC_RALT, :KC_NO,    :KC_NO
]
kbd.add_layer :lower, [
  :KC_BSLS, :KC_CIRC, :KC_EXLM, :KC_AMPR, :KC_PIPE, :KC_DLR,   :KC_AT,   :KC_ASTER,:KC_PLUS, :KC_EQL,  :KC_PERC,  :KC_BSPC,
  :CT_TAB,  :KC_1,    :KC_2,    :KC_3,    :KC_4,    :KC_5,     :KC_6,    :KC_7,    :KC_8,    :KC_9,    :KC_0,     :KC_DQUO,
  :KC_LSFT, :KC_Z,    :KC_X,    :KC_C,    :KC_V,    :KC_B,     :KC_N,    :KC_COLON,:KC_LABK, :KC_RABK, :KC_QUES,  :KC_UNDS,
  :KC_NO,   :KC_NO,   :KC_LGUI, :KC_LANG2,:LOWER,   :KC_SPC,   :KC_ENT,  :ADJUST,  :KC_LANG1,:KC_LGUI, :KC_NO,    :KC_NO
]
kbd.add_layer :raise, [
  :KC_BSLS, :KC_CIRC, :KC_EXLM, :KC_AMPR, :KC_PIPE, :KC_DLR,   :KC_AT,   :KC_ASTER,:KC_PLUS, :KC_EQL,  :KC_PERC,  :KC_BSPC,
  :KC_HASH, :KC_GRV,  :KC_LBRC, :KC_RBRC, :KC_LPRN, :KC_RPRN,  :KC_PGUP, :KC_HOME, :KC_UP,   :KC_END,  :KC_SCOLON,:KC_DQUO,
  :KC_LSFT, :KC_TILD, :KC_X,    :KC_C,    :KC_LCBR, :KC_RCBR,  :KC_PGDN, :KC_LEFT, :KC_DOWN, :KC_RGHT, :KC_SLSH,  :SF_MINS,
  :KC_NO,   :KC_NO,   :KC_LGUI, :KC_LANG2,:ADJUST,  :KC_SPC,   :KC_ENT,  :RAISE,   :KC_LANG1,:KC_LGUI, :KC_NO,    :KC_NO
]
kbd.add_layer :adjust, [
  :KC_NO  , :KC_NO,   :KC_NO  , :KC_NO  , :KC_NO  , :KC_NO  ,  :KC_F1,   :KC_F2,   :KC_F3,   :KC_F4,   :KC_F5,    :KC_F6,
  :KC_NO  , :KC_NO  , :KC_NO  , :KC_NO  , :KC_NO  , :KC_NO  ,  :KC_F7,   :KC_F8,   :KC_F9,   :KC_F10,  :KC_F11,   :KC_F12,
  :KC_NO  , :KC_NO  , :KC_VOLD, :KC_VOLU, :KC_MUTE, :KC_NO  ,  :KC_NO  , :KC_NO  , :KC_CAPS, :KC_NO  , :KC_NO  ,  :KC_NO  ,
  :KC_NO,   :KC_NO,   :KC_NO  , :KC_NO  , :KC_NO  , :KC_NO  ,  :KC_NO  , :KC_NO  , :KC_NO  , :KC_NO  , :KC_NO,    :KC_NO
]
```


## KMK Firmwareの導入方法


KMK Firmware を動作させるために、 [KMKPython](https://github.com/KMKfw/kmkpython) または [CircuitPython](https://circuitpython.org/) が必要になりますが、ここでは CircuitPython を使います。

まず、必要なファイルを入手しましょう。

* [ここ](https://circuitpython.org/board/raspberry_pi_pico/) から、Raspberry Pi Pico 向けの CircuitPython イメージをダウンロードします。バージョン 7.0.0 以上の uf2 ファイルをダウンロードしてください。
* [ここ](https://github.com/adafruit/Adafruit_CircuitPython_Bundle/releases/) から、CircuitPython のバンドルファイル一式が格納された zip ファイルをダウンロードして、任意の場所に展開します。
* [ここ](https://github.com/KMKfw/kmk_firmware) から、KMK Firmware のファイル一式が格納された zip ファイルをダウンロードして、任意の場所に展開します。
* [ここ](https://github.com/adafruit/Adafruit_CircuitPython_NeoPixel) から、Underglow LED を制御するための neopixel.py ファイルをダウンロードします。

次に、CircuitPython を Raspberry Pi Pico に書き込みます。まず、左手側の基板に差し込んでいる Raspberry Pi Pico の緑色の基板上にある白いボタン（BOOTSEL）を押しっぱなしにした状態で、USB ケーブルを差し込みます。

![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-28.webp" | prepend: site.baseurl }})


これにより、OS 側で `RPI-RP2` という名前のリブーバブルディスクとして認識されます。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-29.webp" | prepend: site.baseurl }})


この RPI-RP2 に、先程ダウンロードした CircuitPython 7 以降の uf2 ファイルをドラッグアンドドロップでコピーします。コピーが完了すると、自動的に uf2 が Raspberry Pi Pico に書き込まれます。その後、自動的に RPI-RP2 ディスクは接続解除され、その直後に `CIRCUITPY` という名前で再接続されます。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-30.webp" | prepend: site.baseurl }})

この CIRCUITPY ディスクにある全てのファイルとディレクトリ（lib, boot_out.txt, code.py）を削除します。次に、以下のファイルやディレクトリをコピーします。

* CircuitPython のバンドルファイルを展開した結果のディレクトリにある `lib` ディレクトリ内の `adafruit_bus_device` ディレクトリを、CIRCUITPYTHON ディスクに `lib` ディレクトリを作成してその中にコピーします。
* KMK Firmware（kmk_firmware-lunakey-pico.zip ファイルを展開した結果の）ディレクトリにある `kmk` ディレクトリと `boot.py` ファイルを、CIRCUITPY ディスクにコピーします。
* KMK Firmware（kmk_firmware-lunakey-pico.zip ファイルを展開した結果の）ディレクトリにある `boards/lunakey_pico` ディレクトリ内の `main.py` ファイルと `kb.py` ファイルを、CIRCUITPY ディスクにコピーします。
* neopixel.py ファイルを、CIRCUITPY ディスクにコピーします。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-31.webp" | prepend: site.baseurl }})


次に、CIRCUITPY ディスクにコピーした `main.py` ファイルを、何らかのテキストエディタで開きます。27行目をコメント行に変更して、この `main.py` ファイルが左手側として機能するように修正します。



```
# TODO Comment one of these on each side
split_side = SplitSide.LEFT
# split_side = SplitSide.RIGHT
```

修正後、保存してテキストエディタを閉じます。以上の操作が完了した後に、USB ケーブルを一度抜き、再度 Raspberry Pi Pico に差し込みます。

手順が全て正しく行われた際には、Raspberry Pi Pico の LED が緑色に点灯します。

以上の手順を、右手側の Raspberry Pi Pico についても行います。左手側との違いは、 `main.py` ファイルを修正する際に、先ほどとは異なる 26 行目をコメント行に変更することです。


```
# TODO Comment one of these on each side
# split_side = SplitSide.LEFT
split_side = SplitSide.RIGHT
```

以上が完了したら、USB ケーブルを一度抜き、左右の TRRS ジャックを TRRS ケーブルで接続した状態で、左手側の Raspberry Pi Pico に USB ケーブルを差し込んでください。これで最低限の準備が完了し、キーボードとして文字入力ができるようになります。

キー入力を確認するために、テキストエディタなどの文字入力が可能なアプリケーションや、入力を確認することができるウェブアプリ [QMK Confugulator](https://config.qmk.fm/#/test) などを開いておきます。そして、キーソケットのランドをピンセットなどでショートさせてください。これを、左右の基板で行ってください。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-32.webp" | prepend: site.baseurl }})


それぞれのソケットでキー入力がされれば成功です。ただし、親指で押すことになるキーで左右一つずつ文字入力がされないキーがあります。これらは、レイヤの操作に割り当てられているため、PC側にキーコードが送られない状態が正常となります。

上記のファームウェアにて適用されるキーマップは以下となりますので、動作確認の際の参考になさってください。 KC_*** については、 [Keycodes Overview](https://docs.qmk.fm/#/keycodes) をご参照ください。



```
# DEFAULT
#      +--------+--------+--------+--------+--------+--------+                 +--------+--------+--------+--------+--------+--------+
        KC.GESC, KC.Q,    KC.W,    KC.E,    KC.R,    KC.T,                      KC.Y,    KC.U,    KC.I,    KC.O,    KC.P,    KC.BSPC,\
#      +--------+--------+--------+--------+--------+--------+                 +--------+--------+--------+--------+--------+--------+
        CT_TAB,  KC.A,    KC.S,    KC.D,    KC.F,    KC.G,                      KC.H,    KC.J,    KC.K,    KC.L,    KC.SCLN, CT_QUOT,\
#      +--------+--------+--------+--------+--------+--------+                 +--------+--------+--------+--------+--------+--------+
        KC.LSFT, KC.Z,    KC.X,    KC.C,    KC.V,    KC.B,                      KC.N,    KC.M,    KC.COMM, KC.DOT,  KC.SLSH, SF_MINS,\
#      +--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+
                                   KC.LALT, KC.MHEN, LOWER,   KC.SPC,  KC.ENT,  RAISE,   KC.HENK, KC.RALT
#      +--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+

# LOWER
#      +--------+--------+--------+--------+--------+--------+                 +--------+--------+--------+--------+--------+--------+
        KC.BSLS, KC.CIRC, KC.EXLM, KC.AMPR, KC.PIPE, KC.DLR,                    KC.AT,   KC.ASTR, KC.PLUS, KC.EQL,  KC.PERC, KC.BSPC,\
#      +--------+--------+--------+--------+--------+--------+                 +--------+--------+--------+--------+--------+--------+
        _______, KC.N1,   KC.N2,   KC.N3,   KC.N4,   KC.N5,                     KC.N6,   KC.N7,   KC.N8,   KC.N9,   KC.N0,   KC.DQT,\
#      +--------+--------+--------+--------+--------+--------+                 +--------+--------+--------+--------+--------+--------+
        _______, _______, _______, _______, _______, _______,                   _______, KC.COLN, KC.LABK, KC.RABK, KC.QUES, KC.UNDS,\
#      +--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+
                                   KC.LGUI, _______, _______, _______, _______, ADJUST,  _______, KC.LGUI
#      +--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+

# RAISE
#      +--------+--------+--------+--------+--------+--------+                 +--------+--------+--------+--------+--------+--------+
        KC.BSLS, KC.CIRC, KC.EXLM, KC.AMPR, KC.PIPE, KC.DLR,                    KC.AT,   KC.ASTR, KC.PLUS, KC.EQL,  KC.PERC, KC.BSPC,\
#      +--------+--------+--------+--------+--------+--------+                 +--------+--------+--------+--------+--------+--------+
        KC.HASH, KC.GRV,  KC.LBRC, KC.RBRC, KC.LPRN, KC.RPRN,                   KC.PGUP, KC.HOME, KC.UP,   KC.END,  _______, KC.DQT,\
#      +--------+--------+--------+--------+--------+--------+                 +--------+--------+--------+--------+--------+--------+
        _______, KC.TILD, _______, _______, KC.LCBR, KC.RCBR,                   KC.PGDN, KC.LEFT, KC.DOWN, KC.RGHT, _______, _______,\
#      +--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+
                                   KC.LGUI, _______, ADJUST,  _______, _______, _______, _______, KC.LGUI
#      +--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+

# ADJUST
#      +--------+--------+--------+--------+--------+--------+                 +--------+--------+--------+--------+--------+--------+
        _______, KC.RESET,_______, _______, _______, _______,                   KC.F1,   KC.F2,   KC.F3,   KC.F4,   KC.F5,   KC.F6,\
#      +--------+--------+--------+--------+--------+--------+                 +--------+--------+--------+--------+--------+--------+
        _______, _______, _______, _______, _______, _______,                   KC.F7,   KC.F8,   KC.F9,   KC.F10,  KC.F11,  KC.F12,\
#      +--------+--------+--------+--------+--------+--------+                 +--------+--------+--------+--------+--------+--------+
        _______, _______, KC.VOLD, KC.VOLU, KC.MUTE, _______,                   SG_PSCR, SF_PSCR, KC.CAPS, _______, CG_RGHT, _______,\
#      +--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+
                                   _______, _______, _______, _______, _______, _______, _______, _______
#      +--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+
```



# 圧電スピーカーの取り付け

音を鳴らすための圧電スピーカーを取り付けます。圧電スピーカーは2つの部品が必要となります

* 圧電スピーカー
* 1kΩ 抵抗


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-33.webp" | prepend: site.baseurl }})


圧電スピーカーと抵抗は、キーボードとして左手の側に来る基板のみに取り付けます。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-34.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-35.webp" | prepend: site.baseurl }})


まずは1kΩの抵抗を取り付けます。予め抵抗の足を折っておき、基板のLEFT-BACK側からスルーホールに差し込みます（方向はありません）。そして、抵抗の足を基板にハンダ付けしてください。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-36.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-37.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-38.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-39.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-40.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-41.webp" | prepend: site.baseurl }})


次に、圧電スピーカーを基板に取り付けます。基板のLEFT-BACK側の圧電スピーカーを取り付けるためのランド2つに、予備ハンダを乗せます。そして、圧電スピーカーを予備ハンダの上に乗せた状態（方向はありません）で、マスキングテープで固定します。最後に、左右からハンダ付けを行って取り付けを行います。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-42.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-43.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-44.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-45.webp" | prepend: site.baseurl }})


予備ハンダをせずにハンダ付けを行ってしまうと、基板のランドと圧電スピーカーの端子が接触していない状況になりますので、予備ハンダは必ず行ってください。

# Underglow LEDの取り付け

キーボードの背面に視覚効果を与えるためのUnderglow LEDを基板に取り付けます。LEDは、WS2812Bという型番の表面実装にて取り付けるタイプのLEDとなります。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-46.webp" | prepend: site.baseurl }})


左右それぞれ6個ずつ取り付けます。LEDは、基板上で直列に接続されています。下の写真に書かれた赤い矢印は、LEDの接続順を示しています。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-47.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-48.webp" | prepend: site.baseurl }})


Underglow LEDは、基板のLEFT-BACKおよびRIGHT-BACK側に取り付けます。取り付け場所は、正方形のマークが書かれている場所です。WS2812Bは方向があり、WS2812Bの四角の一つが三角形に欠けていて、その角を基板に書かれている三角形に合わせて取り付けます。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-49.webp" | prepend: site.baseurl }})


WS2812Bのハンダ付けは、熱に非常に弱いため、注意が必要です。ハンダ付けのコツは、一つのWS2812Bを一気に4カ所ハンダ付けをしないことです。具体的には、以下の手順で行うと良いでしょう。

1. WS2812Bを実装するランド（LED1つあたり4つ）に予備ハンダを乗せておく。
2. 片方の基板にWS2812B 6個を一つずつマスキングテープで固定する。
3. WS2812Bの4個あるうちの一つの端子のみをハンダ付けしていく。WS2812Bの6個それぞれで1カ所のみハンダ付けされている状態になる。
4. 前の手順を3回繰り返す。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-50.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-51.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-52.webp" | prepend: site.baseurl }})


上記の手順により、WS2812Bの温度上昇をできるだけ抑えた状態でハンダ付けを行うことができます。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-53.webp" | prepend: site.baseurl }})


もし「LEDが1つずつちゃんと点灯することを確認しながら取り付けたい」という場合は、急いでハンダ付けした結果熱でLEDが壊れた、とならないために十分にな時間と余裕を持って、接続されている順に一つずつ取り付けて、都度Raspberry Pi PicoにUSBケーブルを挿して通電させていきながら点灯するかどうか確認すると良いでしょう。

# キーソケットの取り付け

キースイッチを交換可能にするためのキーソケットを取り付けます。Lunakey Pico では、Cherry MX互換のキースイッチと、Kailh Choc V1のキースイッチの両方をサポートしていて、両方のキーソケットが同梱されています。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-54.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-55.webp" | prepend: site.baseurl }})


基板にCherry MX互換のキーソケットとKailh Choc V1のキーソケットのどちらかを取り付けることも可能ですし、両方を取り付けてしまうことも可能です。両方を取り付けた際には、気分に応じてキースイッチを取り替えることもできるようになります。

キーソケットは、キーの個数分（片側22個、両方で44個）取り付けます、基板のLEFT-BACKおよびRIGHT-BACK側に、キーソケットを取り付けるためのマークがありますので、それに合わせて取り付けを行っていきます。

キーソケットは基板にできるだけ密着した状態で固定することが望ましいので、キーソケットを細く切ったマスキングテープで基板に固定してからハンダ付けをすると良いでしょう。キーソケットの両側にある端子と、基板上にあるランドをハンダ付けしていきます。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-56.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-57.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-58.webp" | prepend: site.baseurl }})


先の細いハンダごて先をお持ちであれば、端子の間に差し込んでハンダを流し込むと固定させることができます。もし太いハンダごて先しかない場合は、基板上のランドに予備ハンダを盛ってからキーソケットをハンダ付けすると良いでしょう。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-59.webp" | prepend: site.baseurl }})


Kailh Choc V1向けのキーソケットの取り付けですが、実用上はほとんど問題にはならないのですが、取り付けには方向が存在しています。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-60.webp" | prepend: site.baseurl }})


基板に描かれたシルク（キーソケット部品の形状を示した白色の部分）には角が欠けた側と角が丸い側が存在します。Rev.5 以降の基板は正しい方向でシルクが描かれていますので、シルクの形状に合わせて取り付けてください。

しかし、Rev.4 では逆になってしまっています。Rev.4 の場合、シルクに合わせて取り付けてしまうと、キースイッチの取り付けに少し力が必要となります。「シルクの角のマークと実際の部品の方向を逆にして取り付ける」ようにすると、正しい向きとなりますので、ご注意ください。

参考: [Kailh Choc ソケットの方向について - Self-Made Keyboards in Japan](https://scrapbox.io/self-made-kbds-ja/Kailh_Choc_%E3%82%BD%E3%82%B1%E3%83%83%E3%83%88%E3%81%AE%E6%96%B9%E5%90%91%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6)

以下の写真が、正しい取り付け方向となります。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-61.webp" | prepend: site.baseurl }})


# カバープレートの取り付け

Raspberry Pi Picoが取り付けられている状態で、M2スペーサー（7.5mm）を片側2本ずつ、M2ネジ（5mm）を使って基板に取り付けます。

M2ネジを基板の裏側（先ほどキーソケットを取り付けた面と同じ側）から差し込んで、M2スペーサーをM2ネジに合わせてねじ込み、最後にドライバーを使って固定すると良いでしょう。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-62.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-63.webp" | prepend: site.baseurl }})


カバープレートには保護シートが接着されていますので、それを剥がします。剥がしにくい場合は、ぬるま湯につけると、糊が溶けて剥がしやすくなります。

M2スペーサーに、カバープレートをM2ネジ（5mm）を使って取り付けます。これにより、Raspberry Pi Picoの上にアクリル板が覆い被さるように配置されれば大丈夫です。

![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-64.webp" | prepend: site.baseurl }})



# トッププレート、キースイッチの取り付け


トッププレートは、キースイッチを取り付けるための穴が22個空いているアクリル板です。キースイッチを固定するための板となります。

トッププレートには保護シートが接着されていますので、それを剥がします。剥がしにくい場合は、ぬるま湯につけると、糊が溶けて剥がしやすくなります。

トッププレートには、6つの小さな丸い穴が開いています。この穴に、M2スペーサー（7.5mm）をM2ネジ（5mm）を使って取り付けます。このM2スペーサーは、基板に予め開いている穴を通って、ボトムプレートに固定されます。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-65.webp" | prepend: site.baseurl }})


トッププレートの穴それぞれに、キースイッチを差し込みます。設計として、トッププレートにキースイッチがうまく固定されるようにしてありますので、トッププレートにキースイッチを差し込む際には、少し力が必要となるでしょう。

特に、Kailh Choc V1のキースイッチをトッププレートに取り付ける際には、強めにプレートにはめ込んでください。もしプレートが割れることを懸念される場合は、ヤスリなどで穴の大きさを調整してください。

基板のキーソケットの穴に合わせて、キースイッチのピンを基板のLEFT-FRONTおよびRIGHT-FRONT側から差し込みます。

キースイッチの取り付けのコツとしては、以下の2つの方法があります。

* トッププレートの全ての穴にキースイッチを取り付けてしまうのではなく、トッププレートの外周のいくつかにキースイッチを取り付けた状態で基板に差し込むと、後からキースイッチを取り付けていく際にスムーズな作業となります。
* トッププレートの全ての穴にキースイッチを取り付けてしまって、基板にトッププレートを乗せて位置を合わせます。その後、トッププレートの中央付近のキースイッチから基板にピンを押し込んでいきます。これを中央から外側に向かって取り付けていきます。

どちらか良いかは、キースイッチの種類によって変わってきますので、上記2つのうちスムーズに取り付けができる手順を選択してください。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-66.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-67.webp" | prepend: site.baseurl }})


注意点としては、キースイッチから2本出ている端子がまっすぐに伸びている状態で基板に差し込んでいただくことと、親指で押すことになる4つのキーについては上下が逆となっているために、ピンの方向を基板にちゃんと合わせることを確認してから差し込んでください。

# ボトムプレートの取り付け

基板の裏側（先ほどトッププレートを取り付けた面と反対側）の6つの穴から、トッププレートに取り付けたM2スペーサー（7.5mm）が見えていると思います。このM2スペーサーに合わせて、ボトムプレートを取り付けます。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-68.webp" | prepend: site.baseurl }})


ボトムプレートには保護シートが接着されていますので、それを剥がします。剥がしにくい場合は、ぬるま湯につけると、糊が溶けて剥がしやすくなります。

ボトムプレートを基板の形に合わせてM2スペーサーの上に乗せます。そして、M2ネジ（5mm）を使って固定します。

# ゴム足の取り付け

ボトムプレートの6カ所にそれぞれゴム足を取り付けます。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-69.webp" | prepend: site.baseurl }})


# キーキャップの取り付け

最後に、キースイッチにキーキャップを取り付けて、Lunakey Picoの完成です。お疲れさまでした。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-70.webp" | prepend: site.baseurl }})


# 使用方法

組み立てが完了したLunakey Picoを使用する際には、以下を行ってください。

* TRRSケーブルを、左右のTRRSジャックにそれぞれ差し込んでください。
* USB Micro B ケーブルを、左手側のRaspberry Pi Picoに差し込み、PCと接続してください。


![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-71.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-72.webp" | prepend: site.baseurl }})



![]({{ "/images/2021/10/lunakey-pico-build-guide-rev1-73.webp" | prepend: site.baseurl }})


これにより、Lunakey Picoをご利用できるようになります。組み立てお疲れさまでした。Lunakey Pico が素敵なPCライフにお役に立てることを期待しています！


# キーボードのカスタマイズ

書き込んだファームウェアによって、キーボードのカスタマイズ方法が異なります。

## QMK Firmware

Lunakey Pico に QMK Firmware の uf2 ファイルを書き込んだ場合は、キーマップの変更や Underglow LED の光らせ方の変更については、QMK Firmware のビルドを行うことなく Remap というウェブアプリを使って手軽に変更することが可能です。

Remap は Chrome または Edge のバージョン 89 以降で利用することができます。以下の URL にアクセスしてください。

[https://remap-keys.app/](https://remap-keys.app/)

Lunakey Pico を PC に接続して、「START REMAP FOR YOUR KEYBOARD」ボタンを押してください。Lunakey Pico が Remap に認識され、キーマップが表示されます。もし表示されていない場合は、「+KEYBOARD」ボタンを押して、Lunakey Pico へのアクセスを許可してください。以下、画像は Lunakey Mini になっていますが、Lunakey Pico でも同様です。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-85.webp" | prepend: site.baseurl }})


左側にある数字を切り替えることで、レイヤを切り替えることができます。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-86.webp" | prepend: site.baseurl }})


Remap の画面の下には、設定可能なキーが表示されています。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-87.webp" | prepend: site.baseurl }})


各キーを押すことで、割り当てるキーを変更することができます。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-91.webp" | prepend: site.baseurl }})


キーの割り当ての変更は、右上にある FLASH ボタンを押すことで、キーボードに反映されます。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-92.webp" | prepend: site.baseurl }})


LIGHTING アイコンをクリックすることで、Underglow LED をどう光らせるかを変更させることが可能です。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-88.webp" | prepend: site.baseurl }})


より詳しい Remap の操作方法は、以下をご覧になってください。

[（初心者編）Remapを使ってキーマップを書き換えよう - 自作キーボード温泉街の歩き方](https://salicylic-acid3.hatenablog.com/entry/remap-manual)

## PRK Firmware

PRK Firmware を Raspberry Pi Pico に書き込んだときは、ファイルシステムとして OS にマウントされて、keymap.rb ファイルが見える状態になっています。keymap.rb ファイルはテキストファイルですので、テキストエディタを使って内容を変更することが可能です。

キーマップを変更したいときは、keymap.rb ファイルの下の方にある kbd.add_layer の設定内容を書き換えます。ファイルを保存すると、直ちに新しい内容が反映されます。

また PRK Firmware は、キーマップの変更については Remap というウェブアプリを使って手軽に変更することが可能です。

Remap は Chrome または Edge のバージョン 89 以降で利用することができます。以下の URL にアクセスしてください。

[https://remap-keys.app/](https://remap-keys.app/)

Lunakey Pico を PC に接続して、「START REMAP FOR YOUR KEYBOARD」ボタンを押してください。Lunakey Pico が Remap に認識され、キーマップが表示されます。もし表示されていない場合は、「+KEYBOARD」ボタンを押して、Lunakey Pico へのアクセスを許可してください。以下、画像は Lunakey Mini になっていますが、Lunakey Pico でも同様です。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-85.webp" | prepend: site.baseurl }})


左側にある数字を切り替えることで、レイヤを切り替えることができます。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-86.webp" | prepend: site.baseurl }})


Remap の画面の下には、設定可能なキーが表示されています。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-87.webp" | prepend: site.baseurl }})


各キーを押すことで、割り当てるキーを変更することができます。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-91.webp" | prepend: site.baseurl }})


キーの割り当ての変更は、右上にある FLASH ボタンを押すことで、キーボードに反映されます。


![]({{ "/images/2020/12/lunakey-mini-build-guide-rev4-92.webp" | prepend: site.baseurl }})


より詳しい Remap の操作方法は、以下をご覧になってください。

[（初心者編）Remapを使ってキーマップを書き換えよう - 自作キーボード温泉街の歩き方](https://salicylic-acid3.hatenablog.com/entry/remap-manual)


## KMK Firmware

KMK Firmware を Raspberry Pi Pico に書き込んだときは、ファイルシステムとして OS にマウントされて、main.py ファイルが見える状態になっています。main.py ファイルはテキストファイルですので、テキストエディタを使って内容を変更することが可能です。

キーマップを変更したいときは、main.py ファイルの下の方にある keyboard.keymap の設定内容を書き換えます。ファイルを保存すると、直ちに新しい内容が反映されます。

