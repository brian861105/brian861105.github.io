---
title: K8s Development Tool
date: 2025-08-03 00:56:01
tags: 
    - development tool
    - K8s
categories:
    - K8s
---
Kubernetes 是現在最常被使用的容器編排平台 (container orchestration platform)，對於想要在 local 環境開發的開發者，選擇一套能夠模擬 Kubernetes 行為的工具，來進行本地的測試開發，變得越來越重要。

像是 Kind 和 Minikube 等工具就提供了方便的解決方式。該篇文章中會提及每一種工具所著重的地方在哪裡，而我們需要根據怎樣的情景選擇對應的工具進行開發。

接下來針對以下四套不同的開源軟體 kind, Minikube, k3s, kubeadm 進行介紹

## kind 介紹
> kind is a tool for running local Kubernetes clusters using Docker container "nodes". kind was primarily designed for testing Kubernetes itself, but may be used for local development or CI.

kind 是 Kubernetes 底下的一個子專案，他的全名是 Kubernetes in Docker，他被設計的最主要理由是測試 K8s 本身功能，後來多被使用在 local 環境或者 CI 上。

因為 kind 具備了有 Docker 就可以執行的設計，也成為了本地端開發的一個選項。開發者不需要額外的 VM，或者安裝其他的 K8s 在自己的開發環境中。正因為它只需要 Docker 環境就能運作，因此能夠與 GitHub Actions、GitLab CI 等 CI 工具和 Docker Runner 來搭建 Kubernetes 的測試環境。

### 特色與優勢
- 部署方式：使用 Docker 即可在本機或 CI 環境中快速建立 Kubernetes 集群
- 系統資源使用：在使用 kind 中，每個 Kubernetes Node 視為一個 Docker 容器。一個容器比一個完整的虛擬機輕巧得多
- 支援多 Node 叢集：可以輕易的創建一個多 node 的叢集，在測試功能以及需要多 node 的應用程式上有著一定的優勢，雖然 minikube 也有提供類似的功能，但是需要額外的驅動程式（如 VirtualBox、Hyper-V），相較之下，kind 提供的方法更簡潔有效。kind 則因為所有 node 都是 Docker 容器，多 Node 的配置完全基於 Docker 的 network，設定檔簡單明瞭，這正是它在多 node 測試上更具優勢的原因
- 快速啟動：基於 Docker 容器的特性，啟動速度極快
- CI/CD 友善：與 GitHub Actions、GitLab CI 等工具完美整合

### 適用情景
- CI/CD 環境中的自動化測試
- 本機快速測試和開發
- 多節點 Kubernetes 功能測試
- Kubernetes 本身的開發和測試
- 需要快速啟動和銷毀叢集的場景

### 優點
- 資源使用量低，基於 Docker 容器
- 啟動和銷毀速度極快
- 多節點配置簡單，基於 Docker 網路
- 無需額外的虛擬化驅動程式
- 與 CI/CD 工具整合度高

### 缺點
- 由於 Kind 的 Node 是 Docker 容器，它無法完全模擬真實機器或虛擬機環境中的所有功能。這可能導致在測試一些底層的硬體相關功能或需要特定虛擬化驅動的應用程式時，會遇到問題
- Kind 的主要設計目的就是測試 Kubernetes 本身和開發環境，而不是作為一個生產級的解決方案。它不具備 Minikube 在 local 開發時所具有的一些便利功能，例如 dashboard
- 功能相對簡單，缺少一些開發便利功能

## Minikube 介紹
> minikube is local Kubernetes, focusing on making it easy to learn and develop for Kubernetes.

minikube 是一個功能豐富的本地 Kubernetes 工具，它的設計目標是讓開發者能夠容易地學習和開發。

### 特色與優勢
- 支援最新版本的 K8s，且還能支援最多達 6 個之前的次要版本，確保開發者可以測試不同版本的環境
- 跨平台支援，他能在 Linux、macOS 和 Windows 等作業系統上運行
- 可以在 VM, container 或者是 bare-metal（裸機）上部署
- 提供多種不同 runtime，例如 CRI-O、containerd 和 docker
- 提供了一個直接的 API endpoint，可以快速載入 (image load) 和建置 (image build) image
- 內建多種進階功能，如 LoadBalancer, filesystem, FeatureGates, 以及 network policy
- Addons 用於輕鬆安裝 Kubernetes 應用程式的附加元件
- 支援常見的 CI 環境

### 適用情景
- 本地 Kubernetes 開發和學習
- 需要豐富功能和插件的開發環境
- 測試不同版本的 Kubernetes
- 需要真實 VM 環境的應用測試
- 使用 Kubernetes Dashboard 進行圖形化管理

