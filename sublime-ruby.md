---
title: Sublime Text 2で、rubyのインデントだけ変更する
tags: SublimeText2
author: skoji
slide: false
---

2012年09月25日

### 1. rubyのファイルを開く
### 2. Preferences -> Settings - More -> Syntax Specific User を選ぶ
### 3. 開いたRuby.sublime-settingsに次のようにかく

    {
      "tab_size": 2,
      "translate_tabs_to_spaces": true 
    }

Syntax modeがナントカの時専用の設定はこのように書けます。
