---
layout: post
title: Remapがファームウェア書き込みでやってること
categories:
- keyboard
---

このエントリは、 [キーボード #1 Advent Calendar 2021](https://adventar.org/calendars/6246) の 21 日目の記事です。

昨年は [ウェブブラウザからキーマップを書き換える「WebVIA」を開発中です](https://www.eisbahn.jp/yoichiro/2020/12/webvia.html) という記事を書きましたが、WebVIA は Remap という名前となり、今では登録キーボード数が 250 を超えていて、毎日のキーマップ書き込み数も 100 〜 300 回行われる規模まで成長しました。

[Remap](https://remap-keys.app/)

Remap を開発し始めて、キーマップカスタマイズ、キーマップシェア、キーボードカタログ、キーマクロ、マトリクステスト、LED カスタマイズなど、数多くの機能を実装してきました。その中でも今年自分にとってチャレンジだった開発が、

「ファームウェア書き込み」機能

でした。


![]({{ "/images/2021/12/remap-bootloader-1.webp" | prepend: site.baseurl }})


キーボードに搭載されたマイコン（MCU）にファームウェアを「ウェブブラウザから書き込む」ということができれば、キーボードキットを組み立てたあとに「ウェブブラウザさえあれば Remap によってキーボードを利用するための準備が整う」という環境を皆さんに提供することができるのでは、と考えました。今までは、ファームウェアを書き込むために、専用のアプリケーションをインストールしたり、黒い画面（コマンドプロンプトなど）から複雑で難解なコマンドを実行したりすることが必要でした。

「どう考えても初心者には無理だろ」ということから卒業したく、試行錯誤を繰り返して、無事開発に成功しました。このエントリを執筆している現在では、ProMicro や Elite-3 といった MCU であれば、ウェブブラウザから Remap を使ってファームウェアを書き込むことが可能です。具体的には、Catarina ブートローダーと、Atmel-DFU ブートローダーの2つに対応しています。


![]({{ "/images/2021/12/remap-bootloader-2.webp" | prepend: site.baseurl }})


このエントリでは、ファームウェアを書き込むために、Remap が MCU とどんなやり取りを行っているのか、雑に紹介したいと思います。ちなみに、異常系の処理は省いていますので、詳細は [ソースコード](https://github.com/remap-keys/remap/tree/main/src/services/firmware) をご覧ください。

# ブートローダーって何ですか？


そもそもブートローダーってなによ？って方も多いかと思いますので、簡単に紹介しておきます。

もしかしたら PC に詳しい方であれば、「あ、ディスクからまず読み込まれて OS を起動してくれるプログラムのことね」「IPL でしょ？」と思うかもしれません。

マイコンは、何らかの記憶装置に書き込まれたプログラムに従って動作を行います。つまり、マイコンに何か仕事をさせたいのであれば、プログラムをその記憶装置に書き込まなければなりません。

一般的にはその記憶装置はフラッシュメモリであることが多いのですが、フラッシュメモリにプログラムを送り込むには、専用のハードウェアが必要になります。デバッグユニットと呼ばれるみたいですが、マイコンにアクセスするための専用のピンや端子経由でフラッシュメモリにアクセスし、プログラムを送り込みます。

ただ、デバッグユニットを使ったプログラムの書き込みは、ハードウェアを準備するのも手間ですし、誰でもできることではありません。ひじょーに面倒です。

マイコン内の記憶装置にもっと気軽にアクセスできて、特殊なハードウェアを準備することなくプログラムの書き換えができると良さそうだよね、という発想で生み出された機構が、ブートローダーです。

ブートローダーがマイコンに存在することで利用可能なメモリ量は減ってしまいますが、ブートローダーはマイコンに搭載された通信インタフェース（USB や UART など）経由でプログラムを記憶装置に書き込んでくれます。ブートローダーがいてくれることで、マイコンを PC に繋いでプログラムを送り込むことが簡単な手順で行えるようになります。頼もしいですね。


![]({{ "/images/2021/12/remap-bootloader-3.webp" | prepend: site.baseurl }})


マイコンの種類によって方法は異なりますが、RESET ボタンを長押しするとか、BOOTSEL ボタンを押したままケーブルを差し込む、など行うことで、マイコンは書き込まれたユーザプログラムの実行ではなく、ブートローダーが起動します。ブートローダーモードになったら、適切な手順で通信してあげることで、ユーザプログラムの書き込みや更新を行うことでできる、という仕組みです。

よく「ProMicro が文鎮化してしまった！」という発言を見かけますが、これを見かけたら「あー、ブートローダー壊れちゃったのかな」と思うとだいたい正解です。ブートローダーの復旧には、前述した専用ハードウェアを使ったアクセスが必要になります。

ブートローダーにはいくつか種類があります。Remap では、Catarina という名前のブートローダーと、DFU という名前のブートローダーをサポートしました。ブートローダーが違うと、プログラムを書き込むために必要となる通信の手順が全く異なってきます。それぞれ異なる開発が必要です。

では、具体的にそれぞれのブートローダーと Remap がどんなやり取りをしているのか、見ていきましょう。

# Catarinaブートローダー


Catarina ブートローダーの場合は、Remap は [WebSerial API](https://wicg.github.io/serial/) を使ってファームウェアの書き込みを行います。


![]({{ "/images/2021/12/remap-bootloader-4.webp" | prepend: site.baseurl }})


MCU のシリアルポートに接続してオープンしたあと、最初にやることは「 [初期化](https://github.com/remap-keys/remap/blob/main/src/services/firmware/caterina/CaterinaBootloader.ts#L226) 」です。といっても、いくつかの情報を MCU から取得して、その内容を確認する処理となります。

* Software Identifier のチェック
  * 'S' を MCU に送信。
  * MCU から 7 バイト受信。
  * 結果が 'CATERIN' かどうかチェック。
* バージョン番号の取得
  * 'v' を MCU に送信。
  * MCU から 1 バイト受信。
  * 結果が '?' だった場合は、MCU からさらに 1 バイト受信。
  * `{1st byte}.{2nd byte} がバージョン番号。
* Program Type の取得
  * 'p' を MCU に送信。
  * MCU から 1 バイト受信。
* Auto Address Increment Support の取得
  * 'a' を MCU に送信。
  * MCU から 1 バイト取得。
  * 結果が 'Y' の場合は、Auto Address Increment Support が有効。
* Buffer Access, Buffer size の取得
  * 'b' を MCU に送信。
  * MCU から 1 バイト取得。
  * 結果が 'Y' の場合は、Buffer Access が有効。
  * MCU から 2 バイト取得。
  * `{1st byte} << 8 | {2nd byte}` が Buffer Size 値。
* Device Type の取得と設定
  * 't' を MCU に送信。
  * MCU から 1 バイト受信。- (1)
  * 0 以外の数値が得られるまで、MCU から受信。
  * 'T' と (1) 値を MCU に送信。
* Extended FUSE Bits の取得
  * 'Q' を MCU に送信。
  * MCU から 1 バイト取得。
* Low Fuse Bits の取得
  * 'F' を MCU に送信。
  * MCU から 1 バイト取得。
* High Fuse Bits の取得
  * 'N' を MCU に送信。
  * MCU から 1 バイト取得。
* Lock Bits の取得
  * 'r' を MCU に送信。
  * MCU から 1 バイト取得。

以上が初期化処理です。様々な値を MCU から取得していますが、以降の処理で使っているのは Buffer Size のみです。他の値は、UI 上に表示をしていて、書き込み処理で何か問題が発生したときの手がかりとして情報提供しています。

次に、 [Program Mode に入ります](https://github.com/remap-keys/remap/blob/main/src/services/firmware/caterina/CaterinaBootloader.ts#L384) 。これは、MCU に 'P' を送信するのみです。

ファームウェアを書き込む前に、 [フラッシュメモリのアプリケーションセクションをクリア](https://github.com/remap-keys/remap/blob/main/src/services/firmware/caterina/CaterinaBootloader.ts#L392) しておきます。これは、MCU に 'e' を送るだけですが、消去に時間がかかることを想定して、6000 ms だけ長めに MCU からの結果を待っています。

では、いよいよ [ファームウェアの書き込み](https://github.com/remap-keys/remap/blob/main/src/services/firmware/caterina/CaterinaBootloader.ts#L338) です。

* アドレスを 0 にセット
  * 'A', Buffer Size を MCU に送信。
* バイト列の書き込み
  * 'B', Buffer Size 値, 'F' (フラッシュメモリ), Buffer Size 分のバイト列を MCU に送信。- (2)
  * ファームウェアのバイト列を全部 MCU に送信するまで (2) を繰り返す。

ファームウェアを書き込んだあとは、MCU に書き込まれたバイト列を取得して、正しく書き込みが行われたかどうかを [検証](https://github.com/remap-keys/remap/blob/main/src/services/firmware/caterina/CaterinaBootloader.ts#L307) します。

* アドレスを 0 にセット
  * 'A', Buffer Size を MCU に送信。
* バイト列の読み込み
  * 'g', Buffer Size 値, 'F' (フラッシュメモリ) を MCU に送信。- (3)
  * MCU から Buffer Size 分だけ受信。- (4)
  * 先ほど書き込んだバイト数だけ、(3), (4) を繰り返し、最後に全部連結して一つのバイト列にする。
* 書き込んだバイト列と、MCU から読み込んだバイト列が一致するか検証。

検証結果が無事一致していれば、 [Program Mode から抜けます](https://github.com/remap-keys/remap/blob/main/src/services/firmware/caterina/CaterinaBootloader.ts#L388) 。これは、MCU に 'L' を送信するのみです。

最後に、一連の処理の [終了を MCU に告げます](https://github.com/remap-keys/remap/blob/main/src/services/firmware/caterina/CaterinaBootloader.ts#L380) 。これは、MCU に 'E' を送信します。あとはシリアルポートを閉じて、Catarina ブートローダーによるファームウェアの書き込み処理は完了となります。

# Atmel DFUブートローダー


Remap で最初に対応したブートローダーは、Catarina ブートローダーでした。ProMicro という自作キーボードキットで広く使われている MCU 向けには Catarina ブートローダーで対応できるのですが、MCU はそれだけではありません。例えば、同じ Atmega32u4 であっても、ProMicro として販売されているものには Catarina ブートローダーが書き込まれていますが、ATmega32u4 を PCB に直接実装している場合は、異なるブートローダーが書き込まれていることが珍しくありません。

Catarina ブートローダーの次に対応すべきブートローダーを何にするか調査をしてみたところ、DFU が良さそうだという結論に達しました。そこでさらに DFU について仕様を調査しまくったのですが、なんと DFU は方言だらけであることが判明しました。Atmel 製の MCU でも種類によって異なっていますし、STM32 系とも異なっていました。

DFU は「標準仕様」として策定されたはずだよなぁ、と思いつつ、嘆いていても先に進めません。国内のキーボードで多く採用されている MCU の種類から、Atmel-DFU という種類の DFU ブートローダーをサポートすることにしました。

Atmel-DFU ブートローダーは、USB のプロトコルを直接話すことで MCU にファームウェアを書き込みに行きます。Remap では、 [WebUSB API](https://wicg.github.io/webusb/) を使っています。


![]({{ "/images/2021/12/remap-bootloader-5.webp" | prepend: site.baseurl }})


MCU と Web USB API にて接続しオープンしたあと、まずは [サポート可能な MCU かどうかを検証](https://github.com/remap-keys/remap/blob/main/src/services/firmware/dfu/DfuBootloader.ts#L199) します。これには、Vendor ID と Product ID を利用します。予めサポート可能な MCU のリストを [ここ](https://github.com/remap-keys/remap/blob/main/src/services/firmware/dfu/DfuBootloader.ts#L31-L188) で定義してあります。このリストに存在する Vendor ID と Product ID かどうかをチェックします。

ここで MCU が特定され、以下の値が決定します。

* Memory Size
* Bootloader Size
* Bootloader Location
* Flash Page Size

まずは、 [初期化処理](https://github.com/remap-keys/remap/blob/main/src/services/firmware/dfu/AbstractDfuBootloader.ts#L81) です。

* DFU Interface の取得
  * 個々の Device Configuration に対して以下を処理。
    * 対象の Device Configuration を selectConfiguration() する。
    * 対象の Device Configuration の個々の Interface に対して以下を処理。
      * 対象の Interface の個々の Alternate に対して以下を処理。
        * 1つ目の Alternate に対する Interface を DFU Interface として決定。

DFU の方言によっては Alternate の Interface Class 値と Interface Subclass 値が DFU であることを確認しないといけないのですが、Atmel-DFU ブートローダーの場合はそういった Interface を返してはくれません。そのため、1つ目の Alternate を決め打ちで処理します。

決定した Configuration と Interface に対して、setConfiguration() と claimInterface() を使って、処理対象として設定します。

ファームウェアの書き込みを始める前に、 [MCU がアイドル状態であることを確認](https://github.com/remap-keys/remap/blob/main/src/services/firmware/dfu/AbstractDfuBootloader.ts#L195) します。

* Abort する
  * コントロール転送で以下を送信。
    * requestType: 'class'
    * recipient: 'interface'
    * request: 0x06
    * value: 0
* status, state 値を受信。
  * コントロール転送で以下を受信。
    * requestType: 'class'
    * recipient: 'interface'
    * request: 0x03
    * value: 0
    * 受信バイト数: 6
  * status, pollTimeout, state を受信バイトから決定。
* state, status 値による条件分岐
  * status 値が 0x02 (DFU_IDLE) かつ state 値が 0x00 (OK) なら、アイドルと判断。
  * 上記以外の場合は、エラーとする or リトライする。

いよいよファームウェアの書き込みに進みます。まずは、すでに [MCU 内に書き込まれた内容を消去](https://github.com/remap-keys/remap/blob/main/src/services/firmware/dfu/AtmelDfuBootloader.ts#L205) します。

* コントロール転送で以下を送信。
  * requestType: 'class'
  * recipient: 'interface'
  * request: 0x01
  * data: 0x04, 0x00, 0xff (all)
* status, state 値を受信。 - (1)
  * コントロール転送で以下を受信。
    * requestType: 'class'
    * recipient: 'interface'
    * request: 0x03
    * value: 0
    * 受信バイト数: 6
  * status, pollTimeout, state を受信バイトから決定。
* もし status 値が 0x09 (ERROR_NOTDONE) かつ state 値が 0x04 (DFU_DOWNLOAD_BUSY) の場合は、100 ms 後に (1) に戻る。
* もし status, state 値が上記以外であれば、消去されたとみなす。

そして、 [ファームウェアを書き込み](https://github.com/remap-keys/remap/blob/main/src/services/firmware/dfu/AtmelDfuBootloader.ts#L180) ます。順番としては、まず MCU の内容がブランク (0) であることを確認した後に、ファームウェアのバイト列を書き込みます。その際には、ブロック単位で処理を行います。

* MCU の内容がすべてブランクかどうか確認
  * ページを選択 - (2)
    * コントロール転送で以下を送信。
      * requestType: 'class'
      * recipient: 'interface'
      * request: 0x01
      * data: 0x06, 0x03, 0x00, ページ番号
    * status, state 値を受信。
      * コントロール転送で以下を受信。
        * requestType: 'class'
        * recipient: 'interface'
        * request: 0x03
        * value: 0
        * 受信バイト数: 6
      * status, pollTimeout, state を受信バイトから決定。
    * status 値が 0x00 (OK) であることをチェック
  * ページがブランクかチェック
    * コントロール転送で以下を送信。
      * requestType: 'class'
      * recipient: 'interface'
      * request: 0x01
      * data: 0x03, 0x01, Start Address, End Address
    * status, state 値を受信。
      * コントロール転送で以下を受信。
        * requestType: 'class'
        * recipient: 'interface'
        * request: 0x03
        * value: 0
        * 受信バイト数: 6
      * status, pollTimeout, state を受信バイトから決定。
    * status 値が 0x00 (OK) であることをチェック
  * Memory Size 分すべてブランクであることが確認できるまで (2) に戻る。
* ファームウェアのバイト列を MCU に書き込み
  * ページを選択 - (3)
    * コントロール転送で以下を送信。
      * requestType: 'class'
      * recipient: 'interface'
      * request: 0x01
      * data: 0x06, 0x03, 0x00, ページ番号
    * status, state 値を受信。
      * コントロール転送で以下を受信。
        * requestType: 'class'
        * recipient: 'interface'
        * request: 0x03
        * value: 0
        * 受信バイト数: 6
      * status, pollTimeout, state を受信バイトから決定。
    * status 値が 0x00 (OK) であることをチェック
  * バイト列を MCU に書き込む
    * コントロール転送で以下を送信。
      * requestType: 'class'
      * recipient: 'interface'
      * request: 0x01
      * data: 0x01, 0x00, 開始アドレス, 終了アドレス, バイト列, CRC, 16, 'D', 'F', 'U', 0x01, 0x10, Vendor ID, Product ID, bcdFirmware
    * status, state 値を受信。
      * コントロール転送で以下を受信。
        * requestType: 'class'
        * recipient: 'interface'
        * request: 0x03
        * value: 0
        * 受信バイト数: 6
      * status, pollTimeout, state を受信バイトから決定。
    * status 値が 0x00 (OK) であることをチェック
  * ブロック単位ですべてのバイト列の書き込みが終わるまで (3) に戻る

送り込むバイト列の各値は、実際にはもっと泥臭い調整をかけています。16 ビット値として、0xFFFF, 0xFF, それ以外でそれぞれちゃんと意味があって、使い分けないといけません。また、各ブロックの大きさや書き込み位置なども計算をして求めないといけないのですが、それらを説明していると「コードを日本語に変換しているのと変わらない」程度に詳細になってしまうため、このエントリでは省きました。

書き込みが終わったあとは、MCU から書き込まれた内容を読み込んできて、 [正しく書き込みが行われたかを検証](https://github.com/remap-keys/remap/blob/main/src/services/firmware/dfu/AtmelDfuBootloader.ts#L234) します。

* ページを選択 - (4)
  * コントロール転送で以下を送信。
  * requestType: 'class'
    * recipient: 'interface'
    * request: 0x01
    * data: 0x06, 0x03, 0x00, ページ番号
  * status, state 値を受信。
    * コントロール転送で以下を受信。
      * requestType: 'class'
      * recipient: 'interface'
      * request: 0x03
      * value: 0
      * 受信バイト数: 6
    * status, pollTimeout, state を受信バイトから決定。
  * status 値が 0x00 (OK) であることをチェック
* バイト列を MCU から読み込み
  * コントロール転送で以下を送信。
    * requestType: 'class'
    * recipient: 'interface'
    * request: 0x01
    * data: 0x03, 0x00, 開始アドレス, 終了アドレス
  * コントロール転送で以下を受信。
    * requestType: 'class'
    * recipient: 'interface'
    * request: 0x02
    * 受信バイト数: Block End - Block Start +1
  * status, state 値を受信。
    * コントロール転送で以下を受信。
      * requestType: 'class'
      * recipient: 'interface'
      * request: 0x03
      * value: 0
      * 受信バイト数: 6
    * status, pollTimeout, state を受信バイトから決定。
  * status 値が 0x02 (ERROR_FILE) でないことをチェック
* すべての読み込みが終わるまで (4) に戻る
* MCU から読み込んだバイト列と、書き込んだバイト列が一致するかチェック

検証が無事済んだら、最後に [MCU をリセット](https://github.com/remap-keys/remap/blob/main/src/services/firmware/dfu/AtmelDfuBootloader.ts#L134) します。

* コントロール転送で以下を送信。
  * requestType: 'class'
  * recipient: 'interface'
  * request: 0x01
  * data: 0x04, 0x03, 0x00
* コントロール転送で以下を受信。
  * requestType: 'class'
  * recipient: 'interface'
  * request: 0x03
  * 受信バイト数: 0

あとは USB 接続を閉じて、Atmel-DFU ブートローダーでのファームウェアの書き込み処理は完了となります。

# まとめ


かなり雑に説明しましたが、Catarina ブートローダーと Atmel-DFU ブートローダーでのファームウェアの書き込みの手順について、Remap での実装内容を紹介いたしました。自分でもよく調査して実装したな、と思うほどに、複雑です。しかも、まともな仕様書も見当たらず、試行錯誤の連続でした。よく MCU を破壊しなかったな、と今でも不思議に思ってます。

なにはともあれ、Remap からファームウェアの書き込みができるようになりました。Windows の場合は USB ドライバの種類を変えれば書き込みができます（[FAQ](https://remap-keys.app/docs/faq)） ので、お試しください。

Remap により皆さんのキーボードがより活用されることを願ってやみません。

