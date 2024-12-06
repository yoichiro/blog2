---
layout: post
title: Azureの開発環境をDockerで作ってみた
categories:
- azure
---

ひょんなことから、最近（昨年の12月末くらいから）Microsoft が提供している Azure に興味を持ち始めました。Google アシスタント向けアクションを作るためのバックエンドサーバは、HTTPがお話しできれば基本的に何でも良く、もちろん GCP でなくても構いません。では、Azure を使ってフルフィルメントを作るにはどうすれば良いか、とか、Actions on Google SDK (Nodejs & Java) は Azure でも使えるのか、といった検証を最近はしていました。

その結果は、以下にまとめてあります。

* [Azure Functions Node.jsでActions on Google Client Libraryを使う方法](https://www.eisbahn.jp/yoichiro/2019/01/aog_client_library_azure_function_nodejs.html)
* [Azure App ServiceでActions on Google Client Libraryを使う方法](https://www.eisbahn.jp/yoichiro/2019/01/aog_client_library_azure_app_service.html)
* [Azure App ServiceでActions on Google Java Client Libraryを使ってアクションを作る方法](https://www.eisbahn.jp/yoichiro/2019/01/aog_java_azure_app_service.html)
* [Azure App Service/Functions向けのGradleプラグイン](https://www.eisbahn.jp/yoichiro/2019/01/azure-gradle-plugin.html)

自分にとって新しいことを試すことは、本当に新鮮でエキサイティングです。

そして、これも昨年末からなのですが、プライベートで使ってきた MacBook Pro、Intel CPU が積まれた初代からずっと何台も使ってきましたが、遂にこのほど使うのをやめました。既に最後の MacBook Pro 13' は自分の手元になく、ビッグカメラで買い取りしてもらっちゃってます。その代わりに、Crostini （Linux Container）が動作する Chromebook に移行しました。自分にとっては、

* Chrome が快適に動いて（言うまでもない）、
* Chrome Apps も動作して（Chrome Apps 開発者なので）、
* Linux が GUI 込みで使えて（標準では Debian 9）、
* Android アプリも動作して （これはさほど使ってないけど）、
* Googleアシスタントも装備されていて（Assistant GDE なので）、
* IntelliJ IDEA も Visual Studio Code も動作する（ソフトウェアエンジニアです）

こんな環境、最高すぎるわけです。

着々開発環境を Crostini にて作っていったのですが、最初は何も考えずにどんどんインストールしていきました。しかし、その Linux 自体がコンテナなので、そんな中に個別に直接いろいろ入れていって良いものなのか、と、一通り入れ終わってから気がつきました。別に親がコンテナだからって個別に入れていけばそれでも良いのですが、せっかくなので、今まで避けてきたこともやってみようじゃないか、と。

そう、Docker です。

Docker 使うと、いろいろなメリットを得られるのですが、その中の一つとして「目の前にあるローカル環境とは隔離された別の環境を作ることができる」ということがあります。しかも、Virtual Machine 程のコストをかけずに、です。Docker のコンテナ単位で入れたり消したりできるので、必要な時に必要な環境を作って、必要なくなれば破棄する、そういったことがとてもシンプルにできるようになります。

今回の場合、自分にあった開発環境を作りたいので、Docker Hub から探すのではなく、自分で Docker イメージを定義してコンテナを作ることにチャレンジしてみました。そのためには、 `Dockerfile` というファイルを作って、その中に作りたい環境を構築するためのコマンドを書いていきます。OS の再インストール時の環境構築の手間を軽減させるために、昔ならシェルスクリプトで書いていたようなことを、 `Dockerfile` に書いていく、そんなイメージです。

最初に書いた Dockerfile は、Chrome Native Client（NaCl）の開発環境でした。僕は Chromebook 向けのアプリをいくつか開発して提供しています。その中のいくつかは NaCl によって機能を実現しているのですが、その開発環境は非常に特殊です。今も 32bit な Chromebook は多く、それらをサポートするために i386 向けの Toolchain も必要になってきます。そういったものを自分の Linux に直に入れたくはありません。そう、Docker の出番です。

[https://github.com/yoichiro/docker-chromeapp](https://github.com/yoichiro/docker-chromeapp)

さて、Azure 向けの開発において、いくつかのツールをインストールして整備することが必要になります。僕は今のところ、Azure Functions や Azure WebApps くらいしか使っていないのですが、それだけでも、以下のツールが必要でした。

* Azure CLI
* .Net Core Runtimr
* .Net Core SDK
* Azure Functions Core Tools
* OpenJDK
* Gradle
* Maven
* Spring Boot
* NodeJS

これらのインストールは、そこそこ骨が折れる作業です。一発で全て整備でき、しかも分離された環境となるように、 [Dockerfile を書きました](https://github.com/yoichiro/docker-azure/blob/master/Dockerfile) 。

[https://github.com/yoichiro/docker-azure](https://github.com/yoichiro/docker-azure)

Docker がインストールされていれば、あとは以下のコマンドにて Azure の開発環境を手にすることができます。

```bash
$ git clone https://github.com/yoichiro/docker-azure.git
$ cd docker-azure
$ docker build --build-arg USERNAME=<YOUR_USERNAME> -t azure-dev:latest ./
$ docker run -v <YOUR_PROJECT_PATH>:/home/<YOUR_USERNAME>/project --name azure-dev -i -t -d azure-dev:latest /bin/bash
$ docker exec -it azure-dev /bin/bash
```

これにより、コンテナが起動して、そのコンテナにインストールされた `az` コマンドや `func` コマンドなどを使えます。Docker の Volume 機能によってマウントされたプロジェクトは、コンテナ内で `/home/ユーザ名/project` に配置されますので、そこに行って `az` コマンドなどでプロジェクトをデプロイしたりすることが可能です。

僕は NodeJS や Java が今のところ主戦場だったので、その環境のみとなっていますが、皆さんも [僕の Dockerfile](https://github.com/yoichiro/docker-azure/blob/master/Dockerfile) を参考にして、ぜひ自分なりの開発環境を作ってみてください。
