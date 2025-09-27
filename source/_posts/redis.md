---
title: redis
date:2025-09-26 04:52:28
categories:
    - backend
---

## 什麼是 Redis？

Redis 是一種快速、開放原始碼的記憶體內鍵值資料結構存放區。Redis 隨附一組多功能的記憶體內資料結構，讓您能夠輕鬆地建立各種自訂應用程式。Redis 主要使用案例包含快取、工作階段管理、發佈/訂閱以及排行榜。

Redis 位於其他資料庫「之前」，目的是建立一套快取系統，減輕 noSQL or RDB 的負擔，並且加速服務。

ref : https://aws.amazon.com/tw/elasticache/what-is-redis/

## 我們該如何使用它？

上一段曾說道， Redis 是一套鍵值資料結構存放區，我們可以利用 key 來存放資料，在官方文件有提到 Redis 可以儲存各種位元組序列，包括文字內容、序列化後的物件、計數器數值，以及二進位資料陣列。

以 golang 為例

```golang

hashFields := []string{
    "model", "Deimos",
    "brand", "Ergonom",
    "type", "Enduro bikes",
    "price", "4972",
}

res1, err := rdb.HSet(ctx, "bike:1", hashFields).Result()

fmt.Println(res1) // >>> 4

res2, err := rdb.HGet(ctx, "bike:1", "model").Result()

fmt.Println(res2) // >>> Deimos

res3, err := rdb.HGet(ctx, "bike:1", "price").Result()

fmt.Println(res3) // >>> 4972

cmdReturn := rdb.HGetAll(ctx, "bike:1")
res4, err := cmdReturn.Result()

fmt.Println(res4)
// >>> map[brand:Ergonom model:Deimos price:4972 type:Enduro bikes]

type BikeInfo struct {
    Model string `redis:"model"`
    Brand string `redis:"brand"`
    Type  string `redis:"type"`
    Price int    `redis:"price"`
}

var res4a BikeInfo

if err := cmdReturn.Scan(&res4a); err != nil {
    panic(err)
}

fmt.Printf("Model: %v, Brand: %v, Type: %v, Price: $%v\n",
    res4a.Model, res4a.Brand, res4a.Type, res4a.Price)
// >>> Model: Deimos, Brand: Ergonom, Type: Enduro bikes, Price: $4972

hashFields := []string{
    "model", "Deimos",
    "brand", "Ergonom",
    "type", "Enduro bikes",
    "price", "4972",
}

_, err := rdb.HSet(ctx, "bike:1", hashFields).Result()

cmdReturn := rdb.HMGet(ctx, "bike:1", "model", "price")
res5, err := cmdReturn.Result()

fmt.Println(res5) // >>> [Deimos 4972]

type BikeInfo struct {
    Model string `redis:"model"`
    Brand string `redis:"-"`
    Type  string `redis:"-"`
    Price int    `redis:"price"`
}

var res5a BikeInfo

if err := cmdReturn.Scan(&res5a); err != nil {
    panic(err)
}

fmt.Printf("Model: %v, Price: $%v\n", res5a.Model, res5a.Price)
// >>> Model: Deimos, Price: $4972

hashFields := []string{
    "model", "Deimos",
    "brand", "Ergonom",
    "type", "Enduro bikes",
    "price", "4972",
}

_, err := rdb.HSet(ctx, "bike:1", hashFields).Result()

res6, err := rdb.HIncrBy(ctx, "bike:1", "price", 100).Result()

fmt.Println(res6) // >>> 5072

res7, err := rdb.HIncrBy(ctx, "bike:1", "price", -100).Result()

fmt.Println(res7) // >>> 4972

res8, err := rdb.HIncrBy(ctx, "bike:1:stats", "rides", 1).Result()

fmt.Println(res8) // >>> 1

res9, err := rdb.HIncrBy(ctx, "bike:1:stats", "rides", 1).Result()

fmt.Println(res9) // >>> 2

res10, err := rdb.HIncrBy(ctx, "bike:1:stats", "rides", 1).Result()

fmt.Println(res10) // >>> 3

res11, err := rdb.HIncrBy(ctx, "bike:1:stats", "crashes", 1).Result()

fmt.Println(res11) // >>> 1

res12, err := rdb.HIncrBy(ctx, "bike:1:stats", "owners", 1).Result()

fmt.Println(res12) // >>> 1

res13, err := rdb.HGet(ctx, "bike:1:stats", "rides").Result()

fmt.Println(res13) // >>> 3

res14, err := rdb.HMGet(ctx, "bike:1:stats", "crashes", "owners").Result()


fmt.Println(res14) // >>> [1 1]
```

## Document database
....待補

## High Available(HA)

為什麼我們需要在乎 HA
1. 我們需要思考 server 現實中的停機成本以及停機的真實代價
2. 零容忍停機
3. 不重視 HA，漸漸會有技術債的產生
4. 客戶的信賴

在一個具備廣大用戶的服務，我們需要時刻提醒自己*我們承受得起沒有 HA 的風險嗎？*

回到 redis 本身，redis 本身具備兩種 HA 的機制。
1. Redis Sentinel
2. Redis Cluster

Redis Sentinel 本身是一個監控和自動故障轉移系統，專門為 Redis 主從複製架構提供高可用性解決方案。

### Redis Sentinel 的核心功能
- 監控（Monitoring）

持續監控 Redis 主節點和從節點的健康狀態
定期發送 PING 命令檢查節點是否正常回應
追蹤節點的網路連接狀況

- 通知（Notification）

當 Redis 實例出現故障時，可以透過 API 通知系統管理員
支援腳本通知機制
可整合告警系統

- 自動故障轉移（Automatic Failover）

當主節點故障時，自動將一個從節點提升為新的主節點
重新配置其他從節點指向新主節點
通知客戶端新的主節點位置

- 配置提供者（Configuration Provider）

作為客戶端的服務發現機制
客戶端連接到 Sentinel 而非直接連接 Redis
Sentinel 告訴客戶端當前的主節點位置

Redis Sentinel 的工作原理
- 仲裁機制（Quorum）

需要多個 Sentinel 實例達成共識才能執行故障轉移
防止「腦裂」問題和誤判
通常建議至少部署 3 個 Sentinel 實例

- 主觀下線 vs 客觀下線

主觀下線（SDOWN）：單個 Sentinel 認為節點故障
客觀下線（ODOWN）：多個 Sentinel 達成共識，確認節點故障

### Redis Cluster 的不同
相較於 Sentinel，Redis Cluster 是：

分散式存儲系統
- 自動將資料分片到多個節點
- 每個節點只存儲部分資料
- 使用哈希槽（Hash Slots）進行資料分布

原生高可用
- 內建故障檢測和自動故障轉移
- 不需要額外的監控程序
- 每個主節點可配置多個從節點

水平擴展能力
- 可以動態增減節點
- 自動重新分布資料
- 支援在線擴容

### 兩種機制的適用場景

選擇 Redis Sentinel 當：
- 現有單一 Redis 實例需要高可用
- 資料量不大，不需要分片
- 希望保持 Redis 操作的簡單性
- 主要需求是自動故障轉移

選擇 Redis Cluster 當：
- 資料量超過單機記憶體限制
- 需要水平擴展能力
- 高併發讀寫需求
- 需要真正的分散式架構

Redis Sentinel 部署
- 最少 3 個 Sentinel 實例（奇數個）
- Sentinel 可以跨機房部署
- 配置相對簡單

Redis Cluster 部署
- 最少 6 個實例（3主3從）
- 需要客戶端支援 Cluster 協議
- 配置較複雜，但功能更強大