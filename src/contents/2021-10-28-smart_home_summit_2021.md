---
layout: post
title: Google Smart Home Summit '21のまとめです
categories:
- actions on google
---

先日の2021年10月22日に、オンラインにて Google Smart Home Summit '21 が行われました。Google のスマートホームブランドの話や、Matter の進捗状況、それに合わせた新しいプラットフォームの発表などがテーマでした。

[Google Smart Home Developer Summit 2021](https://eventsonair.withgoogle.com/events/smarthomesummit2021)

日本ではスマートホームはまだまだこれからという感じですが、米国では Matter の正式リリースのタイミングに合わせて、数多くのスマートホームデバイスが登場する予定となっています。それらは、手軽に設定して、すぐに利用可能になります。そして、複数のデバイスとの連携や、状況に応じて「空気を読んでデバイスが自動的に操作される」という未来のスマートホームが多くの家庭に訪れることが期待されています。遅くとも来年の春には、米国では普及が始まりそうです。

このエントリでは、Smart Home Summit '21 のキーノートで語られたキーワードを紹介いたします。

# Google Home リブランディング



![]({{ "/images/2021/10/smart-home-summit-2021-1.webp" | prepend: site.baseurl }})


今まで Google Home という言葉は、Google から最初に発売されたスマートスピーカーの商品名であり、スマートホームデバイスを管理して操作するための Android アプリの名前でもありました。

Google のスマートホームのブランディングとして、この程「Google Home」という言葉は、エコシステムが提供する全ての機能（スマートホームプラットフォームと開発者プログラム全体）を統合する名称となりました。

これからは「Google Home」と聞いたら「あ、Google が展開するスマートホーム関連一式のことね」と理解してください。

ちなみに、Nest などの既存の個々のブランドについては変更はありません。

# Google Home Developer Center



![]({{ "/images/2021/10/smart-home-summit-2021-2.webp" | prepend: site.baseurl }})


2022年のはじめに、Google Home Developer Center という新しいサイトが公開されます。

今までは、スマートホーム関連のドキュメントやコンソールは、Actions on Google の一部であったり、単独のウェブサイトであったりしました。Google Home Developer Center では、スマートホーム関連のデベロッパーサイトとコンソールを完全に再設計したサイトとなります。

Google Home Developer Center では、スマートホームデバイスやアプリの管理、自動化の学習や構築など、開発、展開、分析の各ステップ用のツールが搭載されることになっています。

# Matter Support



![]({{ "/images/2021/10/smart-home-summit-2021-3.webp" | prepend: site.baseurl }})


Matter は正式リリースに向けて現在仕様策定とデバイスの開発が並行して進められています。Google では、Google Home エコシステムに関係した全てのプロダクトやアプリなどに対して、Matter をサポートする予定となっています。サポート開始は、もちろん正式リリースされたタイミングから Matter をサポートします。

Matter がサポートされることにより、どこからでもスマートホームデバイスを簡単に設定して利用を始められるようになります。

# Google Home Device SDK



![]({{ "/images/2021/10/smart-home-summit-2021-4.webp" | prepend: site.baseurl }})


Matter をサポートするデバイスを最速で開発することを可能とする Google Home Device SDK がリリースされる予定です。

この SDK により、シームレスな設定や制御、相互運用性をデバイスにもたらすことができます。

# Google Home IDE



![]({{ "/images/2021/10/smart-home-summit-2021-5.webp" | prepend: site.baseurl }})


Goolge Home IDE は、Visual Studio Code 向けのプラグインです。これはすでに公開されていて、手元の Visual Studio Code にインストールして利用することが可能です。

[Google Home IDE for VS Code - Actions on Google Smart Home](https://developers.google.com/assistant/smarthome/tools/google-home-ide)

Visual Studio Code を日頃使っている開発者は、慣れ親しんだ方法を使って、Matter 向けのデバイスを容易に開発することができます。この Google Home IDE から、様々なツールを簡単に活用することが可能です。

# Matter support in Google Play services



![]({{ "/images/2021/10/smart-home-summit-2021-6.webp" | prepend: site.baseurl }})


Google Play services が、Matter をサポートします。

これにより、Android アプリでの Matter デバイスの設定フローが容易になります。WiFi や Thread を通じて、新しい Matter デバイスを接続することができます。また、設定フローをブランドやデバイスの説明によりカスタマイズすることも可能です。


![]({{ "/images/2021/10/smart-home-summit-2021-7.webp" | prepend: site.baseurl }})


ユーザがデバイスを Google に接続すると、アプリや Android のスマートホーム用コントロールに自動表示され、そこから制御が可能です。しかも、そのための追加開発は、必要ありません。

# Google Home Mobile SDK



![]({{ "/images/2021/10/smart-home-summit-2021-8.webp" | prepend: site.baseurl }})


Google Home Mobile SDK は、Android 向けの Matter ネイティブアプリを開発することが可能になる SDK です。

この SDK を Android アプリから利用することによって、一つの設定フローでユーザがスマートホームデバイスをアプリにリンクすることができるようになります。

# Parners



![]({{ "/images/2021/10/smart-home-summit-2021-9.webp" | prepend: site.baseurl }})


Matter に参加しているデバイスメーカーは数百となり、すでに数十社がデバイスを開発しています。

# Silicone Support



![]({{ "/images/2021/10/smart-home-summit-2021-10.webp" | prepend: site.baseurl }})


Matter デバイスの開発を支援するために、主要な半導体メーカーと協力して、半導体や開発ツールを提供します。

# ルーティンの提案



![]({{ "/images/2021/10/smart-home-summit-2021-11.webp" | prepend: site.baseurl }})


新しいデベロッパーセンターにて、独自のルーティンを提案することができるようになります。ユーザは、Google Home アプリから直接簡単に見つけることができます。

ルーティンにブランド情報を追加して、デバイスの新しい使い方を提案することもできます。また、他のデバイスや家の状況シグナルと連動して拡張することも可能です。

独自のルーティンの提案機能は、2022年春頃に提供予定です。

# Smart Home Directory



![]({{ "/images/2021/10/smart-home-summit-2021-12.webp" | prepend: site.baseurl }})


Smart Home Directory は、すでに一般公開されている、スマートホームデバイスを検索することができるウェブサイトです。

[Control Your Smart Home - Google Assistant](https://assistant.google.com/smart-home/)

訪問者は、カテゴリから、または使用例から、スマートホーム製品を見つけることができます。そして、Google ショッピングにてレビューや評価を読んでから、好きなショップにて製品を購入することができます。

使用例は継続的に増えていて、初心者のニーズにも、先進的なユーザのニーズにも対応することができるようになりました。

# 既存のテクノロジ



![]({{ "/images/2021/10/smart-home-summit-2021-13.webp" | prepend: site.baseurl }})


今まで展開されてきた Smart Home API、Local Home SDK、Seamless Setup などの既存テクノロジは、継続されます。引き続き利用可能です。

# まとめ

Google Smart Home Summit '21 のセッション動画は、以下から見ることができます。

[Smart Home Summit 2021 - YouTube](https://www.youtube.com/playlist?list=PLOU2XLYxmsILEbh0ykPF6xNbWNanY8LlU)

個々の発表内容の詳細のセッション動画もありますので、興味ある方はぜひご覧になってみてください。

Matter によって、スマートホーム業界が来年一気に加速することが予想されます。日本の家電メーカーも、Matter への参加や仕様に準拠したデバイスの開発が進むと、日本でも一気にスマートホームが盛り上がるのではないかと期待しています。Matter は、Google に特化した話ではなく、例えば Amazon や Apple といったプレーヤーも参加している、業界標準となる規格です。これに乗り遅れる手はないかと思います。

僕も継続してウォッチしていこうと思っています。

