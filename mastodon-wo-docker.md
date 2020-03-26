---
title: Mastodon DockerありからDockerなし移行（Dockerはがし）の簡単な手順
tags: mastodon Rails
author: skoji
slide: false
---

2018年05月10日

この手順は、標準的な構成でDockerを使っている環境から、Ubuntu上で[公式ガイド](https://github.com/tootsuite/documentation/blob/d9ea83d90826d09ea1061369782489f6fd36c1a3/Running-Mastodon/Production-guide.md)にしたがって構築するMastodonへの引越しを想定している。いずれもオブジェクトストレージは使わない想定。（オブジェクトストレージを使っているのであれば、メディアファイルのコピーが不要になるので少しだけ楽になる）

## DBのバックアップ・レストア

DBのファイルをそのまま使うと引越し先でPostgreSQLのバージョンを気にしなくてはならないので、`pg_dumpall`を使う。

```
# db以外のコンテナを停止する
docker-compose stop web streaming sidekiq redis
# dbのバックアップをbackup.sqlとして取る
docker exec mastodon_db_1 pg_dumpall -U postgres > ./backup.sql
```

引っ越し先では、psqlで使ってレストアする。

```
psql -U postgres -f backup.sql
```

## メディアファイル

別サーバへの引っ越しであれば、`scp`などで`public/system`以下をコピーする。コピー先ではownerをユーザ`mastodon`にしておく。

```
scp -r ./public/system new_server:/user/mastodon/live/public/system
```

## Redis

`redis/dump.rdb`を、`/var/lib/redis/dump.rdb`としてコピーする。ownerはredisとしてコピーする。

## .env.productionなど

元の.env.productionをコピーして適宜改変しても良い（Docker外した場合には、DBやRedisは設定変更が必要なはず）。が、今のMastodonには、`mastodon:setup`という便利な対話的taskがあるので、これを使っても良い。

```
RAILS_ENV=production bundle exec rake mastodon:setup
```

このtaskを使うとき、DBのリセット・管理者ユーザの作成はskipする。（バックアップから書き戻しているから）

## おまけ

MastodonはUbuntu 16.04 LTSを想定している。[現時点の公式ガイド](https://github.com/tootsuite/documentation/blob/d9ea83d90826d09ea1061369782489f6fd36c1a3/Running-Mastodon/Production-guide.md)から、以下を変更することで、Ubuntu 18.04 LTSでも問題なく動作した。

* libgdbm3ではなくlibgdbm5を入れる
* nodejsのrepository設定にはsetup_6.xではなくsetup_8.xを使う
