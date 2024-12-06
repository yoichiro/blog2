---
layout: post
status: publish
published: true
title: iCalendar形式でイベント情報をエクスポートできるようにしました
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 467
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=467
date: '2007-11-17 11:17:00 +0900'
date_gmt: '2007-11-17 02:17:00 +0900'
categories:
- Commusuke
---

[こみゅすけ](http://commusuke.eisbahn.jp)に登録されたイベント情報は、
[RSS Feed](http://commusuke.eisbahn.jp/rss)によってRSSリーダーなどで購読できるようにしている。また、Google Calendarから、イベント情報をGData APIを使ってインポートする機能も提供している。

[id:yoshiori](http://yoshiori.org/blog/)さんからの要望もあり、iCalendar形式でイベント情報をエクスポートできるようにしてみた。

![commusuke_ical.jpg](http://www.eisbahn.jp/yoichiro/images/commusuke_ical.jpg)
これにより、こみゅすけに登録されたイベントを、iCalendar形式に対応したスケジューラ(Google CalendarとかmacのiCalとか)に取り込むことができるようになる。ぜひ活用してみて欲しい。
