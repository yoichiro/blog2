---
layout: post
title: Azure App ServiceでActions on Google Java Client Libraryを使ってアクションを作る方法
categories:
- actions on google
---

遂に、Actions on Google向けの新しいクライアントライブラリがリリースされました、その名は、"Actions on Google Java/Kotlin Client Library"です。

[actions-on-google/actions-on-google-java](https://github.com/actions-on-google/actions-on-google-java)

今までほとんどの開発者は、"Actions on Google Client Library for Node.js" という名前のクライアントライブラリを使うことができました。つまり、Node.js を使ったアクションの構築方法しかなかったことになります。しかし、今、Java/Kotlin Client Libraryを使って、Javaプログラミング言語により観覧にアクションを構築することができます。

使い方の概要は、以下の記事にて説明されています。

[Announcing the Java & Kotlin client library for Actions on Google](https://medium.com/google-developers/announcing-the-java-kotlin-client-library-for-actions-on-google-217828dead)

この記事では、Java/Kotlin Client Libraryを使ったアクションの構築方法およびそれをAzure App Serviceをデプロイするための方法について紹介します。

![]({{ "/images/2019/01/aog_action_java_azure_app_service_01.webp" | prepend: site.baseurl }})

# 事前準備

以下の準備が必要です。

* Java Development Kit Version 1.8 もしくはそれ以上。
* [Maven](https://maven.apache.org/) version 3.6.0 もしくはそれ以上。
* Azureにアカウントがすでに登録されていること。
* [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) がすでにインストールされていて、Azureにサインインしていること。
* Google Cloud PlatformもしくはAmazon Web Services上でのGoogleアシスタント向けアクションの構築方法をすでに理解していること。
* Actions on Googleプロジェクトと、それに接続されたDialogflowエージェントをすでに持っていること。

もしアクションの構築方法を知らない場合は、コードラボにて学習することが可能です。

[Build Actions for the Google Assistant (Level 1)](https://codelabs.developers.google.com/codelabs/actions-1/index.html#0)

[Build Actions for the Google Assistant (Level 2)](https://codelabs.developers.google.com/codelabs/actions-2/index.html#0)

まだAzure CLIを使ってAzureにサインインしていない場合は、以下のコマンドを実行してください

```bash
$ az login
$ az account set --subscription <SUBSCRIPTION_ID>
```

# リソースグループの作成

ではアクションの作成を始めましょう。最初に、以下のコマンドを使って、リソースグループを作成します。

```bash
$ az group create --name <RESOURCE_GROUP_NAME> --location <LOCATION_NAME>
```

# プロジェクトの作成

次に、Mavenを使って、新しいプロジェクトを作成します。この記事では、プロジェクト名として "simple-action" を、パッケージ名として "jp.eisbahn.actions.simpleaction" を使います（"jp.eisbahn" は僕が持っているドメインです）。

```bash
$ mvn archetype:generate -DgroupId=jp.eisbahn.actions.simpleaction -DartifactId=simple-action -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false
```

上記のコマンドを実行後、 `simple-action` ディレクトリが作成され、そしてそのディレクトリ内にいくつかのファイルが生成されます。しかし、この記事においては、いくつかのファイルは必要ではありません。以下のファイルとディレクトリを削除します。

* `src/main/webapp/WEB-INF/web.xml`
* `src/main/webapp/index.jsp`

# pom.xmlファイルの編集

プロジェクトの生成後、以下の内容で `pom.xml` ファイルを置き換えます。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>jp.eisbahn.actions.simpleaction</groupId>
  <artifactId>simple-action</artifactId>
  <packaging>war</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>simple-action Maven Webapp</name>
  <url>http://maven.apache.org</url>
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <java.version>1.8</java.version>
  </properties>

  <dependencies>
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.1.0</version>
      <scope>provided</scope>
    </dependency>
    <dependency>
      <groupId>com.google.actions</groupId>
      <artifactId>actions-on-google</artifactId>
      <version>1.0.1</version>
    </dependency>
  </dependencies>

  <build>
    <finalName>simple-action</finalName>

    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.8.0</version>
        <configuration>
          <source>${java.version}</source>
          <target>${java.version}</target>
        </configuration>
      </plugin>

      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-war-plugin</artifactId>
        <version>3.2.2</version>
        <configuration>
          <failOnMissingWebXml>false</failOnMissingWebXml>
        </configuration>
      </plugin>

      <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.5.2</version>
        <configuration>
          <schemaVersion>v2</schemaVersion>
          <resourceGroup>__RESOURCE_GROUP_NAME__</resourceGroup>
          <appName>__APP_NAME__</appName>
          <region>__LOCATION_NAME__</region>
          <pricingTier>__PRICE_TEAR_NAME__</pricingTier>
          <runtime>
            <os>windows</os>
            <javaVersion>1.8</javaVersion>
            <webContainer>tomcat 8.5</webContainer>
          </runtime>
          <deployment>
            <resources>
              <resource>
                <directory>${project.basedir}/target</directory>
                <includes>
                  <include>*.war</include>
                </includes>
              </resource>
            </resources>
          </deployment>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

上記のコード内で、 `azure-webaoo-maven-plugin` plugin 要素はいくつかのプレースホルダーを持っています。以下のプレースホルダーを置き換える必要があります、

* __RESOURCE_GROUP_NAME__: 作成したリソースグループ名。
__APP_NAME__: アプリケーションの識別子。
__LOCATION_NAME__: アプリを作成したいロケーション名。
__PRICING_TIER_NAME__: Pricing Tier の名前。

`azure-webapp-maven-plugin` は、もし存在していなければ、自動的に新しい App Service を生成します。ここでそれらの名前を決定し、置き換えてください。

## Java Development Kit version 11 向け設定

もしJava Development Kit バージョン 11 もしくはそれ以上を使っている場合は、上記の `pom.xml` を使って `mvn` コマンドを実行するとエラーが発生します。その理由は、JAXBが廃止になったためです。この問題を解決するために、 `azure-webapp-maven-plugin` plugin 要素に以下の定義を追加する必要があります。

```xml
<plugin>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-webapp-maven-plugin</artifactId>
  <version>1.5.2</version>
  <configuration>
    ...
  </configuration>
  <!-- Add the following definition -->
  <dependencies>
    <dependency>
      <groupId>javax.xml.bind</groupId>
      <artifactId>jaxb-api</artifactId>
      <version>2.4.0-b180830.0359</version>
    </dependency>
    <dependency>
      <groupId>com.sun.xml.bind</groupId>
      <artifactId>jaxb-impl</artifactId>
      <version>2.4.0-b180830.0438</version>
    </dependency>
    <dependency>
      <groupId>com.sun.xml.bind</groupId>
      <artifactId>jaxb-core</artifactId>
      <version>2.3.0.1</version>
    </dependency>
    <dependency>
      <groupId>javax.activation</groupId>
      <artifactId>activation</artifactId>
      <version>1.1.1</version>
    </dependency>
  </dependencies>
</plugin>
```

# フルフィルメントコードの作成

次に、アクションのフルフィルメントコードを作成する必要があります。以下のコマンドを使って、ソースディレクトリ作成します。

```bash
$ mkdir -p src/main/java/jp/eisbahn/actions/simpleaction
```

その後、"SimpleApp.java" というフルフィルメントファイルを `simpleaction` ディレクトリに以下の内容で作成します。

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
        builder.add("Hello, Azure App Service!");
        return builder.build();
    }

}
```

上記のコードでは、`SimpleApp` クラスは `DialogflowApp` クラスを継承しています。つまり、そのクラスは、Dialogflowエージェントからのリクエストを処理することが可能です。そして、 `ForIntent` アノテーションを使うことで、インテントハンドラメソッドを作ることができます。

最初の引数は、リクエストの情報を持っています。そして、 `ResponseBuilder` クラスを使って、 `ActionResponse` インスタンスを作成可能です。

[ResponseBuilder](https://actions-on-google.github.io/actions-on-google-java/com/google/actions/api/response/ResponseBuilder.html)

上記のAPIリファレンスを読むことで、レスポンスの構築方法を知ることができます。

# サーブレットコードの作成

ところで、 `SimpleApp` クラスは、HTTPリクエストを処理する能力を持っていません。代わりに、あなたはHTTPリクエストおよびレスポンスを処理するためのハンドラクラスを作成する必要があります。この記事では、HTTPリクエスト及びレスポンスを処理するために、Javaサーブレットコードを作成します。

以下の内容で、 `simpleaction` ディレクトリ内に "SimpleServlet.java" という名前のフルフィルメントファイルを作成します。

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

* リクエストボディ文字列。
* すべてのリクエストヘッダエントリを持つ `Map<String, String>` オブジェクト。

その後、上記を引数に伴う `SimpleApp` インスタンスの `handleRequest()` メソッドを呼び出します。もしそのメソッドの呼び出し結果が成功であれば、 `CompletableFuture<String>` オブジェクトが返されます。これは、JavaScriptでのPromiseのような非同期処理のためです。上記のコードでは、 `get()` メソッドを呼び出すことで、実際の結果を取得しています。

結果は、JSON文字列として表現されるレスポンスの文字列値です。 `SimpleServlet` は、 `getWriter()` メソッドの呼び出しによって得られる Writer オブジェクトを使って、そのレスポンス文字列を送信します。

# コードのデプロイ

やりました！必要なファイルが全て揃いました。ここで、Azureにアクションをデプロイすることができます。

アクションをデプロイするために、以下のコマンドを実行します。

```bash
$ mvn package azure-webapp:deploy
```

もし以下のような出力があれば、デプロイは成功です。

```bash
[INFO] Scanning for projects...
[INFO]
[INFO] -----------< jp.eisbahn.actions.simpleaction:simple-action >------------
[INFO] Building simple-action Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
...
[INFO] Deploying the war file simple-action.war...
[INFO] Successfully deployed the artifact to https://<APP_NAME>.azurewebsites.net
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  25.064 s
[INFO] Finished at: 2019-01-23T15:57:41+09:00
[INFO] ------------------------------------------------------------------------
```

以下のコマンドによって、テストすることができます。

```bash
$ curl -v -X POST -d "{}" https://<APP_NAME>.azurewebsites.net/
```

もし、以下のような出力がされれば、OKです。

```bash
< HTTP/1.1 500
< Content-Length: 55
< X-Powered-By: ASP.NET
< Set-Cookie: ARRAffinity=932c5351a290b46f876f6d4453f2bdc8625e8682cbfa134e04eb88e0677cb1fc;Path=/;HttpOnly;Domain=<APP_NAME>.azurewebsites.net
< Date: Wed, 23 Jan 2019 07:02:34 GMT
<
* Connection #0 to host <APP_NAME>.azurewebsites.net left intact
java.lang.Exception: Intent handler not found - INVALID%
```

"Intent handler not found" というメッセージは、Actions on Google Java/Kotlin Client Library から返されたメッセージです。

デプロイ後、そのURLをDialogflowエージェントのフルフィルメントWebhook URLとして登録します。その後、Actionsシミュレータ上でアクションを呼び出します。Googleアシスタントから "Hello, Azure App Service!" というレスポンスを得るはずです。

# まとめ

もしあなたがJavaプログラマであれば、Actions on Google Java/Kotlin Client Libraryと、慣れ親しんだJava言語により、Googleアシスタント向けアクションを構築することができます。あなたがGoogleアシスタント向けのアクションの構築に興味を持った際に、この記事が役に立てば幸いです。

最後に、以下のGitHubリポジトリから、完全なコードセットを入手することができます。

[yoichiro/simple-action-azure-java-maven](https://github.com/yoichiro/simple-action-azure-java-maven)
