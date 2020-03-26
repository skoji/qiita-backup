---
title: OpenSSLをより古いOS Xにも対応するようにビルドする
tags: openssl
author: skoji
slide: false
---

2015年12月24日に投稿


makeする前に、Makefileの変更をする。

```
sed -ie "s/^CFLAG=/CFLAG= -mmacosx-version-min=10.6 /" Makefile
```

以上。
