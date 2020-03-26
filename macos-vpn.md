---
title: macOS : 特定hostをVPN経由で接続させる
tags: macos VPN
author: skoji
slide: false
---
小ネタです。

VPN接続時、IPアドレス制限をしている特定hostなどへの接続をVPN経由させたいことがあります。しかしVPN側じゃない方からたどり着けるhostだと、VPN経由してくれません。

macOSでは、ネットワーク環境設定でVPN接続の優先順位を上げれば解決するんですが、そうするとVPNを経由しなくてもいい接続までVPN経由になってしまいます。

VPN接続の優先順位を上げず、しかし特定hostはVPNを経由させるには、例えば`/etc/ppp/ip-up`にこんな風に書きます。(macOS Mojaveで確認）

```sh:/etc/ppp/ip-up
#!/bin/sh

if [ "$1" = "ppp0" ]; then
  /sbin/route add 203.0.113.12 -interface ppp0 
fi
```

しかし、FQDNに対して複数IPアドレスが割り当たっていて、しかも時々変化するようなケースだと、これだとうまくありません。

完璧ではないですが、実用上はこれで割といけるかなあ、というのが以下です。

```sh:/etc/ppp/ip-up
#!/bin/sh

if [ "$1" = "ppp0" ]; then
  dig host.to.route.example.jp +short | xargs -I {} /sbin/route add {} -interface ppp0 
fi
```
