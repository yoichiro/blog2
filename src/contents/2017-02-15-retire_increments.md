---
layout: post
title: Increments社を退職します
categories:
- Other
---
昨年の5月からIncrements社でQiitaの開発に従事していましたが、今月末をもってIncrements社を退職します。在籍期間は10ヶ月。今日が最終出社日です。

[![retire_increments_5.png]({{ "/images/2017/02/retire_increments_5.png" | prepend: site.baseurl}})](https://increments.co.jp/)

Incrementsでは、Qiitaの機能追加開発を主に担当していました。Qiita Blogには、僕がリリースしてきた機能が掲載されています。

* [yoichiroがIncrementsにJOINしました - Qiita Blog](http://blog.qiita.com/post/144075765264/welcome-yoichiro)
* [Email Markupに対応しました - Qiita Blog](http://blog.qiita.com/post/146384133544/email-markup)
* [外部リンクへの属性が変わります - Qiita Blog](http://blog.qiita.com/post/149486954709/externallinkattrib) (僕が実作業者)
* [Qiita Organizationで組織の紹介などを書ける「About」の提供を開始しました - Qiita Blog](http://blog.qiita.com/post/150997725844/organizationabout)
* [details,summary要素に対応し、投稿内で指定箇所を折りたためるようになりました - Qiita Blog](http://blog.qiita.com/post/151329031329/detailssummarysupport)
* [「Qiita Advent Calendar 2016」の募集が始まりました - Qiita Blog](http://blog.qiita.com/post/152366526084/adventcalendar2016) (動作テストや改修、いいね対応を担当)
* [Organization一覧ページでAbout記載の絞り込みができるようになりました - Qiita Blog](http://blog.qiita.com/post/152849925164/organizationabout-refine)
* [投稿記事やコメントに「いいね」できるようになりました（Contributionの算定基準も変わります）... - Qiita Blog](http://blog.qiita.com/post/153200849029/qiita-like-button) (開発と動作確認を担当)
* [2016年度版Qiita Advent Calendarランキングをお届けいたします - Qiita Blog](http://blog.qiita.com/post/153456931899/adventcalendar2016rule)
* [特定の技術に強い組織を知ることができます - Qiita Blog](http://blog.qiita.com/post/153888164959/organization-ranking-on-tag-page)
* [自分のストックをより便利に一覧できる機能をリリースしました - Qiita Blog](http://blog.qiita.com/post/156073949044/new-stock-page) (MyPageの変更開発を担当)
* [Organization一覧ページをリニューアルしました - Qiita Blog](http://blog.qiita.com/post/156741170829/organizationlist)

上記以外にも、「知り合いかも」っていう表示や、重たい処理（MarkdownからHTMLへ多くの記事を再変換するなど）をゆっくり適用していく仕組み、名寄せのための類似度計算、[ステージング環境のDBを自動で再構築するための仕組み](http://blog.qiita.com/post/150486142999/increments-hack-week-3) など、振り返ると結構いろいろやってましたね。

[![retire_increments_6.png]({{ "/images/2017/02/retire_increments_6.png" | prepend: site.baseurl}})](http://qiita.com/yoichiro6642)

昔から趣味プログラミングはかなりハードにやってましたが、本職でのプログラミングとなると数年離れていたため、Incrementsに入社してからの10ヶ月間は、久々に緊張感持ってプログラミングができました。在籍中は、趣味プログラミングをほとんどやらなかったですね。ProductionレベルでのRuby on Railsアプリケーションの開発、効率的なMySQLへの問い合わせ、それらに関するコードレビュー、どれも「かなり昔に僕が置き忘れてきたもの」だったりするので、それらを取り戻すことができたかな、と思ってます。gitコマンドの使い方一つ取っても、入社前と現在ではかなり良く変わりました。MySQLのEXPLAINも読めるようになったと思ってます。そういった点では、鍛え直していただいて、Incrementsのエンジニア陣には感謝しております。

[![retire_increments_4.png]({{ "/images/2017/02/retire_increments_4.png" | prepend: site.baseurl}})](https://www.youtube.com/watch?v=yxV9X0AYhzA)

そして、Qiita Product Managerの[takoratta](https://twitter.com/takoratta)さんとの仕事は、いつも刺激的でした。刺激的というか、常に緊張してました。「んなことないでしょ」って言われそうですけど、そうだったんです。いわゆるProduct Managerという職種の方と接する機会も今までなく（企画担当者って人はいっぱい関わってきたけど）、そばでProduct Manager職を体感できたのは、すごく良かったかな、と。そして、一緒にQiitaの機能開発ができたことを、本当に嬉しく思っています。「"あの及川さん"と一緒に仕事をしていた」というのは、今でも夢なんじゃないかと思ってしまうほど、なんか、はい、言葉では言い表せないです。

[![retire_increments_3.jpg]({{ "/images/2017/02/retire_increments_3.jpg" | prepend: site.baseurl}})](https://www.facebook.com/photo.php?fbid=794271310674069&set=a.114101568691050.13267.100002736228619&type=3&theater)

Incrementsは、QiitaやQiita:Teamというサービスを運営していますが、エンジニアとしてみると、Ruby on RailsをAWSで運用している会社、ということになります。何か特別なことをしているわけではなく、普通に作ってる、そんな印象です。ただし、Ruby on Railsの作法に則ろう、という強い姿勢が強すぎるほど強かったかな、と。僕はJavaで育ってきた背景があって、Javaの進化とウェブへの対応をずっと見てきました。Ruby on Railsでのレイヤの考え方、それから来る責務や実装箇所の判断、ユニットテストの考え方、どれも違いがありました。品質の求め方は言語だとかフレームワークだとかそういったものとは独立だ、という理解が僕にはありましたが、一概にそうではないのかなぁ、と発見もあり、悩みも増えた、そんな10ヶ月でした。

![retire_increments_7.jpg]({{ "/images/2017/02/retire_increments_7.jpg" | prepend: site.baseurl}})

昨年5月に入ってから、社員数も増えてきました。今後Incrementsが、そしてQiita、Qiita:Teamがどう成長していくか、今後は外から応援していこうと思います。Incrementsの皆さん、お世話になりました。僕もIncrementsで得た知識や経験を今後活かしていきたいと思っていますので、皆さんも少しでも僕が残したものを活かしていっていただけると嬉しいです。

さて、2月後半はゆっくり休んで、3月から新しい環境で再始動する予定です。新しい分野にチャレンジします。僕と何らかでつながっている方々、3月になったらプロフィールの所属欄を更新しますので、それで次がどこなのか、気がつくかと思います。

では、Qiita++、Qiita:Team++、そして、Increments++。

end

そういえば、Rubyに++がないことに、今気がついた。+=1。
