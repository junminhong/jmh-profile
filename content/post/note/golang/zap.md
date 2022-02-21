---
title: 日誌神器-zap
description:
date: 2022-01-22
categories:
- 學習筆記
tags:
- Golang旅程
---
### zap
在系統發生問題時，要如何快速收斂問題，並解決問題呢？

這時候會需要所謂的日誌系統，去詳細的記錄在系統運行中所發生的大小事情。

那這邊我就來稍微介紹一下Uber開源的一個非常好用的日誌神器[zap](https://github.com/uber-go/zap)。
<!--more-->
### 安裝
```bash
# 首先先將zap這個package加進專案
go get -u go.uber.org/zap
```
這邊官方有提到兩種方法
#### 方法一
In contexts where performance is nice, but not critical, use the SugaredLogger. It's 4-10x faster than other structured logging packages and includes both structured and printf-style APIs.

假側如果你對於性能速度沒有過度得追求的話可以使用SugaredLogger。
它比其他結構化日誌記錄包快4-10倍，並且包括結構化和printf樣式的API。
```golang
logger, _ := zap.NewProduction()
defer logger.Sync() // flushes buffer, if any
sugar := logger.Sugar()
sugar.Infow("failed to fetch URL",
  // Structured context as loosely typed key-value pairs.
  "url", url,
  "attempt", 3,
  "backoff", time.Second,
)
sugar.Infof("Failed to fetch URL: %s", url)
```
當然如果你的server可能規格沒這麼好，或者你很追求性能速度的話，那你可以考慮使用最基本的logger。
#### 方法二
```golang
logger, _ := zap.NewProduction()
defer logger.Sync()
logger.Info("failed to fetch URL",
  // Structured context as strongly typed Field values.
  zap.String("url", url),
  zap.Int("attempt", 3),
  zap.Duration("backoff", time.Second),
)
```
### 實戰
> 注意雷坑
```golang
// 這裡的erroroutput不代表error level的log輸出，他只是zap本身運行error時才會輸出在這邊
zap.ErrorOutput()
```
這邊舉個nginx為例好了，nginx的日誌系統是有access.log和error.log分別記錄不同的訊息。

但是呢，zap本身並沒有『直接』提供這樣功能，你需要透過zapcore和tee去搭配實現。

以下code可以實現同時將info和error、fatal等訊息分開寫入access.log和error.log，並保持輸出訊息至console。

> zap本身沒有提供log rotate的功能，所以要搭配第三方套件[lumberjack](https://github.com/natefinch/lumberjack)去實現。

```golang
func Init() *zap.SugaredLogger {
	// 建立一個info會觸發的條件
	infoLevel := zap.LevelEnablerFunc(func(level zapcore.Level) bool {
		return level == zapcore.InfoLevel
	})
	// 建立一個只有error和fatal會觸發的條件
	errorFatalLevel := zap.LevelEnablerFunc(func(level zapcore.Level) bool {
		return level == zapcore.ErrorLevel || level == zapcore.FatalLevel
	})
	// write syncers
	stdoutSyncer := zapcore.Lock(os.Stdout)
	stderrSyncer := zapcore.Lock(os.Stderr)
	// info訊息會寫到access log
	accessWriter := zapcore.AddSync(&lumberjack.Logger{
		Filename:   "./log/access.log", // 要寫入的檔案
		MaxSize:    10,                 // log檔最大的大小，單位：MB(megabytes)
		MaxBackups: 3,                  // 最多幾個備份
		//MaxAge:     28, // log檔要存放幾天後自動刪除
	})
	// error訊息會寫到error log
	errorWriter := zapcore.AddSync(&lumberjack.Logger{
		Filename:   "./log/error.log",
		MaxSize:    10,
		MaxBackups: 3,
	})
	// 建立兩個core，分別去寫入info和error
	core := zapcore.NewTee(
		zapcore.NewCore(
			zapcore.NewJSONEncoder(zap.NewProductionEncoderConfig()),
			// 這邊用Multi是因為要同時寫入檔案也要同時show在console
			zapcore.NewMultiWriteSyncer(stdoutSyncer, accessWriter),
			infoLevel,
		),
		zapcore.NewCore(
			zapcore.NewJSONEncoder(zap.NewProductionEncoderConfig()),
			zapcore.NewMultiWriteSyncer(stderrSyncer, errorWriter),
			errorFatalLevel,
		),
	)

	// 要記得加個caller和stacktrace
	logger := zap.New(core, zap.AddCaller(), zap.AddStacktrace(zap.ErrorLevel))
	return logger.Sugar()
}

```