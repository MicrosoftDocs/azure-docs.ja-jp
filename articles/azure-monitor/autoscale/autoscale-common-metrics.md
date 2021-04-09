---
title: 自動スケールの一般的なメトリック
description: Cloud Services、Virtual Machines、Web Apps の自動スケールに一般的に使用されるメトリックについて説明します。
ms.topic: conceptual
ms.date: 12/6/2016
ms.subservice: autoscale
ms.openlocfilehash: 4b763f39d3b88a7884e89dddbc2c483c1bb84d31
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101717776"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Azure Monitor の自動スケールの一般的なメトリック

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor の自動スケールを使用すると、テレメトリ データ (メトリック) に基づいて、実行インスタンス数を増減してスケールすることができます。 このドキュメントでは、一般的なメトリックについて説明します。必要に応じて利用してください。 Azure portal で、スケールに使用するリソースのメトリックを選択できます。 ただし、スケールには、さまざまなリソースのメトリックを選択できます。

Azure Monitor の自動スケーリングは、[Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/)、[Cloud Services](https://azure.microsoft.com/services/cloud-services/)、[App Service - Web Apps](https://azure.microsoft.com/services/app-service/web/)、および [API Management サービス](../../api-management/api-management-key-concepts.md)にのみ適用されます。 他の Azure サービスでは、異なるスケーリング方法が使用されています。

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Resource Manager ベースの VM のコンピューティング メトリック
既定では、Resource Manager ベースの Virtual Machines と Virtual Machine Scale Sets によって基本 (ホスト レベル) メトリックが出力されます。 また、Azure VM と VMSS の診断データ収集を構成する場合は、Azure 診断拡張機能によってゲスト OS パフォーマンス カウンター (一般に「ゲスト OS メトリック」と呼ばれる) も出力されます。  自動スケールの規則では、これらすべてのメトリックを使用します。

VMSS リソースに使用できるメトリックを確認するには、`Get MetricDefinitions` API/PoSH/CLI を使用します。

VM Scale Sets を使用し、特定のメトリックが一覧に表示されない場合は、診断拡張機能で "*無効になっている*" 可能性があります。

特定のメトリックが、目的の頻度でサンプリングまたは転送が行われない場合は、診断の構成を更新することができます。

上記のいずれかに該当する場合は、「[PowerShell を使用して Windows を実行している仮想マシンで Azure Diagnostics を有効にする](../../virtual-machines/extensions/diagnostics-windows.md)」を参照し、PowerShell で Azure VM 診断拡張機能を構成してメトリックを有効に変更してください。 また、この記事には、診断構成ファイルの例も紹介されています。

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Resource Manager ベースの Windows と Linux VM のホスト メトリック
以下のホスト レベルのメトリックは、Windows と Linux の両方のインスタンスで Azure VM と VMSS に既定で出力されます。 以下のメトリックでは Azure VM について説明しますが、ゲスト VM にインストールされているエージェントを介してではなく、Azure VM ホストから収集されます。 自動スケールの規則では、以下のメトリックを使用する場合があります。

- [Resource Manager ベースの Windows と Linux VM のホスト メトリック](../essentials/metrics-supported.md#microsoftcomputevirtualmachines)
- [Resource Manager ベースの Windows と Linux VM Scale Sets のホスト メトリック](../essentials/metrics-supported.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-for-resource-manager-based-windows-vms"></a>Resource Manager ベースの Windows VM のゲスト OS メトリック
診断拡張機能を使用して Azure で VM を作成すると、診断は有効になります。 診断拡張機能では、VM 内から取得した一連のメトリックを出力します。 これで、既定では出力されないメトリックから自動スケールができます。

メトリックの一覧を生成するには、PowerShell で次のコマンドを実行します。

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

次のメトリックのアラートを作成できます。

| メトリックの名前 | ユニット |
| --- | --- |
| \Processor(_Total)\% Processor Time |Percent |
| \Processor(_Total)\% Privileged Time |Percent |
| \Processor(_Total)\% User Time |Percent |
| \Processor Information(_Total)\Processor Frequency |Count |
| \System\Processes |Count |
| \Process(_Total)\Thread Count |Count |
| \Process(_Total)\Handle Count |Count |
| \Memory\% Committed Bytes In Use |Percent |
| \Memory\Available Bytes |バイト |
| \Memory\Committed Bytes |バイト |
| \Memory\Commit Limit |バイト |
| \Memory\Pool Paged Bytes |バイト |
| \Memory\Pool Nonpaged Bytes |バイト |
| \PhysicalDisk(_Total)\% Disk Time |Percent |
| \PhysicalDisk(_Total)\% Disk Read Time |Percent |
| \PhysicalDisk(_Total)\% Disk Write Time |Percent |
| \PhysicalDisk(_Total)\Disk Transfers/sec |CountPerSecond |
| \PhysicalDisk(_Total)\Disk Reads/sec |CountPerSecond |
| \PhysicalDisk(_Total)\Disk Writes/sec |CountPerSecond |
| \PhysicalDisk(_Total)\Disk Bytes/sec |BytesPerSecond |
| \PhysicalDisk(_Total)\Disk Read Bytes/sec |BytesPerSecond |
| \PhysicalDisk(_Total)\Disk Write Bytes/sec |BytesPerSecond |
| \PhysicalDisk(_Total)\Avg.ディスク キューの長さ |Count |
| \PhysicalDisk(_Total)\Avg.Disk Read Queue Length |Count |
| \PhysicalDisk(_Total)\Avg.Disk Write Queue Length |Count |
| \LogicalDisk(_Total)\% Free Space |Percent |
| \LogicalDisk(_Total)\Free Megabytes |Count |

### <a name="guest-os-metrics-linux-vms"></a>ゲスト OS メトリック Linux VM
診断拡張機能を使用して Azure で VM を作成すると、既定で診断は有効になります。

メトリックの一覧を生成するには、PowerShell で次のコマンドを実行します。

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 次のメトリックのアラートを作成できます。

| メトリックの名前 | ユニット |
| --- | --- |
| \Memory\AvailableMemory |バイト |
| \Memory\PercentAvailableMemory |Percent |
| \Memory\UsedMemory |バイト |
| \Memory\PercentUsedMemory |Percent |
| \Memory\PercentUsedByCache |Percent |
| \Memory\PagesPerSec |CountPerSecond |
| \Memory\PagesReadPerSec |CountPerSecond |
| \Memory\PagesWrittenPerSec |CountPerSecond |
| \Memory\AvailableSwap |バイト |
| \Memory\PercentAvailableSwap |Percent |
| \Memory\UsedSwap |バイト |
| \Memory\PercentUsedSwap |Percent |
| \Processor\PercentIdleTime |Percent |
| \Processor\PercentUserTime |Percent |
| \Processor\PercentNiceTime |Percent |
| \Processor\PercentPrivilegedTime |Percent |
| \Processor\PercentInterruptTime |Percent |
| \Processor\PercentDPCTime |Percent |
| \Processor\PercentProcessorTime |Percent |
| \Processor\PercentIOWaitTime |Percent |
| \PhysicalDisk\BytesPerSecond |BytesPerSecond |
| \PhysicalDisk\ReadBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\WriteBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\TransfersPerSecond |CountPerSecond |
| \PhysicalDisk\ReadsPerSecond |CountPerSecond |
| \PhysicalDisk\WritesPerSecond |CountPerSecond |
| \PhysicalDisk\AverageReadTime |Seconds |
| \PhysicalDisk\AverageWriteTime |Seconds |
| \PhysicalDisk\AverageTransferTime |Seconds |
| \PhysicalDisk\AverageDiskQueueLength |Count |
| \NetworkInterface\BytesTransmitted |バイト |
| \NetworkInterface\BytesReceived |バイト |
| \NetworkInterface\PacketsTransmitted |Count |
| \NetworkInterface\PacketsReceived |Count |
| \NetworkInterface\BytesTotal |バイト |
| \NetworkInterface\TotalRxErrors |Count |
| \NetworkInterface\TotalTxErrors |Count |
| \NetworkInterface\TotalCollisions |Count |

## <a name="commonly-used-app-service-server-farm-metrics"></a>一般的に使用される App Service (サーバー ファーム) のメトリック
HTTP キューの長さなどの一般的な Web サーバー メトリックに基づいて、自動スケールを実行することもできます。 そのメトリック名は **HttpQueueLength** です。  次のセクションに、使用できるサーバー ファーム (App Service) のメトリックを一覧表示します。

### <a name="web-apps-metrics"></a>Web Apps のメトリック
Web Apps メトリックの一覧を生成するには、PowerShell で次のコマンドを実行します。

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

これらのメトリックに基づいてアラートしたり、スケールすることができます。

| メトリックの名前 | ユニット |
| --- | --- |
| CpuPercentage |Percent |
| MemoryPercentage |Percent |
| DiskQueueLength |Count |
| HttpQueueLength |Count |
| BytesReceived |バイト |
| BytesSent |バイト |

## <a name="commonly-used-storage-metrics"></a>一般的に使用される Storage のメトリック
Storage キューの長さ (Storage キュー内のメッセージ数) に応じてスケールすることができます。 Storage キューの長さは特殊なメトリックであり、しきい値は、1 インスタンスあたりのメッセージ数です。 たとえば、2 つのインスタンスがあり、しきい値が 100 に設定されている場合、キュー内の合計メッセージ数が 200 になるとスケーリングが発生します。 インスタンスごとに 100 メッセージある場合や、120 と 80 メッセージ、またはその他の合計で最大 200 メッセージ以上となる組み合わせがあります。

この設定は、Azure Portal の **[設定]** ブレードで構成します。 VM Scale Sets の場合、Resource Manager テンプレートの [自動スケール] 設定で *ApproximateMessageCount* として *metricName* を使用するように更新し、*metricResourceUri* としてストレージ キューの ID を渡すことができます。

たとえば、従来のストレージ アカウントを使用すると、自動スケール設定 metricTrigger は、次のようになります。

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

(非従来の) ストレージ アカウントの場合、metricTrigger は、次のようになります。

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>一般的に使用される Service Bus のメトリック
Service Bus キューの長さ (Service Bus キュー内のメッセージ数) に応じてスケールすることができます。 Service Bus キューの長さは特殊なメトリックであり、しきい値は、1 インスタンスあたりのメッセージ数です。 たとえば、2 つのインスタンスがあり、しきい値が 100 に設定されている場合、キュー内の合計メッセージ数が 200 になるとスケーリングが発生します。 インスタンスごとに 100 メッセージある場合や、120 と 80 メッセージ、またはその他の合計で最大 200 メッセージ以上となる組み合わせがあります。

VM Scale Sets の場合、Resource Manager テンプレートの [自動スケール] 設定で *ApproximateMessageCount* として *metricName* を使用するように更新し、*metricResourceUri* としてストレージ キューの ID を渡すことができます。

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> Service Bus の場合、リソース グループの概念は存在しませんが、Azure Resource Manager でリージョンごとに既定のリソース グループが作成されます。 通常、リソース グループは 'Default-ServiceBus-[region]' 形式です。 たとえば、'Default-ServiceBus-EastUS'、'Default-ServiceBus-WestUS'、'Default-ServiceBus-AustraliaEast' などです。
>
>