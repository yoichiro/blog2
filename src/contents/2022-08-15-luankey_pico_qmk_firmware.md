---
layout: post
title: Lunakey PicoでQMK Firmwareを動かしてみました
categories:
- keyboard
---

自作キーボードのファームウェアとして最も有名で広く使われている QMK Firmware ですが、今までは Atmega32u4 や STM32 などで使われることがほとんどだったかと思います。最近では、RP2040 という MCU が台頭してきていて、RP2040 を載せた MCU が増えてきました。ProMicro とピン構成などに互換がある RP2040 搭載 MCU も登場していて、KMK Firmware や PRK Firmware といったファームウェアが使われています。

もちろん、QMK Firmware の RP2040 対応はその登場当時から要望されていました。そして今日では、 [QMK Firmware の develop ブランチ](https://github.com/qmk/qmk_firmware/tree/develop) に、RP2040 対応のコードセットがマージされています。

僕も Lunakey Pico という Raspberry Pi Pico を使った左右分割型の 40% キーボードを BOOTH にて販売していますが、これは RP2040 になります。つまり、QMK Firmware の develop ブランチを使えば、Lunakey Pico にて QMK Firmware が動くはずです。

さっそくやってみました。このブログエントリでは、動かしてみた結果をシェアしたいと思います。

# Lunakey Pico とは

[Lunakey Pico](https://booth.pm/ja/items/3324672) は、RP2040 を搭載した Raspberry Pi Pico を使った左右分割型の 40% キーボードです。


![]({{ "/images/2022/08/lunakey-pico-qmk-firmware-1.webp" | prepend: site.baseurl }})


左右のキーボードは、TRRS ケーブルを使って接続されています。具体的には、左の GP0 と右の GP1、左の GP1 と右の GP0 をそれぞれ結線しています。これにより、2本の接続を使った全二重でのシリアル通信ができるようにしています。


![]({{ "/images/2022/08/lunakey-pico-qmk-firmware-2.webp" | prepend: site.baseurl }})


# RP2040 対応のコード

では、Lunakey Pico に対応した QMK Firmware を動かすためのコードを紹介していきます。ファイルを置く場所は、keyboards/yoichiro/lunakey_pico ディレクトリです。主に「分割キーボードとしてちゃんと機能するための記載内容」について説明します。

まずは、rules.mk ファイルです。


```
MCU = RP2040
BOOTLOADER = rp2040

BOOTMAGIC_ENABLE = no
MOUSEKEY_ENABLE = yes
EXTRAKEY_ENABLE = yes
CONSOLE_ENABLE = yes
COMMAND_ENABLE = no
NKRO_ENABLE = no
BACKLIGHT_ENABLE = no
RGBLIGHT_ENABLE = yes
RGBLIGHT_SPLIT_ENABLE = yes
AUDIO_ENABLE = no
SPLIT_KEYBOARD = yes

LAYOUT = LAYOUT_split_3x6_4

WS2812_DRIVER = vendor
SERIAL_DRIVER = vendor

# BOARD = GENERIC_RP_RP2040
```

MCU と BOOTLOADER に RP2040 を指定することで、このキーボードが RP2040 対応のものと宣言されます。***_ENABLE 系の記載は、QMK Firmware をお使いの方にとっては馴染みのある記載ばかりかと思います。

Lunakey Pico は左右分割キーボードですので、SPLIT_KEYBOARD = yes の記載が必要になります。僕はこの記載を忘れてしまい、1日以上無駄な時間を過ごしてしまいました。

RP2040 対応でもう一つポイントが、SERIAL_DRIVER = vendor です。この記載も忘れずに書いておきましょう。BOARD = GENERIC_RP_RP2040 を定義すると、いろんな定義を全部自分でやらないといけなくなるので、今回の動作確認では無指定にしました。

次は、info.json ファイルです。


```
{
  "keyboard_name": "yoichiro/lunakey_pico",
  "url": "",
  "maintainer": "yoichiro",
  "layouts": {
    "LAYOUT_split_3x6_4": {
      "layout": [
        …
      ]
    }
  }
}
```

本来であれば、今まで config.h ファイルなどに記載してきた内容を、この info.json ファイルに書くことになります。個人的に info.json ファイルに記載するやり方に慣れていないということもあり、余計な問題を発生させたくなかったので、今回の動作確認では古い書き方を採用しています。そのため、info.json ファイルについては、QMK Configurator 向けのレイアウト定義のみ記載しています。RP2040 対応とは関係ないですね。

次のファイルは、lunakey_pico.h です。


```
#pragma once

#include "quantum.h"

#define LAYOUT_split_3x6_4( \
  L00, L01, L02, L03, L04, L05,           R00, R01, R02, R03, R04, R05, \
  L10, L11, L12, L13, L14, L15,           R10, R11, R12, R13, R14, R15, \
  L20, L21, L22, L23, L24, L25,           R20, R21, R22, R23, R24, R25, \
                 L30, L31, L32, L33, R30, R31, R32, R33 \
  ) \
  { \
    { L00, L01, L02, L03, L04, L05 }, \
    { L10, L11, L12, L13, L14, L15 }, \
    { L20, L21, L22, L23, L24, L25 }, \
    { KC_NO, KC_NO, L30, L31, L32, L33 }, \
    { R05, R04, R03, R02, R01, R00 }, \
    { R15, R14, R13, R12, R11, R10 }, \
    { R25, R24, R23, R22, R21, R20 }, \
    { KC_NO, KC_NO, R33, R32, R31, R30 } \
  }
```

これも、キーレイアウトを定義しているだけで、RP2040 に特化した話ではなく、どの MCU に対しても必要となる記載ですね。

lunakey_pico.h と対になるファイルが、lunakey_pico.c ファイルです。


```
#include "lunakey_pico.h"

void keyboard_post_init_kb(void) {
    debug_enable   = true;
    debug_matrix   = true;
    debug_keyboard = true;
    debug_mouse    = true;
    keyboard_post_init_user();
}
```

keyboard_post_init_kb 関数にて、デバッグ向けの出力を有効にしています。今回の検証で動かない原因を知るために記載しましたが、正しく動作するようになったら消しても問題ありません。

さて、次はとても重要な定義を持つ config.h ファイルです。


```
#pragma once

#define VENDOR_ID    0x5954
#define PRODUCT_ID   0x0003
#define DEVICE_VER   0x0003
#define MANUFACTURER yoichiro
#define PRODUCT Lunakey Pico

#define MATRIX_COL_PINS { GP21, GP20, GP19, GP18, GP17, GP16 }
#define MATRIX_ROW_PINS { GP12, GP13, GP14, GP15 }

#define MATRIX_ROWS 8
#define MATRIX_COLS 6

#define DIODE_DIRECTION COL2ROW

#define DEBOUNCE 5

#define RP2040_BOOTLOADER_DOUBLE_TAP_RESET
#define RP2040_BOOTLOADER_DOUBLE_TAP_RESET_LED GP25
#define RP2040_BOOTLOADER_DOUBLE_TAP_RESET_TIMEOUT 500U

#define SERIAL_USART_FULL_DUPLEX
#define SERIAL_USART_TX_PIN GP0
#define SERIAL_USART_RX_PIN GP1

#define SERIAL_DEBUG

#define WS2812_PIO_USE_PIO1

#define RGB_DI_PIN GP6
#ifdef RGB_DI_PIN
  #define RGBLED_NUM 12
  #define RGBLIGHT_SPLIT
  #define RGBLED_SPLIT { 6, 6 }
  #define RGBLIGHT_LED_MAP { 0, 1, 2, 3, 4, 5, \
                             11, 10, 9, 8, 7, 6 }
  #define RGBLIGHT_HUE_STEP 8
  #define RGBLIGHT_SAT_STEP 8
  #define RGBLIGHT_VAL_STEP 8
  #define RGBLIGHT_LIMIT_VAL 255
  #define RGBLIGHT_SLEEP
  #define RGBLIGHT_EFFECT_RAINBOW_SWIRL
#endif

#define TAPPING_TERM 500
#define PERMISSIVE_HOLD
```

VENDOR_ID や PRODUCT_ID などの定義は今までどおりです。MATRIX_COL_PINS と MATRIX_ROW_PINS についても、列と行に対応するピンの名称を指定してあげれば大丈夫です。

RP2040 対応でポイントとなる記載は、まずは以下です。


```
#define RP2040_BOOTLOADER_DOUBLE_TAP_RESET
#define RP2040_BOOTLOADER_DOUBLE_TAP_RESET_LED GP25
#define RP2040_BOOTLOADER_DOUBLE_TAP_RESET_TIMEOUT 500U
```

これは利便性のための設定であり必ずしも必要ってわけではないですが、とても便利になるので使っています。Lunakey Pico では、ピン番号 30（RUN）と GND の間に、タクトスイッチを設けています。これは、リセットボタンとして機能してほしくて設置しました。RP2040_BOOTLOADER_DOUBLE_TAP_RESET を指定しておくことで、そのタクトスイッチを 2 回すばやく押すことでブートローダーを起動することができるようになります。



![]({{ "/images/2022/08/lunakey-pico-qmk-firmware-3.webp" | prepend: site.baseurl }})


そして、最も重要な定義が、以下です。


```
#define SERIAL_USART_FULL_DUPLEX
#define SERIAL_USART_TX_PIN GP0
#define SERIAL_USART_RX_PIN GP1

#define SERIAL_DEBUG
```

Lunakey Pico は、左右の TX と RX をそれぞれクロスさせて結線している「全二重」方式を想定していますので、SERIAL_USART_FULL_DUPLEX を指定しています。そして、TX と RX のそれぞれのピンを GP0, GP1 として教えてあげています。

SERIAL_DEBUG を指定しておくと、qmk console でデバッグ出力を覗いたときに、シリアル通信のネゴシエーション状況などが出てくるので、うまくいかなったときの原因を知る手がかりを得られます。正しく通信できるようになったら、消してしまってもよいかと思います。

config.h ファイルの記載内容だけですと「全二重で通信する」としか定義されていません。実際にどの通信仕様を使うかを決める必要があります。

最初に、SIO を使った通信について必要となる記載を紹介しましょう。halconf.h ファイルを準備します。


```
#pragma once

#define HAL_USE_SIO TRUE

#include_next <halconf.h>
```

HAL_USE_SIO に TRUE を指定することで、SIO を適用することが宣言されます。これに加えて、mcuconf.h ファイルを作ります。


```
#pragma once

#include_next <mcuconf.h>

#undef RP_SIO_USE_UART0
#define RP_SIO_USE_UART0 TRUE
#undef RP_SIO_USE_UART1
#define RP_SIO_USE_UART1 FALSE
```

Raspberry Pi Pico の GP0, GP1 は、UART0 に対応しているため、RP_SIO_UART0 に TRUE を指定しています。そして、念の為に RP_SIO_UART1 に FALSE を指定して無効にしています。

SIO を適用する場合は、上記の halconf.h と mcuconf.h の 2 つのファイルを作ってあげれば良いです。

次に、PIO を適用したい場合ですが、記載内容は PIO の方が簡単です。halconf.h と mcuconf.h ファイルを作る必要はありません。その代わりに、config.h ファイルに以下を追記するだけです。


```
#define SERIAL_PIO_USE_PIO1
```

簡単ですね！

あとは、keymaps/default ディレクトリや keymaps/via ディレクトリを作って、今までどおりに keymap.c ファイルや rules.mk ファイルを置いてあげれば良いです。それらの中では、RP2040 に特化した内容は基本的に出てこないかと思います。

# ビルド方法

上記のファイル群を作り終わったら、以下のコマンドを使って、ビルドします。


```
qmk compile -kb yoichiro/lunakey_pico -km default
```

記載内容がすべて正しければ、yoichiro_lunakey_pico_default.uf2 という名前のような uf2 ファイルが生成されます。これを RP2040 に送り込めば完了です。

Raspberry Pi Pico の場合は、BOOT ボタンを押したまま USB ケーブルを刺すことで、ブートローダーが起動してファイルマウントされますので、uf2 ファイルをそこにコピーすることで、QMK Firmware が動き出します。Lunakey Pico の場合は、左右両方の Raspberry Pi Pico にそれぞれ uf2 ファイルを送り込みます。

上記で説明したファイルの記載内容には、Underglow LED の定義も含まれています。そのため、uf2 ファイルを送り込んだ後に、LED が点灯します。左右ともに LED が連動して点灯していることがわかります。

# まとめ

QMK Firmware の develop ブランチに入った RP2040 対応について、Lunakey Pico で検証を行ってみました。AUDIO 機能はまだなので音は鳴りませんが、分割キーボードとしての機能と、Underglow LED の点灯まで動作確認することができました。

また、rules.mk ファイルに VIA_ENABLE = yes を記載してビルドすることで、VIA の機能も有効にできます。Remap でキーマップのカスタマイズやテストマトリクス、Underglow LED の調整ができることを確認しました。

来月あたりには develop ブランチの内容が master ブランチに入るとは思いますので、いよいよ QMK Firmware の RP2040 MCU への展開が進んでいくと期待できます。もし RP2040 を使ったキーボードをお使いの方がいらっしゃいましたら、このブログエントリの内容が参考になると嬉しいです。

