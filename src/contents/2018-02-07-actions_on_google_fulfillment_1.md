---
layout: actions_on_google
title: Fulfillment "Firebase Services"の日本語訳です
categories:
- actions on google
---
Googleアシスタント向けアプリを開発する際には、多くの場合フルフィルメントを準備するためにプログラミングが必要になります。フルフィルメントを開発し運用するために、Firebaseは最も手軽かつ強力な武器となります。

Actions on Googleのドキュメントにある
[Fulfillment - Firebase Services](https://developers.google.com/actions/tools/assistant-firebase-services)にて、
Firebaseが提供する各種機能の中でGoogleアシスタント向けアプリの実装に使える機能の概要が説明されています。以下はその日本語訳です。

---

# Firebase Services

さまざまなFirebaseサービスの設定を使用して、Googleアシスタントアプリのアクションに機能を実行し、そして拡張します。

## Firebaseの機能

フルフィルメントは、アプリの機能と会話要素を定義します。アプリが外部APIとやりとりする場合、複雑なロジックを必要としたり、データベースに読み書きしたりする必要がある際には、フルフィルメントが必要です。Firebaseの機能を使用すると、アプリのフルフィルメントがHTTP Webサービスとしてホストされ、デプロイと保守が迅速かつ容易になります。

Actions on GoogleでフルフィルメントのためにHTTP Webサービスを使用することができますが、アシスタントアプリのフルフィルメントを定義する最も簡単な方法の1つは、Firebase Functionsです。

1. CLI設定の構成や追加ライブラリのインポートに費やす時間を最小限に抑えながら、エンドポイントを迅速かつシームレスに展開する
1. [Firebase Real Time Database](https://firebase.google.com/products/database/)などの他のFirebaseクラウドサービスと簡単に統合
1. コンピューティングリソースはアプリの使用状況に応じて自動的に調整されるため、サーバーの保守についてはまったく心配する必要がない
1. クレジットカードや請求情報を提供せずにフルフィルメントをセットアップできる

> **注意:** Firebaseの無料版はフルフィルメントの作成とホスティングには最適ですが、アウトバウンドネットワークコールは許可されていません。 この機能が必要な場合は、有料の[課金プラン](https://firebase.google.com/pricing/)のいずれかにアップグレードする必要があります。

FirebaseのCloud Functionsを使い始めるには、[Firebase FUnctions](https://firebase.google.com/docs/functions/)のドキュメントを参照してください。[Cloud FUnctionsへのデプロイメントに関するActions on Googleのドキュメント](https://developers.google.com/actions/tools/fulfillment-hosting#deploying_to_cloud_functions_for_firebase)もご覧ください。

## Firebase Hosting

多くのアシスタントアプリでは、会話においてリッチメディアコンテンツを使用してユーザー体験を向上させています。そして、もしそうしていないのであれば、ぜひそうすべきです！テキストだけでなくイメージやサウンドクリップを使用するアプリは、通常、より鮮やかで思い出深いものになり、その結果、ユーザーとの間のエンゲージメントとリテンションが向上します。

Firebase Hostingを使用すると、アシスタントアプリで使用する静的リッチメディアファイルを簡単にホストすることができます。Firebaseコンソールで作成されるActions on Googleフォルダにファイルをアップロードし、アシスタントのリッチなレスポンスでこれらのファイルの参照することをすぐに開始できます。

既存のFirebaseユーザーの場合は、モバイルアプリまたはWebアプリのためにホストしているアセットを簡単に再利用することもできます。

Firebase Hostingを使用している[この](https://github.com/actions-on-google/dialogflow-number-genie-nodejs)サンプルをチェックし、[Firebase Hostingのドキュメント](https://firebase.google.com/docs/hosting/)を参照して、アシスタントアプリのファイルのホスト方法の詳細を確認してください。

## Firebase Storage

アシスタントアプリがユーザ固有のファイルを生成する場合、Firebase Storageはそれを保存する場所を提供します。テキストファイルや画像ファイルでのアプリのレスポンスのログであれ、どこにいても作成したコンテンツにユーザーがアクセスできるようにします。

これは、ユーザーのデータが複数のプラットフォームにまたがって保持されることを意味します。たとえば、Webアプリがユーザーの画像を生成します。ストレージを使用すると、アシスタントアプリからその画像に同じユーザーがアクセスできます。モバイルアプリを使用していて、接続が不安定な場合、ユーザーのファイルのダウンロードは一時停止し、信号が改善されると再開します。

![](https://developers.google.com/actions/images/firebase-services-storage-1.png){:width="800px"}

より詳しくは、[Firebase Storageドキュメント](https://firebase.google.com/docs/storage/)を訪問してください。

## Firebase Realtime DB

アシスタントアプリでは、単純なデータを保存するためにバックエンドが必要な場合があります。Firebase Realtime Databaseは、データを1つの大きなJSONツリーとして保存するクラウドホスト型のソリューションです。遅延時間が短いということは、Realtime Databaseがクライアント間の同期状態に最適であることを意味します。

さらに、既存のFirebase開発者は、既存のデータベースを再利用して、モバイルアプリとアシスタントアプリのバックエンドを共有することができます。 つまり、アシスタントアプリを通じてユーザーが行った変更をモバイルアプリに通知することができます。究極の同期性です！

Learn more by visiting the Firebase Realtime Database docs.
より詳しくは、[Firebase Realtime Databaseのドキュメント](https://firebase.google.com/docs/database)をご覧ください。

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/tools/assistant-firebase-services)
