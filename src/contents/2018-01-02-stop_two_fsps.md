---
layout: post
title: OneDriveとWevDAVのChromeOS向けFile System Provider実装のメンテナンスをやめます
categories:
- Chrome Apps
---
今日、ChromeOS向けに公開していたFile System Provider実装のうち、2つのメンテナンスをもう続けないと決めました。

* OneDrive
* WebDAV

理由は、それらのアップデートをするための十分な時間を確保することができないことです。僕は全部で5つの実装を開発し、メンテナンスしてきました: Dropbox, SMB, SFTP, OneDrive and WebDAV。でも、メンテナンスを続けることは難しいです。加えて、以下のような問題もありました。

* OneDrive向けに発行されるアクセストークンは、約1時間で失効します。もちろん、長い失効時間を持つアクセストークンを得ることはできますが、Authorization Code Grantを使うためには、サーバが必要になります。ほとんどのユーザは、その短い失効時間に言及し、低い評価をしました。
* OneDrive APIの仕様が既に変更されたようです。しかし、僕はOneDriveユーザではないので、その変更に気がつくことは厳しいです。
* WebDAV実装の評価は、最も低いものでした。少なくとも、ユーザ数は最も少なかったです。加えて、開発のための環境を準備し維持することは、厳しいです。

それらのソースコードは公開されています:

* https://github.com/yoichiro/chromeos-filesystem-onedrive
* https://github.com/yoichiro/chromeos-filesystem-webdav

もし、メンテしてもいいよ、とお考えの際は、僕に連絡ください。

Today, I have stopped maintenances of two File System Provider implementations for ChromeOS:

* OneDrive
* WebDAV

The reason is that I could not have enough times to update them. I developed and maintained five implementations: Dropbox, SMB, SFTP, OneDrive and WebDAV. But, it was hard to me to continue maintenances. In addition, I had problems like the following:

* An access token issued for the OneDrive is expired about 1 hour. Of course, we can get an access token which has a long expiration time, but a server is necessary to use an authorization code grant. Most users mentioned about the short expiration and evaluated a low.
* It seems that a specification of the OneDrive API was already changed. But, I'm not a OneDrive user, therefore, I'm hard to notice the change.
* An evaluation of the WebDAV implementation was most low. At least, a count of the users was most small.In addition, I'm hard to prepare and maintain an environment for the development.

Their source code has been published at:

* https://github.com/yoichiro/chromeos-filesystem-onedrive
* https://github.com/yoichiro/chromeos-filesystem-webdav

If you think that you can maintain them, contact me.
