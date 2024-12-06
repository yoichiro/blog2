---
layout: post
title: file content vs. file contents
categories:
- English
---
英語の冠詞の使い方が難しいと良く言われますが、同じくらい難しいと感じるものに「単数形と複数形の使い分け」があると思います。プログラミングの中で「〜の内容」という表現を使いたいときに、「あれ？contentかな？contentsかな？」と迷うことがあると思います。今回「ファイルの内容」を英語でどう表現するのがより正しいのか、調べてみたら["file content" vs. "file contents"](http://english.stackexchange.com/questions/56831/file-content-vs-file-contents)という記事を見つけたので、日本語訳を作ってみました。

コードを書いていてcontent/contentsという単語を使いたくなる、あるいはコードを読んでいてcontent/contentsという単語が出現する、という機会は結構ありますね。下の回答を参考にすると、例えばHTTPでのレスポンスの内容の種類を示すレスポンスヘッダの名前がContent-TypeであってContents-Typeではないのはなぜなのか、しっくりくると思います。ご参考になれば幸いです。

- - -

# "file content" vs. "file contents"

## 質問

私はcontentとcontentsの違いを理解していますし、普通はその区別は私にとって明確です。しかし、file contentとfile contentsのどちらがより正しいのか迷っています。一方では、ファイルはバイト値の有限な並びであるので、contentsが正しい気がします。もう一方では、それらのバイト値はある実体（例えば画像）を表現するので、file's contentと言及する気もします。この区別は正しいですか？もしくは、私は物事を複雑にし過ぎていますか？

## 質問者がベストだと判断した回答

あなたは正しいです。"Content"(Sなし)は、バイトの列ではない、というある種の意味を暗示しています。もしthefreedictionary.comを見た時、"contents"と同義ではないと示されている最初の定義は、"実質的な、または意味のある部分"であり、この場合はバイト値（の集合）によって表現された実体になります。

## その他の回答

一般的な使用方法は、"File contents"です（含まれている何かを参照する時）。
Google Ngramも、file contentよりもFile contentsが優位であることを示しています。

![](http://i.stack.imgur.com/cagmF.jpg)
