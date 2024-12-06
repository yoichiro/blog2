---
layout: post
status: publish
published: true
title: emacs24で搭載されたELPAでのインストール先指定
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 2523
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=2523
date: '2013-09-03 09:25:59 +0900'
date_gmt: '2013-09-03 00:25:59 +0900'
categories:
- My PC environment
---

emacs24以前では、auto-installなどの外部elispを使って、パッケージをインストールしていました。emacs24からは、package.elというパッケージ管理機能（ELPAと呼ばれています）が標準で搭載されるようになり、機能拡張がより簡単になっています。M-x list-packagesとすれば、インストール可能なパッケージの一覧が表示され、その中からインストールしたいパッケージで"i"をタイプし、"x"でインストールを実行できます。ホントに簡単です。

package.elの導入方法は、ググると多くのページがヒットするので、すぐにわかるでしょう。僕は最初以下のコードを書きました。

[text]
;; Add sites for list-packages
(require 'package)
(add-to-list 'package-archives '("melpa" . "http://melpa.milkbox.net/packages/") t)
(add-to-list 'package-archives '("marmalade" . "http://marmalade-repo.org/packages/"))
(package-initialize)
```

これで多くのパッケージを簡単にインストールできるようにはなるのですが、（ほとんどの人は困らないと思うけど）困ったことにインストール先が、"~/.emacs/elisp/elpa"なんです。僕はDropboxで設定ファイルを複数マシンで共有しているので、パッケージのインストール先もDropbox配下のディレクトリにしたいわけです。いろいろ探してみたのですが、上記の導入の話しか書いてありません。根気よく探したところ、見つけました。

ELPAのインストール先を変更するには、package-user-dirを設定します。例えば僕の設定では、

[text]
;; Add sites for list-packages
(require 'package)
(setq package-user-dir "~/Dropbox/config/emacs.d/elisp")
(add-to-list 'package-archives '("melpa" . "http://melpa.milkbox.net/packages/") t)
(add-to-list 'package-archives '("marmalade" . "http://marmalade-repo.org/packages/"))
(package-initialize)
```

となります。これで、package.elでインストールしたパッケージを、複数マシンで共有できるようになりました。快適！
