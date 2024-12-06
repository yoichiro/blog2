---
layout: post
status: publish
published: true
title: oh-my-zshのテーマでwedisagreeを選んだ時のgitステータスのマークの意味
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2091
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2091
date: '2013-01-16 14:42:03 +0900'
date_gmt: '2013-01-16 05:42:03 +0900'
categories:
- My PC environment
---

今年はzshを使うぞ！と意気込んで、身近にあるシェル環境を片っ端からzshに強制移行してます。もちろん形から入りたいので、
[oh-my-zshをまずはインストール](http://mollifier.hatenablog.com/entry/20101009/p1)。
[かっこいいテーマはないかなぁ、と物色](https://github.com/robbyrussell/oh-my-zsh/wiki/themes)してると、良さそうなのがあるじゃないですか。"wedisagree"というテーマが気に入りました。

![](https://a248.e.akamai.net/camo.github.com/1cf4b8a61f1f70ed430996528e2df777865ec1bf/687474703a2f2f692e696d6775722e636f6d2f307145326d2e706e67)

このテーマ、プロンプトの右に、現在いるディレクトリのgitの状況がアイコン表示されます。現在のブランチ名とかも出て、かなり素敵。しかし、アイコンの意味がわからない。このままでは「ただキラキラしてるだけ」なので、ちゃんと意味がわかるようにoh-my-zshのコードを見ていきました。

まず、"wedisagree"のテーマファイル(~/.oh-my-zsh/themes/wedisagree.zsh-theme)を見てみると、以下のようになってます。

```
...
RPROMPT='${time} %{$fg[magenta]%}$(git_prompt_info)%{$reset_color%}$(git_prompt_status)%{$reset_color%}'
...
ZSH_THEME_GIT_PROMPT_PREFIX=" ☁  %{$fg[red]%}"
ZSH_THEME_GIT_PROMPT_SUFFIX="%{$reset_color%}"
ZSH_THEME_GIT_PROMPT_DIRTY="%{$fg[yellow]%} ☂" # Ⓓ
ZSH_THEME_GIT_PROMPT_UNTRACKED="%{$fg[cyan]%} ✭" # ⓣ
ZSH_THEME_GIT_PROMPT_CLEAN="%{$fg[green]%} ☀" # Ⓞ
ZSH_THEME_GIT_PROMPT_ADDED="%{$fg[cyan]%} ✚" # ⓐ ⑃
ZSH_THEME_GIT_PROMPT_MODIFIED="%{$fg[yellow]%} ⚡"  # ⓜ ⑁
ZSH_THEME_GIT_PROMPT_DELETED="%{$fg[red]%} ✖" # ⓧ ⑂
ZSH_THEME_GIT_PROMPT_RENAMED="%{$fg[blue]%} ➜" # ⓡ ⑄
ZSH_THEME_GIT_PROMPT_UNMERGED="%{$fg[magenta]%} ♒" # ⓤ ⑊
...
```

oh-my-zshでのgitの状況確認は、~/.oh-my-zsh/lib/git.zshファイルで行われています。実際には2つの処理が行われています。1つ目は、git_prompt_info()で、ブランチ名およびcleanな状況かどうかを判断して表示します。2つ目は、git_prompt_status()で、ここでは具体的にどんな状況になっているのかを細かく判断して表示しています。

git_prompt_info()の先で、以下のようにcleanかどうかが判断されています。

```
...
echo "$ZSH_THEME_GIT_PROMPT_PREFIX${ref#refs/heads/}$(parse_git_dirty)$ZSH_THEME_GIT_PROMPT_SUFFIX"
...
if [[ -n $(git status -s ${SUBMODULE_SYNTAX}  2> /dev/null) ]]; then
  echo "$ZSH_THEME_GIT_PROMPT_DIRTY"
else
  echo "$ZSH_THEME_GIT_PROMPT_CLEAN"
fi
...
```

つまり、cleanかどうかで以下のように表示されます。cleanではない状態(=dirty)かどうかは、"git status -s"の実行結果の文字列の長さが1以上かどうか、つまり現在のブランチにコミットされていない変更が作業ディレクトリ内に含まれているかどうかで決まります。

* cleanな状態: 「
☁ 
master 
☀」(快晴！)

* cleanではない状態: 「
☁ 
master 
☂」(雨降り！)

これらのどっちかの後に、git_prompt_status()にてもっと詳細な状況判断と表示が行われます。抜粋すると、こんな感じ。

```
git_prompt_status() {
  INDEX=$(git status --porcelain -b 2> /dev/null)
  STATUS=""
  if $(echo "$INDEX" | grep '^?? ' &> /dev/null); then
    STATUS="$ZSH_THEME_GIT_PROMPT_UNTRACKED$STATUS"
  fi
  if $(echo "$INDEX" | grep '^A  ' &> /dev/null); then
    STATUS="$ZSH_THEME_GIT_PROMPT_ADDED$STATUS"
  elif $(echo "$INDEX" | grep '^M  ' &> /dev/null); then
    STATUS="$ZSH_THEME_GIT_PROMPT_ADDED$STATUS"
  fi
  if $(echo "$INDEX" | grep '^ M ' &> /dev/null); then
    STATUS="$ZSH_THEME_GIT_PROMPT_MODIFIED$STATUS"
  elif $(echo "$INDEX" | grep '^AM ' &> /dev/null); then
    STATUS="$ZSH_THEME_GIT_PROMPT_MODIFIED$STATUS"
  ...
```

さっきの"git status"コマンドの出力内容の中で、どんな種類の変更があるかを見ていってるということです。具体的には以下になります（"_"は空白）。

* "?? [file]": 「
✭」(gitにトラックされていないファイルがある)

* "A [file]" or "M [file]": 「
✚」(追加されたファイルがある)

* "_M [file]" or "AM [file]" or "_T [file]": 「
⚡」(変更されたファイルがある)

* "R [file]": 「
➜」(ファイル名が変更されたファイルがある)

* "_D [file]" or "D [file]" or "AD [file]": 「
✖」(削除されたファイルがある)

* "UU [file]": 「
♒」(マージされていないファイルがある)

現状の"wedisagree"テーマだと、上記でリストアップしたものが表示対象となっています。実際に各状況でどんな表示をするかは、各テーマで決められることなので、サポートしていない状況はスルーされるわけです。ただし、git.zshファイル内では、stashされたものがあるかどうかをチェックして、その結果を出してもくれます。これは"git rev-parse --verify refs/stash"の実行結果から判断しています。

調べた記念に、全部入りだと以下のように表示されるはずです。なかなかこんな状況にはならないと思うけど。

☁ 
master 
☂ 
✭ 
✚ 
⚡ 
➜ 
✖ 
♒

楽しいzshライフが、これから送れそうです。
