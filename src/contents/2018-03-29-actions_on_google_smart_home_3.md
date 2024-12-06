---
layout: actions_on_google
title: Smart Home "Testing and Deploying"の日本語訳です
categories:
- actions on google
---
Actions on Google上で登録し開発したスマートホームアプリは、もちろんテストが行われた後に、公開されることになります。テストは、一人だけで行うのではなく、複数のメンバーによって行われるかも知れません。Actions on Googleでは、公開前の複数人によるテストもサポートされています。そして、テスト項目も[決められていて](https://docs.google.com/spreadsheets/d/1n6iOOX6j42TY-K9UoVG6g-fh5S1DrZ38-1zvEupMwBo/edit?usp=sharing)、スマートホームアプリの品質を開発者は一定以上に保証しなければなりません。

Actions on Googleのドキュメントにある
[Smart Home - Testing and Deploying](https://developers.google.com/actions/smarthome/testing-deploying)にて、
スマートホーム向けアプリのテストおよび公開のための手順が説明されています。以下はその日本語訳です。

---
# テストと公開

## テスト

あなたのスマートホームアプリをテストするために、以下を行ってください。

1. [Actions on Google Developer Console](https://console.actions.google.com/) に行き、あなたのプロジェクトを選択してください。
1. **App information** の下で、 **EDIT** ボタンをクリックしてください。あなたのアプリのための全ての情報をフィールドに入力します。これらのフィールドは、 [Assistant App Directory](https://developers.google.com/actions/distribute/directory) に表示されます。もしアプリに対して最終的な情報を全て持っていなくても、心配しないでください。ページの下の方にある **SAVE** ボタンをクリックすることで、途中でも保存することができます。<br>
   ![](https://developers.google.com/actions/images/app-info.png){:width="241px"}<br>
1. **Account linking** の下で、 **ADD** ボタンをクリックします。ここで、ユーザがあなたのアプリに[ユーザのアカウントをリンクする](https://developers.google.com/actions/identity/account-linking)ことができるようにするために、認証をセットアップすることができます。
1. Googleアシスタントアプリを使って、あなたのGoogleアカウントをスマートホームエージェントにリンクします。

   1. [スマートホーム設定にアクセスします](https://developers.google.com/actions/smarthome/testing-deploying#smart-home-settings)。
   1. デバイスを追加するために、 **+** ボタンをクリックします。
   1.   プロバイダのリストから、あなたのアプリを選択します。
   1. あなたのサービスにサインインします。
1. Actions consoleにて、デバイス上でアプリをテストするために、 **TEST DRAFT** ボタンをクリックします。

### Googleアプリにおけるスマートホーム設定へのアクセス

Androidデバイス（バージョン6以上）にて、以下の手順を行います。

1. Google Play ServicesがAndroidデバイス上で最新担っているかどうかを確認します。
1. Googleアプリの最新のバージョンが実行されていることを確認します。Google Play Storeの[Google app](https://play.google.com/store/apps/details?id=com.google.android.apps.googleassistant)のページに訪問してください。もしあなたのデバイスが現れたら、 **UPDATE** ボタンをクリックしてください。
1. 同一のテストアカウントでデバイスにサインインします。
1. 更新されたGoogleアプリを開きます（Homeボタンをタッチしてそれを保持します）。
1. 右上にある ![](https://developers.google.com/actions/images/settings-icon.png) アイコンをタップします。
1. **Explore** を選択して、 ![](https://developers.google.com/actions/images/3-dots-icon.svg) アイコンをタップします。
1. **Settings** を選択します。正しいアカウントでサインインしているかどうか確認します。
1. **Services** の下で、 **Home control** を選択します。

## あなたのプロジェクトを共有する

他の人々をあなたのプロジェクトに招待します。これにより、あなたがそれを公開する前に、彼らはあなたのアシスタントアプリをテストすることができます。あなたのアプリは、テストのために少なくとも一つのアクションを持っている必要があります。

1. [Actions on Google Developer Console](https://console.actions.google.com/) に行き、あなたのプロジェクトを選択します。
1. 左のナビゲーションにて、歯車のアイコンを選択し、その後 **Permissions** を選択します。これにより、あなたのプロジェクトのIAMページにリダイレクトされます。
1. あなたのプロジェクトにチームメンバーのアクセスをセットするために、 **ADD** をクリックします。あなたはプロジェクトのオーナーでなければなりません。
1. **Members** 領域に、メンバーのメールアドレスをタイプします。 **Roles** メニューにて、アクセスを閲覧するために、 **Project > Viewer** を選択します。<br>
   > **注:** もしチームに共有したい場合は、あなたのチーム向けにGoogle Groupを作って、全てのグループメンバーにアクセスを付与するために、 **Members** 領域にGoogle Groupのメールアドレス（例: your-team-name@googlegroups.com）を追加することができます。

1. Actions on Google Developer Consoleに戻り、Simulatorを選択して **SHARE** をクリックします。
1. 彼らにプロジェクトを共有するために、ダイアログボックスの手順 2にて提供されているリンクをコピーします。
1. 他のユーザにリンクをシェアした後、そのユーザはActions Simulatorのランディングページへのリンクに従う必要がありますが、テストステータスを **Active** に切り替えて、 **START** をクリックします。
   > **注:** システムは追加されたメンバーに積極的な通知は行いません。
   
## 公開

審査のためにアプリを提出する前に、Googleのサードパーティ認定ベンダーがアプリをテストするケースであるため、すべてのアプリ情報フィールドに記入して、 [この一連のQAテストケース](https://docs.google.com/spreadsheets/d/1n6iOOX6j42TY-K9UoVG6g-fh5S1DrZ38-1zvEupMwBo/edit?usp=sharing) を実行してください。すべてのテストに合格したら、審査のために [アプリを提出](https://developers.google.com/actions/sdk/submit-app) し、提出プロセス中の指示に従ってください。

### 分析

あなたのアプリが公開されると、あなたはActions console内で便利なアプリの分析へのアクセス権を得るでしょう。それは以下を含みます。

* 日ごとのアクティブユーザー
* コマンド実行回数
* エラー
* アプリのレイテンシー
* ディレクトリの統計

![](https://developers.google.com/actions/images/smarthome-analytics-usage.png){:width="608px"}<br>
**図1.** Usage タブ

![](https://developers.google.com/actions/images/smarthome-analytics-health.png){:width="608px"}<br>
**図2.** Health タブ

![](https://developers.google.com/actions/images/smarthome-analytics-directory.png){:width="608px"}<br>
**図3.** Directory タブ

---

[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)
[原文](https://developers.google.com/actions/smarthome/testing-deploying)
