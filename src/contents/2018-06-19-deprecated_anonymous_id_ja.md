---
layout: post
title: Googleアシスタント向けの匿名ユーザIDが非推奨になります
categories:
- actions on google
---

Actions on Googleの開始以来、開発者は匿名ユーザID機能を使うことが可能です。

[Anonymous User Identity](https://developers.google.com/actions/identity/user-info) - Actions on Google document

開発者は、ユーザをトラックするためにこの機能を使うことができます。具体的には、ユーザがアクションを利用する際に、Actions on Googleによって
そのユーザ向けに匿名ユーザIDが発行されます。その匿名ユーザIDは、アクションに渡されます。もしユーザがそのアクションを再び利用した場合は、
同じ匿名ユーザIDがそのアクションに再び渡されます。つまり、開発者はその匿名ユーザIDを使って、ユーザを識別することが可能です。更に言えば、
開発者はそのアクションをパーソナライズするために、匿名ユーザIDを利用することができます。おそらく、多くの開発者がその目的で匿名ユーザIDを
利用していると推測できます。

匿名ユーザIDの値は、[AppRequest](https://developers.google.com/actions/reference/rest/Shared.Types/AppRequest) オブジェクトの
[userId](https://developers.google.com/actions/reference/rest/Shared.Types/AppRequest#User.FIELDS.user_id) プロパティにて
渡されます。開発者は、以下のコードを使って、そのID値を取得するためのコードを書くことができます。

```js
app.intent("...", conv => {
    const anonymounsUserId = conv.user.id;
});
```

しかし、Googleは、2018年5月31日より、匿名ユーザIDを非推奨にすることをアナウンスしました。具体的には、
[AppRequest](https://developers.google.com/actions/reference/rest/Shared.Types/AppRequest) の
[userId](https://developers.google.com/actions/reference/rest/Shared.Types/AppRequest#User.FIELDS.user_id) プロパティは、
2019年5月31日以降は Webhook リクエストにもはや含まれなくなります。

[https://plus.google.com/u/0/+GoogleDevelopers/posts/ZFWNxv8q8Dx](https://plus.google.com/u/0/+GoogleDevelopers/posts/ZFWNxv8q8Dx)

つまり、開発者はアプリのために匿名ユーザIDを利用すべきではありません。そして、もしアプリ内で匿名ユーザIDを使っていた場合は、開発者はその
アプリをマイグレートする必要があります。

To migrate apps, developers have three options.
アプリをマイグレートするために、開発者は3つの選択肢を持っています。

# User Storage APIの利用

もしアクションと他のプラットフォームのアプリ（例: Android アプリ）の間で共有されるデータがなく、さらにデータのサイズが 10 キロバイトより
少ない場合は、開発者は User Storage API を使ってアプリをマイグレートすることができます。

[Save data across conversations](https://developers.google.com/actions/assistant/save-data#save_data_across_conversations) - Actions on Google document

User Storage の単位は、匿名ユーザIDと同じです。開発者は、以下のようにして、User Storage API を経由してデータを保管および取得することが
できます。

```js
app.intent("...", conv => {
    // Store a value
    conv.user.storage.count = 1;
    // Retrieve the value
    const count = conv.user.storage.count;
});
```

`conv.user.storage` はオブジェクトです。開発者は、複数の値をオブジェクトとして保管することが可能です。また、保管されたデータは、複数回の
会話を通じて保持されます。User Storage API によって、開発者は `conv.user.storage` にデータをマイグレートすることができるのです。

# Account Linkingの利用

もしアクションと他のプラットフォームのアプリ（例: Android アプリ）の間で共有されるデータがある、もしくは、データのサイズが 10 キロバイトを
超える場合は、開発者は Account Linking を使ってアプリをマイグレートすることができます。

[Account Linking](https://developers.google.com/actions/identity/) - Actions on Google document

つまり、このアプローチは、匿名ユーザIDから、開発者が持つ他のIDに移行することを意味しています。具体的には、開発者は2つの選択肢を持っています。

* Googleアカウントを使うために、[Google Sign-In for the Assistant](https://developers.google.com/actions/identity/google-sign-in) を利用する。
* 開発者が持つアカウントを利用する。

もし開発者が何らかのアプリやサービスで使われているアカウントを持っている場合は、開発者は2番目の選択肢を選択することができます。しかし、
同時に、開発者は Authorization Code Grant や Implicit Grant を提供する OAuth2 サーバを実装する必要があります。もし開発者が
何もアカウントを持っていなければ、開発者のアクションに Google Sign-In for the Assistant を統合することが簡単です。

Account Linking によって、Google Sign-In for the Assistant により認証されたアカウント、もしくは開発者自身のアカウントにデータが
関連付けられます。Account Linking が行われた後に、認証されたユーザのプロフィールが
[`Profile`](https://actions-on-google.github.io/actions-on-google-nodejs/classes/conversation.profile.html) クラスによって
表現される `conv.user.profile` にセットされます。つまり、開発者は以下のようなコードにマイグレートすることができます。

```js
app.intent("...", conv => {
    // Can retrieve the authenticated user's ID after doing Account Linking
    const userId = conv.user.profile.payload.sub; // Instead of `conv.user.id`
    // Store the data
    const data = ...;
    storeDataToDatabase(userId, data);
});
```

開発者は、Firebae RealtimeDB, GCP上のDatastore、GCE上のMySQL DBなどを含む何らかのデータベースを使うことができます。保存されたデータは、
他のアプリやサービスから参照することができるでしょう。

# Webhookで生成されたIDの利用

もし開発者が Account Linking を使用したくない場合で、データのサイズが 10 キロバイトを超える場合は、開発者は User Storage API と
Webhook で生成されたIDとの組み合わせを利用することが可能です。これは、User Storage 内に、生成された ID のみが保存されることを意味して
います。生成されたIDによって関連付けられたデータは、外部のデータベースに格納されます。

例えば、開発者は Webhook で生成された ID を使うための以下のようなコードを書いてマイグレートすることができます。

```js
app.intent("...", conv => {
    let userId;
    if ("userId" in conv.user.storage) {
        // Retrieve the generated user ID.
        userId = conv.user.storage.userId;
    } else {
        // Generate a new user ID.
        userId = generateUserId(); // Webhook-generated ID
        // Store the user ID into the User Storage.
        conv.user.storage.userId = userId;
    }
    // Store the data
    const data = ...;
    storeDataToDatabase(userId, data);
});
```

# まとめ

おそらく、匿名ユーザIDにはいくつかの問題があります（例: GDPR など）。匿名ユーザIDを使っている開発者は、可能な限り早く、上記の選択肢
のうちの一つにアプリをマイグレートすべきです。特に、もし Account Linking を使う場合は、ユーザ体験が変わってきます。しかし、ユーザは
Google アカウントや他のアカウントを使うことで、より多くの利益を得ることができるだろうと推測します。

より詳しい情報は、[the migration guide](https://developers.google.com/actions/identity/user-info) をご覧ください。
