---
layout: post
title: macOS Mojaveのスクリーンショット撮影のプレビューを無効にする方法
categories:
- mac
---

macOS Mojaveでは、スクリーンショット撮影が機能強化されています。それは、Command + Shift + 5 を押すことでわかると思います。
また、スクリーンショットを撮った際に、右下にプレビューが表示されます。これにより、スクリーンショットの撮影に成功したか
どうかをすぐに確認することができます。

が、このプレビュー機能、残念ながら僕にとっては「不要と思わざるを得ない」機能です。もちろん、プレビュー機能自体は良いと
思うのですが、この機能によって僕の使い方に不都合が出てきてしまいました。

# ファイルが生成されるまで待たされる問題

それは、 **プレビューが表示されてから非表示になるまで、ファイルが作成されない** という挙動です。以下の動画を見ていただ
ければ、その動作イメージがわかると思います。

<iframe width="560" height="315" src="https://www.youtube.com/embed/stLIYDxG_I0" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

僕の場合、スクリーンショットを撮影した後は、そのファイルをすぐに移動させたり、開いて編集したりすることが 99 % なため、
プレビューされている間にファイルがないっていうこの空白時間は「単なる待ち時間」となってしまっていて、無駄以外の何者でも
ないのです。待たされるくらいなら、プレビュー表示、僕には必要ありません。

# プレビュー表示を無効にする方法

そんなプレビュー表示、無効にして昔と同じ挙動、つまりすぐにファイルが作成されるようにするための方法があります。ターミナル
から謎のコマンドを打って、とかではなく、簡単にUIから変更可能です。手順は以下となります。

1. Command + Shift + 5 を押します。
1. 画面下に出てくるポップアップから「オプション」をクリックします。
1. 表示されたメニューの中から「フローティングサムネールを表示」のチェックを外します。

これだけです。これにより、スクリーンショットを Command + Shift + 4 などで撮影した際に、プレビュー表示されず、すぐに
ファイルが生成されるようになります。

![]({{ "/images/2018/09/disable_mojave_screenshot_preview_01.webp" | prepend: site.baseurl }})

# まとめ

視覚的にプレビュー表示は派手ですし撮影された内容をすぐに確認可能っていうメリットはあるものの、スクリーンショットを撮影
した後の作業が連続する場合には、かえってストレスです。上記の手順で簡単に無効にでき、やっぱりプレビュー表示させたければ、
簡単に戻せます。

僕のようにお困りの方は、ぜひ試してみてください。
