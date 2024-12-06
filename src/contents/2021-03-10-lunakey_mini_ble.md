---
layout: post
title: Lunakey MiniをBLE Micro Proで無線化する方法
categories:
- keyboard
---

左右分割型の40％キーボードとして販売させていただいているLunakey Miniですが、BLE Micro Pro（BMP）を使って無線化をやってみました。結果は大成功です。Lunakey Miniでも問題なく無線化して利用することができることを確認できました。


![]({{ "/images/2021/03/lunakey-mini-ble-1.webp" | prepend: site.baseurl }})


ここでは、Lunakey Miniを無線対応するための手順を紹介したいと思います。

# Lunakey Miniの入手

まず、Lunakey Miniをそもそも持っていない方は、ここでぜひLunakey Miniを購入して組み立てを行いましょう。Lunakey Miniは、以下より購入することができます。

[Lunakey mini rev5 (ベースキット) - Kichi Keyboard](https://kochikeyboard.stores.jp/items/605ab0a6baeb3a0ff29252a2)

[【委託】Lunakey Mini – 遊舎工房](https://shop.yushakobo.jp/collections/keyboard/products/consign_lunakey-mini)

組立方法は、以下のビルドガイドを参照ください。

[Lunakey Mini ビルドガイド (Rev.4)](https://www.eisbahn.jp/yoichiro/2020/12/lunakey_mini_build_guide_rev4.html)

ここから先は、手元にLunakey Miniがあって動作に問題がない状態であることを前提とします。

# 無線化に必要な部品の購入

Lunakey Miniの無線化を行うために、以下の部品を購入します。

* [BLE Micro Pro Type-C版 – 遊舎工房](https://shop.yushakobo.jp/collections/keyboard-parts/products/ble-micro-pro?variant=37665571340449) - 2個
* [BLE Micro Pro用電池基板 – 遊舎工房](https://shop.yushakobo.jp/collections/keyboard-parts/products/ble-micro-pro-battery-board?variant=37665283211425) - 2個
* [コンスルー（スプリングピンヘッダ） – 遊舎工房](https://shop.yushakobo.jp/collections/keyboard-parts/products/a01mc-00) - 12ピンを2個

遊舎工房さんで売り切れている場合は、以下もチェックしましょう。

[Pro MicroサイズのUSB対応nRF52マイコンボード - のぎけす屋 - BOOTH](https://nogikes.booth.pm/items/1177319)

そして、もう一つ必要な部品があります。それは「Pro Microに付属されてくるピン」です。以下のようなピンがPro Microを買った際に付属されてきたと思います。


![]({{ "/images/2021/03/lunakey-mini-ble-2.webp" | prepend: site.baseurl }})


このピンをあとで使いますので、ご準備ください。

# 電池基板の組み立て

部品が届いたら、早速組み立てを行いましょう。まずは、BLE Micro Proに電力を供給するための電池基板を組み立てます。

まずは、ダイオードを取り付けます。ダイオードには向きがあって、よく見るとダイオードの箱の表面に線が引いてあります。その線を、基板上のドット（・）が書いてある方に合わせて配置し、両方の端子をハンダ付けします。


![]({{ "/images/2021/03/lunakey-mini-ble-3.webp" | prepend: site.baseurl }})


ダイオードの取り付け方は、Lunakey Miniを組み立てた方には最低でも44回行った作業になりますので、特に問題はないでしょう。表面実装になるので、まず片方のランド（基板上のはんだ付けする場所になる明るい部分）に予備ハンダを載せます。そして、ピンセットでダイオードを持ち、ハンダゴテで予備ハンダを溶かしながら、ダイオードの端子をそこに滑り込ませます。


![]({{ "/images/2021/03/lunakey-mini-ble-4.webp" | prepend: site.baseurl }})


そして、反対側もハンダ付けし、予備ハンダの状態を見て必要であればはんだ付けし直します。


![]({{ "/images/2021/03/lunakey-mini-ble-5.webp" | prepend: site.baseurl }})


もう一つのダイオードも同じ手順で取り付けましょう。


![]({{ "/images/2021/03/lunakey-mini-ble-6.webp" | prepend: site.baseurl }})


次に、電源スイッチの取り付けです。これにも向きがありますが、基本的には基板上のランドに合わせれば大丈夫です。


![]({{ "/images/2021/03/lunakey-mini-ble-7.webp" | prepend: site.baseurl }})


スイッチの裏側には黒い出っ張りがあって、基板上の穴に合うようになっています。


![]({{ "/images/2021/03/lunakey-mini-ble-8.webp" | prepend: site.baseurl }})


基板にスイッチを載せて、はんだ付けを行っていきます。


![]({{ "/images/2021/03/lunakey-mini-ble-9.webp" | prepend: site.baseurl }})


ランドが小さいので、注意してはんだ付けを行っていきましょう。


![]({{ "/images/2021/03/lunakey-mini-ble-10.webp" | prepend: site.baseurl }})


スイッチの後は、コンデンサを取り付けます。コンデンサには向きはありません。


![]({{ "/images/2021/03/lunakey-mini-ble-11.webp" | prepend: site.baseurl }})


コンデンサの取り付けは、ダイオードと同じように、片方のランドに予備ハンダを載せておいて、それを溶かしながらコンデンサの片方の端子を滑り込ませます。片方が固定されたところで、反対側をハンダ付けし、予備ハンダについても必要であれば整えます。


![]({{ "/images/2021/03/lunakey-mini-ble-12.webp" | prepend: site.baseurl }})


最後に、ボタン型の電池を収納するためのホルダーを取り付けます。電池が乗る大きな丸いランドの両脇に楕円形のスルーホールがあります。このスルーホールにホルダーの足を差し込みます。


![]({{ "/images/2021/03/lunakey-mini-ble-13.webp" | prepend: site.baseurl }})


コツとしては、ホルダーの足を広げておいて、それを縮めながらスルーホールの足に差し込むと、足が広がろうとする力が働いて、ホルダーがうまく基板に固定されます。


![]({{ "/images/2021/03/lunakey-mini-ble-14.webp" | prepend: site.baseurl }})


2本の足をそれぞれハンダ付けします。


![]({{ "/images/2021/03/lunakey-mini-ble-15.webp" | prepend: site.baseurl }})


もう一つのホルダーも同じように取り付けましょう。


![]({{ "/images/2021/03/lunakey-mini-ble-16.webp" | prepend: site.baseurl }})


以上で電池基板に部品を取り付ける作業が終わりました。


![]({{ "/images/2021/03/lunakey-mini-ble-17.webp" | prepend: site.baseurl }})


もう一つ必要になるので、同じように組み立てておきましょう。

# BLE Micro Proと電池基板の結合

BLE Micro Pro（BMP）自体はすでに必要な部品が実装済みですので、そのまま利用することが可能です。必要なことは、BMP にコンスルーを取り付けることと、先ほど組み立てた電池基板を「背負わせる」ことです。

まず、BMPにコンスルーを取り付けます。これは、Pro Microにコンスルーを取り付ける時と基本的に同じです。


![]({{ "/images/2021/03/lunakey-mini-ble-18.webp" | prepend: site.baseurl }})


注意点としては、以下です。

* BMPには13個の端子がありますが、コンスルーは12個のピンです。つまり、BMP側の端子が一つ余りますが、それで大丈夫です、余らせる端子は、USBコネクタ側の端にある端子となります。
* コンスルーの側面に12個穴が開いていますが、その穴をBMPの基板に近くなるような向きで取り付けます。
* 取り付ける2つのコンスルーの向きは、コンスルーの側面の穴が向かい合わせになるのではなく、同じ方向を向くようにします。

向きを揃えたら、各ピンをハンダ付けしていきます。


![]({{ "/images/2021/03/lunakey-mini-ble-19.webp" | prepend: site.baseurl }})


もう一つのBMPも同様にコンスルーを取り付けます。


![]({{ "/images/2021/03/lunakey-mini-ble-20.webp" | prepend: site.baseurl }})


次に、電池基板をBMPに背負わせます。背負わせる際に、電池基板からBMPに電力が送られないといけないので、ピンで接続します。そのために、Pro Microに付属されてきたピンを使います。


![]({{ "/images/2021/03/lunakey-mini-ble-21.webp" | prepend: site.baseurl }})


まず、ピンを4つニッパーで切り出します。


![]({{ "/images/2021/03/lunakey-mini-ble-22.webp" | prepend: site.baseurl }})


次に、BMPのコンスルーを下にした状態で、USBコネクタ側にある空いている端子に、切り出したピンを差し込みます。このとき、ピンの長い方をBMPの端子に差し込みます。


![]({{ "/images/2021/03/lunakey-mini-ble-23.webp" | prepend: site.baseurl }})


ここで、電池基板のホルダーがない側（ダイオードを取り付けた側）がBMPの基板と面するようなるのですが、端子同士が触れることでショートしてしまう懸念があります。ショートを防ぐために、電池基板もしくはBMPのどちらかに、マスキングテープやビニールテープで覆うと良いでしょう。僕はマスキングテープで電池基板側を覆うことで、ショートを防ぐことに成功しています。

そして、電池基板をピンに合わせて背負わせます。


![]({{ "/images/2021/03/lunakey-mini-ble-24.webp" | prepend: site.baseurl }})


ピンと反対側で、電池基板がBMPに触れて（乗って）しまっていると思います。ハンダ付け後に電池基板がBMPから浮いているようにしたいので、何か電池基板とBMPの間に小さなものを挟めておくと良いでしょう。

ピンをハンダ付けします。


![]({{ "/images/2021/03/lunakey-mini-ble-25.webp" | prepend: site.baseurl }})


裏返して、反対側もハンダ付けします。


![]({{ "/images/2021/03/lunakey-mini-ble-26.webp" | prepend: site.baseurl }})


電池基板とBMPのもう一組も同じようにして背負わせます。


![]({{ "/images/2021/03/lunakey-mini-ble-27.webp" | prepend: site.baseurl }})


これで組み立ては完了です。

# ファームウェアの書き込み

では、できあがったBMPにファームウェアを書き込みます。2つのBMPをそれぞれ「親側」「子側」と決めます。書き込みの際に異なる手順がありますので、取り間違えないように注意しましょう。

手順については、以下のページで説明されていますので、指示に従って「キーマップの書き込みの手前まで」順番に行っていってください。

[自作キーボードキットを無線化する](https://sekigon-gonnoc.github.io/BLE-Micro-Pro/#/getting_started)

注意点としては以下となります。

* 上記ページには「Experimental Web Platform featuresをEnabledにします」と書かれていますが、Chrome 89 Stable 以降ではこの必要はありません。
* キーボードとして「lunakey_mini」を選択してください。
* キーマップの書き込みは、ブラウザからではなく後述する別の手順で行います。

BLE Micro Pro Web Configuratorでは、BMPに何か書き込みが行われる際には、必ず進捗率が表示されます。ボタンを押して書き込みを行った際に進捗率が表示されなかった場合は、書き込み処理が行われていませんので、再度ボタンを押してください。進捗率が表示されれば書き込みができたと考えて良いでしょう。

キーマップの書き込みですが、Web Configuratorから行った際になかなかうまく書き込むことができず、書き込みが途中で終わっていたり全て0が書き込まれていたりして不安定だったので、ここでは確実な方法でキーマップの書き込みを行いたいと思います。その方法は、「テキストファイルを直接書き込む」です。

実は、BMPをUSBケーブルでPCにつなぐと、ファイル共有機能によって、BMP内の設定がファイルとして見えます。


![]({{ "/images/2021/03/lunakey-mini-ble-28.webp" | prepend: site.baseurl }})


この中の「KEYMAP.JSN」ファイルが、キーマップの設定が書かれたファイルとなります。そして、この内容は、QMK Configuratorで出力可能なJSONファイルと互換があります。つまり、QMK Configuratorにてキーマップを定義して、その内容をJSONファイルとしてエクポートします。そのファイル名をKEYMAP.JSNに変更してBMPに上書きすることで、キーマップを書き換えることが可能です。

まず、以下のURLにて、QMK Configuratorにウェブブラウザでアクセスします。

[Lunakey Mini - QMK Configurator](https://config.qmk.fm/#/yoichiro/lunakey_mini/LAYOUT_split_3x6_4)

Lunakey Miniのキーマップの変更画面が開きます。


![]({{ "/images/2021/03/lunakey-mini-ble-29.webp" | prepend: site.baseurl }})


キーマップを変更した後に、「KEYMAP.JSON」と書かれている箇所の左にあるExportボタンを押します。


![]({{ "/images/2021/03/lunakey-mini-ble-30.webp" | prepend: site.baseurl }})


保存先を指定して、エクスポートします。その後、そのファイルの名前を KEYMAP.JSN に変えて、ファイル共有されたBMP内のファイルを置き換えます。


![]({{ "/images/2021/03/lunakey-mini-ble-31.webp" | prepend: site.baseurl }})


両方のBMPに同じKEYMAP.JSONファイルを書き込みます。これで、キーマップの書き込みは完了です。

# PCに接続する

全ての準備は整いました。PCに接続してみましょう。

まず、電池基板のスイッチをONにして、BMPに電力を供給します。スイッチの右にドット「・」が書かれていますので、スイッチをドットの方にスライドさせます。これにより電源が入ります。両方のスイッチをONにしましょう。


![]({{ "/images/2021/03/lunakey-mini-ble-32.webp" | prepend: site.baseurl }})


両方のスイッチをONにすると、自動的に左右のBMPがペアリングされます。この状態で、PCのBluetoothの設定を見てください。


![]({{ "/images/2021/03/lunakey-mini-ble-33.webp" | prepend: site.baseurl }})


Lunakey Miniが表示されていると思いますので、接続します。


![]({{ "/images/2021/03/lunakey-mini-ble-34.webp" | prepend: site.baseurl }})


さあ、接続されたLunakey Miniから文字入力してみてください。無事、入力できたら成功です！

無線化されたLunakey Miniをご活用いただければと思います。

