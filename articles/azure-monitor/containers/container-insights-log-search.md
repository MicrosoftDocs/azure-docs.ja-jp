---
title: Container insights のログのクエリを実行する方法 | Microsoft Docs
description: Container insights により、メトリックとログ データが収集されます。この記事では、レコードについて説明し、サンプル クエリを紹介します。
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: c2b7331255e1109f27f89a84d66e25eb07a20569
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201381"
---
# <a name="how-to-query-logs-from-container-insights"></a>Container insights のログのクエリを実行する方法

Container insights により、コンテナー ホストおよびコンテナーからパフォーマンスのメトリック、インベントリ データ、および正常性状態の情報が収集されます。 データは 3 分ごとに収集され、Azure Monitor の Log Analytics ワークスペースに転送されます。 このデータは、Azure Monitor で[クエリ](../logs/log-query-overview.md)用に使用できます。 このデータは、移行計画、容量の分析、探索、必要に応じたパフォーマンスのトラブルシューティングといったシナリオに適用できます。

## <a name="container-records"></a>コンテナー レコード

Container insights によって収集されるレコードの詳細を次の表に示します。 列の説明の一覧については、[ContainerInventory](/azure/azure-monitor/reference/tables/containerinventory) と [ContainerLog](/azure/azure-monitor/reference/tables/containerlog) のテーブルのリファレンスを参照してください。

| Data | データ ソース | データ型 | フィールド |
|------|-------------|-----------|--------|
| コンテナー インベントリ | kubelet | `ContainerInventory` | TimeGenerated、Computer、Name、ContainerHostname、Image、ImageTag、ContainerState、ExitCode、EnvironmentVar、Command、CreatedTime、StartedTime、FinishedTime、SourceSystem、ContainerID、ImageID |
| コンテナー ログ | Docker | `ContainerLog` | TimeGenerated、Computer、ImageID、Name、LogEntrySource、LogEntry、SourceSystem、ContainerID |
| コンテナー ノード インベントリ | Kube API | `ContainerNodeInventory`| TimeGenerated、Computer、ClassName_s、DockerVersion_s、OperatingSystem_s、Volume_s、Network_s、NodeRole_s、OrchestratorType_s、InstanceID_g、SourceSystem|
| Kubernetes クラスター内のポッドのインベントリ | Kube API | `KubePodInventory` | TimeGenerated、Computer、ClusterId、ContainerCreationTimeStamp、PodUid、PodCreationTimeStamp、ContainerRestartCount、PodRestartCount、PodStartTime、ContainerStartTime、ServiceName、ControllerKind、ControllerName、ContainerStatus、ContainerStatusReason、ContainerID、ContainerName、Name、PodLabel、Namespace、PodStatus、ClusterName、PodIp、SourceSystem |
| Kubernetes クラスター内のノード部分のインベントリ | Kube API | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
|Kubernetes クラスター内の永続ボリュームのインベントリ |Kube API |`KubePVInventory` | TimeGenerated、PVName、PVCapacityBytes、PVCName、PVCNamespace、PVStatus、PVAccessModes、PVType、PVTypeInfo、PVStorageClassName、PVCreationTimestamp、ClusterId、ClusterName、_ResourceId、SourceSystem |
| Kubernetes イベント | Kube API | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Kubernetes クラスター内のサービス | Kube API | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Kubernetes クラスターのノード部分のパフォーマンス メトリック | 使用状況メトリックは cAdvisor から、制限は Kube API から取得されます | `Perf \| where ObjectName == "K8SNode"` | Computer、ObjectName、CounterName &#40;cpuAllocatableNanoCores、memoryAllocatableBytes、cpuCapacityNanoCores、memoryCapacityBytes、memoryRssBytes、cpuUsageNanoCores、memoryWorkingsetBytes、restartTimeEpoch&#41;、CounterValue、TimeGenerated、CounterPath、SourceSystem | 
| Kubernetes クラスターのコンテナー部分のパフォーマンス メトリック | 使用状況メトリックは cAdvisor から、制限は Kube API から取得されます | `Perf \| where ObjectName == "K8SContainer"` | CounterName &#40;cpuRequestNanoCores、memoryRequestBytes、cpuLimitNanoCores、memoryWorkingSetBytes、restartTimeEpoch、cpuUsageNanoCores、memoryRssBytes&#41;、CounterValue、TimeGenerated、CounterPath、SourceSystem | 
| カスタム メトリック ||`InsightsMetrics` | Computer、Name、Namespace、Origin、SourceSystem、Tags<sup>1</sup>、TimeGenerated、Type、Va、_ResourceId | 

