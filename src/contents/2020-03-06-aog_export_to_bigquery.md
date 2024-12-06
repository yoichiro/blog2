---
layout: post
title: Actions on Googleの統計情報をBigQueryにエクスポートする方法
categories:
- actions on google
---

Actions on Googleを使って、Googleアシスタント向けに開発者は機能拡張を行うことができます。その代表格が、会話型のアクションです。

* オッケーグーグル、○○につないで。
* はい、こちらは○○です。こんにちは、○○です。△△を教えてください。
* ✕✕です。
* ✕✕の□□は、☆☆です。また会いましょう。

的な感じで動的に会話を組み立てていくことができます。

GUIであれば、「どのボタンがいつどれだけ押されたか？」といった統計情報に基づいて、UIの改善を行うと思います。では、VUI（Voice User Interface）ではどうかというと、「いつどれだけ会話が行われたのか？」という統計情報に基づいて改善を行います。会話が何回行われたのか、1回の会話で何回メッセージがやり取りされたのか、こういった情報を分析していくわけです。

GCP（Google Cloud Platform）で「分析」といえば、やはりBigQueryですよね。現在Actions on Googleでは、行われた会話に関する統計データをBigQueryにエクスポートする機能があります。

# BigQueryへの統計データのエクスポート方法

では、エクスポートするための手順です。

## 統計情報が蓄積されるまで待つ

まず行うべきことは、「待つ」ことです。「は？」とお思いかもしれませんが、BigQueryにエクスポートする機能は、ある程度アクティブなアクションに限定されています。そのため、ユーザからほとんど呼び出されていないアクションでは、この機能を利用することができません。

ユーザから呼び出してもらえるように、アクションを改善して、統計情報が蓄積されるまで待ちましょう。とはいえ、呼び出し回数が二桁もいけば、おそらくエクスポート機能を使えるようになるかと思います。

## Actions Consoleでエクスポート機能を呼び出す

Actions Consoleにて、エクスポートしたいプロジェクトを選択します。その後、 画面上部の Analytics メニューを選択して、左側から Export を選択します。

![]({{ "/images/2020/03/aog-export-bigquery-01.webp" | prepend: site.baseurl }})

ここで、Get started ボタンが押せなくなっている場合は、統計情報がまだ十分に蓄積されていません。一方、押せる場合は、BigQuery にエクスポートできます。押しましょう。

## エクスポート対象の起点の日付を選択する

次に、エクスポートしたい統計データの起点となる日付を選択します。分析したい期間を考慮して、適切な日付を選択してください。

![]({{ "/images/2020/03/aog-export-bigquery-02.webp" | prepend: site.baseurl }})

特に昔の統計が必要でなければ、Yesterday を選択すれば良いでしょう。一方、もしかなり長くアクションを運用している場合は、エクスポートできる統計データの範囲は1年以内のものに限られるようです。

選択後、Export を押します。

## エクスポートが開始される

上記の操作により、自動的にActions on Googleが持っている統計データが、BigQueryにエクスポートされます。画面も、以下のように切り替わります。

![]({{ "/images/2020/03/aog-export-bigquery-03.webp" | prepend: site.baseurl }})

統計データの大きさによっては、かなり時間がかかります。BigQueryに統計データが無事エクスポートできた後は、Successful の数字が増えます。

## BigQueryにて統計データを閲覧する

エクスポートされた統計データをBigQueryで見てみます。先程の画面の右上にある Go to BigQuery ボタンを押します。Google Cloud Platform Console がブラウザの別タブが開いて、自動的にBigQueryのページが表示されます。

![]({{ "/images/2020/03/aog-export-bigquery-04.webp" | prepend: site.baseurl }})

Actions on Googleが統計データをエクスポートしたことにより、BigQueryに actions_analytics という名前のデータセットが作成されます。その中に、統計データが格納されるテーブルが日ごとに作成されます。具体的には、以下の2つのテーブルです。

* conversational_conversation_stats_daily_YYYYMMDD - 会話数、メッセージ数、会話の長さ。
* conversational_user_stats_daily_YYYYMMDD - ユーザのデバイス、アクション種別、言語、地域。

![]({{ "/images/2020/03/aog-export-bigquery-05.webp" | prepend: site.baseurl }})

1つ目の conversational_conversation_stats_daily_YYYYMMDD の中にある統計データを見ることで、会話がどれだけ行われたのかがわかります。

![]({{ "/images/2020/03/aog-export-bigquery-06.webp" | prepend: site.baseurl }})

2つ目の conversational_user_stats_daily_YYYYMMDD を見ていくことで、どのようなユーザに利用されたのかがわかります。

![]({{ "/images/2020/03/aog-export-bigquery-07.webp" | prepend: site.baseurl }})

毎日新しいテーブルが追加され、その日の統計データがエクスポートされます。もちろんActions on Google Consoleで提供される統計ページを見ても良いですし、BigQueryにエクスポートされたデータを二次加工して独自のビューを作ったりすることも良いでしょう。

# まとめ

VUI の改善は、提供しているアクションとユーザが一体どのような会話を行っているか、その実際を知ることに尽きます。BigQueryにエクスポートされた情報をぜひ活用して、よりよい会話体験を提供してください。
