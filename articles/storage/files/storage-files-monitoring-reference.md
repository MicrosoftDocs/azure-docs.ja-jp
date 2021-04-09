---
title: Azure Files 監視データのリファレンス | Microsoft Docs
description: Azure Files からのデータを監視するためのログとメトリックのリファレンス。
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 4575709a4fa7067b02228036fb2e2b4a60844e21
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100592312"
---
# <a name="azure-files-monitoring-data-reference"></a>Azure Files 監視データのリファレンス

Azure Files の監視データの収集と分析の詳細については、「[Azure Files の監視](storage-files-monitoring.md)」を参照してください。

## <a name="metrics"></a>メトリック

次の表は、Azure Files に関して収集されるプラットフォーム メトリックを示しています。 

### <a name="capacity-metrics"></a>容量メトリック

容量メトリックの値は毎日 (最大 24 時間) 更新されます。 時間グレインは、メトリック値が提供される時間間隔を定義します。 すべての容量メトリックに対して、1 時間 (PT1H) の時間グレインがサポートされます。

Azure Files は、Azure Monitor で次の容量メトリックを提供します。

#### <a name="account-level"></a>アカウント レベル

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="azure-files"></a>Azure Files

次の表は、[Azure Files メトリック](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices)を示しています。

| メトリック | 説明 |
| ------------------- | ----------------- |
| FileCapacity | ストレージ アカウントによって使用されている File ストレージの量。 <br/><br/> 単位:バイト <br/> 集計の種類:Average <br/> 値の例:1024 |
| FileCount   | ストレージ アカウントのファイルの数。 <br/><br/> 単位:Count <br/> 集計の種類:Average <br/> 値の例:1024 |
| FileShareCapacityQuota | Azure Files サービスで使用できる記憶域の容量の上限 (バイト単位)。 <br/><br/> 単位:バイト <br/> 集計の種類:Average <br/> 値の例:1024|
| FileShareCount | ストレージ アカウントのファイル共有の数。 <br/><br/> 単位:Count <br/> 集計の種類:Average <br/> 値の例:1024 |
| FileShareProvisionedIOPS | ファイル共有でプロビジョニングされた IOPS の数。 このメトリックは、Premium ファイル ストレージにのみ適用されます。 <br/><br/> 単位: バイト <br/> 集計の種類:Average |
| FileShareSnapshotCount | ストレージ アカウントの Azure Files サービス内の共有に存在するスナップショットの数。 <br/><br/> 単位: カウント <br/> 集計の種類:Average | 
|FileShareSnapshotSize|ストレージ アカウントの Azure Files サービスのスナップショットによって使用されている記憶域の容量。 <br/><br/> 単位:バイト <br/> 集計の種類:Average|

### <a name="transaction-metrics"></a>トランザクション メトリック

トランザクション メトリックは、ストレージ アカウントへの要求ごとに、Azure Storage から Azure Monitor に出力されます。 ストレージ アカウントにアクティビティがない場合、その間はトランザクション メトリックのデータは存在しません。 すべてのトランザクション メトリックは、アカウントと Azure Files の両方のサービス レベルで使用できます。 時間グレインは、メトリック値が提供される時間間隔を定義します。 すべてのトランザクション メトリックに対してサポートされている時間グレインは PT1H と PT1M です。

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>メトリックのディメンション

Azure Files では、Azure Monitor の次のメトリック ディメンションがサポートされます。

> [!NOTE] 
> ファイル共有ディメンションは、Standard ファイル共有では使用できません (Premium ファイル共有のみ)。 Standard ファイル共有を使用する場合、提供されるメトリックは、ストレージ アカウント内のすべてのファイル共有に対するものです。 Standard ファイル共有の共有ごとのメトリックを取得するには、ストレージ アカウントごとに 1 つのファイル共有を作成します。

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

- Azure Storage 監視の詳細については、「[Azure Files の監視](storage-files-monitoring-reference.md)」を参照してください。
- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。