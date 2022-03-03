---
title: 透過Cloudflare和nginx解決取得客戶真實IP問題
description: 
date: 2022-03-02
draft: false
keywords:
- Golang旅程
- gin
- cloudflare
- nginx
categories:
- 學習筆記
tags:
- Golang旅程
- gin
- cloudflare
- nginx
---

## X-Forwarded-For Not Safe?
[這個問題](https://github.com/gin-gonic/gin/issues/2862)已經在github上面討論的蠻熱烈

主要的問題點就是客戶端可以透過偽造request header中的X-Forwarded-For這個欄位來隱藏自己的真實IP。

但是好消息的是，這個漏洞已經在v1.7.7中被修復了，[詳情可以看一下release](https://github.com/gin-gonic/gin/releases)。
<!--more-->
## 那在Gin裡面要怎麼取得客戶端IP呢？
很簡單，直接上原始碼。
```golang
func getClientIP(c *gin.Context){
	clientIP := c.ClientIP()
}
```

## 等等！本文當然沒這麼簡單
那當我們的service和client中間是有使用像cloudflare這些代理服務的話，那你取得的IP肯定會是...
> 網際網路服務供應商: Cloudflare

是的！沒錯，你會發現你拿到的IP去查詢的結果都會是來自於Cloudflare。

## 那這樣要怎麼取得真實的客戶IP呢？
這邊以nginx為例，首先要先到nginx做一下設定
```bash
# 首先先加上proxy_set_header，確保下沒有有心人士偽造IP
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forward-For $remote_addr;

# 接著再加上這串，要從cloudflare中取得真實IP
real_ip_header CF-Connecting-IP;
```
為什麼會是用CF-Connecting-IP，而不是用X-Forwarded-For呢？

[可以參考這篇](https://developers.cloudflare.com/fundamentals/get-started/http-request-headers/)

## 大功告成，準備交差
根據上述設定完後，就可以順利抓取到真實的客戶端IP了。