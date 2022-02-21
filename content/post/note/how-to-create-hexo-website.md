---
title: 建置屬於自己的小天地 - hexo篇
description: 
date: 2021-07-16
categories:
    - 學習筆記
tags: 
    - hexo
---

~~目前我的個人網站就是利用Hexo+GitHub來創建的，會撰寫此篇是因為想記錄以及分享所知。~~

雖然已經投抱Hugo派了，但還是將此篇記錄起來，順便讓大家有個

<!--more-->
## 開始之前
由於本身都是使用Mac系統，所以此篇都會以Mac系統為主進行介紹。

## 建置步驟
### 安裝nvm
```bash
brew instll nvm
```

### 安裝node js

這邊可以任意選你喜歡的node版本進行安裝
```bash
nvm install node_version
```

### 安裝[hexo]("https://hexo.io/zh-tw/")
```bash
npm install hexo-cli -g
```

### 建置hexo專案
```bash
cd 你的專案資料夾
hexo init
```

### 發布新文章
```bash
hexo new xxx
```

### 啟動本地伺服器
```bash
hexo server
```

## 待續
以上步驟完成後，就可以開始盡情發表文章了。

目前已經投抱Hugo陣營了。

所以目前暫時就不會繼續更新此篇文章了！

## 常見問題
1. 在新電腦clone網站下來，在新電腦執行hexo server時會出現錯誤時
```bash 
cd 到你的網站資料夾內
刪除 node_modules 這個資料夾
npm install
hexo server
這樣就解決問題了
```

2. 如遇圖片的問題時
![](/images/error01.png)
```bash
利用nvm去降低node版本即可
目前我是使用較穩定的12.14
nvm install 12.14
nvm uninstall 原本版本
然後重新安裝hexo即可
```

## 備註
```bash
brew install nvm
export NVM_DIR="$HOME/.nvm"."/usr/local/opt/nvm/nvm.sh"
nvm install node
nvm use node的版本
```