### 優點
- 豐富的 application 可以進行使用
- Kubernetes 功能更加齊全
- 環境更接近真實的 K8s 環境
- 提供豐富的插件生態系統
- 支援多種虛擬化驅動程式

### 缺點
- 資源消耗高：虛擬機，記憶體和 CPU 使用較多
- 啟動較慢：VM 啟動速度較長
- 複雜度高：配置選項多
- 對於 CI/CD 環境可能過於重量級

## k3s 介紹
> Lightweight Kubernetes. Easy to install, half the memory, all in a binary less than 100 MB.

k3s 是由 Rancher Labs 開發的輕量級 Kubernetes 發行版，它被設計為一個完全符合 CNCF 認證的 Kubernetes 發行版，但移除了許多過時的功能和可選的功能，專注於邊緣運算、IoT 裝置和資源受限的環境。

k3s 將所有 Kubernetes 元件打包成一個小於 100MB 的二進位檔案，並且預設使用 SQLite 作為資料存儲（也支援 etcd），這使得它非常適合在資源有限的環境中運行。

### 特色與優勢
- 極致輕量：二進位檔案小於 100MB，記憶體使用量減少一半以上
- 簡單安裝：一個指令即可安裝完成，無需複雜的設定
- 完全相容：100% 符合 CNCF Kubernetes 認證，API 完全相容
- 內建功能：預設包含 Traefik Ingress Controller、Local Path Provisioner 等常用元件
- 多架構支援：支援 x86_64、ARM64、ARMv7 等多種處理器架構
- 邊緣友善：特別適合 IoT 和邊緣運算場景
- 快速啟動：啟動時間極快，通常在 30 秒內完成
- 支援 HA：可以建立高可用性的多節點叢集

### 適用情景
- 邊緣運算和 IoT 裝置部署
- 資源受限的環境（樹莓派、嵌入式裝置）
- 快速原型開發和測試
- CI/CD 環境中的快速測試
- 學習 Kubernetes 概念的輕量環境

### 優點
- 資源使用量極低，適合資源受限環境
- 安裝和管理極其簡單
- 啟動速度快
- 完全符合 K8s 標準，無相容性問題
- 預設配置適合大多數使用情境

### 缺點
- 移除了一些企業級功能，可能不適合複雜的生產環境
- 社群生態相對較小
- 客製化程度不如標準 Kubernetes 高
- 對於需要完整 Kubernetes 功能的開發可能有限制

## kubeadm 介紹
> kubeadm is a tool built to provide kubeadm init and kubeadm join as best-practice "fast paths" for creating Kubernetes clusters.

kubeadm 是 Kubernetes 官方提供的叢集初始化工具，它的設計目標是提供一種快速、簡單且安全的方式來啟動符合最佳實務的 Kubernetes 叢集。kubeadm 專注於叢集的啟動過程，而不是機器的準備工作。

與其他工具不同，kubeadm 更適合用於建立生產級別的 Kubernetes 叢集，而不僅僅是本地開發環境。它是許多 Kubernetes 安裝工具和 Kubernetes 託管服務的基礎。

### 特色與優勢
- 官方支援：Kubernetes 官方維護，遵循最佳實務
- 生產就緒：建立的叢集符合生產環境標準
- 可擴展性：可以輕鬆擴展和管理多節點叢集
- 安全性：預設啟用 RBAC，使用 TLS 加密通訊
- 靈活配置：支援高度客製化的叢集配置
- 標準化：建立的叢集完全符合 Kubernetes 標準
- 證書管理：自動處理叢集證書的生成和輪換
- 升級支援：提供完整的叢集升級流程

### 適用情景
- 建立生產級 Kubernetes 叢集
- 需要完整 Kubernetes 功能的開發環境
- 多節點叢集測試
- 學習 Kubernetes 叢集管理
- 作為其他安裝工具的基礎

### 優點
- 建立真正的生產級 Kubernetes 環境
- 完全遵循 Kubernetes 官方最佳實務
- 提供完整的叢集生命週期管理
- 高度可客製化
- 官方支援和持續更新

### 缺點
- 需要預先準備機器和網路環境
- 學習曲線較陡峭，需要對 Kubernetes 有深入了解
- 資源需求較高，不適合資源受限的環境
- 設定較為複雜，需要手動處理許多細節
- 對於簡單的本地開發可能過於複雜


## 工具選擇建議

根據不同的使用情境，以下是各工具的推薦場景：

本地開發、學習：Minikube（功能豐富）或 kind（輕量快速）

CI/CD 測試：kind（輕量、快速）

邊緣運算/IoT：k3s（專為邊緣設計）

生產環境：kubeadm（官方標準）

多節點測試：kind（簡單）或 kubeadm（完整功能）

資源受限環境：k3s（最輕量）
