---
title: Kind Introduction
date: 2024-10-05 01:56:56
tags: 
    - development tool
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

- 部署方式：使用 Docker 即可在本機或 CI 環境中快速建立 Kubernetes 集群。
- 系統資源使用：在使用 kind 中，每個 Kubernetes Node 視為一個 Docker 容器。一個容器比一個完整的虛擬機輕巧得多。
- 支援多 Node 叢集：可以輕易的創建一個多 node 的叢集，在測試功能以及需要多 node 的應用程式上有著一定的優勢，雖然 minikube 也有提供類似的功能，但是需要額外的的驅動程式（如 VirtualBox、Hyper-V ），相較之下， kind 提供的方法更簡潔有效。kind 則因為所有node 都是 Docker 容器，多 Node 的配置完全基於 Docker 的 network，設定檔簡單明瞭，這正是它在多 node 測試上更具優勢的原因。
- 適用情景：非常適合CI/CD 環境以及本機做測試，因為它可以輕鬆地在任何有 Docker 的機器上運行，包括 GitHub Actions 和 GitLab CI。
- 缺點：
    - 由於 Kind 的 Node 是 Docker 容器，它無法完全模擬真實機器或虛擬機環境中的所有功能。這可能導致在測試一些底層的硬體相關功能或需要特定虛擬化驅動的應用程式時，會遇到問題。
    - Kind 的主要設計目的就是測試 Kubernetes 本身和開發環境，而不是作為一個生產級的解決方案。它不具備 Minikube 在 local 開發時所具有的一些便利功能，例如 dashboard。