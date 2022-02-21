---
title: 如何高效管理ruby環境
description: 
date: 2021-07-28
categories:
  - 學習筆記
tags: 
  - Rails
---

## 前言
相信大家在一開始學習Ruby and ROR的時候都曾經被環境所困擾過。

別擔心！此篇就來告訴你如何高效管理你的ruby環境。

因為本人開發環境都是在Mac，所以本篇也是針對Mac所寫的。
<!--more-->
## 首先先安裝 [rbenv](https://github.com/rbenv/rbenv)

### 為什麼選擇rbenv，而不是rvm?
~~等你用過rvm就知道為什麼了，對於rvm不予置評~~

沒有拉，其實一開始我也是使用rvm，但至從跳巢到rbenv後我覺得我的世界豁然開朗了許多😂。


## 安裝教學
```bash
# 透過brew安裝rbenv
brew install rbenv
# rbenv初始化
rbenv init
# 列出可安裝的ruby穩定版本
rbenv install -l
# 列出全部本地的ruby版本
rbenv install -L
# 安裝某個版本的ruby
rbenv install 2.6.6
# 設定local
rbenv local 2.6.6
# 移除設定local
rbenv local --unset
# 設定global
rbenv global 2.6.6
# 移除設定global
rbenv global --unset
# 設定shell
rbenv shell 2.6.6
# 移除設定shell
rbenv shell --unset
# 顯示rbenv版本
rbenv versions
# 安裝新的ruby時請下這個指令
rbenv rehash

# 設定你的環境變數
export PATH="$HOME/.rbenv/bin:$PATH"
eval "$(rbenv init -)"
export RUBY_BUILD_MIRROR_URL=https://cache.ruby-china.com
```

## Install rails
透過rbenv管理你的ruby後，你會發現同時rails的環境也變得更好掌握了！
```bash
# 透過gem去下載最新的rails
gem install rails
```