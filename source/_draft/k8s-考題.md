---
title: k8s 考題
date: 2025-09-23 15:26:41
tags:
---

請回答以下問題，每題限 200 字內。

### 討論題 1
在設計一個能處理高流量（例如 50k RPS）的系統時，如何優化 Kubernetes 節點和 Pod 的自動擴展？請說明策略、潛在挑戰，以及如何整合內部請求監控來提升效率。

### 討論題 2  
對於訊息系統（如 Pub/Sub 或 MQTT subscriber），在用戶從 10 萬成長到 5 千萬的情境下，如何確保可擴展性和可靠性？請討論設計考量，包括壓力測試方法 。

### 討論題 3
描述如何在微服務架構中實踐故障隔離和服務降級策略，包括工具選擇（如 service mesh）、潛在挑戰，以及如何整合監控來確保系統整體可靠性 。

# 系統架構設計題目

## 1. 架構設計題

### 情境
考慮一個 chatbot system 需要處理 50k RPS，主要服務是與用戶的即時聊天。

請設計一個整體架構，滿足自動擴展、資料庫（包含問答資料庫）、監控系統（standardized logging, metrics-based alerting）和備份/災難恢復計劃。重點在於如何從初期 10 萬用戶擴展到 3 個月內 5 千萬用戶 。

### 要求
1. **繪製簡易架構圖**（包含組件互聯）
2. **說明技術選擇理由、trade-offs**，以及如何進行壓力測試和基礎設施即程式碼管理 
3. **預先討論如何定義 SLI/SLO** 來測量系統可靠性，並權衡成本 vs. 性能 

***

## 2. 問題解決題

### 情境
延續前一題的 chatbot system（處理 50k RPS，即時聊天服務），假設系統已上線，但最近出現間歇性問題：在高峰期（例如用戶從 10 萬暴增到 500 萬時），部分用戶回覆聊天太延遲超過 5 秒，或偶爾出現 429 Too Many Requests 錯誤。監控系統顯示 Kubernetes 叢集的 CPU 使用率偶爾飆升到 90%，但 Pod 自動擴展未及時觸發；同時，資料庫（使用 PostgreSQL）出現 connection timeout 錯誤，且延遲增加。

而且，好像 Kafka 或 RabbitMQ 有 backlog 積壓。日誌顯示一些 connection timeout 錯誤，但沒有明確的單一故障點 。

### 要求（限 400 字內）
1. **診斷步驟**：一步步說明如何調查問題，包括使用的工具（如 Prometheus/Grafana 查詢 metrics、kubectl describe/debug、ELK stack 分析 logs、Jaeger/Zipkin tracing distributed requests）。請列出至少 5 個關鍵步驟，並解釋為何選擇這些工具 

2. **潛在根因分析**：基於給定情境，列出 3-5 個可能原因（例如：HPA 配置錯誤故擴展延遲、資料庫索引不佳、訊息序列 partition 不均、網路 throttling，或 rate limiting 未正確配置）。討論如何驗證每個原因（e.g., 使用 stress testing tools 如 Locust 或 k6 模擬流量）

3. **解決方案與預防**：提出短期修復（e.g., 調整 HPA metrics threshold 或增加 Pod limits）和長期優化（e.g., 實施 circuit breaker with Istio、遷移到更高效的資料庫 sharding，或 autoscaling based on custom metrics）。討論 trade-offs（如成本 vs. 效能），並說明如何更新 IaC（e.g., Terraform/Helm charts）來防止復發。額外討論如何調整 SLI/SLO（e.g., 將 latency P99 從 5s 降到 1s）來監測改善。（或動線製簡單流程圖，如診斷流程。）

***