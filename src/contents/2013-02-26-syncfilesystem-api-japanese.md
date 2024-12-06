---
layout: post
status: publish
published: true
title: SyncFileSystem APIの仕様ドラフトを和訳してみました
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2275
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2275
date: '2013-02-26 16:01:55 +0900'
date_gmt: '2013-02-26 07:01:55 +0900'
categories:
- Chrome extension
---

[時代はそろそろ「オフラインファースト」になる模様](http://www.publickey1.jp/blog/13/2014web.html)ですが、そのためにはブラウザが情報をオフラインで扱うことを可能にする機能が充実してくれないといけません。いろんなAPIを組み合わせて駆使していけば、現状でもできないことはないです。しかし、もちろん僕らは手軽にオフライン対応Webアプリを作りたいですよね？同期処理や競合など、自分で面倒見たくは誰しもないはずです。

SyncFileSystem APIはそんな僕らの要求に応えてくれるかもしれないと考えています。まだChrome Canaryでしか試せませんが、その仕様のドラフトはすでに公開されていますので、和訳してみました。

元の仕様のドラフトは、以下にあります。

[SyncFileSystem API](https://sites.google.com/a/chromium.org/dev/developers/design-documents/extensions/proposed-changes/apis-under-development/syncfilesystem-api) - The Chromium Projects

---

# SyncFileSystem API

## 提案日付
  最終更新 2013-02-11

## このAPIの主要連絡先は誰ですか？
kinuko[at]chromium.org

## 概要

Sync FileSystem APIは、Google Driveのようにストレージの内容がクラウドバックエンドサービスを経由してクライアント間で自動的に同期されることを除いて、アプリケーションごとにプライベートでサンドボックス化されたファイルストレージである、もう一つのローカルオフラインHTML5ファイルシステムストレージとして登場したストレージAPIです。私たちは、最初はそのバックエンドサービスとしてGoogle Driveのみをターゲットにしています。

## ユースケース

このAPIは、同じデータを異なるクライアント間で利用可能にするためにアプリがクラウドストレージにデータを保存/同期したいときに、オフラインまたはキャッシュ目的のためにローカルでユーザによって作成されたデータ（または任意の他のバイナリデータ）を格納することに使えます。

このAPIは、クラウドサービスに格納される任意のユーザの文書をアクセスするためのものではなく（例えばあなたのGoogle Docs文書を開くためではありません）、アプリ特有の同期可能なストレージを提供するものです。

## あなたは内部あるいは外部でこのAPIに興味を持っている誰かを知っていますか？

私は何人かいることを聞いていますが、現時点では誰かはわかりません。

## このAPIはWebプラットフォームの一部になることができますか？

おそらく、一部となり得ます。このAPIは既存のowp（訳注: Open Web Platform）の提案である「File API: Directories and System（
[http://www.w3.org/TR/file-system-api/](http://www.w3.org/TR/file-system-api/)）」と強い関連を持っています。

## あなたはこのAPIをかなり安定したものと期待しますか？将来どの程度拡張あるいは変更されるでしょうか？

このAPIの中で提案されているAPIは、より詳細化された手動での衝突ハンドリングや同期動作の制御をサポートするための追加の機能が将来加わるかもしれませんが、安定することが想定されています。

## もし複数の拡張機能が同時にこのAPIを使った場合、それらは互いに競合する可能性がありますか？もしそうなら、この問題を緩和するためにどのような提案がありますか？

いいえ、複数の拡張機能はそれ自身のプライベートなファイルストレージが使われるべきです。

## 必要となる、あるいはあなたのAPIによって潜在的に影響される全てのUI面のリスト:

* 高度な同期設定（'Apps'同期の無効化は、アプリのデータ同期が向こうになります）

* アプリ設定UI（
[http://crbug.com/141584](http://crbug.com/141584)が適用された場合）

## 拡張APIを使って行われるアクションは、拡張機能に明らかに起因すべきです。ユーザはこの新しいAPIが使われているときに問い合わせることができますか？どうやって？

現在のアーキテクチャでは、ユーザは以下のようにこのAPIの利用を問い合わせることができます:

* ユーザの同期バックエンドサービスストレージ（例: Drive）を見ること。
  具体的には、現在の実装はサインインされたユーザのDriveストレージの"Chrome Syncable Storage"という名前のフォルダ内にファイルを保存します。

* アプリのサンドボックス化されたローカルディレクトリの中を見ること。

* chrome.syncFileSystem.getUsageAndQuota APIによって返される使用バイトを問い合わせること。

ディスクスペースのためのアプリ設定UIを持てば、APIがいつローカルに何かを格納したかをユーザが問い合わせることができるようにもなります。

## どれくらいこのAPIが悪用されますか？

誰かがユーザのローカルディスクや同期されたバックエンドストレージを使い尽くしたとしても、ローカルおよびリモートのストレージ容量によって調整される割り当て以上の利用はできません。

## あなたがDr.邪悪な拡張機能作者だとイメージして、あなたのAPIを使ってコミット可能な3つの最悪な悪行をリスト化してください（もし良いものがあれば、もっと自由に追加してください）。
1)
2)
3)

## あなたはこの新しいAPIによって可能となる悪行を制限するために、どんなセキュリティUIや他の緩和策を提案しますか？

アプリ設定UIは、ローカルおよびリモート（例: 同期バックエンドストレージ）側の両方のストレージ利用情報を表示することができます。そして、このAPIによって格納されたデータを削除するUIも提供可能です。

## さて博士、最後の挑戦です:
あなたのAPIの利用者は、あなたのAPIを利用するユーザのシステムに対して、利用者がシステムから削除しても戻らない何か永続的な変化をもたらしますか？

このAPIは、消費者（訳注: このAPIを利用した何か）をアンインストールした後も、このAPIによって同期されたユーザのデータをリモートの同期バックエンドストレージに残します。しかし、ユーザはその同期バックエンドストレージサービスに直接訪問することで、そのデータを削除できるはずです（例: Google DriveのWebインタフェース）。

## もしこのAPIが存在しなかった場合、あなたは希望する機能をどのように実装しますか？

技術的には、私はオフラインストレージAPI（例: FileSystem APIやIndexedDB）とリモートストレージサービスのAPI（例: Drive SDK API）を組み合わせて、JS層で全ての同期ロジックを実装します。しかし、それは通常より多くのエンジニアリング力を必要とします。サーバサイドの変更プッシュ通知や、効率の良いバッググラウンドでのポーリングメカニズムを使ったカスタムJSアプリを統合するには、（熱心なアプリでは、それ自身で実装していますが）いくつかの制限もあるでしょう。

加えて、アプリにクラウドストレージへのアクセス権限を渡すことは、その認証アイデンティティにおけるストレージ上の全てのファイルにアプリがアクセスできることを通常意味します（例: Drive SDKへのアクセス権限をアプリに与えることは、事実上ユーザのDrive上の全ての文書にアプリがアクセスできることを許可することになります）。このAPは、そのような詳細をJS層から隠し、アプリのプライベートストレージに対してのみの隔離されたアクセスを与えます。

## マニフェストへの変更のドラフト

このAPIは、新しいパーミッション"syncFileSystem"を追加します。

## API仕様のドラフト

chrome.syncFileSystem.requestFileSystem(function (filesystem) { ... });

新しく'同期可能な'ファイルストレージを、要求してきているアプリのためにリクエストします。これは、FileSystem API（
[http://www.w3.org/TR/file-system-api/](http://www.w3.org/TR/file-system-api/)）におけるローカルオフラインファイルシステムと同じ方法で処理可能なFileSystemオブジェクトを返します。これは、アプリがルートディレクトリやファイルリストの取得のためのDirectoryReaderの生成、ファイル内にバイナリデータを書き込むためのFileWriterの取得、ファイルシステム内や他のファイルシステム間でのコピーや移動が可能になることを意味します（ドラッグ＆ドロップされたファイルエントリ群や、chrome.fileSystem APIによって開かれたファイル、chrome.mediaGalleries APIにて利用可能なメディアファイルなどを含みます）。典型的なコード例は以下のようになります:

```
chrome.syncFileSystem.requestFileSystem(function (fs) {
   // FileSystem APIは、ちょうど返された'fs'で動作するはず。
   fs.root.getFile(
         'test.txt', {create:true}, getEntryCallback, errorCallback);
});
```

chrome.syncFileSystem.getUsageAndQuota(function (storageInfo) { ... });

アプリのための'同期可能な'ファイルストレージに関する現状の使用状況と割り当て状況をバイト数で返します。（制限: 現状返却される割り当てサイズは、クラウドサービス側の割り当て設定を適切に反映しないかもしれません。）典型的なコード例は以下のようになります:

```
chrome.syncFileSystem.getUsageAndQuota(
                             fileSystem, function (storageInfo) {
   updateUsageInfo(storageInfo.usageBytes);
   updateQuotaInfo(storageInfo.quotaBytes);
});
```

chrome.syncFileSystem.getFileStatus(fileEntry, function (status) { ... });

現在のファイル同期ステータスを返します。ステータス値は、'synced', 'pending', 'conflicting'のいずれかとなります。

* 'synced'ステータスは、ファイルが完全に同期されていることを意味します。つまり、クラウドストレージへ同期されていない保留状態のローカルな変更はありません。（ファイルのステータスが'synced'でも、まだフェッチされていないクラウド側の変更が保留されている可能性はあります）

* 'pending'ステータスは、ファイルがクラウドストレージへ同期されていない保留状態の変更を持っていることを意味します。通常ローカルの変更は、もしアプリがオンラインで実行されている場合、（ほとんど）直ちにクラウドへ同期されます。保留状態の変更が同期されたときは、'synced'ステータスを持つonFileStatusChangedイベントが発生します（イベントに関する詳しい情報は以下を参照）。

* 'conflicting'ステータスは、ローカルとクラウド（例: 他のクライアント）の両方で競合していることを意味します。ファイルが競合ステータスになったときは、'conflicting'ステータスを持つonFileStatusChangedイベントが発生します。

現状のAPIでは、競合したファイルのための自動的な調整メカニズムは提供していません。もし何もしなければ、そのファイルは他のクライアントで行われるリモートの変更から切り離され続けるでしょう。アプリは、切り離されたローカルオフラインファイルとして、そのファイルを読み書きし続けることができます。その競合は、ローカルファイルまたは競合バージョンのファイルのどちらかが削除されたときに、自動的に解決されます。競合ステータスが解決されたときは、'synced'ステータスを持つonFileStatusChangedイベントが発生します。

chrome.syncFileSystem.onFileStatusChanged.addListener(function (fileInfo) { ... });

このイベントは、ファイルの同期ステータスが変化したときに発生します。'fileInfo'オブジェクトは、以下のフィールドを含んでいます:

* fileEntryは、ステータスが変更された対象のファイルのFileEntryオブジェクトです。このオブジェクトは、もし同期アクションが'deleted'だった場合は、存在しないファイルを刺す蚊も知れません。

* statusは、イベントが発生した結果のファイルステータスです。その値は、'synced', 'pending', 'conflicting'のいずれかになります。

* actionは、このイベントが発生するために行われた同期アクションです。このフィールドは、ステータスが'synced'の場合にのみ有効な値を持ちます。その値は、'added', 'updated', 'deleted'のいずれかとなります。

* directionは、そのイベントにおける同期の方向です。このフィールドは、ステータスが'synced'の場合にのみ有効な値を持ちます。その値は、'local_to_remote'または'remote_to_local'のどちらかとなります。

例えば、ファイルが保留状態の変更を持っていて、'pending'状態と仮定します。その変更が同期されようとしていますが、アプリがオフライン状態になってたとします。その後、同期サービスが保留状態のローカル変更を検知してクラウドストレージに変更をアップロードしたとき、そのサービスはonFileStatusChangedイベントを発生します。そのイベントは、{ fileEntry:a fileEntry for the file, status: 'synced', action: 'updated', direction: 'local_to_remote' } という値を持つでしょう。

同様に、ローカルの活動に関連して、同期サービスはもう一つのクライアントによって作られたリモートの変更を検知して、そしてクラウドストレージからローカルストレージに新しいイメージをダウンロードします。もしリモートの変更が新しいファイルの追加だった場合、イベントは { fileEntry: a fileEntry for the file, status: 'synced', action: 'added', direction: 'remote_to_local' } という値を持って発生します。

もしローカル側とリモート側の両方で同じファイルの変更が競合した場合、ファイルステータスは'conflicting'状態に変更され、それは同期サービスから切り離されます（競合が解決されるまで同期されません）。このケースにおいて、{ fileEntry: a fileEntry for the file, status: 'conflicting', action: null, direction: null } という値を持つイベントが発生します。

## API IDLのドラフト

```
namespace syncFileSystem {
  enum SyncAction {
    added, updated, deleted
  };

enum ServiceStatus {
    // 同期サービスの初期化中 （データベースからデータをリストアする、
    // サービスへの接続や認証をチェックするなど）。
    initializing,

// 同期サービスが起動し実行されている。
    running,

// リモートサービスを処理するためにユーザによって認証される必要があるため、
    // 同期サービスはファイルを同期していません。
    authentication_required,

// いくつかのリカバリ可能なエラーによってリモートサービスが（一時的に）利用
    // 不可能なため、同期サービスはファイルを同期していません。例として、ネット
    // ワークがオフラインである、リモートサービスがダウンしている、または到達不能
    // などであること、があげられます。より詳細は、OnServiceInfoUpdated内の
    // |description|パラメータによって与えられるはずです（サービス特有の詳細を
    // 含むこともあります）。
    temporary_unavailable,

// 同期サービスは使用停止であり、コンテンツは同期されないでしょう。
    // （例: これは、ユーザがリモートサービスのアカウントを持っていない
    // ときや、同期サービスがリカバリ不能なエラーを持っているときに起こ
    // るでしょう。）
    disabled
  };

enum FileStatus {
    // 競合せず、保留状態のローカル変更もありません。
    synced,

// 一つ以上の同期されていない保留されたローカル変更があります。
    pending,

// ファイルがリモートバージョンと競合しているので、手動で解決されるべきです。
    conflicting
  };

enum SyncDirection {
    local_to_remote, remote_to_local
  };

dictionary FileInfo {
    // |fileEntry|は、同期されたファイルの名前とパス情報を含むでしょう。
    // ファイルの削除では、fileEntry情報は依然として利用可能ですが、ファイルは
    // もはや存在しないでしょう。
    [instanceOf=FileEntry] object fileEntry;

FileStatus status;

// ステータスが'synced'の場合のみ適用されます。
    SyncAction? action;

// ステータスが'synced'の場合のみ適用されます。
    SyncDirection? direction;
  };

dictionary StorageInfo {
    long usageBytes;
    long quotaBytes;
  };

dictionary ServiceInfo {
    ServiceStatus state;
    DOMString description;
  };

// [nodoc] requestFileSystemのためのコールバックの型
  callback GetFileSystemCallback =
      void ([instanceOf=DOMFileSystem] object fileSystem);

// [nodoc] getUsageAndQuotaのためのコールバックの型
  callback QuotaAndUsageCallback = void (StorageInfo info);

// もし処理が成功した場合はtrueを返します。
  callback DeleteFileSystemCallback = void (boolean result);

// [nodoc] getFileStatusのためのコールバックの型
  callback GetFileStatusCallback = void (FileStatus status);

interface Functions {
    // Google Driveによって支援された同期可能なファイルシステムを返します。
    // 返されたDOMFileSystemインスタンスは、一時的および永続的なファイルシステム
    // (http://www.w3.org/TR/file-system-api/)と同じ方法で処理を行えます。
    // 同じアプリからのこれに対する複数回の呼び出しは、同じファイルシステムへの
    // 同じハンドルを返すでしょう。
    static void requestFileSystem(GetFileSystemCallback callback);

// |serviceName|を持つ同期ファイルシステムの使用状況と割り当て状況を
    // バイト数で取得します。
    static void getUsageAndQuota([instanceOf=DOMFileSystem] object fileSystem,
                                 QuotaAndUsageCallback callback);

// 同期可能なファイルシステム内の全てのものを削除します。
    static void deleteFileSystem([instanceOf=DOMFileSystem] object fileSystem,
                                 DeleteFileSystemCallback callback);

// 与えられた|fileEntry|のFileStatusを取得します。
    static void getFileStatus([instanceOf=FileEntry] object fileEntry,
                              GetFileStatusCallback callback);
  };

interface Events {
    // エラーや他のステータス変更が同期バックエンドで発生したときに発火します。
    // （例: ネットワークや認証エラーなどで同期が一時的にできなくなった）
    static void onServiceStatusChanged(ServiceInfo detail);

// ファイルがバックグラウンドの同期サービスによって変更されたときに発火します。
    static void onFileStatusChanged(FileInfo detail);
  };

};
```
