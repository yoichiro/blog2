---
layout: post
title: gitで現在のブランチを破棄しつつmasterに戻るシェルスクリプトを書いた話
categories:
- git
---
gitで何かコードを修正してmasterに反映するには、以下のようなことをすると思います。

1. `git checkout -b fix_foobar`
1. コード修正 & `git add ...` & `git commit`
1. `git push origin fix_foobar`
1. GitHub上でPull Request作成 & masterにマージ

ここまでで、「よし、作業終わった」と一段落を迎えるのですが、GitHub上は良くても、手元の状況は残念ながら以下です。

* fix_foobar がカレントブランチのまま。
* master には fix_foobar の差分は入ってない。

これを解消するために、今まで幾度となく以下のことをやってました。

1. `git checkout master`
1. `git pull origin master`
1. `git branch -d fix_foobar`

普通のことかもしれませんが、これ、日に日に苦痛になっていって、「なんで俺はこんなことしてるんだ！？」と自分に対して怒りすら覚えてきました。

だからといってgitを使い始めてから数年ずっと上記をやってたんですけど、昨日やっと以下のスクリプトを書きました。

```bash
#!/bin/sh

set -eu

CURRENT=`git rev-parse --abbrev-ref HEAD`

if [ $CURRENT = "master" ]
then
  echo "The current branch is master."
  exit 1
fi

git checkout master
git pull origin master
git branch -d "${CURRENT}"
```

これで、 `back-to-master` のみ実行すれば、上記3コマンドを一気にやってくれるようになりました。

```bash
$ git:(2018_new_ski) back-to-master 
Switched to branch 'master'
Your branch is up-to-date with 'origin/master'.
remote: Counting objects: 1, done.
remote: Total 1 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (1/1), done.
From github.com:yoichiro/blog
 * branch            master     -> FETCH_HEAD
   f8ca287..4520f97  master     -> origin/master
Updating f8ca287..4520f97
Fast-forward
 _posts/2018-02-11-2018_new_ski.md | 50 ++++++++++++++++++++++++++++++++++++++++++++++++++
 1 file changed, 50 insertions(+)
 create mode 100644 _posts/2018-02-11-2018_new_ski.md
Deleted branch 2018_new_ski (was 9f8b3a9).
```

これでかなりストレス軽減されました。もっと早く作るんだった。。。