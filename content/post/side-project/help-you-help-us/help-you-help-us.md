---
title: 防疫實名制 - 幫助你也幫助我們
description: 
date: 2021-07-19
categories:
  - Side Project
tags: 
  - 防疫
  - 實名制
---
## What is this？
由於今年Covid19在台灣本土爆發，導致許多店家開始使用實名制的方式進行相關疫調工作。

但發現各地都產生許多實名制的亂象，為了改變並且簡化實名制，民眾不需要再輸入過多的個資。

於是我著手利用空閑時間進行開發這套經過簡訊實名制的系統。
<!--more-->

## 好處
- QrCode輕鬆一掃即可登入。
- 經過簡訊認證，防堵亂填亂象，避免疫調找尋不到人的問題。
- 簡化輸入資料，降低民眾個資洩漏問題。
- 資料不經過商家之手，只在政府疫調期間才會提供。
- 可以快速根據民眾查詢近期活動範圍。
- 資料保存期限為30天。

## 使用技術
- 前端：Vue3 + Bootstrap
- 資料庫：FireStore
- Hosting：Firebase
- 簡訊認證：Firebase auth

## Demo Preview
### 客戶端 - 填寫表單
![](/images/help-you-help-us/1.png)
### 客戶端 - 簡訊認證畫面
![](/images/help-you-help-us/2.png)
### 客戶端 - 實名完成畫面
![](/images/help-you-help-us/3.png)
### 商店端 - 填寫表單
![](/images/help-you-help-us/4.png)
### 商店端 - 填寫完成並生成Qrcode
![](/images/help-you-help-us/5.png)

## 回顧開發中的經驗所談
1. 簡訊費用是一筆非常龐大的開銷。
2. 透過Qrcode的方式可以大幅降低填寫難度。

## 參考連結
- [Github Source Code](https://github.com/junminhong/help-you-help-us)