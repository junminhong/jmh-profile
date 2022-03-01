---
title: 建置屬於自己的小天地 - hugo篇
description: 
date: 2021-07-16
categories:
    - 學習筆記
tags: 
    - hugo
---

你是不是想問我為什麼好端端的要跑來用Hugo，而不繼續使用hexo呢？

來 ～ 讓我來告訴你為什麼！！

因為呢，身為一個Golang工程師兼粉絲，當然是要整套都是Golang家族的呀，就像apple一樣，一定要來個全家套餐😂

<!--more-->
## 開始之前
廢話不多，馬上進入教學環節 ～ GO！

老話一句，本人呢目前所使用的環境都是Mac，所以都還是以Mac系統為主。

## 建置步驟
### Install [hugo]("https://gohugo.io/")
```
brew install hugo
```
### Check hugo version
```
hugo version
```
### Create a new site
```
hugo new site your_website
```
### Add a theme
```
cd your_website
git init
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
```
#### Add theme to your config.toml
```
echo theme = \"ananke\" >> config.toml
```
### Add Some Content
```
hugo new posts/my-first-post.md
```
### Start your hugo server
```
hugo server -D
```
### Build static page
```
hugo -D
```

## 延伸教學
經過上述步驟完成後，你將會獲得一個基礎的Hugo網站。

可是你一定會說那我要怎麼將Hugo網站Deploy到雲端

[這裡可以看到各個雲端商的部署方式]("https://gohugo.io/hosting-and-deployment/")

### FireBase Hosting Deploy
我這邊簡單介紹一下我常用的方式
#### 請先到FireBase Hosting註冊一個Website
#### 部署流程
```
1. npm install -g firebase-tools
2. firebase login
3. firebase init
4. hugo -D
5. firebase deploy
```
## 結語
> 感謝各位的收看，相信各位在看完此篇文章後，都能夠自行製作屬於自己的網站。