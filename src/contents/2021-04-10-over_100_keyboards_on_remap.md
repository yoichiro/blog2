---
layout: post
title: Remapのキーボード登録数が100個に到達しました
categories:
- keyboard
---

キーボードのキーマップをウェブブラウザから書き換えることができる「 [Remap](https://remap-keys.app) 」ですが、2021年4月9日に、めでたく登録キーボード数が 100 個に到達しました！


![]({{ "/images/2021/04/over-100-keyboards-on-remap-1.webp" | prepend: site.baseurl }})


最初は日本で販売されているキーボードの登録が中心でしたが、今では海外からの登録の方が多くなってきています。Remapがグローバルに使われることを期待していますが、徐々にですが、でも確実に、Remapが自作キーボードの世界にて知られる存在になってきている手応えを感じています。ありがたいことです。

2021年4月10日時点で登録されているキーボードの名前を、以下に列挙してみましょう。順番は、登録順です。

Treadstone60, Leftover30, aleth42, Zinc, attack25, attack25, NumAttack16, Claw44, bat43, Lily58, NKNL7EN, Naked64SF, NKNL7JP, ErgoArrows, Treadstone32, Treadstone48rev2, SilverBullet44, Cornelius, Lunakey Mini, 7sKB, 7splus, jisplit89, Crkbd, Choc Taro, Rapid Capture Plan, re64, re40, re42, ajisai74, elephant42, Unison, Willow64, ownly, Torn, Keyboard Quantizer, Acperience12, Dozen0, Unison, BIOI G60 BLE, BIOI S65, Vega, Orbit-X, Equinox, Polaris, latin60RGB, Lunar, 10bleoledhub, latin17rgb, latin47ble, latin64ble, latinpad, latinpadble, latin47ble, 10bleoledhub, latinpadble, latin17rgb, Meridian, waffling60 Rev A, waffling60 Rev B, Bootleg Rev A, AEKISO60 Rev A, XD60 rev 3, Steezy60 Rev A, Masterworks Classy TKL, Squishy65, Taleguers75, CMM.Studio_Saka68, voice65, CMM.Studio_Saka68, Pulsar rev.2, XT65, Herringbone, bevi, Herringbone Pro, Wete, Jian rev2, PS8, PS17, AK21, AK81_Relax, AK82, AK40, AK7, AK16, AK17, AK20, PS10, AK0, 28KA, PS17, Geekboards Macropad v2, Discipline, Wampus, Vision, key_toys_73, CapsUnlocked CU80 v2 ANSI RGB, CapsUnlocked CU80 v2 ISO RGB, SU120, SU120 (2 Rotary Encoders), SU120 (4 Rotary Encoders), CapsUnlocked CU80 v2 ISO, CapsUnlocked CU80 v2 ANSI, CapsUnlocked CU65, Graystudio Think6.5, Graystudio Think6.5 Hotswap, Graystudio Space65, Graystudio COD67, Ace of Spades TKL, DZ60, micro_redox_semi_split, sparrow62, Hannah910 Rev.3, Hannah910 Rev.2, Hannah910 Rev.1, Kanu Mechlovin, 75 Pixels, OXALYS80, LXIII PRE GB, cool844, mint60, KBD67 MkII RGB V2, [1 x 4] + 1 Macropad, Let's Split, m3n3van, Geminate60, KBD19RGB, kelowna RGB64, kelowna RGB64, Rainbow65_Y&R, Mouse Quantizer, KPRepublic BM40 RGB, ct_macropad, giabalanai, giabarinaix2, barracuda

ここで、Remapが提供している機能について、簡単に紹介してみたいと思います。

# 簡単なキーアサイン


![]({{ "/images/2021/04/over-100-keyboards-on-remap-2.webp" | prepend: site.baseurl }})


Remapがサポートしているファームウェアは QMK Firmware と呼ばれているオープンソースのソフトウェアなのですが、このファームウェアはかなり多彩なキーアサイン機能が提供されています。普通の文字の入力だけでなく、

* レイヤー機能（ノートパソコンの Fn キーのように、あるキーを押すとキーマップが一時的に変更される）
* 修飾キー（Shift キーや Ctrl キーなど）と他のキーとの自由な組み合わせ
* 普通に押したらこれ、押しっぱなしにしたらこれ、という Hold/Tap キーマップ。

などが可能です。これらのキーアサインは、Remap から直感的なUIを使うことで簡単に設定することができます。

# キーマップの保存と復元


![]({{ "/images/2021/04/over-100-keyboards-on-remap-3.webp" | prepend: site.baseurl }})


僕は普段 Linux を使ってソフトウェア開発を行っていますが、その他にも MacOS や Windows 10 も使っています。OS が変わると、便利なショートカットキーのキーバインディングが異なってきたり、そもそも Win キーや Command キーといった OS 特有のキーも使い分けることになります。つまり、同じキーボードで同じキーマップでは、それぞれの OS を使いこなすのはかなり難しいです。

自作キーボードの利点は、なんといっても「自由にキーマップを変更できること」です。この利点を最大限活かすために、Remap では「キーマップの保存と復元（Save/Restore Keymap）」機能を提供しています。

キーマップに名前をつけて Remap に複数保存しておき、状況に応じて適切なキーマップを読み込んでキーボードに反映させることが可能です。上記の例のように OS ごとに、他にも、日本語入力向けやプログラミング向けとキーマップを分けている場合や、QWERTY や Drorak 配列といった使い分けも、このキーマップ保存/復元機能を使えば気軽に対応することができるでしょう。

# キーマップのシェア


![]({{ "/images/2021/04/over-100-keyboards-on-remap-4.webp" | prepend: site.baseurl }})


自作キーボードを購入して組み立てた経験のある方は、皆さん「キーマップはどうしよう。。。」と最初に悩まれたはずです。もちろんその悩みは楽しい悩みなのですが、スタートラインの選択肢が多ければ多いほど、その悩みは減ります。

また、「このキーボードを使っている他の人は、どんなキーマップなのだろう？」と、誰しも興味があると思います。他の人が使っているキーマップを見ることで、なにか発見があるかもしれません。

Remap では、保存されたキーマップを「シェア」することができます。シェアされている他のユーザのキーマップを、自分のキーボードに簡単に適用することが可能です。さらに、適用した他のユーザのキーマップをベースにして、自分なりにカスタマイズしていくことも、もちろん可能です。

時々シェアされたキーマップが増えたかどうか、Remap に来て確認してみてください。それは、新しい世界の発見につながるかもしれません。

# キースイッチの動作確認機能


![]({{ "/images/2021/04/over-100-keyboards-on-remap-5.webp" | prepend: site.baseurl }})


自作キーボードのほとんどは、未完成の状態で手元に届きます。数多くの部品を取り付けることで、キーボードが完成します。一回で動作することは珍しく、残念ながら組み立てには失敗が付きものです。その失敗をできるだけ早く発見し、リカバリーしていくことが求められます。

Remap の開発チームは、キーボードの組み立て時や組み立て直後でも、ユーザの役に立ちたいと考えています。そのタイミングで便利かつ有益な機能の第一弾として、Test Matrix 機能の提供を開始しました。

Test Matrix 機能を言い換えると、キースイッチの動作確認機能とも呼ぶことができます。ファームウェアの書き込み後にキーボードを接続し、Remap から Test Matrix 機能を開いた後に、キーボードの各キーを打鍵します。キースイッチが正しく取り付けられていれば、そのキーが青く光ります。キーを押し込んだときと離したときに、画面上でもキーが押されたようなアニメーションが表示されます。もしキーを押した後に押しっぱなしになってしまう不具合があったときには、押し込まれたアニメーションのまま、離した際のアニメーションがされませんので、うまく動いていないことが直感的にわかるようになっています。

「あれ？なにかおかしいぞ」と思った際には、この Test Matrix 機能を使ってみてください。

# LED のコントロール


![]({{ "/images/2021/04/over-100-keyboards-on-remap-6.webp" | prepend: site.baseurl }})


キーボードキットの中には、LED が搭載されていて、カラフルに光るキーボードがあります（僕が設計した [Lunakey Mini](https://booth.pm/ja/items/2530075) も、もちろん光ります♪）。無機質なキーボードもかっこいいですが、キレイに光るキーボードは、また違った魅力があります。そして、どう光らせるか、これはあなたの個性の見せ所です。

もしキーボードが Backlight や Underglow といった LED による視覚効果の機能を有している際には、Remap は LED をコントロールするための機能が使えるようになります。LED をどう光らせるか、いくつかプリセットされたモードから選択することが可能です。また、その明るさや、色をも制御することが可能です。場合によっては、色が移り変わっていく際の速度も変化させることができるでしょう。

Remap 上では「どんな色にするのか」や「どの程度の明るさにするのか」を直感的に把握しながらコントロールすることが可能です。この機能を駆使して、自分の個性をキーボードに反映させてください。何も作業することがなくても、毎日キーボードを叩きたくなることでしょう。

# まとめ

今回は Remap にキーボードが 100 個登録された節目として、どんなキーボードが登録されているのか、そしてそれらのキーボードを Remap からどうカスタマイズしていくことができるのか、紹介してみました。今後もっともっとキーボードの登録が増えていくことを期待していますし、そのために Remap 開発チームも Remap の進化を止めてはならないと使命を感じています。

Remap をお使いの方々から、もっともっと多くのフィードバックを求めています。Discord サーバを提供していますので、ぜひご参加ください。そして、Remap を使った感想、見つけた不具合、もっとこうなって欲しい！という要望、Remap 開発チームへの応援など、お聞かせいただけると、開発の励みになります。

多くの方々がキーボードをもっと活用できるように、今後の Remap にご期待ください。

