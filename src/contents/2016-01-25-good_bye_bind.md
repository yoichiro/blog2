---
layout: post
title: さようならbind、こんにちはArrow Functions
categories:
- JavaScript
---
僕は基本的にChromeアプリやChrome拡張機能を作っていることが多いので、Chromeがサポートしている範囲内でコードを書くことが多い。Chromeは他のブラウザに比べて最新技術の取り込みが迅速に行われている印象が強い。ECMAScript 2015についても、たぶん他のブラウザよりもサポート範囲が広いはず。

というわけで、ECMAScript 2015(ES6とも言う)でコードを書くことが多くなった。特に気に入っているのは、Arrow Functionsとclass。どちらも非常に強力で、ES5までの「なんでこんなコード書かなあかんねん」と思ってたことがいくつかなくなって、コーディングがまた楽しくなった。

# Arrow Functions

単純に「関数作りたいだけで"function"って8文字も打つの面倒だよね」ってJavaScriptをDISってた人はかなり多かったはず。これが、アルファベットを1文字も書かなくても関数を作れるようになったのが、Arrow Functions。

ES5では、

{% highlight js %}
function() {
    ...
}
{% endhighlight %}

だったのが、ECMAScript 2015だと、

{% highlight js %}
() => {
    ...
}
{% endhighlight %}

だけで済む。なんか、やっと他の言語に並んだ感じ。非常にこれが気持ちいい。引数があるときは、

{% highlight js %}
(name, age) => {
    ...
}
{% endhighlight %}

と括弧の中に引数を並べれば良い。わかりやすい。何かの関数の引数値として関数を渡したい場合も、

{% highlight js %}
foo((response) => {
    ...
});
{% endhighlight %}

と書ける。良い感じ。

# class

僕はJava歴が長く、まあRubyも少しやってた都合上、classキーワードは好きだ。今までクラス的なものを作りたいときは、

{% highlight js %}
var Person = function(name, age) {
    this.name_ = name;
    this.age_ = age;
}

Person.prototype.say = function() {
    return "Hello, " + this.name_ + "(" + this.age_ + ")-san!";
}
{% endhighlight %}

としていたのが、ECMAScript 2015だと、

{% highlight js %}
class Person {
    constructor(name, age) {
        this.name_ = name;
        this.age_ = age;
    }
    say() {
        return "Hello, " + this.name_ + "(" + this.age_ + ")-san!";
    }
}
{% endhighlight %}

と書ける。今までのprototypeを意識した書き方は何だったのか。。。

# もうbindは必要ない！

と言い切っちゃうのは言い過ぎだけど、極力bind()は書かなくても良くなる。bind()って何かというと、簡単に言ってしまえば「thisを何にするか？を指定するもの」である。例えば、Buttonが押された回数をカウントアップしていきたい、とすると、今までだったら、

{% highlight js %}
var Page = function() {
    this.count_ = 0;
    this.assignEventHandlers();
}

Page.prototype.assignEventHandlers = function() {
    var button = document.querySelector("#button1");
    button.addEventListener("click", function(evt) {
        this.count_ += 1;
    }.bind(this));
};
{% endhighlight %}

というように、ボタンのイベントハンドラとなる関数に対してbind(this)という呼び出しを行った結果をaddEventListener()関数に渡さなければならなかった。self=this的な感じでやる方法もあるけど、僕の場合はとにかく至る所でbind()しまくってた。もし上記の例でbind(this)を忘れると、実際に関数が呼び出された時のthisは、DOMのbutton要素のオブジェクトになるので、何度ボタンをクリックしてもcount_値はカウントアップされない、という不具合になる。他にもbind(this)を忘れることで、「そんな関数知らんし」ってエラーに出くわすことが非常に多かった。とにかく直感的ではなかった。

これが、Arrow Functionsを使うことで、一気に解決する。

{% highlight js %}
class Page {
    constructor() {
        this.count_ = 0;
        this.assignEventHandlers();
    }
    assignEventHandlers() {
        let button = document.querySelector("#button1");
        button.addEventListener("click", (evt) => {
            this.count_ += 1;
        });
    }
}
{% endhighlight %}

別にclassにしなくてもいいんだけど、bind(this)しなくても、Arrow Functionsの場合はそれが定義されている場所でのthisになるので、上記ではbind()関数が出てこなくて済む。bind()関数を使うモチベーションのほとんどが上記のような場面だと思うので、極端な言い方をすれば、bind()関数よさようなら、と言っても良いのかな、と。上記は非常に直感的だと思うし、classの利用と合わせていけば、本当に気持ちよくコードを書いていける。

# まとめ

というわけで、もうclassとArrow FunctionsなくしてJavaScriptコードを書けない体になりそう。もちろん、Chrome以外のブラウザのECMAScript 2015サポート状況はまだまだこれからな感じなこともあると思うので、そういう場合は[Babel](https://babeljs.io/)などのコンパイラを使ってES5相当のコードを吐き出せば良いと思う。
