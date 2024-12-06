---
layout: post
title: Google AppEngine向けのJava Client Libraryを使ったアクションの構築方法
categories:
- actions on google
---

先週、新しいクライアントライブラリがリリースされました。その名は、"Actions on Google Java/Kotlin Client Library" です。

[actions-on-google/actions-on-google-java](https://medium.com/r/?url=https%3A%2F%2Fgithub.com%2Factions-on-google%2Factions-on-google-java)

今まで、ほとんどの開発者は、"Actions on Google Client Library for Node.js" と呼ばれるクライアントライブラリを使用してきました。つまり、私たちは Node.js を使ったアクションの構築方法しかありませんでした。しかし、今、私たちは Java/Kotlin Client Library を使って、Javaプログラミング言語でアクションを作ることができます。

使い方の概要は、以下の記事で説明されています。

[Announcing the Java & Kotlin client library for Actions on Google](https://medium.com/google-developers/announcing-the-java-kotlin-client-library-for-actions-on-google-217828dead)

この記事では、Java/Kotlin Client Library を使ったアクションの構築方法と、Google AppEngineへのデプロイ方法について紹介します。

![]({{ "/images/2019/01/simple_fulfillment_appengine_01.webp" | prepend: site.baseurl }})

# 事前準備

アクションの構築の前に、以下がインストールされていることが必要です。

* Java Development Kit バージョン 1.8 もしくはそれ以上。
* [Gradle](https://gradle.org/install/) バージョン 5.1.1 もしくはそれ以上。
* [Google Cloud SDK](https://cloud.google.com/sdk/docs/)
* App Engine SDK for Java (以下のコマンドでインストール可能です)。

```bash
gcloud components install app-engine-java
```

* Actions on Google プロジェクト、およびそれに接続されている Dialogflow エージェントを持っている。

また、Google Cloud Platform 上のそのプロジェクトにおいて、課金が有効になっている必要があります。

もしアクションの構築方法をご存じではない方は、コードラボにて学習することができます。

[Build Actions for the Google Assistant (Level 1)](https://codelabs.developers.google.com/codelabs/actions-1/index.html#0)

[Build Actions for the Google Assistant (Level 2)](https://codelabs.developers.google.com/codelabs/actions-2/index.html#0)

# プロジェクトの作成

では、アクションの構築を始めましょう。最初に、Gradle を使って、新しいプロジェクトを作成します。この記事では、プロジェクト名として "simple-fulfillment-java" を、パッケージ名として "jp.eisbahn.actions.simpleaction" を使います（"jp.eisbahn" は僕が持っているドメインです）。

```bash
$ mkdir simple-fulfillment-appengine
$ cd simple-fulfillment-appengine
$ gradle init
```

`gradle` コマンドは、いくつかの質問を聞いてきます。以下のようにそれらに答えます。

* Select type of project to generate: 1 (basic)
* Select build script DSL: 1 (groovy)
* Project name: "simple-fulfillment-java"

`build.gradle` ファイルを含むいくつかのファイルが作成されます。

# ビルドファイルの編集

プロジェクトが生成された後、 `build.gradle` ファイルは空のファイルです。以下の内容で置き換えます。

```groovy
buildscript {
    repositories {
        jcenter()
        mavenCentral()
    }
    dependencies {
        classpath 'com.google.cloud.tools:appengine-gradle-plugin:1.+'
    }
}

repositories {
    jcenter()
    mavenCentral()
}

apply plugin: 'java'
apply plugin: 'war'
apply plugin: 'com.google.cloud.tools.appengine'

dependencies {
    compile 'com.google.appengine:appengine-api-1.0-sdk:+'
    compile group: 'com.google.actions', name: 'actions-on-google', version: '1.0.0'
    providedCompile 'javax.servlet:javax.servlet-api:3.1.0'
}

appengine {
    deploy {
    }
}

group = 'jp.eisbahn.actions.simpleaction'
version = '1.0-SNAPSHOT'

sourceCompatibility = '1.8'
targetCompatibility = '1.8'
```

# フルフィルメントコードの作成

次に、アクションのためのフルフィルメントコードを作成します。まず、以下のコマンドを使って、ソースディレクトリを作成します。

```bash
$ mkdir -p src/main/java/jp/eisbahn/actions/simpleaction
```

その後、 `simpleaction` ディレクトリ内に "SimpleApp.java" という名前のフルフィルメントファイルを以下の内容で作成します。

```java
package jp.eisbahn.actions.simpleaction;

import com.google.actions.api.ActionRequest;
import com.google.actions.api.ActionResponse;
import com.google.actions.api.DialogflowApp;
import com.google.actions.api.ForIntent;
import com.google.actions.api.response.ResponseBuilder;

public class SimpleApp extends DialogflowApp {

    @ForIntent("Default Welcome Intent")
    public ActionResponse welcome(ActionRequest request) {
        ResponseBuilder builder = getResponseBuilder(request);
        builder.add("Hello, Google AppEngine!");
        return builder.build();
    }

}
```

上記コードでは、 `SimpleApp` クラスは `DialogflowApp` クラスを継承しています。つまり、そのクラスは Dialogflow Agent からのリクエストを処理することができます。そして、 `ForIntent` アノテーションを使って、インテントハンドラメソッドを作ることが可能です。

最初の引数は、リクエストの情報を持っています。そして、 `ResponseBuilder` クラスを使って、 `ActionResponse` インスタンスを作ることが可能です。

[ResponseBuilder](https://actions-on-google.github.io/actions-on-google-java/com/google/actions/api/response/ResponseBuilder.html)

上記の API リファレスを見て、レスポンスの構築方法を知ることができます。

# サーブレットコードの作成

ところで、 `SimpleApp` クラスは、HTTP リクエストを処理する能力を持ってはいません。代わりに、HTTP リクエストとレスポンスを処理するためのクラスを作成する必要があります。この記事では、HTTP リクエストとレスポンスの処理のために、Java Servlet を作成します。

`simpleaction` ディレクトリ内に、"SimpleServlet.java" という名前のフルフィルメントファイルを、以下の内容で作成してください。

```java
package jp.eisbahn.actions.simpleaction;

import com.google.actions.api.App;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Collections;
import java.util.Map;
import java.util.concurrent.ExecutionException;
import java.util.stream.Collectors;

@WebServlet(name = "SimpleServlet", urlPatterns = {"/"}, loadOnStartup = 1)
public class SimpleServlet extends HttpServlet {

    private App app = new SimpleApp();

    @Override
    protected void doPost(
            HttpServletRequest req, HttpServletResponse resp)
            throws IOException {
        String requestBody =
            req.getReader().lines().collect(Collectors.joining());
        Map<String, String> headersMap =
            Collections.list(req.getHeaderNames())
                .stream()
                .collect(Collectors.toMap(
                    name -> name,
                    req::getHeader));
        try {
            String responseBody =
                app.handleRequest(requestBody, headersMap).get();
            resp.setContentType("application/json; charset=utf-8");
            resp.getWriter().write(responseBody);
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
            resp.setStatus(
                HttpServletResponse.SC_INTERNAL_SERVER_ERROR);
            resp.getWriter().write(e.getMessage());
        }
    }

}
```

`SimpleApp` インスタンスを呼び出すために、以下の情報が必要です。

* リクエストボディの文字列
* 全てのリクエストヘッダ項目を持つ `Map<String, String>` オブジェクト。

その後、それらを引数として、 `SimpleApp` インスタンスの `handleRequest()` メソッドを呼び出します。もしそのメソッドの呼び出し結果が成功であれば、 `CompletableFuture<String>` オブジェクトが返されます。これは、JavaScript の Promise のような非同期処理のためです。上記のコードでは、 `get()` メソッドを呼び出すことで、実際の結果が得られます。

結果は、JSON 文字列として表現されたレスポンスの文字列です。 `SimpleServlet` は、 `getWriter()` メソッドの呼び出しによって得られた Writer オブジェクトを使って、レスポンス文字列を送っています。

# AppEngine のための設定ファイルの作成

最後に、AppEngine のための設定ファイルを準備します。以下のコマンドを使ってそのファイルを置くための新しいディレクトリを作成します。

```bash
$ mkdir -p src/main/webapp/WEB-INF
```

その後、 `WEB-INF` ディレクトリ内に "appengine-web.xml" という名前のファイルを以下の内容で作成します。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<appengine-web-app xmlns="http://appengine.google.com/ns/1.0">
    <runtime>java8</runtime>
    <threadsafe>true</threadsafe>
</appengine-web-app>
```

# AppEngine へのコードのデプロイ

全てのファイルが揃いました。ここで、AppEngine にコードをデプロイしましょう。最初に、もし他のプロジェクトが選択されていた場合は、コードをデプロイしたい対象のプロジェクトを指定する必要があります。

```bash
$ gcloud config set project <YOUR_PROJECT_ID>
```

では、以下のコマンドにてコードをデプロイします。

```bash
$ gradle appengineDeploy
```

もしデプロイが成功すれば、以下のコマンドを使ってテストが可能です。

```bash
$ curl -v -X POST -d "{}" https://<PROJECT_ID>.appspot.com/
```

以下のような出力を得られれば、OK です。

```bash
< HTTP/2 500
< x-cloud-trace-context: 4ba6987ee3351d91cd43fff194b79962;o=1
< date: Mon, 21 Jan 2019 00:36:39 GMT
< content-type: text/html
< server: Google Frontend
< content-length: 55
< alt-svc: quic=":443"; ma=2592000; v="44,43,39,35"
<
* Curl_http_done: called premature == 0
* Connection #0 to host azure-test-65d3c.appspot.com left intact
java.lang.Exception: Intent handler not found - INVALID%
```

"Intent handler not found" というメッセージは、Actions on Google Java/Kotlin Client Library によって返却されたものとなります。

デプロイ後に、Dialogflow エージェントのフルフィルメント Webhook URL としてそのURLを登録してください。その後、Actions シミュレータ上で、アクションを呼び出します。Google アシスタントから "Hello, Google AppEngine!" というレスポンスを得るはずです。

# まとめ

もしなたが Java プログラマであれば、Actions on Google Java/Kotlin Client Library と、慣れ親しんだ Java 言語を使って、Google アシスタント向けアクションを作ることができます。あなたが Google アシスタント向けアクションの構築に興味があれば、この記事があなたにとって有益になると嬉しいです。

最後に、完成したコードセットを以下の GitHub リポジトリから得ることができます。

[yoichiro/simple-fulfillment-appengine](https://github.com/yoichiro/simple-fulfillment-appengine)
