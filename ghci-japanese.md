---
title: GHCiで日本語表示
tags: Haskell ghci
author: skoji
slide: false
date: 2012年06月18日
---

[Haskell Platform](http://hackage.haskell.org/platform/) を前提にしています。
1. utf8-stringをインストール

        % cabal install utf8-string

これだけなんですが、MacのGHCiでは

        Prelude> putStrLn "日本語"
        日本語

と出ますが、WindowsではGHCi/WinGHCiともに化けてしまいます。

