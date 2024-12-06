---
layout: post
status: publish
published: true
title: "オレ流AngularJSを使った設計ポリシー"
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2918
wordpress_url: https://www.eisbahn.jp/yoichiro/?p=2918
date: '2014-10-12 23:39:59 +0900'
date_gmt: '2014-10-12 14:39:59 +0900'
categories:
- AngularJS
---

[Chrome MySQL Admin](https://chrome.google.com/webstore/detail/chrome-mysql-admin/ndgnpnpakfcdjmpgmcaknimfgcldechn)では、
[AngularJS](https://angularjs.org/)を使って実装を行っています。Chrome appsでは、
[何らかのMVC Frameworkの利用が勧められています](https://developer.chrome.com/apps/app_frameworks)。

AngularJSは、Controller、Directive、Template、Serviceなど、いくつかの部品群を組み合わせてアプリケーションを構成することになります。その機能の豊富さ故に、実はちゃんとしたポリシーを決めておかないと、いかようにでも作れてしまうために、かえって複雑さが増してしまうという危険性も出てきます。もちろんアプリケーションの作り始めは試行錯誤の連続なのですが、徐々に自分なりのポリシーみたいなものが確立されてくるはずです。

本エントリでは、Chrome MySQL Adminでの設計/実装ポリシーを簡単に紹介してみたいと思います。ちなみに、全てのソースコードは、以下にあります。

[https://github.com/yoichiro/chrome_mysql_admin](https://github.com/yoichiro/chrome_mysql_admin)

# 画面構成

設計ポリシーは、「全てのアプリケーションで共通なものが使える」というのは皆無であり、実際は目の前にある「作りたいもの」によってそれぞれ違ってくるのが自然かと思います。Chrome MySQL Adminで適用されているポリシーを理解するためには、そのアプリケーションがどういうものか簡単に把握しておいた方が良いでしょう。以下は、代表的なChrome MySQL Adminの画面です。

![cma7](https://www.eisbahn.jp/yoichiro/images/2014/10/cma7.png)

ウィンドウの上に、各機能を呼び出すためのアイコンが並ぶナビゲーションバーがあります。左には、TableやViewの一覧が表示されます。そして、ウィンドウの真ん中には、ナビゲーションバーの各アイコンから呼び出された機能を実現するためのメインコンテンツが表示されます。このメインコンテンツは、複数のパネルが積み重なっていて、そのうちの一つが表示されます。ウィンドウの最上部には、ウィンドウの最大化やアプリケーションの終了を行うための、いわゆるOSが通常は提供してくるウィンドウフレームの代わりとなるタイトルバーがあります。ウィンドウの最下部は、左側にはTableやViewに対する機能を呼び出すためのボタン群、右にはメインコンテンツごとに変化する機能ボタン群が並ぶフッターエリアです。

ワイヤーフレームは、以下のようなイメージになります。

![スクリーンショット 2014-10-09 9.20.12](https://www.eisbahn.jp/yoichiro/images/2014/10/cda5714ad8ad612a822cdb5d868a3c7a.png)

メインコンテンツのパネル群は、執筆現在で以下のものがあります。

* ログインパネル

* プロセス一覧パネル

* グラフパネル

* 行パネル - 選択されたTableやViewにある行の一覧を表示、編集する。

* 構造パネル - 選択されたTableやViewの構造を表示、編集する。

* 情報パネル - 選択されたTableやViewの情報を表示する。

* 関連パネル - 外部参照の一覧を表示する。

* ルーチンパネル - ストアドプロシージャやファンクションの一覧を表示、編集する。

* クエリパネル - クエリエディタとその実行結果を表示する。

たくさんあります。表示されるのは、上記の中から同時に1つのみです。データベースに接続されていない時はログインパネルを、接続されているときは、ログインパネル以外のどれかが表示されます。

これら以外には、所々出てくるダイアログが存在しています。表示された時のイメージは、以下のような感じです。

![cma8](https://www.eisbahn.jp/yoichiro/images/2014/10/cma8.png)

ダイアログは、
[Bootstrap](http://getbootstrap.com/)のModalを使って表示しています。そのため、ダイアログは全ての画面構成要素の最も手前に覆い被さる形で表示されることになります。ワイヤーフレームでは、以下のような感じでしょう。

![スクリーンショット 2014-10-09 9.33.13](https://www.eisbahn.jp/yoichiro/images/2014/10/fcf1053d026ed5c18594c40fdefcf930.png)

これらの構成要素をAngularJSを使って束ねていきます。基本的には、構成要素ごとにAngularJSの何の機構を使っていくか、そして構成要素間の呼び出しをどのようにして実現するか、を考えていきます。

# ベースとなるHTML

最初に、ベースとなるHTMLを見ましょう。これだけでも「あ、そうしてるのか」と感じてくれるAngularJS経験者がいると思います。話をシンプルにするために、いろいろ端折ってます。

```
<html lang="en" ng-app="chromeMyAdmin" ng-csp>
<head>
  ...
</head>
<body>
<progress_panel></progress_panel>
<window-title-panel></window-title-panel>
<navbar-panel></navbar-panel>
<div class="container-fluid">
<div class="row">
<div class="col-sm-3 col-md-3 sidebar">
      <database-object-list-panel></database-object-list-panel>
      <favorite-list-panel></favorite-list-panel>
    </div>
<div class="col-sm-9 col-sm-offset-3 col-md-9 col-md-offset-3 main">
<div id="mainPanel">
        <rows-panel></rows-panel>
        <structure-panel></structure-panel>
        <query-panel></query-panel>
        <database-panel></database-panel>
        <information-panel></information-panel>
        <relation-panel></relation-panel>
<procedures-functions-panel></procedures-functions-panel>
        <status-graph-panel></status-graph-panel>
        <login-form></login-form>
      </div>
      <main-footer></main-footer>
    </div>
  </div>
</div>
<create-table-dialog></create-table-dialog>
<insert-row-dialog></insert-row-dialog>
<confirm-dialog></confirm-dialog>
<error-dialog></error-dialog>
<add-column-dialog></add-column-dialog>
<edit-column-dialog></edit-column-dialog>
<add-index-dialog></add-index-dialog>
<create-database-dialog></create-database-dialog>
<update-row-dialog></update-row-dialog>
<configuration-dialog></configuration-dialog>
<add-relation-dialog></add-relation-dialog>
<create-routine-dialog></create-routine-dialog>
</body>
</html>
```

左のTable, View一覧パネルとメインコンテンツのパネルを左右に並べるためにdiv要素を使ってますが、それ以外は全てカスタムディレクティブが書かれています。ここでのポイントは、以下になると思います。

* 先ほど紹介した構成要素（パネルやダイアログ）ごとに、カスタムディレクティブを作っていること。

* パネルやダイアログの親子関係がないこと。

特に2番目の点が特徴的かなと思います。パネルやダイアログといったものは「それぞれ明確に分離されたUI」であって、それらの親子関係を作らない方が自然だ、という考えです。共通処理を親ディレクティブで提供する、という方針も考えられると思いますが、「共通処理はServiceにより提供されるべき」という考えです。

# パネル、ダイアログの構成

次に、パネルやダイアログが個々にどのような構成で作られているかを紹介します。簡単に図にすると、以下のような感じです。

![スクリーンショット 2014-10-09 20.28.51](https://www.eisbahn.jp/yoichiro/images/2014/10/a4cd4d574080d6b25520c35d1012118f.png)

先ほど「パネルやダイアログといった構成要素は、それぞれカスタムディレクティブにしてる」と言いましたが、実際にはテンプレートを分けるためのみの目的でディレクティブ化してるだけです。パネルやダイアログのディレクティブは、何の処理も持たずに、テンプレートファイルの場所が書かれただけのものになります。

![スクリーンショット 2014-10-09 20.24.05](https://www.eisbahn.jp/yoichiro/images/2014/10/753a30ae8d80552497b92e2cff5a7312.png)

「それなら、別にカスタムディレクティブにしなくてもいいじゃん」って思った方、その通りです。実は、Chrome MySQL Adminを作り始めた当時、AngularJSを触り出したのも実はこれが初めてだったので、AngularJSを良くわからない状態でした。特にディレクティブはさっぱりわからず、まずはng-controllerディレクティブを使ってコントローラを適用するところから始めました。つまり、全てのHTML要素を一つのファイルに書いていったわけです。その後、ファイルが肥大化してきたので、パネルやダイアログのHTML要素群をそれぞれテンプレート化して分離しようと思い立ち、「いつかディレクティブを理解したら役に立つだろう」と思ってカスタムディレクティブ化していった、という経緯になります。

テンプレートは、各パネルやダイアログが持つUIを満たすために必要なHTML要素群が書かれています。それらはBootstrapによるスタイル指定がされています。動的な要素に関しては、ng-repeatディレクティブを使っている箇所がほとんどです。

ng-controllerディレクティブで指定されたコントローラは、対象のパネルやダイアログ内の機能全てに対して責任を持ってます。もう少し具体的に言うと、パネルやダイアログのレンダリングと、各UIからのイベントハンドリングが担当範囲です。いわゆるビジネスロジックについては、Chrome MySQL Adminの場合はそのほとんどがMySQLへのアクセスになるのですが、コントローラ内で行っているものもあれば、DIされたサービスの処理を呼び出しているものもあります。

今振り返ると、カスタムディレクティブに処理を書かずにコントローラに処理を書いたのは、正解だったと思ってます。その理由は、

* AngularJSの$broadcast()された後の受信対象としてコントローラが最適

と考えているからです。ディレクティブでも$on()で受け取れるのかもしれないけど、なんか気持ち悪いです。それは、たぶん、自分の中でディレクティブはMVCのVに相当するもの、という認識があるからでしょう。つまり、MVCへのマッピングとしては、以下の図のようになってます、僕の頭の中では。

![スクリーンショット 2014-10-10 6.47.02](https://www.eisbahn.jp/yoichiro/images/2014/10/deaeaef587f1efb831404453bd8390a2.png)

サービスがCに書かれていますが、Vから直接サービスを参照する、ということはしていません。参照したい場合は、必ずコントローラ経由でやってます。まぁ、自然とそうなりますね。

# コントローラの定形化

各コントローラは、chromeMyAdmin.controller(...)で定義をしていくわけですが、最低限の定形化をしています。主に、コントローラの初期化処理についてです。具体的には、以下の関数を規定して、全てのコントローラ（パネル、ダイアログ両方とも）で適用しています。

* HTMLテンプレートでng-controllerディレクティブによりコントローラが指定される箇所で、必ずng-initディレクティブを使って初期化処理用関数をバインドしておく。

* 初期化処理用関数（$scope.initialize()関数）の中で、「イベントハンドラ登録」「選択肢登録」「その他必要な初期化処理」を書いていく。

* イベントハンドラ登録と、選択肢登録は、それぞれ決まった名前で関数を作っていく（assignEventHandlers()、setupItems()）。

まぁここは趣味の世界かもしれません。HTMLテンプレートは、以下のようになります。

```
<div ng-controller="hogePanelController" ng-init="initialize()">
  ...
</div>
```

コントローラは以下のようになります。

```
chromeMyAdmin.controller("hogePanelController", ["$scope", ... , function($scope, ...) {
    var setupItems = function() {
        $scope.objectTypes = ["TABLE", "VIEW"];
        ...
    };
    var assignEventHandlers = function() {
        $scope.$on(Events.TABLE_CHANGED, function(event, data) {
            onTableChanged();
        });
    };
    var onTableChanged = function() {
        ...
    };
    $scope.initialize = function() {
        setupItems();
        assignEventHandlers();
        ...
    };
}]);
```

こういう定形化は個人的に好きでやってます。あとでコードを追うときに、一定のポリシーで書いておけばエントリポイントを探しやすくなるからです。

setupItems()関数は、例えばUIとしてドロップダウンで何か選ばせるようにしたい時に、その選択肢をセットする場所です。上記の例では、例えばUIで作成対象をTABLEにするのかVIEWにするのかという選択肢をセットしています。HTMLテンプレートに直書きできるものでもありますが、そんなことはせず、コントローラで必ずセットするようにしています。「直書きするか、コントローラでセットするか」を都度迷いたくないからです。

assignEventHandlers()関数で設定するイベントハンドラは、UIに関するイベントハンドリングではありません。そういったものはコントローラにできるだけ書かないようにすべきだと思っています。では何を設定するのかというと、$broadcast()で配送されるメッセージを受け取るための$on()を記述する場所です。つまり、各コントローラ間の通信や、何らか状態が変化した際に、その通知を受け取る定義をする場所、ということになります。ただ、やむを得ずDOMに対してイベントハンドラ登録をしている箇所は数カ所あって、これは僕がカスタムディレクティブを作るだけの知識がなかった時に妥協して書いてしまったものです。ポリシーとしては、コントローラ内ではDOMを触らず、必要な時はカスタムディレクティブを作って対応するのが正解です。

# サービスの作成単位

基本的にはほとんどの処理をコントローラに記述していますが、複数のパネルやダイアログから行われる同じような処理がどうしても出てきます。それを複数の場所にそれぞれ記述してしまうのは、もちろん良くありません。どこか1箇所に書いておいて、複数の箇所からそれを呼び出して使うべきですね。

共通の処理をどこに置くべきか、その方法はいくつか考えられます。

* 親のコントローラを作って共通処理を持たせ、各サブコントローラから呼び出す。

* アプリケーション（chromeMyApp.run()関数で定義される）の中で共通処理の関数を$rootScopeに追加して、各コントローラから利用する。

* サービスを作って共通処理を持たせ、各コントローラにインジェクトして利用する。

* JavaScript的にグローバルな名前空間の中に共通処理を持たせ、各コントローラから直接利用する。

一番最後のは最終手段、コントローラに階層を持たせたくない（一般的に継承は良い思い出がない）、$rootScopeもできるだけ"汚染"したくない、という理由から、選択したのはサービスを利用する方法です。AngularJSのDI機能を生かしてサービスを利用するのは、とても良い戦略だと思ってます。Chrome MySQL Adminではテストコードを書いていませんので、そのメリットを最大限享受できているかというとそうじゃないですが、さまざまなビジネスロジックを分割して記述していく単位としても、サービスは非常に役に立ちます。

Chrome MySQL Adminの中では、執筆時点で以下のサービスがあります。

* configurationService - 設定情報をchrome.storage API使って読み書きする

* favoriteService - 接続先のお気に入り情報をchrome.storage API使って読み書きする

* identityKeepService - 接続先ごとのパスワードを記録しておく（自動補完に利用）

* modeService - 現在のモードを保持しておく

* mySQLClientService - MySQLへの接続管理や基本的な問い合わせ機能を提供する

* mySQLQueryService - MySQLへの具体的な問い合わせを実行する

* pingService - MySQLへ定期的にPingを送って接続状態を監視する

* queryHistoryService - 問い合わせ履歴をchrome.storage API使って読み書きする

* relationSelectionService - 関連を扱うパネル内で選択されたある関連を保持する

* routineSelectionService - ストアドプロシージャやファンクションを扱うパネル内で選択されたルーチンを保持する

* rowsPagingService - 行パネル内でのページング情報を保持する

* rowsSelectionService - 行パネル内で選択された行の情報を保持する

* sqlExpressionService - 各種SQL文を組み立てるために便利な機能を提供する

* targetObjectService - Database, TABLE, VIEWなどについて何が選択されたかを保持する

* typeService - MySQLの型情報を提供する

数多くのサービスがありますが、それぞれ明確に決まった範囲の仕事を担当していることがわかると思います。各コントローラを作っていく中で、「あ、そういえば別のコントローラでも書いた処理だ」と発見されれば、その処理はサービスに移動させます。いきなりサービスに共通処理をまとめていく、というのも難しいので、同じような処理を2回目に書いたと気がついた時点で、共通化を行っています。

コントローラにインジェクトされるサービスの個数は、パネルやダイアログが行いたい処理それぞれに応じて変化します。やることが多いコントローラだと、以下のような状況になることもあるわけです。これはこれで正しいと思ってます。

![スクリーンショット 2014-10-10 9.30.22](https://www.eisbahn.jp/yoichiro/images/2014/10/73dafc43b255fedc72eedb59bd94f2ad1.png)

サービスの内容は、シンプルです。共通化したい関数を持たせるだけです。ただ、あるサービスが持つ複数の処理の中でも共通処理が出てくることがあります。その場合は、サービスの中で閉じた（外からは見えない）形で関数を切り出しています。以下のような感じですね。

```
chromeMyAdmin.factory("hogeService", [$rootScope, function($rootScope) {
    var doSomeCommonProcessForInternal = function() {
        ...
    };
    return {
        // Functions for the client of this service
        doSomeCommonProcess: function(...) {
            doSomeCommonProcessForInternal();
            ...
        },
        ...
    };
}]);
```

サービスが別のサービスをDIでインジェクトすることは、ポリシーとしてはありです。ただ、普通に責務をちゃんと分けていけば、滅多にサービス間の依存が出てくることはないかと思います。

そして特徴的なのは、サービスに$rootScopeをインジェクトしていることです。「さっき、"$rootScopeを汚染したくない"って言ってたじゃん」と思ったことでしょう。各サービスの中で$rootScopeに何か追加することは、ここでのポリシーとしては厳禁です。たぶんやった瞬間にスパゲッティかと思います。では、なぜわざわざ$rootScopeをインジェクションしているかというと、理由は一つです。

* $broadcast()を使うため

これだけです。

# 状態が変更された時の通知

先ほどの$broadcast()をなぜ使うのか、それを説明していきましょう。例えば、Chrome MySQL Adminでは「どのパネルを表示すべきか？」という課題があります。Chrome MySQL Adminの上部にあるナビゲーションバーに配置された各アイコンボタンを押すことで、それに対応したパネルをメインコンテンツエリアに表示しないといけません。それと同時に、押されたアイコンボタンにより決定されたパネル以外のその他のパネルについては、全て非表示にならなければなりません。

![スクリーンショット 2014-10-10 21.43.17](https://www.eisbahn.jp/yoichiro/images/2014/10/9f5ff1ce63296fbc360dec99a4d40c9a1.png)

ナビゲーションバーパネルが他のパネルの表示および非表示を制御することもできるでしょうけど、それはパネル間の依存関係ができてしまいます。さらに、コントローラの中ではできるだけDOM操作を行わないというポリシーに反することもあるでしょう。つまり、あるコントローラは、別のコントローラを一切知らない状況を保証してあげるべきですね。他のパネルに対して影響を直接与えるのではなく、「あ、状況が変化したので、自分の振る舞いを変えよう」という受動的な処理の実行が行われれば、疎結合になっていると言える状況になりそうです。

そこで、サービスをうまく使います。パネルの表示と非表示を切り替えるために、「今どのパネルが表示されているべきか」という状況を保持するサービスを一つ作ります。Chrome MySQL Adminではその状態のことを「モード」と呼んでいますので、modeServiceを定義します。modeServiceサービスは、各パネルのコントローラにインジェクトされます。modeServiceはモード値を内部に持っていて、それを変更するための関数をコントローラに提供します。例えば、ナビゲーションバーパネル上のあるボタンがクリックされた際に、ナビゲーションバーパネルのコントローラはmodeServiceサービスに対してモードの変更をするために関数呼び出しを行います。これにより、modeServiceサービス内のモード値は変化します。

![スクリーンショット 2014-10-10 21.34.34](https://www.eisbahn.jp/yoichiro/images/2014/10/099689d197b435ad456d9de6c0c15339.png)

これだけでは、他のパネルは何もできません。modeServiceサービスは、モード値の変更をいろいろなところに通知して教えてあげなければなりません。そこで、$broadcast()関数の出番です。$rootScopeの$broadcast()関数を使って、モード値が変更されたことを通知します。ここでポイントは、「modeServiceサービスは通知を実際に受け取る相手を知らない」ということです。

モード値を受け取って何らかの処理をしたいコントローラでは、$scopeの$on()関数を使って、モード値の変更通知を受け取ることを宣言しておきます。つまり、モード値変更通知に関するイベントハンドラを登録しておくということです。$broadcast()関数が呼び出された後に、$on()関数で登録されたイベントハンドラが次々と実行されていきます。その際に渡される「新しいモード値」に従って、自分は表示状態になるべきか、非表示状態になるべきか判断する、という流れです。

コードで表すと、以下のようになります。まず、modeServiceサービスです。

```
chromeMyAdmin.factory("modeService", ["$rootScope", function($rootScope) {
    var mode = "database";
    return {
        changeMode: function(newMode) {
            mode = newMode;
            $rootScope.$broadcast("mode_changed", mode);
        },
        getMode: function() {
            return mode;
        }
    };
}]);
```

ナビゲーションバーパネルのコントローラでは、以下のようになります。modeServiceサービスをインジェクトして、QueryEditorパネルを表示するためのボタンが押されたら、modeServiceサービスのchangeMode()関数に新しいモード値を渡して更新しているのがわかると思います。

```
chromeMyAdmin.controller(
        "NavbarController",
        ["$scope", "modeService" function($scope, modeService) {
    $scope.onQueryEditorButton = function() {
        modeService.changeMode("queryEditor");
    };
}]);
```

modeServiceサービスから発行されるmode_changedイベント通知を受け取るパネル側のコントローラです。

```
chromeMyAdmin.controller(
        "databasePanelController",
        ["$scope", "$timeout", ... , function($scope, $timeout, ...) {
    var autoUpdatePromise;
    var assignEventHandlers = function() {
        $scope.$on("mode_changed", function(event, newMode) {
            if ("database" === newMode) {
                autoUpdatePromise = $timeout(loadProcessList, 5000);
            } else {
                $timeout.cancel(autoUpdatePromise);
                autoUpdatePromise = null;
            }
        });
    };
    $scope.initialize = function() {
        assignEventHandlers();
        ...
    };
    $scope.isDatabasePanelVisible = function() {
        return modeService.getMode() === "database";
    }
}]);
```

上記はdatabaseパネルの例です。databaseパネルでは、接続されているプロセスの一覧表示を行っていますが、モード値の変更通知を受け取って、自分が表示対象になったかどうかを判断しています。もし表示対象であれば、プロセス一覧を取得する処理を一定期間で繰り返し行っています。そのために、$timeout()関数を使っています。もし、期待したモード値ではなかった、つまり他のパネルが表示されていた場合は、プロセス一覧を取得するための処理をキャンセルしています。

databaseパネルの表示/非表示の判断は、mode_changedイベント通知を受けて処理しても良いのですが、もっと簡単に、modeServiceサービスが保持している現在のモード値を返す処理を呼び出して、自分が表示されるべきモード値かどうかを比較して結果を返すメソッドを追加しておきます。上記の例では、$scope.isDatabasePanelVisible()関数がそれです。これをHTMLテンプレートからng-showディレクティブを使って参照しておくことにより、modeServiceサービスが保持する値の変化に応じて自動的に各パネルの表示状況が変わるようになります。

```
<div ... ng-controller="databasePanelController" ng-show="isDatabasePanelVisible()">
  ....
</div>
```

以上のようにして、コントローラ間の依存度の排除と、アプリケーション内の状態管理とその変更通知を行っているわけです。

# フッターパネルからダイアログからメインコンテンツパネルへのイベント伝播

上記のmodeServiceサービスによるモード値の共有と変更イベント伝達の仕組みは、他の場所でもアグレッシブに採用しています。例えば、選択されているTableに行を追加する場合は、複数のパネル、ダイアログ間でイベント伝達が行われています。

行追加のためのボタンは、フッターパネルにあります。

![スクリーンショット 2014-10-12 11.18.22](https://www.eisbahn.jp/yoichiro/images/2014/10/8cec4080449e1c18e93d3684a24029ad.png)

行追加ボタンを押すと、行追加ダイアログが表示されます。

![スクリーンショット 2014-10-12 11.20.33](https://www.eisbahn.jp/yoichiro/images/2014/10/a735b03df2310d62ce4439c0df897d9c.png)

行追加ダイアログにて実際の行追加処理が完了した後、行一覧表示パネルの内容を更新します。

![スクリーンショット 2014-10-12 11.21.44](https://www.eisbahn.jp/yoichiro/images/2014/10/73b45a7195e8571f3b570b1f73901290.png)

つまり、行追加という1つの処理の中で、2つのパネルと1つのダイアログが互いに連携しなければなりません。そこで、イベント伝達の出番です。Chrome MySQL Adminでは、以下のようなイベント伝達が行われています。イベント送信、つまり$broadcast()関数の呼び出しはサービスから行いますので、実は2つのパネルと1つのダイアログの他にもサービスが絡んできています。

![スクリーンショット 2014-10-12 11.44.34](https://www.eisbahn.jp/yoichiro/images/2014/10/3e80d0cad659a357753daf7b19ba3c8e.png)

関係するサービスは以下です。

* targetObjectService - 現在処理対象となっている（=UI上で選択されている）Database, Table, View, Rowなどの状態を保持する

* rowsPagingService - 行一覧パネルに対して、ページングの情報（何行目から何件が表示対象になっているか）を保持する

設計ポリシーとしては、コントローラからサービスに対しては「状態を変更するための関数呼び出し」を行い、サービスは「状態が変化したことをイベント通知する」ことを行います。上の図は、コントローラとサービスの処理の流れを表しています。説明を加えるとするならば、以下となるでしょう。

* mainFooterController - 行追加ボタンが押された時に呼び出されるイベントハンドラ関数の中で、予めインジェクトされていたtargetObjectServiceサービスのrequestInsertRow()関数を呼び出す。これは「新しい行が必要になるらしいよ」とコントローラがサービスに教えることを意味する。

```
chromeMyAdmin.controller(
        "mainFooterController",
        ["$scope", "targetObjectService", ... ,
         function($scope, targetObjectService, ...) {
    ...
    $scope.onInsertRowButtonClicked = function() {
        targetObjectService.requestInsertRow();
    };
}]);
```

* targetObjectService - 「新しい行が必要になった」という状態変化をきっかけとして、"show_insert_row_dialog"イベントを$broadcast()関数で伝達する。これを誰かが拾ってくれることを期待して。。。

```
chromeMyAdmin.factory(
        "targetObjectService",
        [$rootScope, ... ,
         function($rootScope, ...) {
    ...
    return {
        ... ,
        requestInsertRow: function() {
            $rootScope.$broadcast("show_insert_row", {});
        }
    };
}]);
```

* insertRowDialogController - 予め"show_insert_row_dialog"イベントを$on()関数で監視することを登録しておいたinsertRowDialogControllerコントローラは、このイベントを受け取り、ダイアログを表示してユーザに新規に追加したい行の内容を入力させる。

```
chromeMyAdmin.controller(
        "insertRowDialogController",
        ["$scope", "rowsPagingService", ... ,
         function($scope, rowsPagingService, ...) {
    ...
    var assignEventHandlers = function() {
        $scope.$on("show_insert_row_dialog", function(event, data) {
            // Show this dialog.
        });
    };
    $scope.initialize = function() {
        assignEventHandlers();
        ...
    };
}]);
```

* insertRowDialogController - ユーザが行追加内容の入力を完了して"Insert"ボタンを押した後、予めインジェクトされていたrowsPagingServiceサービスのrefresh()関数を呼び出す。これは「データベースの行の構成が変わった可能性あるかもよ」とコントローラがサービスに教えることを意味する。

```
chromeMyAdmin.controller(
        "insertRowDialogController",
        ["$scope", "rowsPagingService", ... ,
         function($scope, rowsPagingService, ...) {
    ...
    $scope.onInsertButtonClicked = function() {
        // Insert a new row.
        rowsPagingService.refresh();
    };
}]);
```

* rowsPagingService - 「データベースの行の構成が変わった」という状態変化をきっかけとして、"rows_paging_changed"イベントを$broadcast()関数で伝達する。これを誰かが拾ってくれることを期待して。。。

```
chromeMyAdmin.factory(
        "rowsPagingService",
        ["$rootScope", ... ,
         function($rootScope, ...) {
    ...
    return {
        ...
        refresh: function() {
            $rootScope.$broadcast("rows_paging_changed", {});
        }
    };
}]);
```

* rowsPanelController - 予め"rows_paging_changed"イベントを$on()関数で監視することを登録しておいたrowsPanelControllerコントローラは、このイベントを受け取り、再度クエリをMySQLに発行して行一覧を再取得して表示する。

```
chromeMyAdmin.controller(
        "rowsPanelController",
        [$scope, ... ,
         function($scope, ...) {
    ...
    var assignEventHandlers = function() {
        $scope.$on("rows_paging_changed", function(event, data) {
            // Execute query and refresh UI
        });
    };
    $scope.initialize = function() {
        assignEventHandlers();
    };
}]);
```

上記を書いてみて、"show_insert_row_dialog"イベントという名前は失敗したかな、と思いました。例えば、"require_new_row"イベントという名前の方が相応しかったかもしれません。あくまで状態変化を示すことが求められているわけで、"show_insert_row_dialog"という名前はあまりにも直球過ぎましたね。

一連の処理を眺めて「結構複雑だな」と感じた人もいるかもしれませんが、それぞれ読み解いていけば、それほど難しいものではなく、むしろ処理の流れに一貫性があるため、むしろ見通しやすい構成ではないかと自分では思っています。コードの追い方としては、コントローラの中では「どのサービスをどのように使っているか」を見ていけば良く、またサービスは「どのイベントを発行しているか」、そしてコントローラは追加として「どのイベントを待ち受けているか」をチェックしていけば、それだけで追っていけます。「assignEventHandlers()関数という決まった名前で作っていきましょう」というポリシーは、このコードの追い方をより簡単にするための工夫なわけです。

このイベント伝播を使った仕組みの最大の利点としては、「状態変化に応じて実行したい処理を複数書くことが容易」ということです。つまり、あるイベントが伝播する際に、別々のコントローラで$on()関数によりそのイベントに対応するイベントハンドラを登録しておけば、イベントの伝播処理は一回でも、それに応答する箇所を手軽に複数にできるわけです。これが単なる関数の呼び出しであれば、「後から○○という状態変化に応じた処理を追加したい」という場合に、状態変化側のコードで関数呼び出しを追記する必要があります。しかし、イベント伝播の仕組みを採用しておけば、イベントハンドリングしたい場所で$on()関数を使って処理を書けば済みます。

# イベント名の定数化

上記まではイベント名を文字列で書いていましたが、Chrome MySQL Adminの実際のコードでは、もちろん定数化しています。

```
chromeMyAdmin.constant("Events", {
    MODE_CHANGED: "modeChanged",
    ROWS_PAGING_CHANGED: "rows_paging_changed",
    SHOW_INSERT_ROW_DIALOG: "show_insert_row_dialog",
    ...
});
```

各コントローラは、このEventsをインジェクトして参照しています。このような定数宣言は、随所で積極的に利用しています。

# 共通UI部品のカスタムディレクティブ化

UI部品として繰り返し似たようなものが並ぶ場合には、カスタムディレクティブを定義して、独自タグによる可読性の向上を行っています。例えば、フッターパネルに並ぶボタン群は、機能としては似たようなものばかりです。

![スクリーンショット 2014-10-12 23.19.50](https://www.eisbahn.jp/yoichiro/images/2014/10/e510fcd4feaf1389c7b41d5283578f2b.png)

ボタンによって変わるものとすれば、「アイコン画像」「ツールチップ文字列」「クリックイベントハンドラ」「Enable/disable」くらいですね。これらを指定可能なカスタムディレクティブを定義しています。

```
<div class="pull-left footerButton{{align}}"
       ng-class="!enable() && 'footerButtonDisable'"
       ng-click="click()">
  <span class="fa fa-{{icon}}" title="{{title}}"></span>
</div>
```

```
chromeMyAdmin.directive("footerButton", [function() {
    return {
        restrict: "E",
        templateUrl: "templates/footer_button.html",
        replace: true,
        scope: {
            icon: "@",
            title: "@",
            click: "&",
            enable: "&"
        },
        link: function(scope, element, attrs) {
            if (!attrs.enable) {
                scope.enable = function() {
                    return true;
                };
            }
            if (attrs.align === "left") {
                scope.align = "Left";
            } else {
                scope.align = "Right";
            }
        }
    };
}]);
```

![スクリーンショット 2014-10-12 23.35.50](https://www.eisbahn.jp/yoichiro/images/2014/10/5a75c60c4ca5c50ac43b485be3762aab.png)

データバインディングや関数バインディングの機能をうまく使って、このディレクティブに柔軟性を持たせています。他にも、ナビゲーションバーに並んでいるボタン群や、各グラフ表示についても、カスタムディレクティブ化しています。

# まとめ

AngularJSを自分のアプリケーションにどのように適用していくか、試行錯誤を多くの方々が行っていると思います。今まで説明してきた内容は、その中の解決策の一つです。僕が知らないもっと良い解決策があるかもしれません。実は、上記のイベント伝播による処理の組み立ては、昔Dojo toolkitでも同じようなことをやっていて、それをChrome MySQL Adminの開発の時に持ち込んだものです。一度作ったものを次々と機能追加していく中で、スパゲッティコード、継ぎ接ぎコードにできるだけならないようにするための工夫として、自分なりにたどり着いた現時点での結論になります。

AngularJSを使っている方々へ、何かのヒントになれば幸いです。
