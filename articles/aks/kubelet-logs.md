---
title: Azure Kubernetes Service (AKS) で kubelet ログを表示する
description: Azure Kubernetes Service (AKS) ノードから kubelet ログのトラブルシューティング情報を表示する方法について説明します
services: container-service
ms.topic: article
ms.date: 03/05/2019
ms.openlocfilehash: 355c665db2627fe04595a8b519b16bd475ebcadf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735150"
---
# <a name="get-kubelet-logs-from-azure-kubernetes-service-aks-cluster-nodes"></a>Azure Kubernetes Service (AKS) クラスター ノードから kubelet ログを取得する

場合によっては、AKS クラスターの操作の一環として、ログを確認して問題のトラブルシューティングを行う必要があります。 Azure portal には、[AKS マスター コンポーネント][aks-master-logs]や [AKS クラスター内のコンテナー][azure-container-logs]のログを表示する機能が組み込まれています。 場合によっては、トラブルシューティングの目的で、AKS ノードから *kubelet* ログを取得しなければならない可能性があります。

この記事では、`journalctl` を使用して AKS ノード上の *kubelet* ログを表示する方法を示します。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

## <a name="create-an-ssh-connection"></a>SSH 接続を作成する

最初に、*kubelet* ログを表示する必要があるノードで SSH 接続を作成します。 この操作の詳細については、「[Azure Kubernetes Service (AKS) クラスター ノードへの SSH 接続][aks-ssh]」を参照してください。

## <a name="get-kubelet-logs"></a>kubelet ログの取得

ノードに接続したら、次のコマンドを実行して、*kubelet* ログをプルします。

```console
sudo journalctl -u kubelet -o cat
```

> [!NOTE]
> Windows ノードの場合、ログデータは `C:\k` にあり、*more* コマンドを使用して表示できます。
> ```
> more C:\k\kubelet.log
> ```

*kubelet* ログ データの出力の例を次に示します。

```
I0508 12:26:17.905042    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:27.943494    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:28.920125    8672 server.go:796] GET /stats/summary: (10.370874ms) 200 [[Ruby] 10.244.0.2:52292]
I0508 12:26:37.964650    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:47.996449    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:58.019746    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:05.107680    8672 server.go:796] GET /stats/summary/: (24.853838ms) 200 [[Go-http-client/1.1] 10.244.0.3:44660]
I0508 12:27:08.041736    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:18.068505    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:28.094889    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:38.121346    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:44.015205    8672 server.go:796] GET /stats/summary: (30.236824ms) 200 [[Ruby] 10.244.0.2:52588]
I0508 12:27:48.145640    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:58.178534    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:05.040375    8672 server.go:796] GET /stats/summary/: (27.78503ms) 200 [[Go-http-client/1.1] 10.244.0.3:44660]
I0508 12:28:08.214158    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:18.242160    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:28.274408    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:38.296074    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:48.321952    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:58.344656    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
```

## <a name="next-steps"></a>次のステップ

Kubernetes マスターからさらにトラブルシューティング情報が必要な場合は、[AKS での Kubernetes マスター ノード ログの表示][aks-master-logs]に関するページをご覧ください。

<!-- LINKS - internal -->
[aks-ssh]: ssh.md
[aks-master-logs]: ./view-control-plane-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-master-logs]: ./view-control-plane-logs.md
[azure-container-logs]: ../azure-monitor/containers/container-insights-overview.md