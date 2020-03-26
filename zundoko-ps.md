---
title: ズンドコキヨシ(PostScript)
tags: ズンドコキヨシ PostScript
author: skoji
slide: false
---

2016年03月17日に

[まとめ](http://qiita.com/shunsugai@github/items/971a15461de29563bf90)にPostScript版がなかったので作りました。
 Ghostscript 9.18で動作確認しています。

[出力例PDF](https://www.dropbox.com/s/w6i247radtpsdgb/zundoko-kiyoshi.pdf?dl=0)
 (ps2pdfでPDF変換しています)

```postscript:zundoko-kiyoshi.ps
% zundoko-kiyoshi in postscript

/Palatino-Italic findfont 40 scalefont setfont
/height 50 def
clippath pathbbox /top exch def pop pop pop newpath

10 top height sub moveto

/zd {
rand 2 mod 0 eq
{ (zun) }
{ (doko) }
ifelse
} def

(%Calendar%) currentdevparams
dup /Second get
exch dup /Minute get 60 mul
exch /Hour get 60 60 mul mul
add add srand

/zunct 0 def
/kiyoshi false def
{
    currentpoint exch pop height lt {
        showpage 10 top height sub moveto
    } if
    kiyoshi { 
        (kiyoshi) show
        showpage 
        exit 
    } if
    zd
    dup (zun) eq zunct 4 lt and
    {
        /zunct zunct 1 add def
    } if
    dup (doko) eq 
    {
        zunct 4 eq {
            /kiyoshi true def
        }
        {
            /zunct 0 def
        } ifelse
    } if
    gsave
    show
    grestore
    0 height neg rmoveto
} loop

```
