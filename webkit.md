---
title: WebKitの使い方をコードを読まずに推測する
tags: WebKit Blink
author: skoji
slide: false
---

2014年10月23日

WebKitってそこらじゅうで使われているけれども、中身もよく知らないし、使っている側の構造もわからない。WebKitはソース公開されているけど、それをいきなり組み込めるのだろうか。APIどうなってるの?

いきなりソース読もうにも、どこから手をつけてよいものか。

まずは概要がわかる資料を探してみました。それから、「WebKitを使っている」ライブラリなどをざっと眺めてみました。

## 資料
### ブラウザの仕組み: 最新ウェブブラウザの内部構造

http://www.html5rocks.com/ja/tutorials/internals/howbrowserswork/

HTML5 Rocksの記事、2011年。

WebKitやGeckoの内部構造、動作の概要。WebKitプラットフォームごとの違いには立ち入らず、共通(と思われる)部分の動作がつかめる。

### 猫でも分かる Android WebKit
http://www.slideshare.net/TNaruto/android-web-kit

前半は、Androidに限らないWebKitの構造を書いている。WebKitとWebKit2の違いもよく分かる。24ページ目「WebKitをプラットフォームへ移植したい」が簡潔。そうだったのか!

### 開発者のための WebKit
http://myakura.github.io/n/webkit4devs.html

[WebKit for Developers](http://www.paulirish.com/2013/webkit-for-developers/)の日本語訳。

これも、プラットフォームごとのWebKit(port)がどう同じでどう違うかについて語られる。中程にある一覧表はわかりやすい

## WebKitを使っている、クロスプラットフォームのライブラリ/ランタイムなど

### PhantomJS
http://phantomjs.org
ヘッドレスブラウザ。QtWebKitを使っている。

### wkhtmltopdf
http://wkhtmltopdf.org
HTML to PDFコンバータ。QtWebKitを使っている。

### atom-shell
https://github.com/atom/atom-shell
Atomのコア部分。Chromiumを使っている。

### node-webkit
https://github.com/rogerwang/node-webkit

Node + Chromiumを使ったアプリケーションランタイム

## ここまでコードを一行も読まずに推測できたこと

ここにあげた4つは、QtWebKitまたはChromiumを使っている。

WebCoreを直接使うだけだと、WebKit/WebKit 2のAPI部分や、Platform部分の実装もいちからやらないといけない。だから、PhantomJSやNode-webkitなどは、クロスプラットフォームで既に一通り実装されているQtWebKitやChromiumを使っている

## 結論

なんとなく雰囲気はつかめたので、これからコード読んだりatom-shellやnode-webkitを使ってみたりします。

間違いの指摘など大歓迎です。
