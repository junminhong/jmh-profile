---
title: Golang旅程 - JWT
description: 
date: 2021-07-26
Lastmod: 2021-07-26
slug: learn-note/golang-jwt
categories:
  - 學習筆記
tags: 
  - Golang旅程
---
## 前言
[JWT官網](https://jwt.io/)，你可以從官網裡面找到你所使用的語言的套件。
<!--more-->
![](/images/golang-jwt/1.png)

你也可以從裡面去解析你的jwt裡面的訊息。
> 從這張圖你可以了解到說千萬不要把敏感性資料放在token裡面，不然人家隨便到個官網就可以知道你在傳什麼資料了

![](/images/golang-jwt/2.png)

## 什麼是JWT
JWT是一個經過Base64加密過後並且透過.串接的字串。
1. header：含token的種類及產生簽章要使用的雜湊演算法。
```bash
{
  "alg": "HS256", 
  "typ": "JWT"
}
```
2. payload：帶有想存放的資訊(用戶非敏感性訊息資料等)。
```bash
{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1516239022
}
```
3. Signature：編譯後的Header、Payload與密鑰透過雜湊演算法所產生。

## JWT套件
1. [舊的go-jwt(沒有維護了)](https://github.com/dgrijalva/jwt-go)
2. [新的go-jwt(推薦使用)](https://github.com/golang-jwt/jwt)

## 開始之前
網路上太多的文章都是講解基本的HS256的雜湊演算的token教學，所以此篇以比較少的RSA為主。

## HS256和RSA256之間的差異
HS256：只需要一組secret key就可以做加解密的動作。

RSA256：他需要公鑰和私鑰，私鑰做加密，公鑰做解密的動作。

### 那你一定會好奇說RSA有什麼好處呢？
由於系統在做解密的時候是透過公鑰做解密，所以當有心人士想要竊取鑰匙的時候，基本上都只會拿到公鑰，那拿到公鑰理所當然就只能做解密的動作，而不能偽裝加密去做請求。

## 產生token
- 請先創建你的key
```bash
# 你會獲得公鑰和私鑰
ssh-generate
```
- 把公鑰和私鑰放進project裡面
```golang
// 取得目前工作目錄
nowWorkDir, _ := os.Getwd()

// 讀取private key.pem(rsa 私鑰)
SECRETKEY, _ := ioutil.ReadFile(nowWorkDir + "/key.pem")

// 創建一個客製化的claims
type MyCustomClaims struct {
	Foo string `json:"foo"`
	jwt.StandardClaims
}

// 取得現在的時間
now := time.Now()

// 透過時間去生成jwtID
jwtID := strconv.FormatInt(now.Unix(), 10)

// 創建claims
claims := MyCustomClaims{
	"bar",
	jwt.StandardClaims{
        // 
		Audience:  "junmin.hong",
        // 過期時間60=一分鐘後過期
		ExpiresAt: now.Add(60 * time.Second).Unix(),
        // 這個token的ID
		Id:        jwtID,
        // token簽署日期
		IssuedAt:  now.Unix(),
        // 簽署者
		Issuer:    "junmin.hong",
        // 在什麼時間點後這個token是無效的
		//NotBefore: now.Add(20 * time.Second).Unix(),
        // 主旨
		Subject: "junmin.hong",
	},
}

// 透過rsa256演算法encode token
token := jwt.NewWithClaims(jwt.SigningMethodRS256, claims)

// 從剛剛的private key.pem解析出私鑰
privateKey, _ := jwt.ParseRSAPrivateKeyFromPEM([]byte(SECRETKEY))

// 拿私鑰簽署token
// 等於說access_token已經經過私鑰透過rsa256簽署了
access_token, _ := token.SignedString(privateKey)

// 這樣就可以獲得一個access_token了
fmt.Println(access_token)
```

## 驗證token
```golang
// 取得目前工作目錄
nowWorkDir, ＿ := os.Getwd()

// 要驗證token就是要去讀取公鑰 pubkey.pem
SECRETKEY, _ := ioutil.ReadFile(nowWorkDir + "/pubkey.pem")

// 把pubkey.pem公鑰解析出來
PUBKEY, _ := jwt.ParseRSAPublicKeyFromPEM([]byte(SECRETKEY))

// 將token拆成三個部分
parts := strings.Split(token, ".")

// 這裡做驗證，驗證token是不是有效的，基本上如果被串改這邊都會是無效的
err = jwt.SigningMethodRS256.Verify(strings.Join(parts[0:2], "."), parts[2], PUBKEY)
if err != nil {
	fmt.Println(err.Error())
}

type MyCustomClaims struct {
	Foo string `json:"foo"`
	jwt.StandardClaims
}

// 解析token claims，取得裡面的資料
token, err := jwt.ParseWithClaims(Token, &MyCustomClaims{}, func(token *jwt.Token) (interface{}, error) {
	return PUBKEY, nil
})
fmt.Println(token)
fmt.Println(token.Claims)

// Valid可以判斷token是否過期了
fmt.Println(token.Claims.Valid().Error())
```
## 結語
網路上的資源都是再講HS256，很少RSA256的講解文章。

所以寫到後面都變成在拆jwt-go這個套件的source code了。

還好有這個經驗讓我在看source code又有更進一步的成長。