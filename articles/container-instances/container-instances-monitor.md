---
title: Azure Container Instances のコンテナーを監視する
description: Azure Container Instances のコンテナーによる CPU やメモリなどのコンピューティング リソースの使用状況を監視する方法の詳細について説明します。
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 04/24/2018
ms.author: danlep
ms.openlocfilehash: 950d8b4b5ec1a55e2054039a01d6807915b5c714
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784075"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Azure Container Instances のコンテナー リソースを監視する

Azure Monitor を使用すると、コンテナー インスタンスによって使用されているコンピューティング リソースを把握できます。 Azure Monitor を使用して、コンテナー グループとそのコンテナーの CPU とメモリ使用率を追跡します。 このリソース使用量データは、コンテナー グループの最適な CPU とメモリ設定を決定するために役立ちます。

このドキュメントでは、Azure Portal と Azure CLI の両方を使用して、コンテナー インスタンスの CPU とメモリ使用量を収集する方法について詳しく説明します。

> [!IMPORTANT]
> 現時点で、リソース使用量メトリックは Linux コンテナーにのみ使用できます。
>

## <a name="available-metrics"></a>使用可能なメトリック

Azure Monitor を使用すると、Azure Container Instances の **CPU** と**メモリ**の両方の使用量に関するメトリックを把握できます。 両方のメトリックは、コンテナー グループと個々のコンテナーで使用できます。

CPU メトリックは**ミリコア**単位で表されます。 1 ミリコアは CPU コアの 1/1000 なので、500 ミリコア (つまり 500 m) は CPU コアの 50% の使用率を表します。

メモリ メトリックは**バイト**単位で表されます。

## <a name="get-metrics---azure-portal"></a>メトリックを取得する - Azure Portal

コンテナー グループが作成されると、Azure Portal で Azure Monitor データを利用できるようになります。 コンテナー グループのメトリックを表示するには、リソース グループを選択してからコンテナー グループを選択します。 ここでは、CPU とメモリの両方の使用量について事前に作成されたグラフを確認できます。

![2 つのグラフ][dual-chart]

複数のコンテナーを含むコンテナー グループがある場合は、[ディメンション][monitor-dimension]を使用して個々のコンテナーのメトリックを表示します。 個々のコンテナー メトリックを使用してグラフを作成するには、次の手順を実行します。

1. 左側のナビゲーション メニューから **[モニター]** を選択します。
2. コンテナー グループとメトリック (CPU またはメモリ) を選択します。
3. 緑色のディメンション ボタンを選択し、**[コンテナー名]** を選択します。

![ディメンション][dimension]

## <a name="get-metrics---azure-cli"></a>メトリックを取得する - Azure CLI

コンテナー インスタンスの CPU とメモリの使用量は、Azure CLI を使用して収集することもできます。 まず、次のコマンドを使用してコンテナー グループの ID を取得します。 `<resource-group>` をリソース グループ名に、`<container-group>` をコンテナー グループの名前に置き換えます。


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

次のコマンドを使用して、**CPU** 使用量のメトリックを取得します。

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:39:00  CPU Usage
2018-04-22 04:40:00  CPU Usage
2018-04-22 04:41:00  CPU Usage
2018-04-22 04:42:00  CPU Usage
2018-04-22 04:43:00  CPU Usage      0.375
2018-04-22 04:44:00  CPU Usage      0.875
2018-04-22 04:45:00  CPU Usage      1
2018-04-22 04:46:00  CPU Usage      3.625
2018-04-22 04:47:00  CPU Usage      1.5
2018-04-22 04:48:00  CPU Usage      2.75
2018-04-22 04:49:00  CPU Usage      1.625
2018-04-22 04:50:00  CPU Usage      0.625
2018-04-22 04:51:00  CPU Usage      0.5
2018-04-22 04:52:00  CPU Usage      0.5
2018-04-22 04:53:00  CPU Usage      0.5
```

次のコマンドを使用して、**メモリ**使用量のメトリックを取得します。

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:38:00  Memory Usage
2018-04-22 04:39:00  Memory Usage
2018-04-22 04:40:00  Memory Usage
2018-04-22 04:41:00  Memory Usage
2018-04-22 04:42:00  Memory Usage  6.76915e+06
2018-04-22 04:43:00  Memory Usage  9.22061e+06
2018-04-22 04:44:00  Memory Usage  9.83552e+06
2018-04-22 04:45:00  Memory Usage  8.42906e+06
2018-04-22 04:46:00  Memory Usage  8.39526e+06
2018-04-22 04:47:00  Memory Usage  8.88013e+06
2018-04-22 04:48:00  Memory Usage  8.89293e+06
2018-04-22 04:49:00  Memory Usage  9.2073e+06
2018-04-22 04:50:00  Memory Usage  9.36243e+06
2018-04-22 04:51:00  Memory Usage  9.30509e+06
2018-04-22 04:52:00  Memory Usage  9.2416e+06
2018-04-22 04:53:00  Memory Usage  9.1008e+06
```

複数コンテナー グループの場合、`containerName` ディメンションを追加して、コンテナーごとにこのデータを返すことができます。

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --dimension containerName --output table

Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2018-04-22 17:03:00  Memory Usage  aci-tutorial-app      1.95338e+07
2018-04-22 17:04:00  Memory Usage  aci-tutorial-app      1.93096e+07
2018-04-22 17:05:00  Memory Usage  aci-tutorial-app      1.91488e+07
2018-04-22 17:06:00  Memory Usage  aci-tutorial-app      1.94335e+07
2018-04-22 17:07:00  Memory Usage  aci-tutorial-app      1.97714e+07
2018-04-22 17:08:00  Memory Usage  aci-tutorial-app      1.96178e+07
2018-04-22 17:09:00  Memory Usage  aci-tutorial-app      1.93434e+07
2018-04-22 17:10:00  Memory Usage  aci-tutorial-app      1.92614e+07
2018-04-22 17:11:00  Memory Usage  aci-tutorial-app      1.90659e+07
2018-04-22 16:12:00  Memory Usage  aci-tutorial-sidecar  1.35373e+06
2018-04-22 16:13:00  Memory Usage  aci-tutorial-sidecar  1.28614e+06
2018-04-22 16:14:00  Memory Usage  aci-tutorial-sidecar  1.31379e+06
2018-04-22 16:15:00  Memory Usage  aci-tutorial-sidecar  1.29536e+06
2018-04-22 16:16:00  Memory Usage  aci-tutorial-sidecar  1.38138e+06
2018-04-22 16:17:00  Memory Usage  aci-tutorial-sidecar  1.41312e+06
2018-04-22 16:18:00  Memory Usage  aci-tutorial-sidecar  1.49914e+06
2018-04-22 16:19:00  Memory Usage  aci-tutorial-sidecar  1.43565e+06
2018-04-22 16:20:00  Memory Usage  aci-tutorial-sidecar  1.408e+06
```

## <a name="next-steps"></a>次の手順

[Azure Monitoring の概要][azure-monitoring]に関するページで Azure Monitoring の詳細について学習します。

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - Internal -->
[azure-monitoring]: ../monitoring-and-diagnostics/monitoring-overview.md
[monitor-dimension]: ../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics
