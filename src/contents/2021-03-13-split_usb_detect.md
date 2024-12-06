---
layout: post
title: Lunakey Miniが片手だけ動かなかったときに確認すること
categories:
- keyboard
---

先週Lunakey Miniの試作を家で行っていた時に、「さあ完成した！」と思った後に、最後の最後で「あれ、片手だけ一切動かない、なんで？」という状況になりました。いろいろな原因を考え、試行錯誤したのですが、結論的には「組み立てに問題はなかった」ということで、無事両手とも正常に動作するに至りました。

「片手だけ動かない」とは、USBケーブルを指していない子側のキーボードのキーを押しても何も入力されない状況です。Underglow LEDは点灯することもあればしていないこともあります（僕の場合は両方共経験しました）。子側のキー入力ができませんが、Pro Microの緑のLEDは点灯していることが特徴です。

下の写真は、問題が生じているLunakey Miniです。USBケーブルを挿していない右側のLEDが赤く点灯していて、キー入力を受け付けない状態になっています。


![]({{ "/images/2021/03/split-usb-detect-1.webp" | prepend: site.baseurl }})


この「片方だけ動かない」という現象は、Lunakey Miniだけではなく、Pro Microを採用している左右分割型のキーボードでは発生する可能性があります。

もちろん、組み立てにミスがあったとか、基板に初期不良があったとか、TRRSジャックが壊れていたとか、片手だけ動作しなかった場合の原因はいろいろ考えられます。ただ、片方ずつ動作確認するとちゃんと動作し、テスターで導通確認をしても全て正常、でもTRS/TRRSケーブルでつないだ場合にのみ片手だけ動作しない、という状況になったときは、少なくとも、 **Pro Microの故障だ！と思って真っ先に買い換えないでください** 。Pro Micro を交換せずに動作する可能性が十分にあります。

# この問題が発生する原因

完成後に片手のみ動作しなくなる原因は、Kochi Keyboardのブログエントリにて紹介されています。

[Elite-Cや一部のPro Micro互換機における挙動の問題と対策 - Kochi Keyboard](https://kochikbd.hatenablog.com/entry/2021/03/13/153000)

[純正Pro Microと互換機の電源の罠](https://zenn.dev/koron/articles/7cdfd5382f9ee3)

出力電圧の問題で「USBケーブル挿してあるのか、それともUSBケーブルが挿してなくてTRS/TRRSケーブルから電源をもらっているか」の判断がPro Microで失敗し、左右どちらのPro Microも「俺はUSBケーブルが挿してあるんだもんね！」と判断してしまうことによってシリアル通信ができなくなる、ということが原因のようです。

# この問題を回避するには

原因となる「USBケーブルが挿してあるかどうか」の判断さえ適切に行われれば、左右ともに正常に動作するようになるはずです。実はこの問題はQMK Firmwareでは対応済みであり、解決するための手段が提供されています。Kochi Keyboardのブログエントリにも回避策が書いてあります。

[Elite-Cや一部のPro Micro互換機における挙動の問題と対策 - Kochi Keyboard](https://kochikbd.hatenablog.com/entry/2021/03/13/153000)

その回避方法は、 `#define SPLIT_USB_DETECT` を利用することです。詳しくは、上記ブログエントリを参照ください。もし、ファームウェアのビルドをする環境がない、手順がわからない、という場合は、 [@yoichiro](https://twitter.com/yoichiro)  まで気軽に連絡ください。

この SPLIT_USB_DETECT の指定によってPro Microの処理に影響が出てキーボードの通常使用に支障が出ることはない（これもKochiさんブログエントリにて説明されています）のですが、副作用として「USBケーブルが挿してあるかどうかの検出＝親なのか子なのか、の判断に約2秒かかってしまう」という現象が生じます。この副作用は、USBケーブルがさしてあるかどうかの検出にタイムアウトを利用した処理になっていることが理由です。

実際に SPLIT_USB_DETECT を指定してこの問題を回避したときの、Lunakey Miniの電源投入時の挙動を動画にしてみました。

<iframe width="560" height="315" src="https://www.youtube.com/embed/n72yRphylgU" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

こんな感じで、子側のLEDが約2秒後に点灯しているのがわかると思います。SPLIT_USB_DETECT が機能した証拠です。

# まとめ

今回は、Lunakey MiniなどのPro Microを採用している左右分割型のキーボードにて「TRS/TRRSケーブルで接続して動作させた時に子側だけ動作しない」状況が発生したときの対処方法について紹介しました。この状況になったときには、 `#define SPLIT_USB_DETECT` を試してみて、それでもダメだった場合に Pro Micro の買い替えなどを検討してください。それを試さずに買い替えてしまうのは、お金がもったいないです。

ご参考になれば幸いです。ではでは。

