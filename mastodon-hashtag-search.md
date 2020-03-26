---
title: Mastodonインスタンス横断でハッシュタグ検索した結果をまとめたページを作る
tags: Ruby mastodon
author: skoji
slide: false
---

2018年01月02日

Mastodonでハッシュタグをまとめたページをつくりたくて、書きました。

## 概要

* UserLocal提供の[マストドンリアルタイム検索](http://realtime.userlocal.jp)でハッシュタグを検索する
* 検索結果からインスタンスURLだけをあつめる
* 各インスタンスのハッシュタグタイムラインを取得して連結する
* `uniq`して`sort`した結果をHTMLに吐き出す

最初は埋め込みのコードを生成していましたが、iframeがtootの数だけできておもくなる上に、レイアウトの調整がとても難しくなるのでやめました。

検索結果をそのまま使わずに各インスタンスに問い合わせているのは、リアルタイム検索だけでは取りこぼすtootがあるからです。

## 制限

* **検索結果が複数ページになった場合には未対応**です。

## 実行例

[「2017年思い出の本」で生成したページ](https://skoji.jp/movabletype/hidden_pages/books-2017.html)

## コード

[ここ](https://gist.github.com/skoji/2e79d9f2d9a4a4dbaa7d17495234a791)にもおいています。

### スクリプト

#### 使い方

```
ruby mastodon-search-hashtag-and-createpage.rb keyword [title] > output.html
```

#### コード

```mastodon-search-hashtag-and-createpage.rb
require 'open-uri'
require 'json'
require 'nokogiri'
require 'uri'

raise 'should specify keyword' if ARGV.size < 1
keyword = ARGV[0]
title = keyword
title = ARGV[1] if ARGV.size >= 2

uri = 'http://realtime.userlocal.jp/toots/index?q=' + URI.encode('#' + keyword)
charset = nil
html = open uri do |f|
  charset = f.charset
  f.read
end

doc = Nokogiri::HTML.parse(html, nil, charset)

domains = doc.css('.user_name').map do |n|
  URI.split(n.css('a')[0].attribute('href'))[2]
end
toots = []
domains.uniq.each do |d|
  toots += JSON.parse open("https://#{d}/api/v1/timelines/tag/#{URI.encode(keyword)}").read
end
toots.sort_by! do |t|
  DateTime.parse t['created_at']
end.uniq! do |t|
  t['url']
end
puts <<EOF
<!DOCTYPE html>
<html>
  <head>
    <meta charset='utf-8'>
    <meta name='viewport' content='width=device-width, initial-scale=1.0' />
    <title>#{title}</title>
    <link rel='stylesheet' href='mastodon-toots.css' />
  </head>
  <body>
  <h1>#{title}</h1>
EOF

toots.each do |t|
  domain = URI.split(t["url"])[2]
  user_link = "https://#{domain}/@#{t['account']['username']}"
  puts "<section class='toot'>\n"
  puts "<div class='toot-head'>\n"  
  puts "<span class='avatar'><img width='48' height='48' class='avatar' src='#{t["account"]["avatar"]}' /></span>"
  puts "<a class='display-name' href='#{user_link}'>"
  puts "#{t['account']['display_name']}"
  puts "</a>"
  puts "<a class='account-name' href='#{user_link}'>"
  puts "@#{t['account']['username']}@#{domain}"
  puts "</a>"
  puts "</div>\n"
  puts "<div class='toot-body'>\n"
  puts t['content']
  puts "</div>\n"
  puts "<div class='toot-foot'>"
  puts "<a href='#{t['url']}'>"
  puts DateTime.parse(t['created_at']).strftime('%Y年%m月%d日 %H:%M')
  puts "</a></div>\n" 
  puts "</section>\n"
end

puts '</body></html>'
```

## スタイルシート

```mastodon-toots.css
@font-face {
  font-family: "Yu Gothic";
  src: local("Yu Gothic Medium");
  font-weight: 100;
}
@font-face {
  font-family: "Yu Gothic";
  src: local("Yu Gothic Medium");
  font-weight: 200;
}
@font-face {
  font-family: "Yu Gothic";
  src: local("Yu Gothic Medium");
  font-weight: 300;
}
@font-face {
  font-family: "Yu Gothic";
  src: local("Yu Gothic Medium");
  font-weight: 400;
}
@font-face {
  font-family: "Yu Gothic";
  src: local("Yu Gothic Bold");
  font-weight: bold;
}

body {
  line-height: 1.8;
  font-family: "Yu Gothic", YuGothic, sans-serif;
  font-size: 12pt;
  color: #020202;
  background-color: #f0f0f0;
}
a {
  text-decoration: none;
  color: #0000ff;
}
a:visited {
  color: #0000ff;
}
a:hover {
  text-decoration: underline;
}
h1 {
  font-size: 16pt;
  max-width: 400px;
  text-align: center;
  margin: 0.5em auto;
}
.toot {
  background-color: #fefefe;
  max-width: 400px;
  margin: 0.5em auto;
  padding: 0.5em;
  border: 1px solid #888888;
  border-radius: 0.5em;
}

.toot-head {
  display: grid;
  white-space: nowrap;
  grid-template-columns: 60px 1fr;
  grid-template-rows: 24px 24px;
}
.toot-head a {
  color: #000000;
}
.toot-head a:visited {
  color: #000000;
}
.toot-head > .display-name {
  grid-column: 2;
  grid-row: 1;
  overflow: hidden;
  text-overflow: ellipsis;
}
.toot-head > .account-name {
  grid-column: 2;
  grid-row: 2;
  font-size: 10pt;
  color: #808080;
}
.invisible {
  display: none;
}
.ellipsis::after {
  content: "...";
}
.toot-foot {
  font-size: 10pt;
  color: #808080;
}
.toot-foot a {
  color: #808080;
}
.toot-foot a:visited {
  color: #808080;
}
```

