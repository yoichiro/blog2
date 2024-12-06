---
layout: post
status: publish
published: true
title: Three implementations of Chrome OS File System Provider have been released
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 3278
wordpress_url: https://www.eisbahn.jp/yoichiro/?p=3278
date: '2015-03-24 20:38:10 +0900'
date_gmt: '2015-03-24 11:38:10 +0900'
categories:
- Chrome Apps
---

Hello. I write down about applications of Chrome OS File System Provider implementation I developed here.

Last year, I developed and published the application called "Chrome MySQL Admin". This application provides you the UI to manage your MySQL server. In this application, I implemented certain feature. It is to connect to the MySQL server with SSH2 protocol. For instance, the Port Forwarding feature was implemented in the application. That is, users can connect to his/her MySQL server via firewall/ssh2 server. This feature was implemented with libssh2, of course, as NaCl module.

![chrome_mysql_admin](https://www.eisbahn.jp/yoichiro/images/2015/03/chrome_mysql_admin.png)

The Chrome MySQL Admin is hosted on Github: 
[https://github.com/yoichiro/chrome_mysql_admin](https://github.com/yoichiro/chrome_mysql_admin)

One day of this January, I knew the existance of the ChromeOS File System Provider API. I translated the API reference to Japanese: 
[here](https://www.eisbahn.jp/yoichiro/2015/01/file_system_provider_api_for_chromeos.html) to study the usage of the API set. It was Beta yet at the time.

So, I also knew that the SSHFS was already existed for Linux/Mac. Specifically, the implementation of the SSHFS uses the SFTP protocol internally.In other words, the SSHFS is a wrapper of SFTP. That is, if I can write the code to communicate between my application and SSH2 server with SFTP, I can develop/provide the file system like as same as the SSHFS. The 
[libssh2](http://libssh2.org) library has some functions to use SFTP protocol. Finally, I guessed that I will be able to develop the File System Provider implementation for SFTP.

I ordered my Chromebook to develop the File System Provider implementation, and got it! (of course, I checked "Get it today")

![chromebook](https://www.eisbahn.jp/yoichiro/images/2015/03/chromebook.jpg)

If I start developing the SFTP implementation soon, I thought that it will fail. Because I don't know the detail behavior of the File System Provider API. As the first development, the FSP API and NaCl module combination is very difficult to me. Therefore, I started developing the first implementation using 
[Dropbox Core API](https://www.dropbox.com/developers/core/docs) for training. In this case, can write with JavaScript code only. In other words, can develop the application on Chrome Dev Editor in Chromebook only.

![chrome_dev_editor](https://www.eisbahn.jp/yoichiro/images/2015/03/chrome_dev_editor.png)

Fortunately, Dropbox is providing enough APIs for implementing the File System Provider. I finally could complete developing the implementation for Dropbox. Also, I could study the behavior of File System Provider API via developing of the implementation.

![dropbox](https://www.eisbahn.jp/yoichiro/images/2015/03/dropbox.png)

Then, I started developing of the SFTP implementation by using the experience of developing the Dropbox implementation. The communication logic must be written as NaCl module, because the libssh2 C library must be applied. To write and build the C++ code is difficult for Chrome OS. Therefore, I wrote and built the C++ code of NaCl module on my macbook air, then wrote the JavaScript code on my Chromebook. Of course, the implementation of the File System API can be tested on the Chrome OS only. Therefore, I needed to write the JavaScript code on my Chromebook.

![web-app-with-nacl](https://www.eisbahn.jp/yoichiro/images/2014/12/web-app-with-nacl.png)

As the first implementation for SFTP, I applied the multi-thread architecture. That is, when this application calls certain API, this application establishes the connection, handshakes, calls API and closes the connection. By this logic, multiple API calls are executed async. However, the handshaking cost was very large than my image.  I modified the logic. That is, I implemented a queue to store the API call request. Each request will be executed sequentially, and the connection handshaked is shared by all requests. As the result, the application behavior became stable.

![queue](https://www.eisbahn.jp/yoichiro/images/2015/03/queue.jpg)

First, the SFTP implementation (
[SFTP File System](https://chrome.google.com/webstore/detail/sftp-file-system/gbheifiifcfekkamhepkeogobihicgmn)) has been released. Second, the Microsoft OneDrive implementation (
[File System for OneDrive](https://chrome.google.com/webstore/detail/file-system-for-onedrive/jbfdfcehgafdbfpniaimfbfomafoadgo)) has been released. I could develop the OneDrive implementation easily, because I already have the code set for Dropbox. Last, the Dropbox implementation (
[File System for Dropbox](https://chrome.google.com/webstore/detail/file-system-for-dropbox/hlffpaajmfllggclnjppbblobdhokjhe)) has been released. And, I'm improving their implementations continuously to increase the performance and fix a bug...

![sftp](https://www.eisbahn.jp/yoichiro/images/2015/03/sftp.png)

In Japan, Chromebook is not spreaded. But, I believe that Chromebook has many possibilities in Japan as well. And, I'm already spending a long time in Chromebook than my MacBook Air. The file system provider api provides many users many usage we don't know yet, I guess. I intend to contribute a lot of things for Chromebook world!

If you are interested in each implementation, please visit each Github repository from the following URLs:

* [yoichiro/chromeos-filesystem-sftp](https://github.com/yoichiro/chromeos-filesystem-sftp)

* [yoichiro/chromeos-filesystem-onedrive](https://github.com/yoichiro/chromeos-filesystem-onedrive)

* [yoichiro/chromeos-filesystem-dropbox>](https://github.com/yoichiro/chromeos-filesystem-dropbox)

Also, if you want to develop a new implementation, you should read the following page first:

* [File System Provider API Reference](https://developer.chrome.com/apps/fileSystemProvider)

Oh, do you have a Chromebook? No? If no, you should buy some Chromebook right now!
