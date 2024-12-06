---
layout: actions_on_google
title: Smart Home "Create a Smart Home App"の日本語訳です
categories:
- actions on google
---
Actions on Googleでは、スマートホームアプリを開発することができます。これは、会話によって何かユーザに価値をもたらす会話型のアプリとは開発手順が違ってきます。

Actions on Googleのドキュメントにある
[Smart Home - Create a Smart Home App](https://developers.google.com/actions/smarthome/create-app)にて、
スマートホーム向けアプリの開発手順が説明されています。以下はその日本語訳です。

---
# スマートホームアプリの作成

スマートホームアプリは、アシスタントの従来のアプリとは構造が異なります。ユーザーがアクションとアクションの会話をどうトリガーするかは、あなたによって処理されます。あなたがする必要があることは、あなたのサービス上でスマートホームのインテントを処理することが全てです。

スマートホームアプリを作るには、以下を行います。

1. アカウントリンクのための[OAuth 2.0サーバをセットアップします](https://developers.google.com/actions/smarthome/create-app#setup-server)。
1. [Actions on Google開発者プロジェクトを作成します](https://developers.google.com/actions/smarthome/create-app#create-project)。
1. [スマートホームアプリを選択します](https://developers.google.com/actions/smarthome/create-app#select-app)。
1. スマートホームインテントのための[フルフィルメントを提供します](https://developers.google.com/actions/smarthome/create-app#provide-fulfillment)。
1. [承認のためにあなたのアプリをテストして提出します](https://developers.google.com/actions/smarthome/testing-deploying)。

## OAuth 2.0サーバーを設定する

ユーザーのサードパーティのOAuth 2アクセストークンは、インテントがあなたのフルフィルメントに送信される際に、"Authorization" ヘッダーに渡されます。アカウントがリンクされる前に実行することはできません。なぜなら、アカウントリンクが必要な `action.devices.SYNC` インテントでデバイス情報がアシスタントに送信されるためです。

OAuth統合は、[account linking overview](https://developers.google.com/actions/identity/)で説明されています。

あなたのアプリは、同じエージェントユーザーアカウントに接続している複数のGoogleユーザーをサポートすることが期待されています（たとえば、ユーザーが家庭内の他のユーザーにアクセスする場合）。サービスが複数のユーザー接続をサポートできない場合は、アカウントのリンク時にエラーが発生するべきです。

ユーザーとアプリを認証するための公開OAuth 2.0サーバーがあることを確認してください。[OAuth 2.0 Authorization Codeフロー](https://developers.google.com/actions/identity/oauth2-code-flow)に準拠する必要があります。セットアップが完了したら、これらの[手順](https://developers.google.com/actions/identity/account-linking)に従ってOAuth 2.0サーバを検証します。

## Actions on Google開発者プロジェクトを作成する

開発者側プロジェクトを作成するために以下を行います。

1. [Actions on Google Developer Console](http://console.actions.google.com/)に行きます。
1. **Add/import project** をクリックします。
1. プロジェクト名を入力し、 **CREATE PROJECT** をクリックします。

## スマートホームアプリを選択する

Actionsコンソールの **Overview** 画面で、 **Smart home** カードの **BUILD** をクリックします。ウィザードがスマートホームアプリを自動的に作成します。プロジェクトの種別は、 `action.devices` インテントを満たすアクションを宣言することによって、スマートホームインテントのサポートを定義します。

![](https://developers.google.com/actions/images/smarthome-card.png){:width="315px"}

以下のインテントは、 `action.devices` 名前空間に含まれ、それらのためにフルフィルメントを提供する必要があります。

* `action.devices.SYNC` - ユーザが接続し、利用可能なデバイスの一覧を要求します。
* `action.devices.QUERY` - デバイスの現在の状況を問い合わせます。
* `action.devices.EXECUTE` - スマートホームデバイス上で実行するコマンドを要求します。もし利用可能なら、レスポンス内に新しい状態を提供するべきです。一つの `EXECUTE` インテントは、複数のコマンドを使って、複数のデバイスを対象にすることができます。

## フルフィルメントを提供する

あなたのフルフィルメントは、スマートホームインテントを処理し、Conversation Webhookによって定義された通りに、アシスタントにレスポンスを返さなければなりません。リクエストとレスポンスの形式を遵守している限り、任意の言語を使用できます。フルフィルメントは、アシスタントとクラウドAPIの間の待ち時間を最小限に抑える必要があります。

ウィザート内で、スマートホームインテントのためのフルフィルメントを提供する予定のバックエンドサーバのURLを入力し、 **DONE** をクリックします。

![](https://developers.google.com/actions/images/fulfillment-url.png){:width="715px"}

以下のセクションでは、アシスタントとあなたのアプリ間で通信されるリクエストとレスポンスの書式を説明します。

> **注:** Node.jsの 'actions-on-google' クライアントライブラリは、現在スマートホームアプリをサポートしていません。

### スマートホームアプリのConversation Webhookの違い

スマートホームのインテントはユーザーの状態ではなくデバイスを対象に焦点を当てるため、以下の変更がConversation Webhookに適用されます。

1. `device` と `conversation` は提供されません。会話プロトコルの `device` フィールドは、IoTターゲットではなくサーフェスクライアントを参照します。
1. `user` フィールドは提供されません。認可トークンは、HTTPリクエストの Authorization ヘッダーに含まれます。
1. スマートホームのインテントは、 `inputs` 内の単一のオブジェクトを使用して、 `intent` 値を含み、オートメーション固有のオブジェクトを持つ `payload` オブジェクトを使用します。
1. 同様に、レスポンスにはスマートホームのインテントに合わせた `payload` オブジェクトが含まれています。
1. すべてのリクエストには、Googleとパートナーアプリ間のデバッグ用の任意の識別子を持つ `requestId` フィールドが含まれています。

### action.devices.SYNC

このインテントは、ユーザーセットアップ時や、デバイスをバッチで再同期することが必要なときに（再接続や切断など）、アプリを再接続するときにトリガーされます（例として、新しい特性が追加されたときなど）。

アカウントのリンクを解除して再リンクすることなくユーザーのデバイスを更新するには、[Request Sync](https://developers.google.com/actions/smarthome/create-app#request-sync)を使用します。

このインテントがトリガーされると、アシスタントはあなたのフルフィルメントにリクエストを送信します。フルフィルメントは次のように応答する必要があります。

* **必須。** すべてのデバイスを返します。一時的に使用できないデバイスも含まれるべきです。そうしないと、オフラインデバイスは認識されなくなります。
* **必須。** 各デバイスの属性を返します。

**Request**

```json
{
  "requestId": "ff36a3cc-ec34-11e6-b1a0-64510650abcf",
  "inputs": [{
    "intent": "action.devices.SYNC"
  }]
}
```

**リクエストフォーマット**

* requestId: 文字列。必須。トレースを簡単にするためのリクエストのID。
* inputs:
  * intent: 文字列。必須。 `action.devices.SYNC`
  * (payloadはありません)
  
**Response**

```json
{
  "requestId": "ff36a3cc-ec34-11e6-b1a0-64510650abcf",
  "payload": {
    "agentUserId": "1836.15267389",
    "devices": [{
      "id": "123",
      "type": "action.devices.types.OUTLET",
      "traits": [
        "action.devices.traits.OnOff"
      ],
      "name": {
        "defaultNames": ["My Outlet 1234"],
        "name": "Night light",
        "nicknames": ["wall plug"]
      },
      "willReportState": false,
      "deviceInfo": {
        "manufacturer": "lights-out-inc",
        "model": "hs1234",
        "hwVersion": "3.2",
        "swVersion": "11.4"
      },
      "customData": {
        "fooValue": 74,
        "barValue": true,
        "bazValue": "foo"
      }
    },{
      "id": "456",
      "type": "action.devices.types.LIGHT",
        "traits": [
          "action.devices.traits.OnOff", "action.devices.traits.Brightness",
          "action.devices.traits.ColorTemperature",
          "action.devices.traits.ColorSpectrum"
        ],
        "name": {
          "defaultNames": ["lights out inc. bulb A19 color hyperglow"],
          "name": "lamp1",
          "nicknames": ["reading lamp"]
        },
        "willReportState": false,
        "attributes": {
          "temperatureMinK": 2000,
          "temperatureMaxK": 6500
        },
        "deviceInfo": {
          "manufacturer": "lights out inc.",
          "model": "hg11",
          "hwVersion": "1.2",
          "swVersion": "5.4"
        },
        "customData": {
          "fooValue": 12,
          "barValue": false,
          "bazValue": "bar"
        }
      }]
  }
}
```

**レスポンスフォーマット**

* payload
  * **`errorCode`**: 文字列。任意。SYNCにおけるシステム都合のエラー。
  * **`debugString`**: 文字列。任意。開発時に使われる、ユーザには表示されないがログ出力されるエラーの詳細。
  * **`agentUserId`**: 文字列（256バイトまで）。任意。エージェントのプラットフォーム上で一意な（そして変更不可）ユーザIDを反映します。文字列はGoogleには扱われないので、エージェント側に不変の形式と変更可能な形式がある場合は、不変の形式（例: emailよりもアカウント番号）を使ってください。
  * **`devices`**: `Array<Object>` 。デバイスの配列。ゼロ個もしくは1つ以上のデバイスが返却されます（ゼロのデバイスは、ユーザがデバイスを持っていないこと、または全てそれらが接続されていないことを意味します）。各デバイスは、以下のプロパティを持ちます。
    * **`id`**: 文字列。必須。パートナーのクラウドにおけるデバイスのIDです。これはユーザやパードナーに対して一意でなければなりませんが、共有の場合は同じデバイスの複数のビューと推測するためにこれを使用します。デバイスは不変であるべきです。もし変更した場合は、アシスタントは新しいデバイスとして扱うでしょう。
    * **`type`**: 文字列。必須。デバイスのハードウェア種別です（例: `action.devices.types.LIGHT` ）。デバイス種別のフル[リスト](https://developers.google.com/actions/smarthome/guides/)をご覧ください。
    * **`traits`**: `Array<String>` 。必須。このデバイスがサポートするトレイト（特徴）の一覧です（例: `action.devices.traits.OnOff` ）。これは、デバイスが持つコマンド、属性、そして状態を定義します。トレイトのフル[リスト](https://developers.google.com/actions/smarthome/traits/)をご覧ください。
    * **`name`**: オブジェクト。必須。このデバイスの名前です。個別のフィールドは任意ですが、各デバイスは少なくとも一つの名前を持たなければならないことに注意してください。
      * **`defaultNames`**: `Array<String>` 。任意。ユーザからではなくパートナーから与えられた名前のリストです。これらはしばしば製造業者の名前や、SKU（在庫商品識別番号）などになります。
      * **`name`**: 文字列。任意。デバイスの主要な名前であり、一般的にユーザによって与えられます。これはまた、アシスタントがレスポンスにてデバイスを説明するために参照する名前です。
      * **`nicknames`**: `Array<String>` 。任意。デバイスにユーザが与えた追加の名前です。
    * **`willReportState`**: 論理値。必須。このデバイスがリアルタイムフィードによってこの状態が更新されたかどうかを示します。（TRUEは状態の報告のためにリアルタイムフィードを使うことを示し、FALSEはポーリングモデルを使うことを示します。）
    * **`deviceInfo`**: オブジェクト。任意。必要に応じて、デバイスを一度限りのロジックにて使用するために説明するフィールドを含みます。（例: "ライトYの破損したファームウェアバージョンXは、色の調整が必要です"、"セキュリティの欠陥は、ファームウェアZの全てのユーザに通知する必要があります"）
      * **`manufacturer`**: 文字列。特に、パートナーが他のデバイスのハブである際に便利です。Googleは、ここで製造業者の標準一覧を提供します。例として、TP-LinkおよびSmartthingsの両方は同じ方法 'osram' を説明します。
      * **`model`**: 文字列。特定のデバイスのモデルまたはSKU識別子です。
      * **`hmVersion`**: 文字列。利用可能な場合に、ハードウェアに割り当てられたバージョン番号を指定します。
      * **`swVersion`**: 文字列。利用可能な場合に、ソフトウェア/ファームウェアに割り当てられたバージョン番号を指定します。
    * **`attributes`**: オブジェクト。任意。下にあるAttributesにて、トレイトごとの属性と連携します。右辺の値は、string|int|boolean|numberです。
    * **`customData`**: オブジェクト。任意。これは、パートナーによって定義される特別なオブジェクトです。これは、未来の `QUERY` や `EXECUTE` リクエストに添えられます。パートナーは、デバイスのパフォーマンスを改善するため、またはデバイスのグローバルな地域といったクラウドでのルーティングについての追加情報を保存するために、このオブジェクトを使用することが可能です。オブジェクト内のデータは、いくつかの制約を持ちます。
      * 個人を識別する情報を入れてはいけません。
      * データの変更は、他の属性と同様に、滅多に起きないべきです。これは、リアルタイムの状態を含むべきではないためです。
      * 全体のオブジェクトは、デバイスごとに512バイトに制限されます。

### 同期リクエスト

同期リクエストは、指定した [`agentUserId`](https://developers.google.com/actions/smarthome/create-app#agentUserId) （元のSYNCリクエストで送信したもの）が関連付けられているデバイスを持つ任意のGoogleユーザーのSYNCコールを行うことをGoogleに依頼します。この識別子にリンクされたすべてのユーザーは、[SYNCリクエスト](https://developers.google.com/actions/smarthome/create-app#actiondevicessync)を受け取ります。

フルフィルメントへのSYNCリクエストをトリガーするには、以下を実行します。これにより、アカウントのリンクを解除して再リンクすることなく、ユーザーの端末を更新することができます。

1. Cloud Platform Consoleにて、[Projects](https://console.cloud.google.com/project)ページに行きます。そして、あなたのスマートホームプロジェクトIDにマッチするプロジェクトを選択します。
1. [Google HomeGraph API](https://console.cloud.google.com/apis/api/homegraph.googleapis.com/overview)を有効にします。
1. APIキーを生成します。左のナビゲーションバーから、 **APIs & Services** の下にある **Credentials** を選択します。そして、 **Create Credentials** ボタンをクリックして、 **API key** を選択します。
1. APIキーをコピーします。リクエストにおいて、このAPIキーと `agentUserId` を使用します。テストとして、 `curl` コマンド行でリクエストを作る方法の例をここに示します。
   ```bash
   curl -i -s -k -X POST -H "Content-Type: application/json" -d "{agent_user_id: \"agentUserId\"}" \
     "https://homegraph.googleapis.com/v1/devices:requestSync?key=API_KEY"
   ```

### action.devices.QUERY

このインテントは、パートナーからの現在のデバイス状態を照会します。これは、真にリアルタイム精度が要求される照会（例えば、ドアロックの状況）に使用されます。 `action.devices.QUERY` がトリガーされると、状態のみが返されます。デバイスのプロパティや特性、永続的な要素を更新するには、 `action.devices.SYNC` が使用されます。

**Request**

```json
{
  "requestId": "ff36a3cc-ec34-11e6-b1a0-64510650abcf",
  "inputs": [{
    "intent": "action.devices.QUERY",
    "payload": {
      "devices": [{
        "id": "123",
        "customData": {
          "fooValue": 74,
          "barValue": true,
          "bazValue": "foo"
        }
      },{
        "id": "456",
        "customData": {
          "fooValue": 12,
          "barValue": false,
          "bazValue": "bar"
        }
      }]
    }
  }]
}
```

**リクエストフォーマット**

* **`requestId`**: 文字列。必須。トレースを簡単にするためのリクエストのIDです。
* inputs
  * **`intent`**: 文字列。必須。 `action.devices.QUERY`
  * **`payload`**: オブジェクト。必須。
    * **`devices`**: `Array<Object>` 。必須。
    * **`customData`**: 任意。もし曖昧なcustomDataオブジェクトが `SYNC` で提供された場合は、ここで送信されます。

**Response**

```json
{
  "requestId": "ff36a3cc-ec34-11e6-b1a0-64510650abcf",
  "payload": {
    "devices": {
      "123": {
        "on": true,
        "online": true
      },
      "456": {
        "on": true,
        "online": true,
        "brightness": 80,
        "color": {
          "name": "cerulean",
          "spectrumRGB": 31655
        }
      }
    }
  }
}
```

**レスポンスフォーマット**

* payload
  * **`errorCode`**: 文字列。任意。トランザクション全体のエラーコードです。これは、認証失敗やパートナーのシステムの利用不可です。個々のデバイスのために、エラーはデバイスオブジェクト内でerrorCodeを使用します。
  * **`debugString`**: 文字列。任意。開発時に使われる、ユーザには表示されないがログ出力されるエラーの詳細。
  * **`devices`**: オブジェクト。デバイスのMapです。各プロパティは以下の名前と値を持ちます。
    * **`<id>`**: オブジェクト。必須。下のStatesセクションで定義された、stateプロパティのオブジェクトにパートナーデバイスIDを関連づけます。
    * **`errorCode`**: 文字列。任意。必要に応じて、事前に設定されたエラーコードから `ERROR` 状態を拡張します。これはユーザに表示されたエラーに関連づけられます。
    * **`debugString`**: 文字列。任意。開発時に使われる、ユーザには表示されないがログ出力されるエラーの詳細。
    
### action.devices.EXECUTE

このインテントは、スマートホームデバイスを実行するコマンドを提供するためにトリガーされます。新しい状態は、利用可能であれば、レスポンスとして提供されるべきです。1つのトリガされたインテントは、複数のコマンドを使用して、複数のデバイスをターゲットにできます。たとえば、トリガされたインテントは、複数のライトに明るさと色の両方を設定したり、複数のライトをそれぞれ異なる色に設定したりすることがあります。

**Request**

```json
{
  "requestId": "ff36a3cc-ec34-11e6-b1a0-64510650abcf",
  "inputs": [{
    "intent": "action.devices.EXECUTE",
    "payload": {
      "commands": [{
        "devices": [{
          "id": "123",
          "customData": {
            "fooValue": 74,
            "barValue": true,
            "bazValue": "sheepdip"
          }
        },{
          "id": "456",
          "customData": {
            "fooValue": 36,
            "barValue": false,
            "bazValue": "moarsheep"
          }
        }],
        "execution": [{
          "command": "action.devices.commands.OnOff",
          "params": {
            "on": true
          }
        }]
      }]
    }
  }]
}
```

**リクエストフォーマット**

* **`requestId`**: 文字列。必須。トレースを簡単にするためのリクエストのID。
* inputs
  * **`intent`**: 文字列。必須。 `EXECUTE`
  * payload
    * **`commands`**: `Array<Object>` 。必須。各オブジェクトはコマンドを割り当て対象の1つ以上のデバイスを含みます。
      * **`devices`**: `Array<Object>code>` 。必須。
        * **`id:`** 必須。`SYNC` にて提供されたIDに従って問い合わせるパートナーのIDです。
        * **`customData`**: 任意。不明確なcustomDataオブジェクトが `SYNC` にて提供される場合は、ここで送信されます。
      * **`execution`**: `Array<Object>` 。必須。IDに割り当てられた実行コマンドのソート済みのリストです。
        * **`command`**: 文字列。必須。（通常）同時に付随するパラメータを持つ実行するコマンド（以下を参照）です。
        * **`params`**: `Map<string, Object>` 。任意ですが、各コマンド（以下）のパラメータに合わせてください。
          * **`<name>`**: 文字列。必須。パラメータの名前です。
          * **`<value>`**: String | Number | Boolean です。

**Response**

```json
{
  "requestId": "ff36a3cc-ec34-11e6-b1a0-64510650abcf",
  "payload": {
    "commands": [{
      "ids": ["123"],
      "status": "SUCCESS",
      "states": {
        "on": true,
        "online": true
      }
    },{
      "ids": ["456"],
      "status": "ERROR",
      "errorCode": "deviceTurnedOff"
    }]
  }
}
```

**レスポンスフォーマット**

* payload
  * **`errorCode`**: 文字列。任意。トランザクション全体のエラーコードです。これは、認証失敗やパートナーのシステムの利用不可です。個々のデバイスのために、エラーはデバイスオブジェクト内でerrorCodeを使用します。
  * **`debugString`**: 文字列。任意。開発時に使われる、ユーザには表示されないがログ出力されるエラーの詳細。
  * **`commands`**: `Array<Object>` 。必須。各オブジェクトには、レスポンスの詳細を持つ1つ以上のデバイスが含まれています。これらは、リクエストと同じようにグループ化されないことがあるので注意してください。たとえば、リクエストが7つのライトをオンにすると、3つのライトが成功し、4つが失敗したら、レスポンス内に2つのグループがあることになります。
    * **`ids`**: `Array<String>` 。必須。レスポンス向けのパートナーデバイスIDです。。
    * **`status`**: 文字列。必須。現在のステータスの種別です。
      * **`SUCCESS`** - コマンド（群）が成功したことを確認した。
      * **`PENDING`** - コマンド群がキューに入り、成功することが期待されている。
      * **`OFFLINE`** - 対象のデバイス（群）がオフラインステータスである、または到達できない。
      * **`ERROR`** - コマンドの実行ができない。
    * **`errorCode`**: 文字列。任意。必要に応じて、事前に設定されたエラーコードから `ERROR` 状態を拡張します。これはユーザに表示されたエラーに関連づけられます。
    * **`debugString`**: 文字列。任意。開発時に使われる、ユーザには表示されないがログ出力されるエラーの詳細。
    * **`states`**: オブジェクト。任意。下にあるAttributesにて、トレイトごとの属性と連携します。もし利用可能であれば、これらは実行の _後_ の状態です。
      * **`<name>`**: 文字列。必須。パラメータの名前です。
      * **`<value>`**: String | Number | Boolean です。

## エラーレスポンス

これらはエラーコードであり、あなたのレスポンスにて返すことができます。このリストは、拡張される可能性があり、もちろん全てのエラーが全てのコンテキストにおいて関連があるわけではありません。これらのエラーコードは一般的に、私たちがデバイスのために与えたアシスタントのスピーチレスポンスに関連付けられます。

```json
{
  "requestId": "ff36a3cc-ec34-11e6-b1a0-64510650abcf",
  "payload": {
    "errorCode": "notSupported"
  }
}
```

* **authExpired**: クレデンシャルが失効されている。
* **authFailure**: 認証に対する一般的な失敗。
* **deviceOffline**: 対象に到達できない。
* **timeout**: 内部的なタイムアウト。
* **deviceTurnedOff**: デバイスがハード的にOFFにされている（到達不能と区別がつく場合）。
* **deviceNotFound**: デバイスがパートナー側に存在していない。これは通常データの同期もしくは競合状態による失敗を示す。
* **valueOutOfRange**: パラメータの幅が境界を越えている。
* **notSupported**: コマンドまたはそのパラメータがサポートされていない（これは一般的に起きるべきではなく、トレイトおよびビジネスロジックはそれを妨げるべき）。
* **protocolError**: リクエストの処理に失敗した。
* **unknownError**: その他全てについては、これをスローする側が実際のエラーコードを使って置き換えてください。

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/smarthome/create-app)
