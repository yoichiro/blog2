---
layout: post
status: publish
published: true
title: Chrome OSに仮想ファイルシステムを提供するためのAPIのリファレンスを和訳してみました
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 3226
wordpress_url: https://www.eisbahn.jp/yoichiro/?p=3226
date: '2015-01-12 22:53:32 +0900'
date_gmt: '2015-01-12 13:53:32 +0900'
categories:
- Chrome extension
---

最近Chromebookを買いました。Dell Chromebook 11 です。ちゃちい作りかと思ってたのですが、かなりしっかりした作りで、かなり気に入りました。ただし、macbook airを持ってるので、もちろん普段はChromebookを使う機会は少ないです。ただし、僕にはどうしても作ってみたいものがあり、その動作検証のため、そして僕が作ったものによって、僕がChromebookを利用する機会が増えるように、と購入を決意しています。

その作りたいものとは何か、それはFile System Providerのとある実装です。Chrome MySQL Adminの開発で培った技術を別のことに応用したくなり、また開発意欲が強くなってます。その準備として、File System Provider API Referenceを和訳してみました。ここではその和訳を掲載したいと思います。

原文: 
[chrome.fileSystemProvider](https://developer.chrome.com/apps/fileSystemProvider)

****


# chrome.fileSystemProvider

* 説明: Chrome OSのファイルマネージャからアクセス可能なファイルシステムを作成するために、chrome.fileSystemProvider APIを利用します。

* 利用可能: Chrome 40以降。警告:これは現在
Beta channelです。
[詳しくはこちら。](https://developer.chrome.com/apps/api_index#beta_apis)

* 権限: "fileSystemProvider"

重要: このAPIはChrome OSのみで機能します。

# Manifest

File System Provider APIを使うために、あなたは
[extension manifest](https://developer.chrome.com/apps/manifest)の中で"fileSystemProvider"パーミッションを宣言する必要があります。例えば以下です。

```
{
  "name": "My extension",
  ...
  "permissions": [
    "fileSystemProvider"
  ],
  ...
}
```

# 概要

File System Provider APIは、Chrome OS内のファイルマネージャが利用可能な仮想ファイルシステムを拡張機能がサポートできるようにします。Drive以外のクラウドサービスにあるファイルにアクセスしたり、アーカイブを展開したりするユースケースが含まれます。

# ファイルハンドラ

提供されるファイルシステムは、（リモートサーバのような）外部ソースからファイルシステムの内容を提供すること、またはその入力として（アーカイブのような）ローカルファイルを利用すること、のどちらかが可能です。

２番目のケースでは、提供する拡張機能は、ファイルがファイルマネージャ内で選択された際にそれを起動するための
[file_handlers](https://developer.chrome.com/apps/manifest/file_handlers)マニフェストエントリを持つべきです。拡張機能がハンドルされたファイルを実行する時、拡張機能はファイルシステムをマウントし、提供されたファイルから内容を提供することを開始します。

# ライフサイクル

マウント済みの提供されたファイルシステムはChromeによって記憶され、リブートやリスタートの後に自動的に再マウントされます。従って、提供する拡張機能によってファイルシステムが
[マウント](https://developer.chrome.com/apps/method-mount)されたら、拡張機能がアンロードされる、あるいは拡張機能が
[unmount](https://developer.chrome.com/apps/fileSystemProvider#method-unmount)メソッドを呼ぶことのどちらかまで、マウントは継続されます。

ファイルハンドラとして機能するために、ハンドルされたファイルは、リブートやサスペンド、提供される拡張機能のイベントページのレジュームの後でそれにアクセスするために、格納される必要があるでしょう。そのような場合は、
[chrome.fileSystem.retainEntry](https://developer.chrome.com/apps/fileSystem#method-retainEntry)や
[chrome.fileSystem.restoreEntry](https://developer.chrome.com/apps/fileSystem#method-restoreEntry)が使われることになります。

# サマリー

型

* [ProviderError](https://developer.chrome.com/apps/fileSystemProvider#type-ProviderError)

* [ChangeType](https://developer.chrome.com/apps/fileSystemProvider#type-ChangeType)

* [EntryMetadata](https://developer.chrome.com/apps/fileSystemProvider#type-EntryMetadata)

* [Watcher](https://developer.chrome.com/apps/fileSystemProvider#type-Watcher)

* [AddWatcherRequestedOptions](https://developer.chrome.com/apps/fileSystemProvider#type-AddWatcherRequestedOptions)

* [RemoveWatcherRequestedOptions](https://developer.chrome.com/apps/fileSystemProvider#type-RemoveWatcherRequestedOptions)

* [NotifyOptions](https://developer.chrome.com/apps/fileSystemProvider#type-NotifyOptions)

メソッド

* [mount](https://developer.chrome.com/apps/fileSystemProvider#method-mount) - chrome.fileSystemProvider.mount(object options, function callback)

* [unmount](https://developer.chrome.com/apps/fileSystemProvider#method-unmount) - chrome.fileSystemProvider.unmount(object options, function callback)

* [getAll](https://developer.chrome.com/apps/fileSystemProvider#method-getAll) - chrome.fileSystemProvider.getAll(function callback)

イベント

* [onUnmountRequested](https://developer.chrome.com/apps/fileSystemProvider#event-onUnmountRequested)

* [onGetMetadataRequested](https://developer.chrome.com/apps/fileSystemProvider#event-onGetMetadataRequested)

* [onReadDirectoryRequested](https://developer.chrome.com/apps/fileSystemProvider#event-onReadDirectoryRequested)

* [onOpenFileRequested](https://developer.chrome.com/apps/fileSystemProvider#event-onOpenFileRequested)

* [onCloseFileRequested](https://developer.chrome.com/apps/fileSystemProvider#event-onCloseFileRequested)

* [onReadFileRequested](https://developer.chrome.com/apps/fileSystemProvider#event-onReadFileRequested)

* [onCreateDirectoryRequested](https://developer.chrome.com/apps/fileSystemProvider#event-onCreateDirectoryRequested)

* [onDeleteEntryRequested](https://developer.chrome.com/apps/fileSystemProvider#event-onDeleteEntryRequested)

* [onCreateFileRequested](https://developer.chrome.com/apps/fileSystemProvider#event-onCreateFileRequested)

* [onCopyEntryRequested](https://developer.chrome.com/apps/fileSystemProvider#event-onCopyEntryRequested)

* [onMoveEntryRequested](https://developer.chrome.com/apps/fileSystemProvider#event-onMoveEntryRequested)

* [onTruncateRequested](https://developer.chrome.com/apps/fileSystemProvider#event-onTruncateRequested)

* [onWriteFileRequested](https://developer.chrome.com/apps/fileSystemProvider#event-onWriteFileRequested)

* [onAbortRequested](https://developer.chrome.com/apps/fileSystemProvider#event-onAbortRequested)

# 型

ProviderError

APIのメソッド呼び出しの際のエラーの場合だけではないリクエストに対するレスポンスでの提供された拡張機能によって使われるエラーコード。成功の場合は、OKが使われなければなりません。

* Enum: "OK", "FAILED", "IN_USE", "EXISTS", "NOT_FOUND", "ACCESS_DENIED", "TOO_MANY_OPENED", "NO_MEMORY", "NO_SPACE", "NOT_A_DIRECTORY", "INVALID_OPERATION", "SECURITY", "ABORT", "NOT_A_FILE", "NOT_EMPTY", "INVALID_URL", or "IO"

ChangeType

監視されるディレクトリで検知された変更の種別です。

* Enum: "CHANGED", or "DELETED"

EntryMetadata

プロパティ

* isDirectory (boolean): ディレクトリの場合はTrueです。

* name (string): このエントリの名前（フルパス名ではない）です。"/"を含んではいけません。

* size (double): バイト単位のファイルサイズです。

* modificationTime (Date): このエントリの最終変更時間です。

* mimeType (string: 任意): エントリのMIMEタイプです。

* thumbnail (string: 任意): PNG, JPEG, WEBPフォーマットのどれかのサムネイル画像のdata URIです。最大32KBです。任意項目ですが、onGetMetadataRequestedイベントによって明示的に要求される時のみは、これは提供されなければなりません。

Watcher

プロパティ

* entryPath (string): 監視されているエントリのパスです。

* recursive (boolean): 監視が再帰的に全ての子エントリを含むかどうかです。これはディレクトリのみtrueにできます。

* lastTag (string: 任意): ウォッチャーへの最後の通知によって使われるタグです。

AddWatcherRequestedOptions

プロパティ

* fileSystemId (string): この動作に関係するファイルシステムの識別子です。

* requestId (integer): このリクエストのユニークな識別子です。

* entryPath (string): 監視されているエントリのパスです。

* recursive (boolean): 監視が再帰的に全ての子エントリを含むかどうかです。これはディレクトリのみtrueにできます。

RemoveWatcherRequestedOptions

プロパティ

* fileSystemId (string): この動作に関連するファイルシステムの識別子です。

* requestId (integer): このリクエストのユニークな識別子です。

* entryPath (string): 監視されるエントリのパスです。

* recursive (boolean): ウォッチャーのモードです。

NotifyOptions

プロパティ

* fileSystemId (string): この変更に関連するファイルシステムの識別子です。

* observedPath (string): 監視されるエントリのパスです。

* recursive (boolean): 監視されるエントリのモードです。

* changeType (
[ChangeType](https://developer.chrome.com/apps/fileSystemProvider#type-ChangeType)): 監視されるエントリに起きた変更の種別です。もしDELETEDの場合、その後監視されたエントリは監視対象エントリの一覧から自動的に削除されるでしょう。* changes (array of object: 任意): 監視されるディレクトリ内にあるエントリの変更一覧です（そのエントリ自身も含みます）。
各オブジェクトのプロパティ:

* entryPath (string): 変更されたエントリのパスです。

* changeType (
[ChangeType](https://developer.chrome.com/apps/fileSystemProvider#type-ChangeType)): エントリに起きた変更の種別です。

* tag (string: 任意): 通知のタグです。supportsNotifyTagオプション付きでファイルシステムがマウントされた場合は、必須です。ファイルシステムがシャットダウンされた時でも、このフラグは変更された更新内容の通知を提供するために必要であることに注意してください。

# メソッド

mount

```
chrome.fileSystemProvider.mount(object options, function callback)
```

与えられたfileSystemIdとdisplayNameを使ってファイルシステムをマウントします。displayNameは、Files.appの左パネルに表示されます。displayNameは、"/"を含む任意の文字を含むことができますが、空の文字列にはできません。displayNameは説明的でなければなりませんが、ユニークである必要はありません。fileSystemIdは、空の文字列ではいけません。

エラーの場合は、chrome.runtime.lastErrorにエラーコードに相当する値がセットされるでしょう。

プロパティ

* options (object):

* fileSystemId (string): ファイルシステムの文字列識別子です。それぞれの拡張機能でユニークでなければなりません。

* displayName (string): ファイルシステムの人間が読むことができる名前です。

* writable (boolean: 任意): ファイルシステムの内容を変更することが可能な動作をファイルシステムがサポートするかどうか（ファイルの作成、削除、書き込みなど）です。

* openedFilesLimit (integer: 任意): 
完全な実装ではありません。このAPIを試すために、あなたはChromiumをソースコードからビルドする必要があります。[詳しくはここをご覧ください。](http://www.chromium.org/developers)
1回に開くことが可能なファイルの最大数です。もし指定されなかった場合は、制限されません。

* callback (function: 任意): 成功または失敗を示すための一般的な結果のコールバックです。もしコールバックパラメータを指定する場合は、以下のような関数になるはずです:

```
function() {...};
```

unmount

```
chrome.fileSystemProvider.unmount(object options, function callback)
```

与えられたfileSystemIdのファイルシステムのマウントを解除します。これは、onUnmountRequestedが呼び出される後に呼ばれなければなりません。また、提供される拡張機能は、（例: 接続を失った時やファイルエラーなどの場合など）要求されなかった場合にマウント解除を実行することを決定することができます。

エラーの場合は、chrome.runtime.lastErrorにエラーコードに相当する値がセットされるでしょう。

プロパティ

* options (object):

* fileSystemId (string): マウント解除されるファイルシステムの識別子です。

* callback (function: 任意): 成功または失敗を示すための一般的な結果のコールバックです。もしコールバックパラメータを指定する場合は、以下のような関数になるはずです:

```
function() {...};
```

getAll

```
chrome.fileSystemProvider.getAll(function callback)
```

拡張機能によってマウントされた全てのファイルシステムを返します。

* callback (function): getAll()関数の結果を受け取るためのコールバックです。コールバックのパラメータは以下のような関数になるはずです:

```
function(array of object fileSystems) {...};
```

* fileSystems (array of object): 各オブジェクトのプロパティ:

* fileSystemId (string): ファイルシステムの識別子です。

* displayName (string): ファイルシステムの人間が読める名前です。

* writable (boolean): ファイルシステムの内容を変更することが可能な動作をファイルシステムがサポートするかどうか（ファイルの作成、削除、書き込みなど）です。

# イベント

onUnmountRequested

fileSystemId識別子を持つファイルシステムのマウント解除が要求されるときに発火します。結果として、unmount APIメソッドはsuccessCallbackと共に呼ばれなければなりません。もしマウント解除ができない場合（例: 動作のペンディングなど）、その時はerrorCallbackが呼び出されなければなりません。

## addListener

```
chrome.fileSystemProvider.onUnmountRequested.addListener(function callback)
```

パラメータ

* callback (function): コールバックパラメータは以下のような関数になるはずです:

```
function(object options, function successCallback, function errorCallback) {...};
```

* options (object):

* fileSystemId (string): マウント解除されるファイルシステムの識別子です。

* requestId (integer)

* successCallback (function): 提供される拡張機能によって成功の場合に呼び出されるコールバックです。

* errorCallback (function): 提供される拡張機能によって失敗の場合に呼び出されるコールバックです。

パラメータは以下のような関数になるはずです:

```
function( ProviderError error) {...};
```

* error (
[ProviderError](https://developer.chrome.com/apps/fileSystemProvider#type-ProviderError))

onGetMetadataRequested

エントリパスのファイルまたはディレクトリのメタデータが要求されるときに発火します。メタデータはsuccessCallback呼び出しを使って返却されなければなりません。エラーの場合は、errorCallbackを呼び出さなければなりません。

## addListener

```
chrome.fileSystemProvider.onGetMetadataRequested.addListener(function callback)
```

パラメータ

* callback (function): コールバックパラメータは以下のような関数になるはずです:

```
function(object options, function successCallback, function errorCallback) {...};
```

* options (object):

* fileSystemId (string): この動作に関係するファイルシステムの識別子です。

* requestId (integer): この要求のユニークな識別子です。

* entryPath (string): メタデータを取得するエントリのパスです。

* thumbnail (boolean): サムネイルが必要な場合はtrueがセットされます。

* successCallback (function): onGetMetadataRequestedイベントの成功コールバックです。
パラメータ:

* metadata (
[EntryMetadata](https://developer.chrome.com/apps/fileSystemProvider#type-EntryMetadata))

* errorCallback (function): 提供される拡張機能によって失敗の場合に呼び出されるコールバックです。

パラメータは以下のような関数になるはずです:

```
function( ProviderError error) {...};
```

* error (
[ProviderError](https://developer.chrome.com/apps/fileSystemProvider#type-ProviderError))

onReadDirectoryRequested

directoryPathのディレクトリの内容が要求されるときに発火します。結果は、successCallbackを数回呼び出すことによってチャンクで返される必要があります。エラーの場合は、errorCallbackを呼び出さなければなりません。

## addListener

```
chrome.fileSystemProvider.onReadDirectoryRequested.addListener(function callback)
```

パラメータ

* callback (function): コールバックパラメータは以下のような関数になるはずです:

```
function(object options, function successCallback, function errorCallback) {...};
```

* options (object):

* fileSystemId (string): この動作に関係するファイルシステムの識別子です。

* requestId (integer): この要求のユニークな識別子です。

* directoryPath (string): 内容を要求されたディレクトリのパスです。

* successCallback (function): onReadDirectoryRequiestedイベントの成功コールバックです。もし追加のエントリが返却される場合は、hasMoreはtrueが返される必要があり、また追加のエントリを再び返す必要があります。もし追加のエントリが利用できない場合は、その時はhasMoreはfalseを返します。
パラメータ:

* array of metadata (
[EntryMetadata](https://developer.chrome.com/apps/fileSystemProvider#type-EntryMetadata))

* hasMore (boolean)

* errorCallback (function): 提供される拡張機能によって失敗の場合に呼び出されるコールバックです。

パラメータは以下のような関数になるはずです:

```
function( ProviderError error) {...};
```

* error (
[ProviderError](https://developer.chrome.com/apps/fileSystemProvider#type-ProviderError))

onOpenFileRequested

filePathのファイルのオープンが要求されるときに発火します。もしファイルが存在しない場合は、この動作は失敗としなければなりません。一度に開かれるファイルの上限数は、MountOptionsを使って指定可能です。

## addListener

```
chrome.fileSystemProvider.onOpenFileRequested.addListener(function callback)
```

パラメータ

* callback (function): コールバックパラメータは以下のような関数になるはずです:

```
function(object options, function successCallback, function errorCallback) {...};
```

* options (object):

* fileSystemId (string): この動作に関係するファイルシステムの識別子です。

* requestId (integer): 連続したread/write, close要求によって利用されることになる要求IDです。

* filePath (string): オープンされるファイルのパスです。

* mode (enum of "READ" or "WRITE"): ファイルが読み込みと書き込みのどちらで使われるか？

* successCallback (function): 提供される拡張機能によって成功の際に呼び出されるコールバックです。

* errorCallback (function): 提供される拡張機能によって失敗の場合に呼び出されるコールバックです。

パラメータは以下のような関数になるはずです:

```
function( ProviderError error) {...};
```

* error (
[ProviderError](https://developer.chrome.com/apps/fileSystemProvider#type-ProviderError))

onCloseFileRequested

openRequestIdで前回開かれたファイルを閉じる要求があったときに発火します。

## addListener

```
chrome.fileSystemProvider.onCloseFileRequested.addListener(function callback)
```

パラメータ

* callback (function): コールバックパラメータは以下のような関数になるはずです:

```
function(object options, function successCallback, function errorCallback) {...};
```

* options (object):

* fileSystemId (string): この動作に関係するファイルシステムの識別子です。

* requestId (integer): 連続したread/write, close要求によって利用されることになる要求IDです。

* openRequestId (integer): ファイルをオープンするために使われるリクエストIDです。

* successCallback (function): 提供される拡張機能によって成功の際に呼び出されるコールバックです。

* errorCallback (function): 提供される拡張機能によって失敗の場合に呼び出されるコールバックです。

パラメータは以下のような関数になるはずです:

```
function( ProviderError error) {...};
```

* error (
[ProviderError](https://developer.chrome.com/apps/fileSystemProvider#type-ProviderError))

onReadFileRequested

openRequestIdにて前回開かれたファイルの内容の読み込みが要求されるときに発火します。結果は、successCallbackの数回の呼び出しによってチャンクで返される必要があります。エラーの場合は、errorCallbackが呼び出されなければなりません。

## addListener

```
chrome.fileSystemProvider.onReadFileRequested.addListener(function callback)
```

パラメータ

* callback (function): コールバックパラメータは以下のような関数になるはずです:

```
function(object options, function successCallback, function errorCallback) {...};
```

* options (object):

* fileSystemId (string): この動作に関係するファイルシステムの識別子です。

* requestId (integer): このリクエストのユニークな識別子です。

* openRequestId (integer): ファイルをオープンするために使われるリクエストIDです。

* offset (double): 読み込みを開始するファイルの位置（バイト）です。

* length (double): 返されるバイト数です。

* successCallback (function): onReadFileRequestedイベントの成功コールバックです。もし追加のデータが返されることになる場合は、hasMoreはtrueを返す必要があり、また追加のエントリを再び呼び出す必要があります。もし利用可能なデータがない場合は、hasMoreはfalseをセットしなければなりません。
パラメータ:

* data (ArrayBuffer)

* hasMore (boolean)

* errorCallback (function): 提供される拡張機能によって失敗の場合に呼び出されるコールバックです。

パラメータは以下のような関数になるはずです:

```
function( ProviderError error) {...};
```

* error (
[ProviderError](https://developer.chrome.com/apps/fileSystemProvider#type-ProviderError))

onCreateDirectoryRequested

ディレクトリの作成が要求されるときに発火します。もし対象のディレクトリが既に存在している場合は、EXISTSエラーでこの動作を失敗させる必要があります。もしrecursiveがtrueの場合、ディレクトリパスの中で存在していないディレクトリは全て作成されなければなりません。

## addListener

```
chrome.fileSystemProvider.onCreateDirectoryRequested.addListener(function callback)
```

パラメータ

* callback (function): コールバックパラメータは以下のような関数になるはずです:

```
function(object options, function successCallback, function errorCallback) {...};
```

* options (object):

* fileSystemId (string): この動作に関係するファイルシステムの識別子です。

* requestId (integer): このリクエストのユニークな識別子です。

* directoryPath (string): 作成されるディレクトリのパスです。

* recursive (boolean): 再帰的に実行するかどうかです（ディレクトリのみ）。

* successCallback (function): 提供される拡張機能によって成功の場合に呼び出されるコールバックです。

* errorCallback (function): 提供される拡張機能によって失敗の場合に呼び出されるコールバックです。

パラメータは以下のような関数になるはずです:

```
function( ProviderError error) {...};
```

* error (
[ProviderError](https://developer.chrome.com/apps/fileSystemProvider#type-ProviderError))

onDeleteEntryRequested

エントリの削除が要求されるときに発火します。もしrecursiveがtrueで、エントリがディレクトリの場合、その中の全てのエントリも再帰的に削除されなければなりません。

## addListener

```
chrome.fileSystemProvider.onDeleteEntryRequested.addListener(function callback)
```

パラメータ

* callback (function): コールバックパラメータは以下のような関数になるはずです:

```
function(object options, function successCallback, function errorCallback) {...};
```

* options (object):

* fileSystemId (string): この動作に関係するファイルシステムの識別子です。

* requestId (integer): このリクエストのユニークな識別子です。

* directoryPath (string): 削除されるエントリのパスです。

* recursive (boolean): 再帰的に実行するかどうかです（ディレクトリのみ）。

* successCallback (function): 提供される拡張機能によって成功の場合に呼び出されるコールバックです。

* errorCallback (function): 提供される拡張機能によって失敗の場合に呼び出されるコールバックです。

パラメータは以下のような関数になるはずです:

```
function( ProviderError error) {...};
```

* error (
[ProviderError](https://developer.chrome.com/apps/fileSystemProvider#type-ProviderError))

onCreateFileRequested

ファイルの作成が要求されるときに発火します。もしファイルが既に存在している場合は、EXISTSエラーコードと共にerrorCallbackが呼び出されなければなりません。

## addListener

```
chrome.fileSystemProvider.onCreateFileRequested.addListener(function callback)
```

パラメータ

* callback (function): コールバックパラメータは以下のような関数になるはずです:

```
function(object options, function successCallback, function errorCallback) {...};
```

* options (object):

* fileSystemId (string): この動作に関係するファイルシステムの識別子です。

* requestId (integer): このリクエストのユニークな識別子です。

* filePath (string): 作成されるファイルのパスです。

* successCallback (function): 提供される拡張機能によって成功の場合に呼び出されるコールバックです。

* errorCallback (function): 提供される拡張機能によって失敗の場合に呼び出されるコールバックです。

パラメータは以下のような関数になるはずです:

```
function( ProviderError error) {...};
```

* error (
[ProviderError](https://developer.chrome.com/apps/fileSystemProvider#type-ProviderError))

onCopyEntryRequested

エントリのコピー（ディレクトリの場合は再帰的）が要求されるときに発火します。もしエラーが発生した場合は、errorCallbackが呼び出されなければなりません。

## addListener

```
chrome.fileSystemProvider.onCopyEntryRequested.addListener(function callback)
```

パラメータ

* callback (function): コールバックパラメータは以下のような関数になるはずです:

```
function(object options, function successCallback, function errorCallback) {...};
```

* options (object):

* fileSystemId (string): この動作に関係するファイルシステムの識別子です。

* requestId (integer): このリクエストのユニークな識別子です。

* sourcePath (string): コピーされるエントリのソースパスです。

* targetPath (string): コピーされるエントリのコピー先のパスです。

* successCallback (function): 提供される拡張機能によって成功の場合に呼び出されるコールバックです。

* errorCallback (function): 提供される拡張機能によって失敗の場合に呼び出されるコールバックです。

パラメータは以下のような関数になるはずです:

```
function( ProviderError error) {...};
```

* error (
[ProviderError](https://developer.chrome.com/apps/fileSystemProvider#type-ProviderError))

onMoveEntryRequested

エントリの移動（ディレクトリの場合は再帰的）が要求されるときに発火します。もしエラーが発生した場合は、errorCallbackが呼び出されなければなりません。

## addListener

```
chrome.fileSystemProvider.onMoveEntryRequested.addListener(function callback)
```

パラメータ

* callback (function): コールバックパラメータは以下のような関数になるはずです:

```
function(object options, function successCallback, function errorCallback) {...};
```

* options (object):

* fileSystemId (string): この動作に関係するファイルシステムの識別子です。

* requestId (integer): このリクエストのユニークな識別子です。

* sourcePath (string): 新しい場所に移動されるエントリのソースパスです。

* targetPath (string): 移動されるエントリの移動先のパスです。

* successCallback (function): 提供される拡張機能によって成功の場合に呼び出されるコールバックです。

* errorCallback (function): 提供される拡張機能によって失敗の場合に呼び出されるコールバックです。

パラメータは以下のような関数になるはずです:

```
function( ProviderError error) {...};
```

* error (
[ProviderError](https://developer.chrome.com/apps/fileSystemProvider#type-ProviderError))

onTruncateRequested

要求された長さにファイルを切り詰めることが要求されるときに発火します。もしエラーが発生した場合、errorCallbackが呼び出されなければなりません。

## addListener

```
chrome.fileSystemProvider.onTruncateRequested.addListener(function callback)
```

パラメータ

* callback (function): コールバックパラメータは以下のような関数になるはずです:

```
function(object options, function successCallback, function errorCallback) {...};
```

* options (object):

* fileSystemId (string): この動作に関係するファイルシステムの識別子です。

* requestId (integer): このリクエストのユニークな識別子です。

* filePath (string): 切り詰められるファイルのパスです。

* length (double): 処理が実行された後に残されるバイト数です。

* successCallback (function): 提供される拡張機能によって成功の場合に呼び出されるコールバックです。

* errorCallback (function): 提供される拡張機能によって失敗の場合に呼び出されるコールバックです。

パラメータは以下のような関数になるはずです:

```
function( ProviderError error) {...};
```

* error (
[ProviderError](https://developer.chrome.com/apps/fileSystemProvider#type-ProviderError))

onWriteFileRequested

openRequestIdで前に開かれたファイルの内容の書き換えが要求されるときに発火します。

## addListener

```
chrome.fileSystemProvider.onWriteFileRequested.addListener(function callback)
```

パラメータ

* callback (function): コールバックパラメータは以下のような関数になるはずです:

```
function(object options, function successCallback, function errorCallback) {...};
```

* options (object):

* fileSystemId (string): この動作に関係するファイルシステムの識別子です。

* requestId (integer): このリクエストのユニークな識別子です。

* openRequestId (integer): ファイルを開くために使われたリクエストIDです。

* offset (double): バイト列の書き込みを開始するファイルの位置（バイト）です。

* data (ArrayBuffer): ファイルに書かれるバイト列のバッファです。

* successCallback (function): 提供される拡張機能によって成功の場合に呼び出されるコールバックです。

* errorCallback (function): 提供される拡張機能によって失敗の場合に呼び出されるコールバックです。

パラメータは以下のような関数になるはずです:

```
function( ProviderError error) {...};
```

* error (
[ProviderError](https://developer.chrome.com/apps/fileSystemProvider#type-ProviderError))

onAbortRequested

operationRequestIdを持つ処理の中止が要求されるときに発火します。operationRequestIdで実行された処理が直ちに停止され、そしてこの中止リクエストのsuccessCallbackが実行されなければなりません。もし中止が失敗した場合、errorCallbackが呼び出されなければなりません。中止される処理のコールバックが呼び出されることがなく、それらが無視されるようにすることに注意してください。errorCallback呼び出しに関わらず、リクエストは強制的に中止されます。

## addListener

```
chrome.fileSystemProvider.onAbortRequested.addListener(function callback)
```

パラメータ

* callback (function): コールバックパラメータは以下のような関数になるはずです:

```
function(object options, function successCallback, function errorCallback) {...};
```

* options (object):

* fileSystemId (string): この動作に関係するファイルシステムの識別子です。

* requestId (integer): このリクエストのユニークな識別子です。

* operationRequestId (integer): 中止されるリクエストのIDです。

* successCallback (function): 提供される拡張機能によって成功の場合に呼び出されるコールバックです。

* errorCallback (function): 提供される拡張機能によって失敗の場合に呼び出されるコールバックです。

パラメータは以下のような関数になるはずです:

```
function( ProviderError error) {...};
```

* error (
[ProviderError](https://developer.chrome.com/apps/fileSystemProvider#type-ProviderError))
