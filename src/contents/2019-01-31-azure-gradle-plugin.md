---
layout: post
title: Azure App Service/Functions向けのGradleプラグイン
categories:
- azure
---

Azure App Serviceにウェブアプリをデプロイするための公式Mavenプラグインがあります。

[Maven Repository: com.microsoft.azure " azure-webapp-maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-webapp-maven-plugin)

Javaプログラマは、そのMavenプラグインを使って、Azure App ServiceにJavaウェブアプリをデプロイすることができます。以下の記事で、Mavenプラグインの使い方について説明しました。

[How to build actions with Java Client Library on Azure App Service](https://medium.com/@yoichiro/how-to-build-actions-with-java-client-library-on-azure-app-service-3df7e6b1aa0b)

多くのJavaプログラマは、プロジェクトを管理するためにMavenを使っています。一方、プロジェクト管理ツールはもう一つあります。その名前は、"Gradle"です。

[Gradle Build Tool](https://gradle.org/)

残念ながら、Azure にウェブアプリをデプロイするための公式のGradleプラグインは、まだありません。しかし、GitHubにそのようなプラグインがあります。

[lenala/azure-gradle-plugins](https://github.com/lenala/azure-gradle-plugins)

`azure-gradle-plugins` は、私が本当に欲しかったプラグインです。なぜなら、私はプロジェクトの管理にGradleを普段使っているからです。そのプラグインは、公式のMavenプラグインと同じように使うことができます。このプラグインは、2つのプラグインを持っています。

* `azure-webapp-gradle-plugin`
* `azure-functions-gradle-plugin`

Javaサーブレットアプリをデプロイする `azure-webapp-gradle-plugin` の使い方を紹介します。

![]({{ "/images/2019/01/azure_gradle_plugin_01.webp" | prepend: site.baseurl }})

# 事前準備

以下をインストールする必要があります。

* Java Development Kit Version 1.8 もしくはそれ以上。
* [Gradle](https://gradle.org/install/) version 5.1.1 もしくはそれ以上。
* Azureにアカウントがすでに登録されていること。
* [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) がすでにインストールされていて、Azureにサインインしていること。

まだAzure CLIを使ってAzureにサインインしていない場合は、以下のコマンドを実行してください

```bash
$ az login
$ az account set --subscription <SUBSCRIPTION_ID>
```

# リソースグループの作成

以下のコマンドを使って、新しいリソースグループを作成します。

```bash
$ az group create --name <RESOURCE_GROUP_NAME> --location <LOCATION_NAME>
```

# azure-webapp-gradle-pluginのインストール

ローカルのMavenリポジトリ内に `azure-webapp-gradle-plugin` をあなた自身でインストールします。これをするために、GitHubリポジトリからcloneして、 `install` オプション付きで `gradle` コマンドを実行する必要があります。

```bash
$ git clone git@github.com:lenala/azure-gradle-plugins.git
$ cd azure-gradle-plugins/azure-webapp-gradle-plugin
$ gradle install
```

# プロジェクトの作成

次に、以下のように `gradle` コマンドを使ってプロジェクトを作成します。

```bash
$ mkdir simple-servlet-app-gradle
$ cd simple-servlet-app-gradle
$ gradle init
```

コマンドはあなたにいくつかの質問を尋ねてきます。以下のように回答してください。

* Select type of project to generate: 1
* Select build script DSL: 1
* Project name: ENTER キーを押してください。

```bash
Starting a Gradle Daemon (subsequent builds will be faster)
Select type of project to generate:
  1: basic
  2: groovy-application
  3: groovy-library
  4: java-application
  5: java-library
  6: kotlin-application
  7: kotlin-library
  8: scala-library
Enter selection (default: basic) [1..8] 1
Select build script DSL:
  1: groovy
  2: kotlin
Enter selection (default: groovy) [1..2] 1
Project name (default: simple-servlet-app-gradle):
BUILD SUCCESSFUL in 1m 10s
2 actionable tasks: 2 executed
```

# サーブレットの作成

ここで、サーブレットのコードを作成します。最初に、以下のコマンドを使って、ソースディレクトリを作成します。

```bash
$ mkdir -p src/main/java/jp/eisbahn/servlets/simpleservlet
```

そして、 `simpleservlet` ディレクトリ内に、"SimpleServlet.java" という名前で新しいサーブレットのファイルを作成します。 `SimpleServlet.java` ファイルのコードは、以下となります。

```java
package jp.eisbahn.servlets.simpleservlet;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet(
    name = "SimpleServlet",
    urlPatterns = {"/"},
    loadOnStartup = 1)
public class SimpleServlet extends HttpServlet {

    @Override
    public void doGet(
            HttpServletRequest req, HttpServletResponse resp)
            throws ServletException, IOException {
        resp.getWriter().print("Hello, App Service!");
    }

}
```

Java Sevlet 3.0 以上においては、 `web.xml` ファイルを作成する必要はありません。

# ビルドファイルの編集

プロジェクトの作成後、内容のない `build.gradle` ファイルが作られています。サーブレットコードをテストするために、以下のコードで `build.gradle` ファイルの内容を編集します。

```groovy
buildscript {
    repositories {
        jcenter()
    }
}

apply plugin: 'war'
apply from: 'https://raw.github.com/gretty-gradle-plugin/gretty/master/pluginScripts/gretty.plugin'

repositories {
    jcenter()
}

dependencies {
    providedCompile 'javax.servlet:javax.servlet-api:3.1.0'
}

sourceCompatibility = '1.8'
targetCompatibility = '1.8'
```

# ローカルでのサーブレットのテスト

ここで、 `gretty` プラグインを使って、ローカルでサーブレットのテストができます。以下のコマンドを実行してください。

```bash
$ gradle appRun
```

その後、もう一つターミナルを開き、サーブレットにアクセスするために以下のコマンドを実行します。

```bash
$ curl http://127.0.0.1:8080/simple-servlet-app-gradle/
```

もし "Hello, App Service!" と表示されれば、サーブレットは正しく動作しています。

# 再びビルドファイルの編集

今、サーブレットが動作しました。では、それをAzureにデプロイしましょう。つまり、 `azure-webapp-gradle-plugin` を使うタイミングが来ました！

`build.gradle` ファイルの内容を以下で置き換えます。

```groovy
buildscript {
    repositories {
        jcenter()
        maven {
            url uri('__LOCAL_MAVEN_REPOSITORY_PATH__')
        }
    }
    dependencies {
        classpath group: 'com.microsoft.azure', name: 'azure-webapp-gradle-plugin', version: '1.0-SNAPSHOT'
    }
}

apply plugin: 'war'
apply from: 'https://raw.github.com/gretty-gradle-plugin/gretty/master/pluginScripts/gretty.plugin'
apply plugin: 'com.microsoft.azure.azurewebapp'

repositories {
    jcenter()
}

dependencies {
    providedCompile 'javax.servlet:javax.servlet-api:3.1.0'
}

sourceCompatibility = '1.8'
targetCompatibility = '1.8'

azureWebApp {
    resourceGroup = '__RESOURCE_GROUP_NAME__'
    appName = '__APP_NAME__'
    pricingTier = '__PRICE_TEAR_NAME__'
    region = '__LOCATION_NAME__'
    appService = {
        type = 'windows'
        javaWebContainer = 'tomcat 8.5'
        javaVersion = '1.8'
    }
    authentication = {
        type = 'azurecli'
    }
    deployment = {
        type = 'war'
    }
}
```

上記のコードでは、 `azureWebApp` プロパティはいくつかのプレースホルダーを持っています。以下のプレースホルダーを置き換える必要があります。

* `__LOCAL_MAVEN_REPOSITORY_PATH__`: ローカルのMavenリポジトリのパスです。例えば、 `/home/yoichiro/.m2/repository` です。
* `__RESOURCE_GROUP_NAME__`: 作成したリソースグループの名前です。
* `__APP_NAME__`: アプリの識別子です。
* `__LOCATION_NAME__`: アプリを作成したいロケーションの名前です。
* `__PRICING_TIER_NAME__`: [Pricing Tier](https://docs.microsoft.com/en-us/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme?view=azure-java-stable#pricingtier) の名前です。

# コードのデプロイ

やりました！必要なファイルが全て揃いました。では、Azureにサーブレットをデプロイできます。

サーブレットをデプロイするために、以下のコマンドを実行します。

```bash
$ gradle war azureWebappDeploy
```

もし以下のような出力がされれば、デプロイは成功です。

```bash
Starting a Gradle Daemon (subsequent builds will be faster)
> Task :azureWebappDeploy
Start deploying to Web App simplewa1...
Authenticate with Azure CLI 2.0
Target Web App doesn't exist. Creating a new one...
com.microsoft.azure.gradle.webapp.handlers.WindowsRuntimeHandlerImpl
Creating App Service Plan 'serviceplan949842'...
Successfully created App Service Plan.
Processing settings
Creating WebApp
Successfully created Web App.
Deploying artifacts
War name is: /home/yoichiro6642/projects/azure/simple-servlet-app-gradle/build/libs/simple-servlet-app-gradle.war
Starting to deploy the war file...
Successfully deployed Web App at https://simplewa1.azurewebsites.net
BUILD SUCCESSFUL in 1m 6s
3 actionable tasks: 2 executed, 1 up-to-date
```

`azure-webapp-gradle-plugin` は、新しいプランと App Service を自動的に作成します。

以下のコマンドで、デプロイされたサーブレットをテストします。

```bash
$ curl https://<APP_NAME>.azurewebsites.net/
```

もし "Hello, App Service!" という文字列を得られれば、成功です。

# まとめ

このエントリにて、Azure App Service のための Gradle プラグインの使い方を説明しました。この `azure-gradle-plugins` は、Gradle を使用している全ての Java 開発者に対して、とても良いツールです。私は本当に、本当に、パブリックMavenリポジトリからこの Gradle プラグインが公式に公開されることを願っています。それをあなたも望みます、よね？

もしそう思っていただけるなら、今すぐ以下の Issue にコメントあるいは +1 を投稿してください！

[deploy to maven repo . Issue #10 . lenala/azure-gradle-plugins](https://github.com/lenala/azure-gradle-plugins/issues/10)

あなたのコメントや投票がこのプラグインの良い未来をリードすると信じています。

最後に、完全なコードセットを以下から入手できます。

[yoichiro/simple-servlet-app-gradle](https://github.com/yoichiro/simple-servlet-app-gradle)
