---
title: Azure Storage Analytics を使用したログとメトリック データの収集 | Microsoft Docs
description: Storage Analytics では、すべてのストレージ サービスのメトリック データを追跡し、BLOB、キュー、Table Storage のログを収集できます。
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: eb85f8c756e7373a8dedabbce362cfa534e56fd8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57849178"
---
# <a name="storage-analytics"></a>Storage Analytics

Azure Storage Analytics では、ログが記録され、ストレージ アカウントのメトリック データを得ることができます。 このデータを使用して、要求のトレース、使用傾向の分析、ストレージ アカウントの問題の診断を行うことができます。

Storage Analytics を使用するには、監視するサービスごとに Storage Analytics を個別に有効にする必要があります。 これは、[Azure portal](https://portal.azure.com) から有効にできます。 詳細については、「[Azure portal でのストレージ アカウントの監視](storage-monitor-storage-account.md)」をご覧ください。 また、プログラムから REST API またはクライアント ライブラリを使用して有効にすることもできます。 各サービスに対して Storage Analytics を有効にするには、[Set Blob Service Properties](/rest/api/storageservices/set-blob-service-properties)、[Set Queue Service Properties](/rest/api/storageservices/set-queue-service-properties)、[Set Table Service Properties](/rest/api/storageservices/set-table-service-properties)、[Set File Service Properties](/rest/api/storageservices/Get-File-Service-Properties) の各操作を使用します。

集計データは、既知の BLOB (ログの場合) と既知のテーブル (メトリックの場合) に格納されます。集計データには、Blob service と Table service の API を使用してアクセスできます。

ストレージ アカウントの合計の制限とは別に、Storage Analytics には、格納されたデータの量に関して 20 TB の制限があります。 ストレージ アカウントの制限の詳細については、「 [Azure ストレージのスケーラビリティおよびパフォーマンスのターゲット](storage-scalability-targets.md)」を参照してください。

Storage Analytics や他のツールを使用した Azure Storage 関連の問題の特定、診断、トラブルシューティングに関する詳しいガイドについては、「 [Microsoft Azure ストレージの監視、診断、およびトラブルシューティング](storage-monitoring-diagnosing-troubleshooting.md)」をご覧ください。

## <a name="billing-for-storage-analytics"></a>Storage Analytics の課金

すべてのメトリック データは、ストレージ アカウントのサービスによって書き込まれます。 したがって、Storage Analytics によって実行される個々の書き込み操作には料金が発生します。 加えて、メトリック データに費やされるストレージの使用量も課金対象となります。

Storage Analytics によって実行される次の操作には料金が発生します。

* ログの BLOB の作成要求
* メトリックのテーブル エンティティの作成要求

データ保持ポリシーを構成した場合、Storage Analytics が古いログ データやメトリック データを削除しますが、その際の削除トランザクションに対する料金は発生しません。 ただし、クライアントからの削除トランザクションは課金対象となります。 保持ポリシーの詳細については、「 [Storage Analytics のデータ保持ポリシーの設定](https://msdn.microsoft.com/library/azure/hh343263.aspx)」をご覧ください。

### <a name="understanding-billable-requests"></a>課金の対象となる要求について

アカウントのストレージ サービスに対して実行されるそれぞれの要求は、課金対象の要求とそうでない要求とに分けられます。 Storage Analytics は、サービスに対するすべての要求をそれぞれログに記録します。たとえば、要求がどのように処理されたかを示すステータス メッセージも記録されます。 同様に、Storage Analytics は、サービスに対するメトリックと、そのサービスの API 操作に対するメトリックを保存しています (特定のステータス メッセージの数やパーセンテージなど)。 これらの機能をうまく組み合わせて利用すると、課金対象の要求を分析したり、アプリケーションを改善したり、サービスに対する要求の問題を診断したりできます。 課金の詳細については、「[Understanding Azure Storage Billing - Bandwidth, Transactions, and Capacity (Azure Storage の課金について - 帯域幅、トランザクション、容量)](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)」をご覧ください。

Storage Analytics のデータで課金対象の要求を調べるときには、「 [Storage Analytics によって記録される操作やステータス メッセージ](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) 」の表が参考になります。 手元のログ データとメトリック データをステータス メッセージと照らし合わせながら、特定の要求が課金対象であったかどうかを確認できます。 この表は、ストレージ サービスまたは特定の API 操作の可用性を調べるときにも利用できます。

## <a name="next-steps"></a>次の手順
* [Azure Portal でのストレージ アカウントの監視](storage-monitor-storage-account.md)
* [Storage Analytics のメトリック](storage-analytics-metrics.md)
* [Storage Analytics のログ記録](storage-analytics-logging.md)
