---
layout: post
status: publish
published: true
title: ChromeのDownload APIリファレンスを和訳してみました
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2404
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2404
date: '2013-06-06 21:53:07 +0900'
date_gmt: '2013-06-06 12:53:07 +0900'
categories:
- Chrome extension
---

そろそろStableになるんじゃないかと思って、Chromeの拡張機能で利用可能になる予定の
[Download API](http://developer.chrome.com/extensions/downloads.html)のリファレンスを和訳してみました。Chrome Canaryでは実際に試すことができます。Download APIは、ファイルダウンロードをプログラムから制御可能にするAPIであり、chrome://downloads/ ページ相当の機能をプログラムで実現可能というものです。

結構このAPIを待っていた人は多いと思います。Stableに入るまでに、ぜひAPIの使い方をマスターしておきましょう。

****


# chrome.downloads

ダウンロードAPIは、ダウンロードに関してプログラムから開始、監視、操作、そして検索を行うことを可能にします。

# Manifest

このAPIを利用するために、拡張機能のマニフェストファイル内で、'downloads'パーミッションを定義する必要があります。また、ダウンロードすることを可能にするホストのホストパーミッションも同時に定義しなければなりません。

```
{
  "name":"My extension",
  ...
  "permissions":[
    "downloads",
    "*://*.google.com"
  ],
  ...
}
```

もしURLのホスト名がパーミッションにて指定されない場合は、ダウンロードはdownloadId値としてnullを持つそのコールバックを呼び、拡張機能がそのホスト名にアクセスするためのパーミッションを持っていないことを示すruntime.lastErrorオブジェクトをセットするでしょう。

# Examples

[examples/api/downloads](http://src.chromium.org/viewvc/chrome/trunk/src/chrome/common/extensions/docs/examples/api/downloads/)ディレクトリにて、ダウンロードモジュールを使ったシンプルな例を見つけることができます。他の例や、ソースコードを見る際の助けとして、
[Samples](http://developer.chrome.com/extensions/samples.html)を参照ください。

# Types

DangerType

* file: ダウンロードファイル名は疑わしいです。

* url: ダウンロードURLは悪意があると知られています。

* uncommon: ダウンロードURLは一般にダウンロードされず、危険です。

* host: ダウンロードは悪意のあるバイナリやそのような危険性を配布するとして知られているホストです。

* safe: ダウンロードはユーザのコンピュータに対して危険とは認識されずに行われます。

これらの文字列定数は、決して変更されないでしょう。しかしながら、DangerTypesのセットは変更されるかも知れません。

State

* in_progress: ダウンロードは現在サーバからデータを受信しています。

* interrupted: エラーがファイルホストとの接続を切りました。

* complete: ダウンロードに成功して完了しました。

これらの文字列定数は決して変更されないでしょう。しかしながら、Statesのセットは変更されるかも知れません。

DownloadItem

* id (integer): ブラウザセッションを超えて永続化される識別子です。

* url (string): 絶対URLです。

* filename (string): 絶対ローカルパスです。

* incognito (boolean): もしこのダウンロードが履歴に記録される場合はfalse、記録されない場合はtrueです。

* danger (DangerType): このダウンロードが安全であるか疑わしいとされるかを示します。

* dangerAccepted (optional boolean): もしユーザがダウンロードの危険性を受け入れた場合はtrueです。

* mime (string): ファイルのMIMEタイプです。

* startTime (string): ダウンロードが開始された時間（ISO 8601フォーマット）です。Dateコンストラクタに直接渡すことができます。

```
chrome.downloads.search({}, function(items){items.forEach(function(item){console.log(new Date(item.startTime))})})
```

* endTime (optional string): ダウンロードが終了された時間（ISO 8601フォーマット）です。Dateコンストラクタに直接渡すことができます。

```
chrome.downloads.search({}, function(items){items.forEach(function(item){if (item.endTime) console.log(new Date(item.endTime))})})
```

* state (State): ダウンロードが進行中、中断、もしくは完了したかどうかを示します。

* paused (boolean): もしダウンロードがホストからのデータの読み出しを停止した場合はtrueです。接続は開いたままです。

* error (optional integer): なぜダウンロードが中断したかを示す数値です。

* bytesReceived (integer): ホストから今までに受信したバイト数です。ファイル圧縮の考慮はされません。

* totalBytes (integer): ファイル全体のバイト数です。ファイル圧縮は考慮されません。もしバイト数が不明の場合は-1となります。

* fileSize (integer): 展開後のファイル全体のバイト数です。もしバイト数が不明の場合は-1となります。

* exists (boolean): ダウンロードファイルがまだ存在するかどうかです。Chromeはファイルの削除を自動的に監視してはいないため、この情報は古いかも知れません。ファイルの存在のチェックを実際に行うために、search()を呼び出してください。存在チェックが完了したとき、もしファイルが削除されていた場合は、onChangedイベントが発生するでしょう。search()からの結果はファイルシステムを正確に反映しないかもしれないため、search()は存在チェックが返される前に終わることを待たないことに注意してください。また、search()は必要に応じて頻繁に呼び出すことができますが、それだけ呼び出したとしても10秒ごとに1回しかファイルの存在をチェックしないでしょう。

# Methods

download

```
chrome.downloads.download(object options, function callback)
```

URLをダウンロードします。もしURLがHTTP[S]プロトコルを使っている場合、リクエストは園ホスト名のために現在セットされている全てのCookieを含むでしょう。もしfilenameとsaveAsが両方とも指定された場合、指定されたfilenameが事前にセットされたSave Asダイアログが表示されるでしょう。もしダウンロードの開始が成功した場合、新しいDownloadItemのdownloadIdを伴ってcallbackが呼び出されます。もしダウンロードの開始時にエラーが発生した場合は、downloadId=undefinedを伴ってcallbackが呼び出され、runtime.lastErrorはエラーの説明文を含みます。エラー文字列は、リリース間の後方互換性は保証されません。拡張機能はそれをパースしてはなりません。



* options (object): 何をどのようにダウンロードするかです。

* url (string):   ダウンロードするURLです。

* filename (optional string):   ダウンロードされたファイルを含むダウンロードディレクトリからの相対ファイルパスです。まだサブディレクトリを含むことはできませんが、このAPIが安定版にリリースされる前にはサブディレクトリのサポートは実装されるでしょう。ファイルのMIMEタイプや仮のファイル名が決定された後に、どのように動的にファイル名をサジェストするかは、onDeterminingFilenameを参照ください。

* saveAs (optional boolean):   ユーザにファイル名を選択させるためのファイル選択(ダイアログ)を使います。

* method (optional enumerated string ["GET", "POST"]):   もしURLにHTTP[S]プロトコルを使う場合の、HTTPメソッドです。

* headers (optional array of object):   もしURLにHTTP[S]プロトコルを使う場合の、リクエストに含まれて送信される拡張HTTPヘッダーです。各ヘッダーは、キーのnameとvalueまたはbinaryValueのどちらかを含む辞書として表現されます。これらは、XMLHttpRequestで許可されたものに限定されます。

* body (optional string):   ポストボディです。

* callback (optional function): 新しいDownloadItemのidを伴って呼び出されます。



もしcallbackパラメータを指定する場合、このような関数を指定することになるでしょう。

```
function(integer downloadId) {...};
```

* downloadId (integer)

search

```
chrome.downloads.search(object query, function callback)
```

DownloadItemを見つけます。全てのDownloadItemを取得するには、空のオブジェクトをqueryにセットしてください。特定のDownloadItemを取得するためには、idフィールドのみをセットしてください。



* query (object)

* query (optional string):   引用符を使ってグルーピング可能な検索条件を示すこの空白区切りの文字列は、ダッシュ記号'-'で始まらない、およびダッシュ記号で始まる検索条件がない、全ての検索条件を満たすfilenameやurlを持つDownloadItemの結果を制限します。

* startedBefore (optional string):   エポックで始まる与えられたミリ秒以前に開始されたDownloadItemに結果を制限します。

* startedAfter (optional string):   エポックで始まる与えられたミリ秒以後に開始されたDownloadItemに結果を制限します。

* endedBefore (optional string):   エポックで始まる与えられたミリ秒以前に終了したDownloadItemに結果を制限します。

* endedAfter (optional string):   エポックで始まる与えられたミリ秒以後に終了したDownloadItemに結果を制限します。

* totalBytesGreater (optional integer):   totalBytes値が指定された値よりも大きいDownloadItemに結果を制限します。

* totalBytesLess (optional integer):   totalBytes値が指定された値よりも小さいDownloadItemに結果を制限します。

* filenameRegex (optional string):   filenameが指令された正規表現にマッチするDownloadItemに結果を制限します。

* limit (optional integer):   この値がセットされることで、結果の件数を制限します。指定されなかった場合は、条件に一致した全てのDownloadItemが返却されるでしょう。

* orderBy (optional string):   上記のフィルタを適用する前にDownloadItemのプロパティでソートするための文字列をセットします。例えば、orderBy='startTime'という指定は、昇順でDownloadItemの開始時間でソートします。降順を指定するために、'-startTime'というようにハイフンをorderByの前に付けてください。

* id (optional integer):   問い合わせのためのDownloadItemのidです。

* url (optional string):   絶対URLです。

* filename (optional string):   絶対ローカルパスです。

* danger (optional DangerType):   このダウンロードが安全であるか疑わしいとされるかを示します。

* dangerAccepted (optional boolean):   もしユーザがダウンロードの危険性を受け入れた場合はtrueです。

* mime (string):   ファイルのMIMEタイプです。

* startTime (string):   ダウンロードが開始された時間（ISO 8601フォーマット）です。

* endTime (optional string):   ダウンロードが終了された時間（ISO 8601フォーマット）です。

* state (State):   ダウンロードが進行中、中断、もしくは完了したかどうかを示します。

* paused (boolean):   もしダウンロードがホストからのデータの読み出しを停止した場合はtrueです。接続は開いたままです。

* error (optional integer):   なぜダウンロードが中断したかを示す数値です。

* bytesReceived (integer):   ホストから今までに受信したバイト数です。ファイル圧縮の考慮はされません。

* totalBytes (integer):   ファイル全体のバイト数です。ファイル圧縮は考慮されません。もしバイト数が不明の場合は-1となります。

* fileSize (integer):   展開後のファイル全体のバイト数です。もしバイト数が不明の場合は-1となります。

* exists (boolean):   ダウンロードファイルがまだ存在するかどうかです。

* callback (function)



もしcallbackパラメータを指定する場合、このような関数を指定することになるでしょう。

```
function(array of DownloadItem results) {...};
```

* results (array of DownloadItem)

pause

```
chrome.downloads.pause(integer downloadId, function callback)
```

ダウンロードを一時停止します。もしリクエストが成功した場合、ダウンロードは一時停止状態になります。もし失敗した場合は、runtime.lastErrorにエラーメッセージが含まれます。ダウンロードがアクティブではない場合は、このリクエストは失敗するでしょう。



* downloadId (integer): 一時停止するダウンロードのidです。

* callback (optional function): 一時停止リクエストが完了したときに呼び出されます。



もしcallbackパラメータを指定する場合、このような関数を指定することになるでしょう。

```
function() {...};
```

resume

```
chrome.downloads.resume(integer downloadId, function callback)
```

一時停止されたダウンロードを再開します。もしリクエストが成功した場合は、一時停止は解除されてダウンロードが進みます。もし失敗した場合は、runtime.lastErrorにエラーメッセージが含まれます。もしダウンロードがアクティブではない場合は、このリクエストは失敗するでしょう。



* downloadId (integer): 再開するダウンロードのidです。

* callback (optional function): 再開リクエストが完了したときに呼び出されます。



もしcallbackパラメータを指定する場合、このような関数を指定することになるでしょう。

```
function() {...};
```

cancel

```
chrome.downloads.cancel(integer downloadId, function callback)
```

ダウンロードをキャンセルします。callbackが実行されたときは、ダウンロードがキャンセルされる、完了する、中断される、もしくはダウンロードがもはや存在しません。



* downloadId (integer): キャンセルするダウンロードのidです。

* callback (optional function): キャンセルリクエストが完了したときに呼び出されます。



もしcallbackパラメータを指定する場合、このような関数を指定することになるでしょう。

```
function() {...};
```

getFileIcon

```
chrome.downloads.getFileIcon(integer downloadId, object options, function callback)
```

指定したダウンロードのアイコンを取得します。新しいダウンロードでは、onCreatedイベントが受け取られた後にファイルアイコンが利用可能になります。ダウンロードが進んでいる間にこの関数によって返される画像は、ダウンロードが完了した後に返される画像と異なるかも知れません。アイコンの取得は、基本的なOSやプラットフォームに依存するツールキットの問い合わせによって行われます。それ故に、返されるアイコンは、ダウンロードの状況を含むいくつかの理由や、プラットフォーム、登録されたファイル種別や視覚的なテーマに依存するでしょう。もしファイルアイコンが決定できない場合は、runtime.lastErrorはエラーメッセージを含むでしょう。



* downloadId (integer): ダウンロードの識別子です。

* options (optional object):

* size (optional integer)  アイコンのサイズです。返却されるアイコンは、sizeピクセル四方の大きさの四角形になるでしょう。アイコンの初期サイズは32x32ピクセルです。

* callback (function): ダウンロードを表す画像のURLです。



もしcallbackパラメータを指定する場合、このような関数を指定することになるでしょう。

```
function(string iconURL) {...};
```

* iconURL (optional string):

open

```
chrome.downloads.open(integer downloadId)
```

DownloadItemが完了した場合に、そのダウンロードファイルを直ちに開きます。もし開けなかった場合は、runtime.lastErrorを通じてエラーが返却されます。最初にアイテムが開かれたときは、onChangedイベントが発生するでしょう。



* downloadId (integer): ダウンロードされたファイルの識別子です。

show

```
chrome.downloads.show(integer downloadId)
```

ファイルマネージャにて、ダウンロードされたファイルをそれが格納されているフォルダ内で表示します。



* downloadId (integer): ダウンロードされたファイルの識別子です。

erase

```
chrome.downloads.erase(object query, function callback)
```

履歴から条件に一致したDownloadItemを削除します。queryにマッチしたDownloadItemごとにonErasedイベントが発生し、その後callbackが呼び出されるでしょう。



* query (object):

* query (optional string): 引用符を使ってグルーピング可能な検索条件を示すこの空白区切りの文字列は、ダッシュ記号'-'で始まらない、およびダッシュ記号で始まる検索条件がない、全ての検索条件を満たすfilenameやurlを持つDownloadItemの結果を制限します。

* startedBefore (optional string): エポックで始まる与えられたミリ秒以前に開始されたDownloadItemに結果を制限します。

* startedAfter (optional string): エポックで始まる与えられたミリ秒以後に開始されたDownloadItemに結果を制限します。

* endedBefore (optional string): エポックで始まる与えられたミリ秒以前に終了したDownloadItemに結果を制限します。

* endedAfter (optional string): エポックで始まる与えられたミリ秒以後に終了したDownloadItemに結果を制限します。

* totalBytesGreater (optional integer): totalBytes値が指定された値よりも大きいDownloadItemに結果を制限します。

* totalBytesLess (optional integer): totalBytes値が指定された値よりも小さいDownloadItemに結果を制限します。

* filenameRegex (optional string): filenameが指令された正規表現にマッチするDownloadItemに結果を制限します。

* limit (optional integer): この値がセットされることで、結果の件数を制限します。指定されなかった場合は、条件に一致した全てのDownloadItemが返却されるでしょう。

* orderBy (optional string): 上記のフィルタを適用する前にDownloadItemのプロパティでソートするための文字列をセットします。例えば、orderBy='startTime'という指定は、昇順でDownloadItemの開始時間でソートします。降順を指定するために、'-startTime'というようにハイフンをorderByの前に付けてください。

* id (optional integer): 問い合わせのためのDownloadItemのidです。

* url (optional string): 絶対URLです。

* filename (optional string): 絶対ローカルパスです。

* danger (optional DangerType): このダウンロードが安全であるか疑わしいとされるかを示します。

* dangerAccepted (optional boolean): もしユーザがダウンロードの危険性を受け入れた場合はtrueです。

* mime (string): ファイルのMIMEタイプです。

* startTime (string): ダウンロードが開始された時間（ISO 8601フォーマット）です。

* endTime (optional string): ダウンロードが終了された時間（ISO 8601フォーマット）です。

* state (State): ダウンロードが進行中、中断、もしくは完了したかどうかを示します。

* paused (boolean): もしダウンロードがホストからのデータの読み出しを停止した場合はtrueです。接続は開いたままです。

* error (optional integer): なぜダウンロードが中断したかを示す数値です。

* bytesReceived (integer): ホストから今までに受信したバイト数です。ファイル圧縮の考慮はされません。

* totalBytes (integer): ファイル全体のバイト数です。ファイル圧縮は考慮されません。もしバイト数が不明の場合は-1となります。

* fileSize (integer): 展開後のファイル全体のバイト数です。もしバイト数が不明の場合は-1となります。

* exists (boolean): ダウンロードファイルがまだ存在するかどうかです。

* callback (optional function)



もしcallbackパラメータを指定する場合、このような関数を指定することになるでしょう。

```
function(array of integer erasedIds) {...};
```

* erasedIds (array of integer)

acceptDanger

```
chrome.downloads.acceptDanger(integer downloadId)
```

ユーザに危険なダウンロードに同意することを促します。危険なダウンロードは、自動的には受け入れません。もしそのダウンロードが同意された場合、その時はonChangedイベントが発生するでしょう。もし受け入れられなかった場合は、何も起きないでしょう。全てのデータが一時ファイルへフェッチされ、そしてダウンロードが危険ではない、またはその危険性に同意されているときは、一時ファイルは対象のファイル名でリネームされ、その状況は'complete'に変更され、onChangedイベントが発生します。



* downloadId (integer): downloadItemの識別子です。

drag

```
chrome.downloads.drag(integer downloadId)
```

他のアプリケーションへダウンロードファイルのドラッグを開始します。



* downloadId (integer)

# Events

onCreated

```
chrome.downloads.onCreated.addListener(function(DownloadItem downloadItem) {...});
```

このイベントは、ダウンロードが開始した時に、DownloadItemと共に発生します。



* downloadItem (DownloadItem)

onErased

```
chrome.downloads.onErased.addListener(function(integer downloadId) {...});
```

ダウンロードが履歴から削除されるときにdownloadIdと共に発生します。



* downloadId (integer): 削除されたDownloadItemのidです。

onChanged

```
chrome.downloads.onChanged.addListener(function(object downloadDelta) {...});
```

bytesReceivedを除く何らかのDownloadItemの属性が変化したときに、このイベントはdownloadIdおよび変化した属性を含むオブジェクトを伴って発生します。



* downloadDelta (object)

* id (integer): 変化したDownloadItemのidです。

* url (optional object): urlが変更された際に含まれます。

* previous (optional string)

* current (optional string)

* filename (optional object): filenameが変更された際に含まれます。

* previous (optional string)

* current (optional string)

* danger (optional object): dangerが変更された際に含まれます。

* previous (optional string)

* current (optional string)

* dangerAccept (optional object): dangerAcceptが変更された際に含まれます。

* previous (optional boolean)

* current (optional boolean)

* mime (optional object): mimeが変更された際に含まれます。

* previous (optional string)

* current (optional string)

* startTime (optional object): startTimeが変更された際に含まれます。

* previous (optional string)

* current (optional string)

* endTime (optional object): endTimeが変更された際に含まれます。

* previous (optional string)

* current (optional string)

* state (optional object): stateが変更された際に含まれます。

* previous (optional string)

* current (optional string)

* paused (optional object): pausedが変更された際に含まれます。

* previous (optional boolean)

* current (optional boolean)

* error (optional object): errorが変更された際に含まれます。

* previous (optional integer)

* current (optional integer)

* totalBytes (optional object): totalBytesが変更された際に含まれます。

* previous (optional integer)

* current (optional integer)

* fileSize (optional object): fileSizeが変更された際に含まれます。

* previous (optional integer)

* current (optional integer)

* exists (optional object): existsが変更された際に含まれます。

* previous (optional boolean)

* current (optional boolean)

onDeterminingFilename

```
chrome.downloads.onDeterminingFilename.addListener(function(DownloadItem downloadItem, function suggest) {...});
```

ファイル名決定プロセスの間、拡張機能は対象のDownloadItem.filenameを上書きする機会を与えられるでしょう。各拡張機能は、このイベントに対して一つ以上のリスナーを登録することはできないかもしれません。各リスナーは、まさに一回だけsuggestが呼び出されなければならず、それは同期的に、もしくは非同期的に呼び出されます。もしリスナーが非同期的にsuggestを呼ぶ場合、それはtrueを返却しなければなりません。もしリスナーが同期的にsuggestを呼ばず、またはtrueを返さなかった場合、suggestは自動的に(再度)呼ばれるでしょう。全てのリスナーがsuggestを呼び出すまで、DownloadItemは完成しないでしょう。リスナーは、downloadItem.filenameをダウンロードファイルのファイル名とすることを許すために、何の引数もなしにsuggestを呼ぶかもしれません。または、対象のファイル名を上書きするために、suggestにFilenameSuggestionオブジェクトを渡すでしょう。もし一つ以上の拡張機能がファイル名を上書きした場合、その時はリスナーがsuggestにFilenameSuggestionオブジェクトを渡した最後のインストールされた拡張機能が勝ちます。このような拡張機能の勝ち負けに関連する混乱を避けるために、ユーザは衝突するかもしれない拡張機能をインストールすべきではありません。もしdownloadによってダウンロードが開始され、対象のファイル名がMIMEタイプによって事前に知られ、そして一時ファイルが決定された場合は、代わりにDownloadOptions.filenameを使用します。



* downloadItem (DownloadItem)

* suggest (function)



suggestパラメータは、このような関数に指定されるべきです。

```
function(object suggestion) {...};
```

* suggestion (optional object)

* filename (string): DownloadItemの新しく対象となるDownloadItem.filenameであり、ユーザの初期ダウンロードディレクトリからの相対パスです。もしかしたら、サブディレクトリを含むかもしれません。絶対パス、空のパス、そして".."という後方参照を含むパスは無視されます。

* overwrite (optional boolean): ファイルが存在した場合に上書きするかどうかです。

# Sample Extensions that use chrone.downloads

* [Download Selected Links](http://developer.chrome.com/extensions/samples.html#31e082ed1a598b3d1ae28197fa2a89bc) - ページ内のリンクを選択してそれらをダウンロードします。

* [Downloads Overwrite Extsing Files](http://developer.chrome.com/extensions/samples.html#f48c39bd36dd5dce891f05be06f6dc13) - (1)や(2)といった追加をする代わりに、全てのダウンロードは既存のファイルを上書きするようにします。
