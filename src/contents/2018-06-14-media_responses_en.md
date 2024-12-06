---
layout: post
title: Providing long audio content using Media responses
categories:
- actions on google
---

On July 14th, [Leon Nicholls](https://plus.google.com/u/0/+LeonNicholls) announced on G+ that Media responses are now
supported for all Actions on Google locales except for ko-KR.

Media Response Update: [https://plus.google.com/u/0/+LeonNicholls/posts/cbATWnXx5cv](https://plus.google.com/u/0/+LeonNicholls/posts/cbATWnXx5cv)

![]({{ "/images/2018/06/media_responses_en_1.webp" | prepend: site.baseurl }})

We have a limitation about audio length with SSML. The length is within 120 seconds. However, we can over the limitation using "Media responses". As the announce, the Media responses feature now can be used in most languages. Great!

I tried to use the feature so that I confirm whether it works normally or not. As the result, it worked normally.

I used the test code below:

```js
app.intent("input.media", conv => {
    if (!conv.surface.capabilities.has("actions.capability.MEDIA_RESPONSE_AUDIO")) {
        conv.ask("The media response audio surface is not supported for the user's device.");
        return;
    }
    conv.ask(new SimpleResponse({
        speech: "This is a reply by the Media responses",
        text: "This is a reply by the Media responses"
    }));
    conv.ask(new MediaObject({
        name: 'Jazz in Paris',
        url: 'http://storage.googleapis.com/automotive-media/Jazz_In_Paris.mp3',
        description: 'A funky Jazz tune',
        icon: new Image({
            url: 'http://storage.googleapis.com/automotive-media/album_art.jpg',
            alt: 'Media icon',
        }),
    }));
    conv.ask(new Suggestions("Test next feature"));
});
```

The new surface `actions.capability.MEDIA_RESPONSE_AUDIO` has been introduced. If we send the media responses, we need
to check whether the user's device supports the surface or not. If supported, we can send the media responses.

To send the media responses, we use the `MediaObject` class. The usage of the class is simple. Basically, we only
pass the audio file's URL. But, in addition, if we want to continue the conversation at the time, we must do the
following:

* Send the `SimpleResponse`.
* Also, send the `Suggestions` to provide the suggestion chips.

After sending the media responses above, we will see the following response from the Google Assistant:

![]({{ "/images/2018/06/media_responses_en_2.webp" | prepend: site.baseurl }})

In the Actions simulator, we can test the media responses. Especially, we can see the response including the audio
player UI. At the time, the specified audio has been starting automatically.

If you want to finish the conversation at sending the media responses, the suggestion chips is unnecessary as like the
following code:

```js
app.intent("input.media", conv => {
    if (!conv.surface.capabilities.has("actions.capability.MEDIA_RESPONSE_AUDIO")) {
        conv.ask("The media response audio surface is not supported for the user's device.");
        return;
    }
    conv.close(new SimpleResponse({
        speech: "This is a reply by the Media responses",
        text: "This is a reply by the Media responses"
    }));
    conv.close(new MediaObject({
        name: 'Jazz in Paris',
        url: 'http://storage.googleapis.com/automotive-media/Jazz_In_Paris.mp3',
        description: 'A funky Jazz tune',
        icon: new Image({
            url: 'http://storage.googleapis.com/automotive-media/album_art.jpg',
            alt: 'Media icon',
        }),
    }));
});
```

We can handle the callback after playback completion. If you use the Dialogflow, you can handle the
`actions_intent_MEDIA_STATUS` event to know the playback completion.

![]({{ "/images/2018/06/media_responses_en_3.webp" | prepend: site.baseurl }})

For more details, see [the official document](https://developers.google.com/actions/assistant/responses#media_responses) for Actionson Google developers.
