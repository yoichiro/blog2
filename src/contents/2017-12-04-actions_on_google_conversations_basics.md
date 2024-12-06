---
layout: actions_on_google
title: Conversations "Basics"の日本語訳です
categories:
- actions on google
---
Googleアシスタントが生息するデバイスは主に2つの形態があります。Googleアシスタント向けアプリの開発では、これらの存在を意識して、ユーザに最適なUIを提供しなければなりません。

* 音声のみのデバイス（Google Homeのような）。
* 音声と画面を持つデバイス（Androidのような）。

Actions on Googleのドキュメントにある[Conversations - Basics](https://developers.google.com/actions/assistant/basics)にて、それらの概要が説明されています。以下はその日本語訳です。

---
# 会話の基本

アプリの開発の大部分は、会話またはアプリのユーザインタフェースの設計と構築です。会話を作成する際には、3つの一般的なことを考慮する必要があります。

**サーフェス機能（Surface Capabilities）** は、ユーザーがあなたのアプリを体験しているサーフェスを表します。サーフェスは、オーディオサポート、スクリーンサポート、またはその両方を持つことができます。Actions on Googleは、あなたのフルフィルメントに対するすべてのリクエストに対してサーフェスの機能を返すため、この情報を使用して適切なUIを提供することができます。

> サーフェス機能の使用方法の詳細については、[Capabilities](https://developers.google.com/actions/assistant/surface-capabilities)のドキュメントを参照してください。

**レスポンス（Response）** は、ユーザーがあなたのアプリに言うことができることと、ユーザーへの返事に対応する応答を定義します。ユーザーが使用しているデバイスのサーフェス機能に応じて、ダイアログが声を出して話すことができ（TTSとSSMLがサポートされています）、また視覚的に表現されることもできます（チャットバブル、カード、リスト、カルーセル、チップが画面に表示されます）。

> より詳細は、[Responses](https://developers.google.com/actions/assistant/responses)をご覧ください。

**ヘルパー（Helpers）** は、アシスタントに共通の機能を実現するためのインテントを実行するよう依頼することができます。

> より詳細は、[Helpers](https://developers.google.com/actions/assistant/helpers)をご覧ください。

## アシスタントの会話を構築する方法

あなたのアプリは、オーディオやディスプレイ体験をサポートする電話機や、オーディオのみの体験をサポートする音声起動スピーカーなど、さまざまなサーフェス上に現れます。すべてのユーザーにとって最高の体験を作るには、まず音声のみの体験を設計します。これは自然な音の会話を作成する最も簡単な方法ですが、視覚体験のためにぜひ必要です。素晴らしいダイアログを書く方法については、[design principles](https://developers.google.com/actions/design/principles)を参照してください。

そこから、基本的なカードやリストなど、画面のコンポーネントを使って会話を補完します。Actions on Googleは、必要に応じて様々なTTSオーディオを提供し、また応答を表示することもできます。

声を出して話すときよりもディスプレイに表示する必要が少ないが、すべてのデバイスで同様の体験を維持したいときには、これを行うとよいでしょう。

![](https://developers.google.com/actions/images/response-branching.svg){:width="300px"}

会話で必要になったときは、ロジックを分岐して、サーフェスの種類ごとに完全に異なるUIを提供できます。

これは、最近のアイテムの迅速な並べ替えや、オーディオやスクリーン出力を備えたデバイスでの完全なショッピングカート体験など、オーディオ専用デバイスの簡略化された体験が必要な時に役立ちます。

両方のタイプの分岐を達成するには、[サーフェス機能](https://developers.google.com/actions/assistant/surface-capabilities)を使用して、ユーザーの要求が来るサーフェスを特定し、実際のUIを構築するための様々なタイプの[レスポンス](https://developers.google.com/actions/assistant/responses)と[ヘルパー](https://developers.google.com/actions/assistant/helpers)を使用します。

![](https://developers.google.com/actions/images/conversation-branching.svg){:width="300px"}

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/assistant/basics)
