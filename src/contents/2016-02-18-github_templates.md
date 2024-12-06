---
layout: post
title: GitHubのIssuesおよびPull Requestsにテンプレートを登録することができるようになりました
categories:
- github
---
GitHubを使っていて今まで不便だった点として、「IssueやPull Requestの投稿内容を規定することができなかった」ことがあると思います。最初の投稿内容がどうしても不十分なものになってしまい、詳細を聞き出すことから毎回始めなければならない、といった苦痛をいつも感じていた人は多いはずです。

この苦痛も過去のものとなりそうです。IssueやPull Requestにテンプレートが作れるようになりました。[Issue and Pull Request templates](https://github.com/blog/2111-issue-and-pull-request-templates)という題名で新規機能のアナウンスがありましたので、さっそく日本語に訳してみました。参考になると幸いです。

- - -

# IssuesおよびPull Requestsのテンプレート

重要な詳細が欠けている問題を解決することは困難です。今、プロジェクトの管理者は、プロジェクトのIssuesやPull Requestsのためにテンプレートを追加することができるようになりました。これらは、スレッドを開始する際に貢献者が正しい詳細を追加することを助けてくれます。これは、私たちがコミュニティからのフィードバックに基づいて行っているIssuesやPull Requestsに対する多くの改良の中で最初のものです。

[![](https://cloud.githubusercontent.com/assets/25792/13120859/733479fe-d564-11e5-8a1f-a03f95072f7a.png)](https://cloud.githubusercontent.com/assets/25792/13120859/733479fe-d564-11e5-8a1f-a03f95072f7a.png)

リポジトリにIssueテンプレートを追加するために、ルートディレクトリ内に__ISSUE_TEMPLATE__という名前のファイルを作成してください。ファイルの拡張子は任意ですが、Markdownファイル（.md）がサポートされます。Markdownサポートは、見出し、リンク、@記号による言及、そして[タスクリスト](https://github.com/blog/1375-task-lists-in-gfm-issues-pulls-comments)をあなたのテンプレートに追加することを簡単にします。

Pull Requestテンプレートも同じパターンに従います: あなたのリポジトリのルートディレクトリに、__PULL_REQUEST_TEMPLATE__という名前のファイルを追加してください。

もしかしたら、あなたのプロジェクトのルートディレクトリがファイルの追加によって散らってしまうことが気になることもあると思いますので、私たちは__.github/__フォルダのサポートも追加しました。あなたは、__CONTRIBUTING.md__、__ISSUE_TEMPLATE.md__, そして__PULL_REQUEST_TEMPLATE.md__ファイルを__.github/__内に配置することができますし、それらは全て期待通りに機能するでしょう。

この機能の追加情報は、[このドキュメントをチェックしてください](https://help.github.com/articles/helping-people-contribute-to-your-project/)。
