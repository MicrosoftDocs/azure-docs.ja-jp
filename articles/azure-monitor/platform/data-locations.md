---
title: Azure Monitor における監視データの場所 | Microsoft Docs
description: Azure Monitor データ プラットフォームを含め、監視データの保存先となる、Azure 内のさまざまな場所について説明します。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/21/2019
ms.openlocfilehash: bb7e92a676115d784bd19714178b3bd442798e26
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515548"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Azure Monitor における監視データの場所

Azure Monitor は、「[Azure Monitor データ プラットフォーム](data-platform.md)」で取り上げた[ログ](data-platform-logs.md)と[メトリック](data-platform-metrics.md)という[データ プラットフォーム](data-platform.md)をベースにしています。 ただし、Azure リソースからの監視データが他の場所に書き込まれ、その後 Azure Monitor にコピーされたり別のシナリオに利用されたりすることもあります。 

## <a name="monitoring-data-locations"></a>監視データの場所

次の表は、Azure における監視データの送信先となるさまざまな場所とそこにアクセスする各種の方法を示しています。

| 場所 | 説明 | アクセスの方法 |
|:---|:---|:---|:--|
| Azure Monitor メトリック | タイムスタンプ付きのデータを分析するために最適化された時系列データベース。 | [メトリックス エクスプローラー](metrics-getting-started.md)<br>[Azure Monitor メトリックス API](/rest/api/monitor/metrics) |
| Azure Monitor ログ    | 強力な分析エンジンと豊富なクエリ言語を備えた Azure Data Explorer に基づく Log Analytics ワークスペース。 | [Log Analytics](../log-query/log-query-overview.md)<br>[Log Analytics API](https://dev.loganalytics.io/)<br>[Application Insights API](https://dev.applicationinsights.io/reference/get-query) |
| アクティビティ ログ | アクティビティ ログからのデータがその利便性を最も発揮するのは、Azure Monitor ログに送信されて、他のデータと一緒に分析されたときですが、このデータだけを収集して直接 Azure portal で閲覧することもできます。 | [Azure Portal](./activity-log.md#view-the-activity-log)<br>[Azure Monitor イベント API](/rest/api/monitor/eventcategories) |
| Azure Storage | 一部のデータ ソースからは、直接 Azure Storage にデータが書き込まれます。そのようなデータ ソースでは、データをログに移動するための構成が必要となります。 Azure Storage にデータを送信して、アーカイブしたり、外部システムと統合したりすることもできます。  | [Storage Analytics](/rest/api/storageservices/storage-analytics)<br>[サーバー エクスプローラー](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) |
| Event Hubs | Azure Event Hubs にデータを送信して、他の場所にストリーム配信します。 | [Storage へのキャプチャ](../../event-hubs/event-hubs-capture-overview.md)  |
| VM に対する Azure Monitor | Azure Monitor for VMs は、その Azure portal における監視エクスペリエンスで使用されるカスタムの場所にワークロードの正常性データを格納します。 | [Azure Portal](../insights/vminsights-overview.md)<br>[Workload Monitor REST API](/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Azure Resource Health REST API](/rest/api/resourcehealth/)  |
| 警告 | Azure Monitor によって作成されるアラート。 | [Azure Portal](alerts-managing-alert-instances.md)<br>[Alerts Management REST API](/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>次のステップ

- [Azure Monitor によって収集される監視データ](data-sources.md)の各種ソースを確認します。
- [Azure Monitor によって収集される監視データの種類](data-platform.md)と、このデータを表示および分析する方法を確認します。
