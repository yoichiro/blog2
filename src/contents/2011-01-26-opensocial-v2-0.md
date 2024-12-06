---
layout: post
status: publish
published: true
title: OpenSocial v2.0の策定が始まっています
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 857
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=857
date: '2011-01-26 10:10:03 +0900'
date_gmt: '2011-01-26 01:10:03 +0900'
categories:
- OpenSocial
---

OpenSocial Foundationでは、既にOpenSocial v2.0の策定が開始されています。現時点で提案されている仕様は、以下のものがあげられます。

* View Level Features Proposal

* [Social Data Model] Identifying 'Guests'

* Embedded Experiences Proposal

* CMIS Status

* [Social Data Model] Proposed Changes

* Simple Gadget Format

* gadget manifest proposal

* Space extension

簡単に言うと・・・

**View Level Features Proposal**
： 今後スマートフォンなどViewの多様化が見込まれ、それに応じてGadget Specファイル内で各Viewに対して機能を柔軟に指定する必要性が出てきています。この提案では、/ModulePrefs/Require, /ModulePrefs/Optional, /ModulePrefs/Locale要素に対してviews属性を追加し、各Viewに対してそれぞれ最適な機能を宣言できるようにしています。

**[Social Data Model] Identifying 'Guests'**
： 特にエンタープライズ領域では、ガジェットを利用する人が特定されていない状態（つまりゲスト扱い）で利用されるシーンが想定されます。OpenSocial v1.1では、Personリソースに対してゲストかどうかを扱うフィールドは残念ながら見当たりません。この提案では、Personリソースに対して isGuest プロパティを追加し、そのユーザがゲストかどうかを識別できるようにしています。

**Embedded Experiences Proposal**
： ある友人からメールをもらったとき、そのメール内にYoutubeの動画や共有されたファイルなどの位置が含まれていた際に、それをその場でプレビューしたくなったりしませんか？この提案では、SNSにわざわざ訪れなくとも、メールやメッセージなどにガジェットを埋め込めるようにすることで、よりリッチな体験をユーザにもたらします。

**CMIS Status**
： OpenSocialとCMISとの連携を行うための提案です。これはIBMにて昨年より進められています。
[CMIS](http://www.computerworld.jp/topics/dm/121449.html)とは、異なるベンダーのコンテンツ管理システム（CMS）や企業におけるコンテンツ管理（ECM）システムを連携させるための仕様だそうです。

**[Social Data Model] Proposed Changes**
： Social Data Model仕様に細かな改善や追加を行おうとしている提案です。例えば、Person Nameに"nativeName"を追加したり、Personリソースに現在地を示す"position"属性の追加、組織でのその人のroleを示す"roleCategory", "roleType"の追加があげられます。加えて、新しいGroup識別子（@friendsが有名）として、@supervisor, @peers, @staff, @reports, @supportsなどの追加が提案されています。

**Simple Gadget Format**
： 現在のGadget spec fileの仕様は、継続された仕様追加によって、かなり重たい仕様となっています。全てのガジェットが多くの仕様を利用するとは限りません。この提案では、ModulePrefs要素をオプション扱いにし、さらにDataセクションやTemplatesセクションを追加してData PipeliningやOS Templatesを活用しながら単純にガジェットを記述できるようにしています。

**gadget manifest proposal**
： OpenSocialの成熟に伴い、ガジェットは複数のOpenSocial Container向けに複数のバージョンをサポートしなければならない場面が多くなりました。これは、複数のGadget spec fileの記述を開発者に求める結果となります。この提案では、マニフェストファイルを新規に定義し、対応するOpenSocialバージョン、ガジェット自身のバージョン、そのガジェットのバージョンのステータス（development, releasedとか）、そしてGadget spec fileのURLをそれぞれ組で宣言することができるようになります。このために（後方互換性を考慮して）gadget-manifest featureが追加されます。

**Space extension**
： 今までのOpenSocialは、あくまで「人（People）」が中心でした。しかし、今日ではガジェットが「人」を中心に考えられるだけでなく、「場所（Space）」を中心に扱われる場面も多くなってきました。この提案では、ガジェットの実行に関わる重要な概念（人、場所）を"Context"と位置づけ、それによってコンテキストが切り替わる仕組みをOpenSocialの拡張仕様として追加します。いくつかのAPIに {type} 識別子（"person" or "space"）を指定可能にすることで、そのガジェットが、人だけでなく、場所も扱えるようにしてします。

・・・といったものが、OpenSocial and Gadgets spec Google Groupにて議論されています。

OpenSocial v1.1が（WAP Extensionという大きな仕様が追加されましたが）比較的おとなしい範囲に収まっていたのに対して、OpenSocial v2.0では上記のような大きな仕様追加がいくつも行われることになりそうです。Social Webの広がりにつれて、OpenSocialも多様化が求められているということが、上記の提案を見てわかりますね。
