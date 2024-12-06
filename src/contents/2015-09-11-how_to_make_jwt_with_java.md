---
layout: post
status: publish
published: true
title: JWTをJavaで作る方法
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 3323
wordpress_url: https://www.eisbahn.jp/yoichiro/?p=3323
date: '2015-09-11 19:28:44 +0900'
date_gmt: '2015-09-11 10:28:44 +0900'
categories:
- Java
---

公私共にJWTをやらんとあかんな、と思い、JWTをJavaで扱うためのjose4jというライブラリを使い始めました。

[https://bitbucket.org/b_c/jose4j/wiki/Home](https://bitbucket.org/b_c/jose4j/wiki/Home)

使い方は簡単。例えば、HMAC-SHA256の署名付きJWTを作るためには、以下のようにすれば良い。

まず、mavenでプロジェクトを作る。

```
mvn archetype:generate -DinteractiveMode=false -DgroupId=jp.eisbahn.arekore -DartifactId=arekore
```

次に、pom.xmlに以下を追加する。

```
<build>
<plugins>
<plugin>
			<artifactId>maven-compiler-plugin</artifactId>
			<configuration>
				<source>1.7</source>
				<target>1.7</target>
			</configuration>
		</plugin>
	</plugins>
</build>
<dependency>
	<groupId>org.bitbucket.b_c</groupId>
	<artifactId>jose4j</artifactId>
	<version>0.4.4</version>
</dependency>
```

もちろん1.8にすることも可能。最低限1.7にしないと、エラーになる。

そして、Javaコードはこんな感じ。

```
String secret = "foobar";
Key key = new HmacKey(secret.getBytes("UTF-8"));
JwtClaims claims = new JwtClaims();
claims.setIssuer("Yoichiro Tanaka");
claims.setAudience("Your service!");
claims.setExpirationTimeMinutesInTheFuture(10);
claims.setGeneratedJwtId();
claims.setIssuedAtToNow();
claims.setNotBeforeMinutesInThePast(2);
claims.setSubject("your_id");
JsonWebSignature jws = new JsonWebSignature();
String claimsString = claims.toJson();
System.out.println(claimsString);
jws.setPayload(claimsString);
jws.setAlgorithmHeaderValue(AlgorithmIdentifiers.HMAC_SHA256);
jws.setKey(key);
jws.setDoKeyValidation(false);
String jwt = jws.getCompactSerialization();
System.out.println("JWT: " + jwt);
```

これを実行すると、以下のようになる。

```
{"iss":"Yoichiro Tanaka","aud":"Your service!","exp":1441967637,"jti":"z76ppZvkmFJH2v2e1rCP7g","iat":1441967037,"nbf":1441966917,"sub":"your_id"}
JWT: eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJZb2ljaGlybyBUYW5ha2EiLCJhdWQiOiJZb3VyIHNlcnZpY2UhIiwiZXhwIjoxNDQxOTY3NjM3LCJqdGkiOiJ6NzZwcFp2a21GSkgydjJlMXJDUDdnIiwiaWF0IjoxNDQxOTY3MDM3LCJuYmYiOjE0NDE5NjY5MTcsInN1YiI6InlvdXJfaWQifQ.wdk1EqNh7coexVVj1MfLA0StNJr4jzYLVus3x-1voK0
```

かーんたん、でした。
