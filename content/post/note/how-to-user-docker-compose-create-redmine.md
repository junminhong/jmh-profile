---
title: 如何快速使用docker-compose創建Redmine
description: 
date: 2021-07-19
categories:
    - 學習筆記
tags: 
    - docker compose
    - redmine
---

## What is Redmine?
Redmine 是一個基於 Ruby on rails 框架開發的 Issues Tracking System。

由於他是屬於Open Source，所以可以依照公司架構的需求去客製化。
<!--more-->
## 開始之前
1. 先去Docker Hub下載Redmine、Postgres兩個image。
2. 準備一台可以運行Docker的機器，本篇使用AWS的EC2。

## 撰寫Docker-Compose
```yaml
version: "3.8"
services:
  postgres:
    image: postgres:latest
    container_name: postgres
    restart: always
    environment:
      - POSTGRES_NAME= # 你資料庫的名稱
      - POSTGRES_USER= # 資料庫使用者或帳號
      - POSTGRES_PASSWORD= # 資料庫密碼
    volumes:
      - ~/redmine/postgres:/var/lib/postgresql/data
      - ~/redmine/sql:/var/lib/postgresql
    ports:
      - "5432:5432"

  redmine:
    image: redmine:latest
    container_name: redmine
    restart: always
    links:
      - postgres
    environment:
      - POSTGRES_PORT=5432
      - POSTGRES_NAME= # 你資料庫的名稱
      - POSTGRES_USER= # 資料庫使用者或帳號
      - POSTGRES_PASSWORD= # 資料庫密碼
      - REDMINE_PORT=80
    volumes:
      - ~/redmine/redmine/files:/usr/src/redmine/files
      - ~/redmine/redmine/log:/usr/src/redmine/log
      - ~/redmine/redmine/plugins:/usr/src/redmine/plugins
      - ~/redmine/redmine/themes:/usr/src/redmine/public/themes
    depends_on:
      - postgres
    ports:
      - "80:3000"
```
## 開始建置
- 首先請先ssh到你的EC2主機上，並確保docker以及docker-compose已被安裝。

- 接著將yml檔命名成docker-compose.yml並移至你想要的資料夾內。

```bash
# 這邊提供如何將本機資料上傳至EC2
scp 要上傳的本機檔案位置 EC2主機IP:要移至EC2的哪個位置
```
- 搬移檔案完成後。
```bash
docker-compose up -d # 啟動所有服務並在背景運行
docker-compose ps # 查看服務是否都有被成功運行
```
- 基本上到了這步就可以開啟瀏覽器看到redmine已經成功被建置成功了，但是redmine預設database是內建的sqlite，所以我們要來改變一下資料庫。
- 先創建或者進去docker裡面拉出database.yml，這邊介紹如何進去docker裡面拉出database.yml，因為創建好像不太需要說明？
```bash
# 沒關係怕有些讀者可能不太清楚linux上如何透過指令新增檔案
# 這邊稍微介紹幾個可以做到新增檔案的方法
1. vim database.yml # 進入vim後:wq指令退出即會得到一個database.yml
2. touch database.yml # 這個步驟比較簡單只要下這行指令就會得到一個database.yml

# 接著我要來介紹如何進入docker拿取database.yml
1. docker exec -it rdmine bash # 進入我們剛創建好的redmine的container裡面
2. cp /usr/src/redmine/config # 進入config資料夾內
3. ls -al # 你會發現到一個database.yml
4. docker cp database.yml 你要在實體機器上存放的位置
5. exit # 退出container
```
- 回到實體機器(EC2)上你就會發現你已經把database.yml存取出來了。
- 接著我們來編輯database.yml。
```bash
1. vim database.yml # 可以看到database預設是使用sqlite，這時候我們需要修改成剛我們創建好的postgres

production:
  adapter: postgresql
  encoding: utf8
  database: redmine
  host: postgres的ip位置
  username: 資料庫使用者或帳號
  password: 資料庫密碼
  pool: 25
```
- 看到這邊你可能會說啊postgres的IP位置我要怎麼得知，別急我接著來告訴你如何取得container的IP位置。
```bash
docker network ls # 列出網路資訊找到剛剛創建的network
docker network inspect networkname # 查詢剛看到的nwtworkname詳細資訊
# 找到container是postgres的IP將他複製就可以了
```
- 修改完database.yml後，最重要的一步就是將database.yml放回去container裡面拉。
```bash
docker cp database.yml 要放到container的哪個位置
docker-compose restart redmine # 重啟redmine
```
- 走到這步，我們終於完成了redmine串接資料庫了，替自己拍拍手吧。

## 總結
多多善用docker-compose，遠離docker。

docker坑上有你有我。