---
title: ズンドコキヨシ パーサー (PEG.js)
tags: ズンドコキヨシ JavaScript PEG.js PEG
author: skoji
slide: false
---

2016年03月17日

PEG(Parsing Expression Grammer)でパーサ書いてみました。処理系はPEG.js。
ズンドコ入れると自動的にキヨシが出力されます。

```:zundoko.pegjs
start = Zundoko

Zundoko = tokens:Zuntoken* { return tokens.join(''); }
Zuntoken = Kiyoshi / Zun / Doko

Kiyoshi = 
  z1:Zun z2:Zun z3:Zun z4:Zun d: Doko Zuntoken*
  { return z1 + z2 + z3 + z4 + d + "キ・ヨ・シ!"; }
Zun = _ "ズン" _ { return text(); }
Doko = _ "ドコ" _ { return text(); }
_ = [ \t\n\r]*
```

## つかいかた

1. [PEG.js Online version](http://pegjs.org/online)を開く
2. 左側の「Write your PEG.js grammar」を消して、上のコードをコピペする
3. 右側の「Test the generated parser with some input」でズンドコする
4. Outputになんか出るよ!

<img width="1217" alt="スクリーンショット 2016-03-17 20.17.23.png" src="https://qiita-image-store.s3.amazonaws.com/0/4650/9ffe25b2-b8b7-41ae-f942-09acfd059bd9.png">

## 問題点

<strike>キ・ヨ・シ! の後も続いてズンドコできてしまう。</strike>
解決済み。キヨシが出た後のズンドコは出力されなくなりました
