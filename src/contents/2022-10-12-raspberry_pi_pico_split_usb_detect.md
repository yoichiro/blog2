---
layout: post
title: QMK FirmwareとRaspberry Pi PicoでSPLIT_USB_DETECTを使わない方法
categories:
- keyboard
---

Raspberry Pi Pico を使った左右分割型のキーボードである Lunakey Pico ですが、PC の起動時に QMK Firmware がハングアップしてしまうのか、正常に起動しない不具合に最近悩まされていました。


![]({{ "/images/2022/10/raspberry-pi-pico-split-usb-detect-1.webp" | prepend: site.baseurl }})


PC の電源投入後に OS が起動してきたかなぁ？ってタイミングで USB ケーブルを挿し直してあげると、正しくファームウェアが起動して普通に使えるようになります。つまり、PC の電源を入れて使い始める度に「USB ケーブルを抜き差しする」という儀式をしないといけなかったわけで、これが毎日かなりのストレスになっていました。

QMK Firmware の Issues には、RP2040 関連の不具合が他にも報告されていて（ [[Bug] RP2040 based keyboard not usable from boot #18511](https://github.com/qmk/qmk_firmware/issues/18511) とか [[Bug] Update ChibiOS-Contrib for USB IRQ and bus handling fixes #18574](https://github.com/qmk/qmk_firmware/pull/18574) とか）、これらの影響を受けてるのかなぁ、と想像していました。

念の為に、Raspberry Pi Pico でうまく動かないよ、とコミッタ達に伝えるために、自分でも Issue を作って報告しました。

[[Bug] The slave side hangs up at cold start of PC in the case of a split keyboard with Raspberry Pi Pico · Issue #18571 · qmk/qmk_firmware](https://github.com/qmk/qmk_firmware/issues/18571#issuecomment-1275250692)

# masterブランチに潜んでいた不具合


で、数日他の Issues を追っていたりしてたのですが、どうやら master ブランチだと「chibios-contrib ライブラリの中にあるUSB ドライバに致命的な問題がある」ということで、左右分割型でなくとも RP2040 を使ったキーボードの PC コールドスタート時に不具合が生じるという状態だったようです。

develop ブランチを使えば動作が改善するかな？と思い、develop ブランチの最新を持ってきて、サブモジュールを取得し直し、qmk コマンドを使ってビルドしなおしてみたものの、状況は改善せず。相変わらず PC のコールドスタート時にキーボードがハングアップしてしまって使えない状況になっています。

QMK Firmware の RP2040 対応は今年の 8 月に master ブランチにマージされた、比較的新しい機能です。この RP2040 対応では、SPLIT_USB_DETECT がデフォ ON 状態であることを知っていました。具体的には、 qmk_firmware/platforms/chibios/chibios_config.h ファイルに、こんなコードが仕込まれています。


```
#ifndef USB_VBUS_PIN
#    define SPLIT_USB_DETECT // Force this on when dedicated pin is not used
#endif
```

SPLIT_USB_DETECT により、USB 接続の検知ではなく、USB 通信の検知によって、USB ケーブルが接続されている側、つまり master がどっちなのかが検知される仕組みです。これが機能しているはずですが、PC コールドスタート時に失敗してしまっています。

# USB通信検知のタイムアウト値の調整


この SPLIT_USB_DETECT が失敗したときに、これを調整するための手段が提供されています。それが、SPLIT_USB_TIMEOUT と SPLIT_USB_TIMEOUT_POLL です。それぞれ初期値が、

* SPLIT_USB_TIMEOUT = 2000
* SPLIT_USB_TIMEOUT_POLL = 10

となっています。これは、10ms 間隔で 2000ms まで USB 通信があるかどうか確認する、という意味になります。つまり、PC のコールドスタート時に USB 通信が安定する（master 側で USB 通信がちゃんと検知できる状態）になる前にきっとタイムアウトを迎えてしまっているのではないか、という失敗原因が想像できます。これに対処するには、タイムアウトする時間を伸ばしてあげれば良いわけです（OS 起動後には USB 抜き差しで大丈夫なわけですから）。

そこで、以下のようにちょっと長めに設定してみました。


```
#define SPLIT_USB_TIMEOUT 5000
#define SPLIT_USB_TIMEOUT_POLL 25
```

僕の PC での結果としては、正しく USB 通信を検知できるようになったようで、USB ケーブルの抜き差しをすることなく、PC コールドスタート時に Lunakey Pico は正しく使える状態になりました。

# なぜRP2040ではSPLIT_USB_DETECTが有効になっているのか


よし、めでたしめでたし... としたかったところですが、どうしても心のモヤモヤが取れていません。それは、

* なぜ RP2040 の場合は SPLIT_USB_DETECT がデフォ ON なのか？

です。「そういうもんだ」と割り切ってしまっても良かったのですが、やはり理由がわからないと気持ちが悪いです。

そこで、Self-Made Keyboards in Japan コミュニティの Discord サーバにて、理由を聞いてみました。 [せきごんさん](https://twitter.com/_gonnoc) が [教えてくれました](https://discordapp.com/channels/376937950409392130/448305600372408326/1029332640219803690) 。

> atmega32u4はマイコンを動作させるための電源ピンと、USBを動作させるための5Vピンが分かれています。pro microのようにこれらのピンがダイオードで分離されていれば、USB用の電源の有無でマスターかどうか判別できます。
> 一方RP2040などではこれらの電源が分かれていないため、USB通信が確立しているかどうかを使って判別する=SPLIT_USB_DETECTを使う必要があります

なるほど、USB 端子から電源を得ているかどうかを検知できれば良いが、RP2040 ではそれが難しいから、という理由でした。このことは、後日 QMK Firmware の Discord サーバにて sigprof さんからも回答をもらうことができました。

> Yes, hardware VBUS detection is much better than the SPLIT_USB_DETECT workaround. Unfortunately, lots of controller boards (e.g., many Pi Pico lookalikes with USB Type-C that are available on AliExpress) don't support VBUS detection properly (some of them just require allocating a pin for VBUS detection and adding an external voltage divider, but some others either don't have VBUS broken out, or don't have an exposed bidirectional power pin that is separated from VBUS by a diode).

訳: はい、ハードウェア VBUS 検知は、SPLIT_USB_DETECT 代替案よりもずっと良い方法です。残念なことに、多くの制御ボード（例: AliExpress で入手可能な USB Type-C を持つ多くの Pi Pico 互換のもの）は VBUS 検知を正しくサポートしていません（それらのいくつかは、VBUS 検知の端子の割り当てと外部電源との分離の追加が少し必要になりますが、他のものについては VBUS が分離されてないか、ダイオードによって VBUS から分離されている露出された双方向の電源端子を持っていません）。

つまり、RP2040 を採用したマイコンボードで Pi Pico 互換のものの多くが、VBUS 検知ができない回路になっているため、QMK Firmware では VBUS を master かどうかの判断に使うことができない可能性が大きく、SPLIT_USB_DETECT をデフォで使うようにした、ということのようです。

# Raspberry Pi PicoではVBUS検知ができる


では、今の僕が対象としている Lunakey Pico に搭載された Raspberry Pi Pico では、VBUS 回りの回路はどうなっているのでしょうか？ [Raspberry Pi Pico のデータシート](https://datasheets.raspberrypi.com/pico/pico-datasheet.pdf) から、回路図を持ってきました。


![]({{ "/images/2022/10/raspberry-pi-pico-split-usb-detect-2.webp" | prepend: site.baseurl }})


USB 端子の 1 番端子から入ってきた電源は、そのまま VBUS 端子（Raspberry Pi Pico の VBUS 端子）に流されています。その先を見てみると、プルアップ抵抗の間に、GPIO24 端子につながっています。これは、USB 端子から電源供給が行われた際には、GPIO24 端子は HIGH の状態になるということになります。

もう一方の分岐には、ダイオードが挟まっていて、その先に VSYS 端子があります。VSYS 端子から電源を入力すると、それはダイオードの作用で VBUS 端子には流れずに、マイコン側にのみ供給されます。これは、USB 端子からではなく、VSYS 端子から電源を供給した場合には、GPIO24 端子には電源がいかず、結果として GPIO24 端子は LOW の状態になるということになります。

そう、Raspberry Pi Pico は、GPIO24 端子によって、VBUS 検知ができる、ということです。

Lunakey Pico では、master 側（= USB ケーブルが挿してある側）は USB 端子から入ってくる電源を使い、slave 側（= USB ケーブルが挿さってない側）は master 側から TRRS ケーブル経由で電源を流して、VSYS 端子に入力しています。これにより、Raspberry Pi Pico を採用している Lunakey Pico では、VBUS 検知による master 側と slave 側の判断ができるはずだ、と推測できます。


![]({{ "/images/2022/10/raspberry-pi-pico-split-usb-detect-3.webp" | prepend: site.baseurl }})


実際にやってみました。develop ブランチにて、以下のように config.h ファイルに設定して、ビルドしました。


```
// #define SPLIT_USB_TIMEOUT 5000
// #define SPLIT_USB_TIMEOUT_POLL 25
#define USB_VBUS_PIN GP24
```

結果は大成功！SPLIT_USB_DETECT が使われなくなったので、PC コールドスタート時にも遅延が全くない状態ですぐに左右が判別され、正しくファームウェアが動作するようになりました。

# まとめ


SPLIT_USB_DETECT のタイムアウト時間を長くしたことでキーボードは使えるようになったものの、タイムアウトするまでの間はキーボードを使うことができません。その間に BIOS の設定画面に行くための猶予時間が過ぎてしまうために、SPLIT_USB_DETECT を使っていては不便なことが残ったままだなぁ、とモヤモヤしていました。

USB_VBUS_PIN により SPLIT_USB_DETECT に頼らないで済むようになって、一瞬でキーボードが利用可能になりましたので、BIOS の設定画面にもすんなり入ることができるようになりました。これで、懸念は一切なくなり、気持ちよく安心して Lunakey Pico を使えるようになりました。

もし Raspberry Pi Pico を使った左右分割キーボードをお使いの方や設計しようとしている方がいらっしゃいましたら、このブログエントリの内容が参考になると幸いです。

