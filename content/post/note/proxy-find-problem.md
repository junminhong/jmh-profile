---
title: APP串接Back-End總是找不到問題？
description: 
date: 2021-07-19
categories:
  - 學習筆記
tags: 
  - mitmproxy
  - wireshark
---
通常使用者對於APP對外向哪一隻APP發出請求，都不太清楚，因為APP不像Web有強大的瀏覽器輔助

那我們要如何得知呢？

透過代理的方式將封包攔截加以分析

但此方法在Android7.0以上不適用，Windows尚未使用不太清楚
<!--more-->

## 實戰之前

請先下載對應軟體

1. [Ｍitmproxy](https://mitmproxy.org/)
2. [Charles](https://www.charlesproxy.com/) (要錢所以暫時不探討)
3. [WireShark](https://www.wireshark.org/)

## Ｍitmproxy教學

- 下載完Ｍitmproxy後
- 打開terminal

```bash
# command line
mitmproxy
# web
mitmweb
```

- 打開欲攔截的裝置，並進入mitm.it找到對應os的cert
- ios很簡單就只要新增憑證即可，並到一般 > 關於本機 > 打開憑證
- android的話比較尷尬的是7.0以上有擋代理，所以要用虛擬機開一隻6.0的並直接新增憑證即可
如果真的只能用7.0以上的話，就只好自行root並把憑證加到system > security > cert裡面即可
- mac跟ios操作步驟一樣
- windows未操作過...不太了解
- 接著將裝置的網路設置代理，輸入代理服務器ip和port
- 完成上述步驟，就會自動解析封包了

## WireShark教學

- 先將瀏覽器的ssl key存出來

```bash
export SSLKEYLOGFILE=/Users/username/Documents/sslkeylog.log 
open -n /Applications/Firefox.app
open -n /Applications/Wireshark.app
```

- 打開WireShark > 點選Preferences > Protocols > 找到tls
- 將剛剛存起來的key.log放進去
- 就可以開始使用WireShark了

為什麼要先加key.log?

因為現在大多網路封包都被tls加密過了，所以你單純攔截封包是看不到任何有意義的資訊的，所以此時要模擬成browse對封包做解密的動作