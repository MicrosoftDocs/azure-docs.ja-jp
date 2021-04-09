---
title: Azure Table ストレージ監視データのリファレンス | Microsoft Docs
description: Azure Table ストレージからのデータを監視するためのログとメトリックのリファレンス。
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: ad56b6af9a9071812ad6fa581954010df3b6b5d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574838"
---
# <a name="azure-table-storage-monitoring-data-reference"></a>Azure Table ストレージ監視データのリファレンス

Azure Storage の監視データの収集と分析の詳細については、「[Azure Storage の監視](monitor-table-storage.md)」を参照してください。

## <a name="metrics"></a>メトリック

次の表は、Azure Storage に関して収集されるプラットフォーム メトリックを示しています。 

### <a name="capacity-metrics"></a>容量メトリック

容量メトリックの値は 1 時間ごとに Azure Monitor に送信され、 毎日更新されます。 時間グレインは、メトリック値が提供される時間間隔を定義します。 すべての容量メトリックに対して、1 時間 (PT1H) の時間グレインがサポートされます。

Azure Storage は、Azure Monitor で次の容量メトリックを提供します。

#### <a name="account-level"></a>アカウント レベル

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="table-storage"></a>テーブル ストレージ

この表は、[Table ストレージのメトリック](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountstableservices)を示しています。

| メトリック | 説明 |
| ------------------- | ----------------- |
| TableCapacity | ストレージ アカウントによって使用されている Table Storage の量。 <br/><br/> 単位:バイト <br/> 集計の種類:Average <br/> 値の例:1024 |
| TableCount   | ストレージ アカウントのテーブルの数。 <br/><br/> 単位:Count <br/> 集計の種類:Average <br/> 値の例:1024 |
| TableEntityCount | ストレージ アカウントのテーブル エンティティの数。 <br/><br/> 単位:Count <br/> 集計の種類:Average <br/> 値の例:1024 |

### <a name="transaction-metrics"></a>トランザクション メトリック

トランザクション メトリックは、ストレージ アカウントへの要求ごとに、Azure Storage から Azure Monitor に出力されます。 ストレージ アカウントにアクティビティがない場合、その間はトランザクション メトリックのデータは存在しません。 すべてのトランザクション メトリックは、アカウントと Table ストレージの両方のサービス レベルで使用できます。 時間グレインは、メトリック値が提供される時間間隔を定義します。 すべてのトランザクション メトリックに対してサポートされている時間グレインは PT1H と PT1M です。

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

- Azure Storage の監視の詳細については、「[Azure Table ストレージの監視](monitor-table-storage.md)」を参照してください。
- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。