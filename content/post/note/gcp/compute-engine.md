---
title: 如何創建一個Compute Engine
description:
date: 2022-01-11
categories:
- 學習筆記
tags:
- GCP
- Cloud
---

> !注意，GCP免費額度已經被改成三個月了
## 進入控制台後
1. 建立一個新的執行個體

你會看到下面這張圖
- 名稱：你創建的VM要叫什麼名字。
- 區域：選擇VM要在哪個區域。
> 這邊建議如果你的服務流量是來自於台灣，那區域這邊建議選擇台灣的資料中心，會比較快。
- 機器設定：這邊設定你想要的機器配置CPU、Ram等(你選越好的配置當然成本越高)。
- 開機硬碟：設定你的VM主要的開機磁區。
<!--more-->>
設置完之後你可以看到旁邊會有預估費用，如果ok就可以建立機器了
![截圖 2022-01-11 下午4.19.46.png](https://library.jmh-su.com/uploads/images/gallery/2022-01/scaled-1680-/HmmEGgJVC89x8KI2-2022-01-11-4-19-46.png)

## 先佔VM
假設你有成本考量，你不仿可以使用先佔功能，顧名思義，你可以使用GCP上面閒置資源。
> 注意！先佔功能位於24小時後自動關閉，或者GCP急需資源時也會將資源收回。

## 連線VM
看到這裡，我相信你已經建立好一台VM了，那接著我就教你怎麼進去這台VM裡面吧。

這邊我們會使用的SSH。
```bash 
# 產生ssh key
ssh-keygen
cd .ssh
cat id_rsa.pub
# 將你的ssh key複製起來
``` 
1. 搜尋ssh keys。
2. 點選安全殼層金鑰。
3. 將你剛剛複製的ssh key新增進來。
4. ssh 你的VM外部IP。
> 注意！你的vM外部IP會隨著關閉重啟VM會有所變動。
5. 接著你就會進入你剛剛創建的VM裡面。
6. 可以開始你的表演了哦。

## 保留VM外部IP
剛剛在透過外部IP連結VM時，有說到外部IP會隨著開關機而有所變動，所以我這邊教各位如和固定外部IP。
1. 搜尋IP，找到外部IP。
2. 點擊保留靜態IP，
3. 接著你就會獲得一個固定IP

## 後續
持續補充在GCP Compute Engine上面遇到的大小事筆記。