<sup>1</sup>*Tags* プロパティは、対応するメトリックの [複数のディメンション](../essentials/data-platform-metrics.md#multi-dimensional-metrics)を表します。 `InsightsMetrics` テーブルに収集されて格納されているメトリックの詳細と、レコードのプロパティの説明については、[InsightsMetrics の概要](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md)に関するページを参照してください。

## <a name="search-logs-to-analyze-data"></a>データを分析するためのログの検索

Azure Monitor ログを使用することにより、傾向の特定、ボトルネックの診断、予想を行ったり、データを関連付けて現在のクラスター構成のパフォーマンスが最適化されているかどうかを判断したりできます。 すぐに使用できる事前定義のログ検索が提供されています。また、検索結果として返される情報の表示方法をカスタマイズすることもできます。

**[分析で表示する]** ドロップダウン リストからプレビュー ウィンドウの **[Kubernetes イベント ログの表示]** または **[コンテナー ログの表示]** オプションを選択することにより、ワークスペース内のデータを対話的に分析できます。 **[ログ検索]** ページは、元の Azure portal ページの右側に表示されます。

![Log Analytics でデータを解析する](./media/container-insights-analyze/container-health-log-search-example.png)

ワークスペースに転送されるコンテナー ログ出力は、STDOUT および STDERR です。 Azure Monitor では、Azure マネージド Kubernetes (AKS) が監視され、大量のデータが生成されるため、現時点では Kube システムのデータは収集されません。 

### <a name="example-log-search-queries"></a>検索クエリの例

多くの場合、1、2 個の例を使ってクエリを作成し、その後、要件に合わせて変更するとうまくいきます。 より高度なクエリを作成できるように、次のサンプル クエリを試すことができます。

### <a name="list-all-of-a-containers-lifecycle-information"></a>コンテナーのライフ サイクル情報をすべて一覧表示します

```kusto
ContainerInventory
| project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime
| render table
```

### <a name="kubernetes-events"></a>Kubernetes イベント

``` kusto
KubeEvents_CL
| where not(isempty(Namespace_s))
| sort by TimeGenerated desc
| render table
```
### <a name="image-inventory"></a>イメージ インベントリ

``` kusto
ContainerImageInventory
| summarize AggregatedValue = count() by Image, ImageTag, Running
```

### <a name="container-cpu"></a>コンテナー CPU

**[折れ線] グラフの表示オプションを選択する**

``` kusto
Perf
| where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" 
| summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName 
```

### <a name="container-memory"></a>コンテナー メモリ

**[折れ線] グラフの表示オプションを選択する**

```kusto
Perf
| where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes"
| summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName
```

### <a name="requests-per-minute-with-custom-metrics"></a>カスタム メトリックでの 1 分あたりの要求数

```kusto
InsightsMetrics
| where Name == "requests_count"
| summarize Val=any(Val) by TimeGenerated=bin(TimeGenerated, 1m)
| sort by TimeGenerated asc<br> &#124; project RequestsPerMinute = Val - prev(Val), TimeGenerated
| render barchart 
```

## <a name="query-prometheus-metrics-data"></a>Prometheus メトリック データのクエリを実行する

次の例は、ノードごとのディスクごとの 1 秒あたりのディスク読み取り数を示す Prometheus メトリック クエリです。

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

Azure Monitor によって収集された Prometheus メトリックを名前空間でフィルター処理して表示するには、"prometheus" を指定します。 `default` kubernetes 名前空間から Prometheus メトリックを表示するクエリの例を次に示します。

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

prometheus のデータは、名前で直接照会することもできます。

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>クエリ構成エラーまたはスクラップ エラー

構成エラーまたはスクラップ エラーを調査するために、次のクエリの例では、`KubeMonAgentEvents` テーブルから情報イベントが返されます。

```
KubeMonAgentEvents | where Level != "Info" 
```

出力では、次の例のような結果が示されます。

![エージェントからの情報イベントのクエリ結果をログに記録する](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>次のステップ

Container insights にはアラートの定義済みのセットは含まれていません。 [Container insights を使用したパフォーマンス アラートの作成](./container-insights-log-alerts.md)に関するページを読んで、CPU やメモリの使用率が高い場合に推奨アラートを作成し、DevOps や運用プロセスまたは手順をサポートする方法について学習します。