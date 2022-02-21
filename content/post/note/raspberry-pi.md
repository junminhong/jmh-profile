---
title: 一起來吃樹莓派 - 弱網學習
description: 
date: 2021-07-19
categories:
  - 學習筆記
tags: 
  - raspberry-pi
---
## 如何安裝樹莓派

- 先到[官網](https://www.raspberrypi.org/software/) 下載安裝檔

- 將SD卡插入電腦，並開啟剛下載完的安裝檔，將OS寫入SD卡
<!--more-->
## 要如何在未連接螢幕的情況下連接樹莓派

- 首先在寫入完SD卡後，先不要把SD卡插入樹莓派
- 先在剛剛寫入完的樹莓派os裡創建ssh、wpa_supplicant.conf

```bash
# mac照著以下步驟即可
cd \Volumes\boot

# 創建wpa_supplicant.conf
touch wpa_supplicant.conf

# 然後wpa_supplicant.conf裡面內容新增以下
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=<兩碼國家碼>

network={
 ssid="<Name of your wireless LAN>"
 psk="<Password for your wireless LAN>"
}

# 創建ssh
touch ssh
```

- 新增完之後再將SD卡插入樹莓派開機
- 等待樹莓派開機完成

```bash
# How to find pi ip address?
# ping raspberrypi.local
ssh pi@ip.address
pi default password: raspberry

# How do you do if forgot password
cd /etc/raspap
rm -rf raspap.auth
```

## Iptables

- 參考資料
    1. [https://gigenchang.wordpress.com/2014/04/19/10分鐘學會iptables/](https://gigenchang.wordpress.com/2014/04/19/10%E5%88%86%E9%90%98%E5%AD%B8%E6%9C%83iptables/)
    2. [http://linux.vbird.org/linux_server/0250simple_firewall.php?thisscreen=800x600#fig9.3-4](http://linux.vbird.org/linux_server/0250simple_firewall.php?thisscreen=800x600#fig9.3-4)
    3. [https://newtoypia.blogspot.com/2016/12/door-god.html](https://newtoypia.blogspot.com/2016/12/door-god.html)
    4. [https://blog.zyzysq.com/2020/07/15/树莓派atc搭建强大的弱网模拟环境/](https://blog.zyzysq.com/2020/07/15/%E6%A0%91%E8%8E%93%E6%B4%BEatc%E6%90%AD%E5%BB%BA%E5%BC%BA%E5%A4%A7%E7%9A%84%E5%BC%B1%E7%BD%91%E6%A8%A1%E6%8B%9F%E7%8E%AF%E5%A2%83/)
    5. [https://read-and-thinking.blogspot.com/2009/06/iptablessnatmasquerade.html](https://read-and-thinking.blogspot.com/2009/06/iptablessnatmasquerade.html)

## Network

- POSTROUTING: 在修改來源 IP
- PREROUTING: 在修改目標 IP
- SNAT(Source NAT): 修改封包表頭的『來源』項目
    - 目的: 讓內部機器網路可以連接到外部網路
- DNAT(Destination NAT):  修改封包表頭的『目標』項目
    - 目的: 讓外部網路可以存取內部網路機器

## 固定IP

```bash
sudo nano /etc/dhcpcd.conf

interface eth0  #有線網路
static ip_address=192.168.1.35
static routers=192.168.1.1
static domain_name_servers=192.168.1.1
interface wlan0 #無線網路
static ip_address=192.168.1.35         #想改成的ip地址，可自訂
static routers=192.168.0.1             #無線基地台地址
static domain_name_servers=192.168.0.1 #無線基地台地址
```

## 網路指令

- route

```bash
觀察的參數：
   -n  ：不要使用通訊協定或主機名稱，直接使用 IP 或 port number；
   -ee ：使用更詳細的資訊來顯示
增加 (add) 與刪除 (del) 路由的相關參數：
   -net    ：表示後面接的路由為一個網域；
   -host   ：表示後面接的為連接到單部主機的路由；
   netmask ：與網域有關，可以設定 netmask 決定網域的大小；
   gw      ：gateway 的簡寫，後續接的是 IP 的數值喔，與 dev 不同；
   dev     ：如果只是要指定由那一塊網路卡連線出去，則使用這個設定，後面接 eth0 等

Destination, Genmask：這兩個玩意兒就是分別是 network 與 netmask 啦！所以這兩個咚咚就組合成為一個完整的網域囉！

Gateway：該網域是通過哪個 gateway 連接出去的？如果顯示 0.0.0.0 表示該路由是直接由本機傳送，亦即可以透過區域網路的 MAC 直接傳訊；如果有顯示 IP 的話，表示該路由需要經過路由器 (通訊閘) 的幫忙才能夠傳送出去。

Flags：總共有多個旗標，代表的意義如下：
	U (route is up)：該路由是啟動的；
	H (target is a host)：目標是一部主機 (IP) 而非網域；
	G (use gateway)：需要透過外部的主機 (gateway) 來轉遞封包；
	R (reinstate route for dynamic routing)：使用動態路由時，恢復路由資訊的旗標；
	D (dynamically installed by daemon or redirect)：已經由服務或轉 port 功能設定為動態路由
	M (modified from routing daemon or redirect)：路由已經被修改了；
	! (reject route)：這個路由將不會被接受(用來抵擋不安全的網域！)

Iface：這個路由傳遞封包的介面。
```

- ip route

```bash
選項與參數：
show ：單純的顯示出路由表，也可以使用 list ；
add|del ：增加 (add) 或刪除 (del) 路由的意思。
    IP或網域：可使用 192.168.50.0/24 之類的網域或者是單純的 IP ；
    via     ：從那個 gateway 出去，不一定需要；
    dev     ：由那個裝置連出去，這就需要了！
    mtu     ：可以額外的設定 MTU 的數值喔！

proto：此路由的路由協定，主要有 redirect, kernel, boot, static, ra 等， 其中 kernel 指的是直接由核心判斷自動設定。
scope：路由的範圍，主要是 link ，亦即是與本裝置有關的直接連線。
```

## 實作
- 構想三個網段
    - 第一個網段: 192.168.0.1 → AP網段 = 內網
    - 第二個網段: 192.168.1.1 → Pi eth0
    - 第三個網段: 192.168.100.1 → Pi wlan0 = manager nerwork
- 先將樹莓派eth0 IP設定成固定(192.168.1.1)
- 內網gateway設定成192.168.0.1
- 192.168.0.1 轉發 192.168.1.1
- 192.168.1.1 轉發 192.168.100.1
- DLink設置網際網路IP 192.168.1.2  gateway 192.168.1.1