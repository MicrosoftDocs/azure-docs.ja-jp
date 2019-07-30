---
title: Azure Container Instances のコンテナーを監視する
description: Azure Container Instances のコンテナーによる CPU やメモリなどのコンピューティング リソースの使用状況を監視する方法の詳細。
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: overview
ms.date: 04/24/2019
ms.author: danlep
ms.openlocfilehash: 4c1208d8cbc795e53128df0ccf93e79dc427abad
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325850"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Azure Container Instances のコンテナー リソースを監視する

[Azure Monitor][azure-monitoring] によって、コンテナー インスタンスによって使用されているコンピューティング リソースの分析情報が提供されます。 このリソース使用量データは、コンテナー グループの最適なリソース設定を決定するために役立ちます。 Azure Monitor には、コンテナー インスタンスのネットワーク アクティビティを追跡するメトリックもあります。

このドキュメントでは、Azure portal と Azure CLI の両方を使用して、コンテナー インスタンスの Azure Monitor メトリックを収集する方法について詳しく説明します。

> [!IMPORTANT]
> Azure Container Instances の Azure Monitor メトリックは現在プレビュー段階にあり、一部の[制限が適用されます](#preview-limitations)。 プレビュー版は、[追加使用条件][terms-of-use]に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

## <a name="preview-limitations"></a>プレビューの制限事項

現時点で、Azure Monitor メトリックは Linux コンテナーにのみ使用できます。

## <a name="available-metrics"></a>使用可能なメトリック

Azure Monitor では、次の [Azure Container Instances 用のメトリック][supported-metrics]が提供されます。 これらのメトリックは、コンテナー グループと個々のコンテナーで使用できます。

* **[CPU 使用率]** - **ミリコア**単位です。 1 ミリコアは CPU コアの 1/1,000 なので、500 ミリコア (つまり 500 m) は CPU コアの 50% の使用率を表します。 すべてのコアにまたがる**平均使用率**として集計されます。

* **[メモリ使用量]** - **平均バイト数**として集計されます。

* **[1 秒あたりに受信したネットワーク バイト数]** および **[1 秒あたりに送信したネットワーク バイト数]** - **1 秒あたりの平均バイト数**として集計されます。 

## <a name="get-metrics---azure-portal"></a>メトリックを取得する - Azure Portal

コンテナー グループが作成されると、Azure Portal で Azure Monitor データを利用できるようになります。 コンテナー グループのメトリックを表示するには、そのコンテナー グループの **[概要]** ページに移動します。 ここでは、使用可能な各メトリックに対して事前に作成されたグラフを見ることができます。

![2 つのグラフ][dual-chart]

複数のコンテナーを含むコンテナー グループで、[ディメンション][monitor-dimension]を使用してコンテナーごとのメトリックを表します。 個々のコンテナー メトリックを使用してグラフを作成するには、次の手順を実行します。

1. **[概要]** ページで、**CPU** などのメトリック チャートを 1 つ選択します。 
1. **[Apply splitting]\(分割の適用\)** を選択し、 **[コンテナー名]** を選択します。

![ディメンション][dimension]

## <a name="get-metrics---azure-cli"></a>メトリックを取得する - Azure CLI

コンテナー インスタンスのメトリックは、Azure CLI を使用して収集することもできます。 まず、次のコマンドを使用してコンテナー グループの ID を取得します。 `<resource-group>` をリソース グループ名に、`<container-group>` をコンテナー グループの名前に置き換えます。


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

次のコマンドを使用して、**CPU** 使用量のメトリックを取得します。

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table

Timestamp            Name       Average
-------------------  ---------  ---------
2019-04-23 22:59:00  CPU Usage
2019-04-23 23:00:00  CPU Usage
2019-04-23 23:01:00  CPU Usage  0.0
2019-04-23 23:02:00  CPU Usage  0.0
2019-04-23 23:03:00  CPU Usage  0.5
2019-04-23 23:04:00  CPU Usage  0.5
2019-04-23 23:05:00  CPU Usage  0.5
2019-04-23 23:06:00  CPU Usage  1.0
2019-04-23 23:07:00  CPU Usage  0.5
2019-04-23 23:08:00  CPU Usage  0.5
2019-04-23 23:09:00  CPU Usage  1.0
2019-04-23 23:10:00  CPU Usage  0.5
```

コマンド内の `--metric` パラメーターの値を変更して、他の[サポートされているメトリック][supported-metrics]を取得します。 たとえば、次のコマンドを使用して、**メモリ**使用量のメトリックを取得します。 

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table

Timestamp            Name          Average
-------------------  ------------  ----------
2019-04-23 22:59:00  Memory Usage
2019-04-23 23:00:00  Memory Usage
2019-04-23 23:01:00  Memory Usage  0.0
2019-04-23 23:02:00  Memory Usage  8859648.0
2019-04-23 23:03:00  Memory Usage  9181184.0
2019-04-23 23:04:00  Memory Usage  9580544.0
2019-04-23 23:05:00  Memory Usage  10280960.0
2019-04-23 23:06:00  Memory Usage  7815168.0
2019-04-23 23:07:00  Memory Usage  7739392.0
2019-04-23 23:08:00  Memory Usage  8212480.0
2019-04-23 23:09:00  Memory Usage  8159232.0
2019-04-23 23:10:00  Memory Usage  8093696.0
```

複数コンテナー グループの場合、`containerName` ディメンションを追加して、コンテナーごとにメトリックを返すことができます。

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --dimension containerName --output table

Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2019-04-23 22:59:00  Memory Usage  aci-tutorial-app
2019-04-23 23:00:00  Memory Usage  aci-tutorial-app
2019-04-23 23:01:00  Memory Usage  aci-tutorial-app      0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-app      16834560.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-app      17534976.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-app      18329600.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-app      19742720.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-app      14786560.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-app      14651392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-app      15470592.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-app      15450112.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-app      15339520.0
2019-04-23 22:59:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:00:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:01:00  Memory Usage  aci-tutorial-sidecar  0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-sidecar  884736.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-sidecar  831488.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-sidecar  819200.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-sidecar  843776.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-sidecar  954368.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-sidecar  868352.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-sidecar  847872.0
```

## <a name="next-steps"></a>次の手順

[Azure Monitoring の概要][azure-monitoring]に関するページで Azure Monitoring について詳しく確認します。

Azure Container Instances のメトリックがしきい値を超えたときに通知されるように、[メトリック アラート][metric-alert]を作成する方法について学習します。

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-monitoring]: ../azure-monitor/overview.md
[metric-alert]: ..//azure-monitor/platform/alerts-metric.md
[monitor-dimension]: ../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics
[supported-metrics]: ../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups
