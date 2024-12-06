---
layout: actions_on_google
title: Build with Actions SDK "Create a Project and Action Package"の日本語訳です
categories:
- actions on google
---
Actions SDKを使ってGoogleアシスタント向けアプリを開発するための第1歩は、プロジェクトの作成と、アクションパッケージの作成です。アクションパッケージの実体はJSONファイルであり、 `gactions` コマンドでそのひな形を得ることができます。

以下は、[Build with Actions SDK - Create a Project and Action Package](https://developers.google.com/actions/sdk/create-a-project)の日本語訳です

---

# プロジェクトとアクションパッケージの作成

## プロジェクトの作成

1. [Actions on Google Developer Console](http://console.actions.google.com/)に行きます。
1. Add Projectをクリックし、プロジェクトの名前を入力して、 **Create Project** をクリックします。
1. **Overview** 画面にて、 **Actions SDK** カードの **BUILD** をクリックし、 `gactions` コマンドのメモを取ります。承認のためにアクションパッケージをアップロードする際に、このコマンドを使う必要があるでしょう。

```bash
gactions update --action_package PACKAGE_NAME --project PROJECT_NAME
```

## アクションパッケージの作成

アクションパッケージは、あなたのアプリにおけるアクションを定義するJSONファイルです。アクションは、実際のフルフィルメントエンドポイントを遂行するために、インテントに関連付けされます。

1. [`gactions` CLI](https://developers.google.com/actions/tools/gactions-cli)をダウンロードします。
1. アプリのソースファイルのためのローカルディレクトリを作成します。ここでは、 `<project-dir>` としてこのディレクトリを参照します。
1. `actions.json` という名前のアクションパッケージファイルのボイラープレートを作成するために、以下のコマンとを実行します。
  ```bash
  cd <project-dir>
  gactions init
  ```
  そのファイルは、このようになります。
  ```json
  {
      "actions": [
        {
          "description": "Default Welcome Intent",
          "name": "MAIN",
          "fulfillment": {
            "conversationName": "<INSERT YOUR CONVERSATION NAME HERE>"
          },
          "intent": {
            "name": "actions.intent.MAIN",
            "trigger": {
              "queryPatterns": [
                "talk to <INSERT YOUR NAME HERE>"
              ]
            }
          }
        }
      ],
      "conversations": {
        "<INSERT YOUR CONVERSATION NAME HERE>": {
          "name": "<INSERT YOUR CONVERSATION NAME HERE>",
          "url": "<INSERT YOUR FULLFILLMENT URL HERE>"
        }
      },
  "locale": "<INSERT YOUR LOCALE HERE>"
  }
  ```

> **注:** アクションパッケージの完全な参照情報は、[Action Package reference](https://developers.google.com/actions/reference/rest/Shared.Types/ActionPackage)をご覧ください。

次のセクションでは、アクションパッケージ内でどのようにアクションを定義するかを学びます。

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/sdk/create-a-project)
