---
title: Gitのリビジョン指定書式
tags: AdventCalendar Git
author: skoji
slide: false
---
このエントリは[Git Advent Calendar / Jun.](http://qiita.com/advent-calendar/git)の4日目です。

あちこちで使う、gitのrevisionを指定する書式。しょっちゅう分からなくなるので、man gitrevisionsを読んでみました。

#### SHA1のオブジェクト名。全部じゃなくて、リポジトリ内部で一意なところまででよい。

        $ git show d489256

#### git describeの出力。

        $ git show tagname-12-gd489256

#### symbolic ref name。
 masterとかtag名とか。

        $ git show v0.0.1

symbolic ref nameは、次の順序で探されます。

1. $GIT_DIR/\<name\> : HEADとかFETCH_HEADとか。
2. refs/\<name\>
3. refs/tags/\<name\>
4. refs/heads/\<name\>
5. refs/remotes/\<name\>
6. refs/remotes/\<name\>/HEADS


#### ref nameに続けて、@{yesterday}, @{2012-06-04 23:59:59} などの日時指定。その時点でのrefの値をとる。

        $ git log 'master@{2 weeks ago}..master'

#### ref nameに続けて、@{数字}。数字のぶんだけ前のcommit object。

        $ git show master@{5}

#### ref名を書かずに@{}をかくと、カレントブランチのreflogが対象になる。
masterにいるなら、master@{2}と@{2}は同じ。

#### @{-n}は、n個前にチェックアウトしたブランチ。
(えー！ なにその規則…)

#### @{upstream} または@{u}: 対象refのupstream branchを指定。
branchが複雑だと使いそう。

#### revision parameterの後ろに^をつけると、そのパラメータの最初のコミットオブジェクトを示す(HEAD^とか)。
^nは、n個目の親を示す。^0だと、そのコミットそのものを示す。

#### 後ろに~nをつけると、n番目の先祖になる。つぎのふたつは同じ。

        $ git show rev~3
        $ git show rev^^^

#### 後ろに^{} をつけると、「タグじゃないcommit object」までさかのぼる。

        $ git show v0.1^{} 

#### ref^{/なんかのテキスト} または ref:/なんらかのテキストは、「なんかのテキスト」部分で示される正規表現にマッチする、refからたどれる最新コミットを指す。

        $ git show HEAD^{/後でなおす} 
        $ git show HEAD:/後でなおす

:/! は予約されてるので注意。！ではじまるパターンを使いたい時は:/!!とする。

#### ref:のうしろにpathをつけると、その時点でのpathで示されるblobかtreeそのもの。
ソースコードそのものとか。

#### :path, :0:path, :1:path, :2:pathは、indexにあるblob objectを示す。
数字はstage number 。

## 反省とまとめ

ちゃんと読んでみたら知らないことがいろいろ書いてありました。manページ重要。
