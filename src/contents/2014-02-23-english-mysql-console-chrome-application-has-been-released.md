---
layout: post
status: publish
published: true
title: MySQL Console Chrome application has been released!
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2609
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2609
date: '2014-02-23 21:49:03 +0900'
date_gmt: '2014-02-23 12:49:03 +0900'
categories:
- Chrome Apps
---

Today, I have released a new Chrome app named "MySQL Console". If you're using Chrome Web browser, you can install this application from the following URL:

![mysql_440](http://www.eisbahn.jp/yoichiro/images/2014/02/mysql_440.png)

[MySQL Console - Chrome WebStore](https://chrome.google.com/webstore/detail/mysql-console/cakepohgdbjbenkcpkkacmohgfjhnjoh)

Basically, you'll use "mysql" command to execute queries and retrieve the result from/to MySQL server in development. In order to use the mysql command, you need to install MySQL server to your PC, or need to logged in to other server which has the mysql command with SSH or etc.

This MySQL Console application can communicate to all MySQL Server directly, because this application has an ability to talk MySQL Client/Server protocol. You are able to send your query and receive the result from/to MySQL server only using this application without installing other any applications and logging in to other any servers.

![screenshot](http://www.eisbahn.jp/yoichiro/images/2014/02/screenshot.png)

Especially, if you use Windows or Chrome OS, this application will be a powerful tool for you.

Currently, this application has the following functions:

* Connect and logged in to MySQL servers which can access from your PC (to localhost, in same network, and etc.).

* Select DB, and execute queries.

* Full-screen supports.

* Multi-window supports. Each window has one connection.

* Query history supports. Currently, this application keeps 100 executed queries.

Also, the current version has the following limitations:

* Only plain communication and plain password. SSL connection not supported.

* Stored procedures not supported.

* Local files not supported.

* This application talks with protocol 10. Therefore, MySQL Server 4.1 or higher supported.

* UTF-8 is used in this application.

Feel free to let me know your feedbacks.
