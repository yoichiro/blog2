---
layout: post
title: Migration points for upgrading to actions-on-google-nodejs version 2
categories:
- actions on google
---

In developing apps for Google Assistant, the actions-on-google-nodejs SDK is a tool hard to replace. The format
of requests and responses on Actions on Google platform and Dialogflow is a JSON. The JSON format has many properties.
Therefore, it is difficult to treat the JSON with our hands. The actions-on-google-nodejs is a JavaScript SDK
to provide many convenience features, and we can write necessary codes to handle Webhook requests easily.
Most developers will be using this SDK. 

Actually, when upgrading from the initial version (v1) to the version 2 (v2), how to use  the SDK has been changed largely.
If you change the SDK version to v2 for your apps, you need to change the codes so drastically. 

I have three apps which has already been published. Finally, I have just completed migrations to v2. I would like to
file some points for the migration. 

# Change Intent name Routing to Action name Routing

In v1, it is a policy that an invoked function of your fulfillment is decided by "Action name" included in an intent
definition of Dialogflow. That is, like the following code:

```js
const App = require("actions-on-google").DialogflowApp;
const app = new App({request: req, response: res});

const handleWelcome = app => {
  app.ask("Welcome to my app! ...");
};

const actionMap = new Map();
actionMap.set("action_welcome", handleWelcome);
app.handleRequest(actionMap);
```

But, in v2, the "Action name" routing has been discontinued. However, (unfortunately!), a new routing mechanism by "Intent name"
has been introduced. For example, like the following:

```js
const { dialogflow } = require("actions-on-google");
const app = dialogflow({ debug: true });

app.intent("Default Welcome Intent", conv => {
  conv.ask("Welcome to my app! ...");
});
```

[It is difficult to accept the change to me](https://github.com/actions-on-google/actions-on-google-nodejs/issues/108#issuecomment-387321310), but the spec is the current one in v2.
I changed all handler functions to `app.intent("...", conv => { ... })`. 

If you specify one action name for some intents and you implement one handler function in your fulfillment so far,
you can specify an array which has the intent names as 1st argument of `app.intent()` function to avoid to write
a duplicate code.

# Change function calls when replying

In v1, we use the `ask` and `tell` functions to respond replies. Especially, there might be many feedback that
the `tell` is not intuitive.

```js
// Return a reply
app.ask("...");

/// Return a reply and end the conversation
app.tell("...");
```

In v2, the `ask` function stays unchanged. But, we must use a `close` function to end a conversation. Also,
we must call these functions against the conv object passed as the argument of the handler function.

```js
app.intent("...", conv => {
  // Return a reply
  conv.ask("...");

  // Return a reply and end the conversation
  conv.close("...");
});
```

On the `ask` function, another following change occurs.

# Change Static Reprompt to Dynamic Reprompt

Reprompt is a feature to re-ask the user from an app when the user doesn't say anything after a certain period of time.
In v1, there was Static Reprompt. The usage was very simple. Developers could an array which has messages to re-ask
the user as 2nd argument of the function.

```js
const repromptMessages = [
    "Hello?",
    "Are you here?",
    ...
];

app.ask("Please say something.", repromptMessages);
```

Unfortunately, this Static Reprompt feature has been discontinued. That is, the v2 doesn't have the feature.
The usage document has already been deleted in the [document](https://developers.google.com/actions/assistant/reprompts).
Instead, we must use the [Dynamic Reprompt](https://developers.google.com/actions/assistant/reprompts#dynamic_reprompts).
In the Dialogflow, for example, we must create a new intent for Reprompt. 

![]({{ "/images/2018/05/aog_sdk_v2_01.webp" | prepend: site.baseurl }})

Then, we need to add a handler function with `app.intent()` function.

# Return a Promise for the asynchronous process

In v1, if some asynchronous process is executed in the called handler function, we can call `app.ask()` normally whenever,
and the response can be returned. For example, we can write the code below.

```js
const request = require("request");

const welcome = app => {
  request(..., (error, response, body) => {
    ...
    app.ask("...");
  });
};
```

In v2, the code above will not be executed normally. That is, we will get an error message like below.

```
Error: No response has been set. Is this being used in an async call that was not returned as a promise to the intent handler? at DialogflowConversation.response
```

If we want to respond a reply after an asynchronous communication, we need to return a Promise object
as a result value of the handler function. For example, its code will be like the following:

```js
const request = require("request");

app.input("welcome", conv => {
  return new Promise((resolve, reject) => {
    ...
    request(..., (error, response, body) => {
      ...
      if (error) {
        ...
        reject(...);
      } else {
        ...
        resolve(...);
      }
  }).then(result => {
    ...
    conv.ask(...);
  }).catch(error => {
    ...
    conv.close(...);
  });
});
```

In my apps, there are communications for external (ex: API call) and an access to the Datastore. Therefore,
I rewrote all target codes to return a Promise object.

# Set and delete contexts

We have only the `app.setContext()` function to operate contexts in handler functions of fulfillment so far.
For example, the code to operate contexts is like the following in v1:

```js
// Set a context (2nd argument is 1 or more）
app.setContext("CONTEXT_FOOBAR", 1);

// Delete the context (need to specify 0 as the 2nd argument)
app.setContext("CONTEXT_FOOBAR", 0);
```

In v2, APIs to operate contexts has been defined.

[class ContextValues<TContexts>](https://actions-on-google.github.io/actions-on-google-nodejs/classes/dialogflow.contextvalues.html)

That is, the code to operate contexts is like the following from v2:

```js
// Set a context
conv.contexts.set("CONTEXT_FOOBAR", 1);

// Delete the context
conv.contexts.delete("CONTEXT_FOOBAR");
```

I think these APIs bring more clean specifications from v1 to v2.

# Conclusion

The change from v1 to v2, relatively large structural change occurred although I used only the basic function of the
apps I have. For apps that used the v1 helper functions, it is possible that the code will change much more (change
to a form that passes necessary objects to the `conv.ask()` function, not a function call).

If you are still using v1, I think that it is better to move to v2 earlier, with reference to the above.
