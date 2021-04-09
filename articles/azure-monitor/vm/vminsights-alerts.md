---
title: VM Insights からのアラート
description: VM Insights によって収集されるパフォーマンス データからアラート ルールを作成する方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/10/2020
ms.openlocfilehash: 06c58b7081ed68724a3c907f8fe76dcf5f7b8057
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046807"
---
# <a name="how-to-create-alerts-from-vm-insights"></a>VM Insights からアラートを作成する方法
[Azure Monitor のアラート](../alerts/alerts-overview.md)では、監視データの興味深いデータやパターンを事前に通知します。 VM Insights には事前に構成されたアラート ルールは含まれませんが、収集されるデータに基づいて独自のものを作成することができます。 この記事では、サンプル クエリのセットを含む、アラート ルールの作成に関するガイダンスを提供します。

> [!IMPORTANT]
> この記事で説明するアラートは、VM Insights によって収集されたデータからのログ クエリに基づいています。 これは、現在パブリック プレビュー中の機能である [Azure Monitor for VM ゲストの正常性](vminsights-health-overview.md)によって作成されたアラートとは異なります。 この機能は近日中に一般公開され、アラートに関するガイダンスが統合されます。


## <a name="alert-rule-types"></a>アラート ルールの種類
Azure Monitor には、アラートの作成に使用されるデータに基づく[さまざまな種類のアラート ルール](../alerts/alerts-overview.md#what-you-can-alert-on)があります。 VM Insights によって収集されるすべてのデータは、[ログ アラート](../alerts/alerts-log.md)をサポートする Azure Monitor Logs に格納されます。 現在、VM Insights から収集されるパフォーマンス データで[メトリック アラート](../alerts/alerts-log.md)を使用することはできません。これは、データが Azure Monitor メトリックに収集されないためです。 メトリック アラートのデータを収集するには、Windows VM 用の[診断拡張機能](../agents/diagnostics-extension-overview.md)、または Linux VM 用の [Telegraf エージェント](../essentials/collect-custom-metrics-linux-telegraf.md)をインストールして、パフォーマンス データをメトリックに収集します。

Azure Monitor には、次の 2 種類のログ アラートがあります。

- [結果の数アラート](../alerts/alerts-unified-log.md#count-of-the-results-table-rows)。少なくとも指定された数のレコードがクエリから返された場合に、単一のアラートが作成されます。 これらは、[Log Analytics エージェント](../agents/log-analytics-agent.md)によって収集される Windows および Syslog イベントなどの数値以外のデータの場合、あるいは複数のコンピューター間のパフォーマンスの傾向を分析する場合に適しています。
- [メトリック測定アラート](../alerts/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value)。値がアラート ルールで定義されているしきい値を超える、クエリ内のレコードごとに個別のアラートが作成されます。 これらのアラート ルールは、VM Insights によって収集されるパフォーマンス データに適しています。これは、コンピューターごとに個別のアラートを作成できるためです。


## <a name="alert-rule-walkthrough"></a>アラート ルールのチュートリアル
このセクションでは、VM Insights からのパフォーマンス データを使用する、メトリック測定アラート ルールの作成について見ていきます。 さまざまなログ クエリでこの基本プロセスを使用して、さまざまなパフォーマンス カウンターに対してアラートを生成することができます。

まず、「[Azure Monitor を使用してログ アラートを作成、表示、管理する](../alerts/alerts-log.md)」の手順に従って、新しいアラート ルールを作成します。 **[リソース]** については、サブスクリプションで Azure Monitor VMs によって使用される Log Analytics ワークスペースを選択します。 ログ アラート ルールのターゲット リソースは常に Log Analytics ワークスペースであるため、ログ クエリには特定の仮想マシンまたは仮想マシン スケール セットのフィルターを含める必要があります。 

アラート ルールの **[条件]** については、[以下のセクション](#sample-alert-queries)のクエリのいずれかを、**検索クエリ** として使用します。 クエリでは、*AggregatedValue* という数値プロパティを返す必要があります。 しきい値を超える仮想マシンごとに個別のアラートを作成できるように、コンピューター別にデータをまとめる必要があります。

**[アラート ロジック]** で、 **[メトリック測定]** を選択してから、 **[しきい値]** を指定します。 **[アラートをトリガーする基準]** には、アラートが作成されるまでにしきい値を超える必要がある回数を指定します。 たとえば、プロセッサでしきい値を 1 回超えてから通常の状態に戻る場合はおそらく気になりませんが、複数の連続した測定値でしきい値を超える状態が続く場合は気になります。

**[評価基準]** セクションでは、クエリの実行頻度とクエリの時間枠を定義します。 以下に示す例では、クエリは 15 分ごとに実行され、過去 15 分間に収集されたパフォーマンス値が評価されます。


![メトリック測定のアラート ルール](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>アラート クエリのサンプル
次のクエリは、VM Insights によって収集されるパフォーマンス データを使用するメトリック測定のアラート ルールで利用できます。 それぞれコンピューター別にデータがまとめられるため、値がしきい値を超えるコンピューターごとにアラートが作成されます。

### <a name="cpu-utilization"></a>CPU 使用率

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-mb"></a>使用可能なメモリ (MB)

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-percentage"></a>使用可能なメモリ (%)

```kusto
InsightsMetrics 
| where Origin == "vm.azm.ms" 
| where Namespace == "Memory" and Name == "AvailableMB" 
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])
| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0 
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---all-disks-on-each-computer"></a>使用されている論理ディスク - 各コンピューター上のすべてのディスク

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---individual-disks"></a>使用されている論理ディスク - 個々のディスク

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk
```

### <a name="logical-disk-iops"></a>論理ディスクの IOPS

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk
```

### <a name="logical-disk-data-rate"></a>論理ディスクのデータ速度

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk
```

### <a name="network-interfaces-bytes-received---all-interfaces"></a>ネットワーク インターフェイスの受信バイト数 - すべてのインターフェイス

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="network-interfaces-bytes-received---individual-interfaces"></a>ネットワーク インターフェイスの受信バイト数 - 個々のインターフェイス

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="network-interfaces-bytes-sent---all-interfaces"></a>ネットワーク インターフェイスの送信バイト数 - すべてのインターフェイス

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="network-interfaces-bytes-sent---individual-interfaces"></a>ネットワーク インターフェイスの送信バイト数 - 個々のインターフェイス

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="virtual-machine-scale-set"></a>仮想マシン スケール セット
サブスクリプション ID、リソース グループ、仮想マシン スケール セットの名前で変更します。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/my-vm-scaleset" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="specific-virtual-machine"></a>特定の仮想マシン
サブスクリプション ID、リソース グループ、VM 名で変更します。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId =~ "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m)
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-subscription"></a>サブスクリプション内のすべてのコンピューティング リソースの CPU 使用率
サブスクリプション ID で変更します。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/")
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-resource-group"></a>リソース グループ内のすべてのコンピューティング リソースの CPU 使用率
サブスクリプション ID とリソース グループで変更します。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/"
or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId

```

## <a name="next-steps"></a>次のステップ

- [Azure Monitor のアラート](../alerts/alerts-overview.md)について、さらに詳しく学習します。
- [VM Insights からのデータを使用するログ クエリ](vminsights-log-search.md)について、さらに詳しく学習します。
