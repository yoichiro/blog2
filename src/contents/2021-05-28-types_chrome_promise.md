---
layout: post
title: Chrome拡張機能向けのDefinitelyTypedにPromise対応関数定義を追加しました
categories:
- Chrome extension
---
  
Chromeウェブブラウザが登場した当時から、開発者は拡張機能（Extensions）を作ってウェブブラウザの機能を追加することができました。歴史が古いChrome拡張機能ですが、現在はManifest Version 3（MV3）が最新の環境です。MV3は、セキュリティ、プライバシー、そしてパフォーマンスといった面で従来よりも進化したバージョンとして登場しました。従来からある拡張機能はこのMV3へのマイグレーションが求められ、そしてこれから新規に開発する拡張機能についても、もちろんMV3をターゲットに開発することになります。

MV3は内部の実行環境が変更になっただけでなく、各種APIにも変更が加えられています。その中の一つに「Promise 対応」があります。

# Promise対応後に書けるようになったコード


従来のAPIでは、何らかの処理の結果を得るために、コールバック関数を渡す形式が採用されていました。例えば、新規にタブを開くAPIを呼び出す際には、以下のようなコードを書いてきました。


```
chrome.runtime.onInstalled.addListener(() => {
  const url = chrome.runtime.getURL("hello.html");
  chrome.tabs.create({ url }, (tab) => {
    console.log(`Created tab ${tab.id}`);
  });
});
```

`chrome.tab.create` 関数の第2引数にコールバック関数を渡していることがわかると思います。タブが開いた後に、そのタブの情報を持つオブジェクトが、コールバック関数の引数として渡されます。

MV3からは、いくつかの関数が戻り値として `Promise` を返すようになりました。それに伴い、コールバック関数を渡す必要はなくなります（もちろんコールバック関数を渡す従来の方法も生きています）。つまり、以下のようにコードを書くことが可能です。


```
chrome.runtime.onInstalled.addListener(() => {
  const url = chrome.runtime.getURL("hello.html");
  chrome.tabs.create({ url }).then((tab) => {
    console.log(`Created tab ${tab.id}`);
  });
});
```

さらに、async/await 構文を適用して、以下のようにも書くことができるようになりました。


```
chrome.runtime.onInstalled.addListener(async () => {
  const url = chrome.runtime.getURL("hello.html");
  const tab = await chrome.tabs.create({ url });
  console.log(`Created tab ${tab.id}`);
});
```

コールバック地獄から抜け出し、then-catch 連鎖からも抜け出し、コードが非常にすっきりしました。良いですね。

# Typescriptでの型定義の追加


拡張機能をTypescriptでコーディングしていた方は、もしかしたらMV3になってから「もどかしい」感情を抱いてきたかもしれません。

その原因は、DefiledTypes の `@types/chrome` には、MV3で新設されたAPIは追加されていても、Promise対応された新しい関数定義がなかったため、IDEで Promise 版の関数を使ってコードを書いたときにおそらく「そんな型定義ないよ」と警告を受けていたから、だと想像します。もしかしたら、警告を無視するように `@ts-ignore` を追記したり、強引にキャストせざるを得なかったかと思います。

そんな状況から抜け出すべく、Promise対応された関数群の定義を追加しました。

[Add definitions of functions which support Promise #53120](https://github.com/DefinitelyTyped/DefinitelyTyped/pull/53120)

無事にレビューを通過し、2021年5月28日にリリースされました。 `@types/chrome` バージョン 0.0.143 以降であれば、Promise 対応された関数の型定義が提供されています。

例えば、IntelliJ IDEAにて、 `chrome.tabs.create` 関数のリファレンスとして、Promise 版とコールバック関数版の2つが提示されます。

以下は、Promise 版です。


![]({{ "/images/2021/05/types-chrome-promise-1.webp" | prepend: site.baseurl }})


そして、以下がコールバック版のヘルプ表示です。


![]({{ "/images/2021/05/types-chrome-promise-2.webp" | prepend: site.baseurl }})


試しに、 `@types/chrome` の 0.0.142 を適用してみると、以下のように警告が表示されます。


![]({{ "/images/2021/05/types-chrome-promise-3.webp" | prepend: site.baseurl }})


# まとめ


このエントリでは、TypescriptでChrome拡張機能を開発している際に、今日からはPromise版の関数も警告表示なしでIDE上でコーディングできるようになったことをお伝えいたしました。

実は全てのAPIがPromise対応を遂げたわけではなく、まだ一部のAPIのみにとどまっています。今後徐々に増えていくと予想されますので、対応され次第、この型定義についても追加していきたいと考えています。


