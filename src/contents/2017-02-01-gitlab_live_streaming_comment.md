---
layout: post
title: GitLabの障害復旧風景
categories:
- Other
---
GitLab.comのデータベースに障害が発生して、IssueおよびMerge Requestsが消し飛ぶ、という事態となりました。障害発生の6時間前までのデータベースのスナップショットがあって、それから復旧させるようです。

[GitLab.com Database Incident - 2017/01/31](https://docs.google.com/document/d/1GCK53YDcBWQveod9kfzW-VCxIABGiryG7_z_6jHdVik/pub)

面白いというと不謹慎なのですが、GitLabのエンジニア達が復旧作業をしているのをライブストリーミングしているんですね。

[GitLab Live Stream](https://www.youtube.com/watch?v=nc0hPGerSd4&feature=youtu.be)

![gitlab_1.png]({{ "/images/2017/02/gitlab_1.png" | prepend: site.baseurl }})

そして、世界中から野次馬が来て、そのライブストリーミングにコメントしてます。かなりの速さでコメントが流れてます。

しばらく僕も風景を眺めていたのですが、思わずコメントしてしまいました。

![gitlab_2.png]({{ "/images/2017/02/gitlab_2.png" | prepend: site.baseurl }})

そしたらなんと！返事が来ました。

![gitlab_3.png]({{ "/images/2017/02/gitlab_3.png" | prepend: site.baseurl }})

さらに返事をしておきました。

![gitlab_4.png]({{ "/images/2017/02/gitlab_4.png" | prepend: site.baseurl }})

まさかコメント読んでるとは。。。ちょっとびっくりしました。

本当にメカニカルキーボードの打鍵音が心地よかったので、ついコメントしてしまったのですが、結構深刻な事態だとは思うんですけど、余裕を持って障害対応にあたってる模様です。まあ、GitLab使ってて直撃しちゃった人たちは気が気じゃないと思いますが。。。

無事復旧されることを願うばかりです。ガンバ！

僕もFILCOのメカニカルキーボードを使って、周りの人が何をどんなこと思おうとも、カチャカチャ音させながら打鍵してます。だって、気持ちいいんだもん。
