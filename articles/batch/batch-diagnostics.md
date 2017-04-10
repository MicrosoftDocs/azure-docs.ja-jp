---
title: "Batch イベントの診断ログの有効化 - Azure | Microsoft Docs"
description: "プールやタスクなど Azure Batch アカウント リソースの診断ログ イベントを記録して分析します。"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: e14e611d-12cd-4671-91dc-bc506dc853e5
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6b6c548ca1001587e2b40bbe9ee2fcb298f40d72
ms.openlocfilehash: 16a13909079306256ded06f2100815c46ff562a3
ms.lasthandoff: 02/28/2017


---
# <a name="log-events-for-diagnostic-evaluation-and-monitoring-of-batch-solutions"></a>Batch ソリューションの診断の評価と監視のログ イベント

多くの Azure サービスと同じく、Batch サービスは特定のリソースに関するログ イベントをそのリソースの存続期間にわたって出力します。 Azure Batch 診断ログを有効にすると、プールやタスクといったリソースのイベントを記録できます。その後、診断の評価や監視にログを使用できます。 プールの作成、プールの削除、タスクの開始、タスクの完了などのイベントが Batch 診断ログに含まれます。

> [!NOTE]
> この記事では、ジョブやタスクの出力データではなく、Batch アカウント リソースそのもののイベントのログについて説明します。 ジョブやタスクの出力データの格納について詳しくは、「[Azure Batch のジョブとタスクの出力の保持](batch-task-output.md)」をご覧ください。
> 
> 

## <a name="prerequisites"></a>前提条件
* [Azure Batch アカウント](batch-account-create-portal.md)
* [Azure Storage アカウント](../storage/storage-create-storage-account.md#create-a-storage-account)
  
  Batch 診断ログを保持するには、Azure がログを格納するための Azure Storage アカウントを作成する必要があります。 このストレージ アカウントを指定してから、Batch アカウントの[診断ログを有効化](#enable-diagnostic-logging)します。 ログ収集を有効化するときに指定するストレージ アカウントは、[アプリケーション パッケージ](batch-application-packages.md)と[タスク出力の保持](batch-task-output.md)の記事で参照されているリンクされたストレージ アカウントとは異なります。
  
  > [!WARNING]
  > Azure Storage アカウントにデータを格納すると**課金**されます。 この記事で説明する診断ログも対象となります。 [ログ保持ポリシー](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)を作成する際にはこのことに注意してください。
  > 
  > 

## <a name="enable-diagnostic-logging"></a>診断ログの有効化
診断ログは Batch アカウントに関して既定では無効です。 監視する Batch アカウントごとに診断ログを明示的に有効化する必要があります。

[診断ログの収集を有効にする方法](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs)

「[Azure 診断ログの概要](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)」をすべて読み、ログを有効化する方法だけではなく、さまざまな Azure サービスでサポートされるログ カテゴリについて理解することをお勧めします。 たとえば、Azure Batch で現在サポートされている&1; つのログ カテゴリは**サービス ログ**です。

## <a name="service-logs"></a>サービス ログ
Azure Batch サービス ログには、プールやタスクなどの Batch リソースの存続期間中に Azure Batch サービスが出力するイベントが含まれます。 Batch が出力する各イベントは、指定のストレージ アカウントに JSON 形式で格納されます。 たとえば、次に示すのはサンプルの**プール作成イベント**の本文です。

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "4",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicated": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

各イベントの本文は、指定の Azure Storage アカウントに .json ファイルとして格納されます。 ログに直接アクセスしたい場合は、[ストレージ アカウント内の診断ログのスキーマ](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account)を確認してください。

## <a name="service-log-events"></a>サービス ログ イベント
現在、Batch サービスは、次のサービス ログ イベントを出力します。 この記事の最終更新の後でイベントが追加される場合があるため、この一覧は包括的でない可能性があります。

| **サービス ログ イベント** |
| --- |
| [プール作成][pool_create] |
| [プール削除の開始][pool_delete_start] |
| [プール削除の完了][pool_delete_complete] |
| [プールのサイズ変更の開始][pool_resize_start] |
| [プールのサイズ変更の完了][pool_resize_complete] |
| [タスク開始][task_start] |
| [タスク完了][task_complete] |
| [タスク失敗][task_fail] |

## <a name="next-steps"></a>次のステップ
診断ログ イベントを Azure Storage アカウントに格納する他に、Batch サービス ログ イベントを [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md) にストリーミングし、[Azure Log Analytics](../log-analytics/log-analytics-overview.md) に送信することもできます。

* [Azure 診断ログを Event Hubs にストリーミングする](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)
  
  非常に拡張性の高いデータ イングレス サービスである Event Hubs に Batch 診断イベントをストリームします。 Event Hubs は、毎秒数百万のイベントを取り込み、任意のリアルタイム分析プロバイダーを使用して変換および格納できます。
* [Log Analytics を使用した Azure 診断ログの分析](../log-analytics/log-analytics-azure-storage.md)
  
  診断ログを Log Analytics に送信して、Operations Management Suite (OMS) ポータルで分析したり、Power BI または Excel で分析するためにエクスポートしたりできます。

[pool_create]: https://msdn.microsoft.com/library/azure/mt743615.aspx
[pool_delete_start]: https://msdn.microsoft.com/library/azure/mt743610.aspx
[pool_delete_complete]: https://msdn.microsoft.com/library/azure/mt743618.aspx
[pool_resize_start]: https://msdn.microsoft.com/library/azure/mt743609.aspx
[pool_resize_complete]: https://msdn.microsoft.com/library/azure/mt743608.aspx
[task_start]: https://msdn.microsoft.com/library/azure/mt743616.aspx
[task_complete]: https://msdn.microsoft.com/library/azure/mt743612.aspx
[task_fail]: https://msdn.microsoft.com/library/azure/mt743607.aspx

