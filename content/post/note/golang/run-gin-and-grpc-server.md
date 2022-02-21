---
title: 如何同時啟動Gin和gRPC伺服器
description: 
date: 2021-12-15
categories:
  - 學習筆記
tags: 
  - Golang旅程
---

## 簡述
最近在開發member center的時候發生了一個小問題，就是當我們正常在啟動gin server的同時是沒有辦法在啟動gRPC的伺服器的。

這邊記錄一下我是如何解決這個問題的
<!--more-->

## 實作思維
主要透過Goroutine搭配sync.WaitGroup去實作同時啟動兩個server
```golang
// main.go
func main(){
    // 先初始化一個sync.WaitGroup出來
    wg := &sync.WaitGroup{}
    // 在這個group裡面新增兩個count
    wg.Add(2)
    // 同時啟動兩個服務
    go startgRPC(wg)
    go startGin(wg)
    // 等待
    wg.Wait()
}

func startgRPC(wg *sync.WaitGroup){
    defer wg.done
    //do something
}

func startGin(wg *sync.WaitGroup){
    defer wg.done
    //do something
}
```

## 參考
如果想更了解實作方法直接看我的原始碼吧

- [Github Source Code](https://github.com/junminhong/member-services-center)