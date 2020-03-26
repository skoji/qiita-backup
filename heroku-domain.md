---
title: Herokuアプリに独自ドメインを割り当てる(VALUE-DOMAIN編)
tags: Heroku Ruby lokka
author: skoji
slide: false
---
Herokuアプリに独自ドメインを割り当てる(VALUE-DOMAIN編)

Lokka CMSをHerokuにデプロイして、独自ドメインを割り当てる方法。とても簡単。

HerokuのCustom DomainsアドオンはdisableとなっていてZerigo DNSに取って代わられています。

このやり方に沿ってやります。
https://devcenter.heroku.com/articles/zerigo_dns

## VAlUE-DOMAIN側設定

対象ドメインの、ネームサーバ変更します。NSってアイコンをクリック。ネームサーバ1〜5に次の設定をします。

    a.ns.zerigo.net
    b.ns.zerigo.net
    c.ns.zerigo.net
    d.ns.zerigo.net
    e.ns.zerigo.net

## Heroku側設定

対象アプリケーションのローカルディレクトリで次の操作をします。

```
$ heroku addons:add zerigo_dns:basic
Installing zerigo_dns:basic into myapp...done.
$ heroku domains:add mydomain.com
```

以上
