---
title: Azure Queue Storage 監視データのリファレンス
description: Azure Queue Storage からのデータを監視するためのログとメトリックのリファレンス。
author: normesta
services: azure-monitor
ms.author: normesta
ms.date: 10/02/2020
ms.topic: reference
ms.service: azure-monitor
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 95f20737b044140fe12ea939e71cd2397cb4826d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576683"
---
# <a name="azure-queue-storage-monitoring-data-reference"></a>Azure Queue Storage 監視データのリファレンス

Azure Storage の監視データの収集と分析の詳細については、「[Azure Storage の監視](monitor-queue-storage.md)」を参照してください。

## <a name="metrics"></a>メトリック

次の表は、Azure Storage に関して収集されるプラットフォーム メトリックを示しています。

### <a name="capacity-metrics"></a>容量メトリック

容量メトリックの値は毎日 (最大 24 時間) 更新されます。 時間グレインは、メトリック値が提供される時間間隔を定義します。 すべての容量メトリックに対して、1 時間 (PT1H) の時間グレインがサポートされます。

Azure Storage は、Azure Monitor で次の容量メトリックを提供します。

#### <a name="account-level-capacity-metrics"></a>アカウントレベルの容量メトリック

[!INCLUDE [Account-level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="queue-storage-metrics"></a>Queue Storage メトリック

この表は、[Queue Storage のメトリック](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsqueueservices)を示しています。

| メトリック | 説明 |
| ------------------- | ----------------- |
| **QueueCapacity** | ストレージ アカウントによって使用されている Queue Storage の量。 <br><br> 単位: `Bytes` <br> 集計の種類: `Average` <br> 値の例: `1024` |
| **QueueCount** | ストレージ アカウントのキューの数。 <br><br> 単位: `Count` <br> 集計の種類: `Average` <br> 値の例: `1024` |
| **QueueMessageCount** | ストレージ アカウント内のキュー メッセージの概数。 <br><br> 単位: `Count` <br> 集計の種類: `Average` <br> 値の例: `1024` |

### <a name="transaction-metrics"></a>トランザクション メトリック

トランザクション メトリックは、ストレージ アカウントへの要求ごとに、Azure Storage から Azure Monitor に出力されます。 ストレージ アカウントにアクティビティがない場合、その間はトランザクション メトリックのデータは存在しません。 すべてのトランザクション メトリックは、アカウントと Queue Storage の両方のサービス レベルで使用できます。 時間グレインは、メトリック値が提供される時間間隔を定義します。 すべてのトランザクション メトリックに対してサポートされている時間グレインは PT1H と PT1M です。

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>メトリックのディメンション

Azure Storage では、Azure Monitor の次のメトリック ディメンションがサポートされます。

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>リソース ログ (プレビュー)

> [!NOTE]
> Azure Monitor の Azure Storage ログはパブリック プレビュー段階にあり、すべてのパブリック クラウド リージョンでプレビュー テスト用に使用できます。 このプレビューでは、汎用 v1 と汎用 v2 ストレージ アカウントの BLOB (Azure Data Lake Storage Gen2 を含む)、ファイル、キュー、テーブル、Premium ストレージ アカウントに対してログが有効になります。 クラシック ストレージ アカウントはサポートされていません。

次の表に、Azure Monitor ログまたは Azure Storage で Azure Storage リソース ログが収集される場合のそれらのプロパティを示します。 プロパティによって、操作、サービス、操作の実行に使用された承認の種類が示されます。

### <a name="fields-that-describe-the-operation"></a>操作を説明するフィールド

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>操作がどのように認証されたかを説明するフィールド

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>サービスを説明するフィールド

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>関連項目

- Azure Queue Storage 監視の詳細については、「[Azure Queue Storage の監視](monitor-queue-storage.md)」を参照してください。
- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。
