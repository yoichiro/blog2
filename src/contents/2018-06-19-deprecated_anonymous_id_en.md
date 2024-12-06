---
layout: post
title: Anonymous User Identity for Google Assistant apps is deprecated
categories:
- actions on google
---

Since starting the Actions on Google, developers can use an Anonymous User Identity feature.

[Anonymous User Identity](https://developers.google.com/actions/identity/user-info) - Actions on Google document

Developers can use this feature to track users. For instance, when a user uses an action, an Anonymous User ID is issued
for the user by the Actions on Google. The Anonymous User ID is passed to the action. If the user uses the action again,
the same Anonymous User ID is passed again to the action. That is, developers can use the Anonymous User ID to distinct
users. Furthermore, they can use the Anonymous User ID to personalize the action. Probably, many developers are using
the Anonymous User ID for the purpose, I guess.

The Anonymous User ID value is passed on the
[userId](https://developers.google.com/actions/reference/rest/Shared.Types/AppRequest#User.FIELDS.user_id) property
of the [AppRequest](https://developers.google.com/actions/reference/rest/Shared.Types/AppRequest) object. Developers
can write a code to retrieve the ID value using the following code:

```js
app.intent("...", conv => {
    const anonymounsUserId = conv.user.id;
});
```

But, Google has announced that Anonymous User Identity is deprecated starting from May 31st 2018. For instance,
The [userId](https://developers.google.com/actions/reference/rest/Shared.Types/AppRequest#User.FIELDS.user_id) property
of [AppRequest](https://developers.google.com/actions/reference/rest/Shared.Types/AppRequest) will no longer be included
in webhook requests starting from May 31st 2019.

[https://plus.google.com/u/0/+GoogleDevelopers/posts/ZFWNxv8q8Dx](https://plus.google.com/u/0/+GoogleDevelopers/posts/ZFWNxv8q8Dx)

That is, developers should not use the Anonymous User ID for their apps. And, they have to migrate apps, if the Anonymous User ID has been used in the apps.

To migrate apps, developers have three options.

# Using the User Storage API

If there is no data which is shared between the Action and other platform apps (ex. Android apps) and the size of data is less than 10k bytes, the developer can migrate the app using the User Storage API.

[Save data across conversations](https://developers.google.com/actions/assistant/save-data#save_data_across_conversations) - Actions on Google document

The unit of the User Storage is the same as the Anonymous User Identity. Developers can store and retrieve data via the User Storage API as like the following:

```js
app.intent("...", conv => {
    // Store a value
    conv.user.storage.count = 1;
    // Retrieve the value
    const count = conv.user.storage.count;
});
```

The `conv.user.storage` is a object. Developers can store multiple values as a object. Also, the stored data are kept
across conversations. By the User Storage API, developers can migrate data to the `conv.user.storage`.

# Using the Account Linking

If there are data which are shared between the Action and other platform apps (ex. Android apps), or the size of data is more than 10k bytes, the developer can migrate the app using the Account Linking.

[Account Linking](https://developers.google.com/actions/identity/) - Actions on Google document

That is, this approach means moving from the Anonymous User IDs to other IDs which the developer has. For instance,
developers have two options:

* Using [Google Sign-In for the Assistant](https://developers.google.com/actions/identity/google-sign-in) to use Google Accounts.
* Using accounts which the developer has.

If the developer has accounts which are used in some app/service, the developer can choose the 2nd option. However,
at the same time, the developer needs to implement an OAuth2 server to provide the authorization code grant or implicit
grant. If the developer does not have any accounts, it is easy to integrate the Google Sign-In for the Assistant to
the action.

By the account linking, the data is related to the account authenticated by the Google Sign-In for the Assistant or
developer own accounts. After doing the Account Linking, the authenticated user's profile is set to the
`conv.user.profile` represented by the [`Profile`](https://actions-on-google.github.io/actions-on-google-nodejs/classes/conversation.profile.html) class. That is, developers can migrate the code to
like the following:

```js
app.intent("...", conv => {
    // Can retrieve the authenticated user's ID after doing Account Linking
    const userId = conv.user.profile.payload.sub; // Instead of `conv.user.id`
    // Store the data
    const data = ...;
    storeDataToDatabase(userId, data);
});
```

Developers can use any databases including Firebase RealtimeDB, Datastore on GCP, MySQL DB on GCE and so on. The stored
data will be able to refer from other apps/services.

# Using the Webhook-generated IDs

If the developer does not want to use the Account Linking and the size of data is more than 10k bytes, the developer
can use a combination of the User Storage API and the Webhook-generated IDs. This means that only the generated ID
is stored into the User Storage. The data related by the generated ID are stored into the external database.

For example, developers can write the code to migrate the action to use the Webhook-generated IDs as like the
following:

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

# Conclusion

Probably, there are some issues against the Anonymous User Identity (ex. GDPR and etc.). Developers who are using
the Anonymous User Identity should migrate the apps to one of options above as soon as possible. Especially,
if using Account Linking, the user experience will be changed. However, the users will be able to get more benefits
by the Google Account or other account, I guess.

Please see [the migration guide](https://developers.google.com/actions/identity/user-info) for more information.
