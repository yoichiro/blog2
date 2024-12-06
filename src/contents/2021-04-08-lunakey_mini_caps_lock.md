---
layout: post
title: Caps Lockを数十年ぶりに活用するようになりました
categories:
- keyboard
---

僕はソフトウェアエンジニアです。日本語も書く機会が多いですが、それ以上にプログラムコードを書いている時間も長いです。毎日何らかのコードを書いています。

キーボードはもちろん自分で設計した [Lunakey Mini](https://booth.pm/ja/items/2530075) です。Lunakey Mini は 40% キーボードと言って、一般的なフルキーボードの40％ほどのキー数しかありません。さらに、左右分割型です。普段はこんな感じで、左右を離して使っています。


![]({{ "/images/2021/04/lunakey-mini-caps-lock-1.webp" | prepend: site.baseurl }})


ここまで離れてしまうと、「右手で左側のキーボードのキーを打鍵する」ことはありえません。逆もしかり、です。

さて、今回の話は「アルファベットの大文字をどう打つか」についてです。「Shift キーを押しながら打つんだろ」という声が返ってきそうですが、はい、そういう話です。

冒頭でも書いたとおり、僕はソフトウェアエンジニアなので、プログラムコードを書く機会がとても多いです。そのため、記号を含んだ入力をする機会がとても多いのですが、その次に多いちょっと特殊な入力が、「アルファベットの大文字」となります。

具体的には、以下のような感じです。


```
import { StorageActions, storageActionsThunk } from './storage.action';
import { sendEventToGoogleAnalytics } from '../utils/GoogleAnalytics';

const PRODUCT_PREFIX_FOR_BLE_MICRO_PRO = '(BMP)';

export const HID_ACTIONS = '@Hid';
export const HID_CONNECT_KEYBOARD =
    `${HID_ACTIONS}/ConnectDevice`;
export const HID_DISCONNECT_KEYBOARD =
    `${HID_ACTIONS}/DisconnectDevice`;
export const HID_UPDATE_KEYBOARD =
    `${HID_ACTIONS}/UpdateKeyboard`;
export const HID_UPDATE_KEYBOARD_LAYER_COUNT =
    `${HID_ACTIONS}/UpdateKeyboardLayerCount`;
export const HID_UPDATE_KEYBOARD_LIST =
    `${HID_ACTIONS}/UpdateKeyboardList`;
export const HID_UPDATE_KEYMAPS = `${HID_ACTIONS}/UpdateKeymaps`;
export const HID_UPDATE_BLE_MICRO_PRO =
    `${HID_ACTIONS}/UpdateBleMicroPro`;
export const HidActions = {
  connectKeyboard: (keyboard: IKeyboard) => {
    return {
      type: HID_CONNECT_KEYBOARD,
      value: { keyboard: keyboard },
```

HID_DISCONNECT_KEYBOARD とか HID_UPDATE_KEYBOARD_LAYER_COUNT とかです。普段日本語の文章を書いているときは、なかなかこういう大文字ばかりのアルファベットを打つ機会はないかと思います。しかし、僕はこういう機会が多いです。

これらを Lunakey Mini を使い始める前に愛用していた FILCO Majestouch ではどう打ち込んでいたかというと、

「左手の小指で左の Shift キーを押しながら、右手でアルファベットのキーを打鍵する」

でした。元々タッチタイピングはできず自己流で「ホームポジションなんて迷信」と思っていたので、それが20年以上も続けていた僕の方法でした。


![]({{ "/images/2021/04/lunakey-mini-caps-lock-2.webp" | prepend: site.baseurl }})


この「右手だけでアルファベットをすべて打鍵する方法」は、一体型のキーボードだからできる技です。Lunakey Mini は左右で離れているため、それができません。やったとしても、こうなります。


![]({{ "/images/2021/04/lunakey-mini-caps-lock-3.webp" | prepend: site.baseurl }})


ありえませんね。

さて、今僕が使っている Lunakey Mini に設定しているキーマップは、以下となります。


![]({{ "/images/2021/04/lunakey-mini-caps-lock-4.webp" | prepend: site.baseurl }})


一般的なキーボードと同じように、Shift キーは左右の外側に配置しています。もちろん、小指で打鍵する想定です。

Lunakey Mini でタッチタイピングの特訓をしていて、小文字でのアルファベットの打鍵をするのは苦労なく慣れたのですが、大文字が連続したアルファベットの打ち込みを始めた途端に、大きな壁を感じました。

両手が踊るんです。

実際にどんな感じの手の動きになるのか、動画を撮影してみましたので、ご覧ください。

<iframe width="560" height="315" src="https://www.youtube.com/embed/Oy6IxqDJRYI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

左のキーボードにあるアルファベットの大文字を打つときは、右手の小指で右のキーボードの Shift キーを押しながら、

そして、

右のキーボードにあるアルファベットの大文字を打つときは、左手の小指で右のキーボードの Shift キーを押しながら、

打ち込むわけです。手が踊っているのがわかると思います。

親指で押すことになるキーに Shift キーを割り当てることで、ホームポジションから外れないと打てない小指担当の外側のキーを打たなくて良くなるので、それはそれで良いかもしれません。ただ、僕が使っている配列だと、もう親指のキーには割り当ての余裕がなく、一度試してみたところ誤打鍵も多かったので、残念ながら諦めました。

見方を変えれば、この「手が踊るような打ち方」は、タッチタイピングを習得したからこそできるようになったわけで、これはこれで成果です。大文字の入力も Lunakey Mini で「両手で正しく」できるようになったわけで、良かったかな、と。

しばらく大文字のアルファベットを打ち込むときには、手を踊らせていました。が、プログラミングで出てくる「定数値」を一般的に示す「大文字のアルファベットの連続」は、結構な頻度で出現します。そのため、

「プログラミング ＝ 手を踊らせる」

になってしまいました。本当にこれで正しいのか、自問自答する日々が続きました。

さて、大文字のアルファベットを打ち込むための方法が、Shift キーとの組み合わせの他に、もう一つあります。それは、

「Caps Lock」

です。Caps Lock を ON にしておくと、何かと同時押しする必要なく、a を打鍵すれば A が入力されます。

この Caps Lock、今までは「こんなもんいらねーよ」と思っていました。Ctrl キーがあって欲しい位置にあったりするので、OS インストール直後に Caps Lock を Ctrl キーに入れ替える、そして Caps Lock はキーボードにないものにする、ということをしてきました。その結果、僕の頭の中には、

「Caps Lock 使ったら、負け」

という考えが出来上がっていました。そのため、手を踊らせてでも、Shift キーとの組み合わせで大文字アルファベットを入力する訓練をしてきました。

ただ、ある日、ふと頭に以下のことがよぎりました。

「You, Caps Lock 使っちゃいなよ」

そう、Caps Lock で大文字にロックさえしてしまえば、大文字アルファベットの入力は、途端に何かと同時押しすることなく、打ち込むことができるわけです。

そこで、実際に試してみました。Caps Lock は、Lower + Raise + Caps Lock という配置をしてみました。


![]({{ "/images/2021/04/lunakey-mini-caps-lock-5.webp" | prepend: site.baseurl }})


プログラムコードで定数値の大文字アルファベットの羅列を入力する際には、このキーバインディングを打鍵してCaps Lock を ON にしてから入力します。

これでどんな感じになったか、さっきの動画の後半にその様子を撮影したので、ご覧ください。

<iframe width="560" height="315" src="https://www.youtube.com/embed/Oy6IxqDJRYI?start=15" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

小指で Shift キーを押す必要がなくなりましたので、とても楽に大文字の羅列を打ち込むことができるようになりました。ポリシーだった「Caps Lock 使ったら敗北」を捨てることで、プログラミングコードの打ち込みが格段に楽になりました。

そう、Caps Lock は、便利だったのです。

Lunakey Mini のような 40％ キーボードでプログラミングをしている方には、Caps Lock の活用をオススメいたします。


