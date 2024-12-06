---
layout: post
status: publish
published: true
title: goo.gl URL Shortener extension has been supporting Read It Later
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 1230
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=1230
date: '2011-12-26 22:10:41 +0900'
date_gmt: '2011-12-26 13:10:41 +0900'
categories:
- Chrome extension
---

My extension "goo.gl URL Shortener extension" helps you to create tiny URL with Google URL Shortener service and to share it for a social media you're using. From its version 1.16.1, the extension has been supporting Read It Later service. For instance, you can register an URL you keep and want to read later to Read It Later quickly using this extension.

The screenshot is the following:

![](http://www.eisbahn.jp/yoichiro/images/2011/12/ext1.png)

You can install this extension from the Chrome Web Store:

[https://chrome.google.com/webstore/detail/pjnggipjiafeklgjdclhhkeefdebipmm](https://chrome.google.com/webstore/detail/pjnggipjiafeklgjdclhhkeefdebipmm)

Basically, this extension will be used for Google URL Shortener service. But I would like to support Read It Later, because both services treats an URL. I think that users checks the URL before reading it with Read It Later, and bookmarks it after reading it with the goo.gl service. My extension can support both at same time.

To use Read It Later in my extension, first the user needs to enter his/her username and password on the option page of my extension. After opening the option page, you can find the section to configure for Read It Later.

![](http://www.eisbahn.jp/yoichiro/images/2011/12/cfg1.png)

Click the button "Use Read It Later". If the pushing button is first, the permission dialog to confirm to access to the API of Read It Later will be shown. If  the user grants, the form to input his/her account will be shown.

![](http://www.eisbahn.jp/yoichiro/images/2011/12/cfg2.png)

After input the username and password and push the button "Save and test authentication", my extension accesses to the API of Read It Later to confirm the credential, and the authentication result message will be shown. The Read It Later icon can be displayed in the popup window by doing steps below.

![](http://www.eisbahn.jp/yoichiro/images/2011/12/cfg3.png)

By clicking the button of my extension and showing the popup window, the URL of the current tab is set automatically to the text field. When the Read It Later icon clicks, the URL is registered to Read It Later. Of course, the user can register the URL from the context menu.

![](http://www.eisbahn.jp/yoichiro/images/2011/12/cfg4.png)

I believe that many users can enjoy the Read It Later life more by my extension!
