---
title: Azure Storage メトリックの移行 | Microsoft Docs
description: 従来のメトリックを Azure Monitor によって管理される新しいメトリックに移行する方法について説明します。
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 03/30/2018
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: f8a73bb6e86a187a504c2d36098001cfd90015f9
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55454928"
---
# <a name="azure-storage-metrics-migration"></a>Azure Storage メトリックの移行

Azure のモニター エクスペリエンスの統合戦略に合わせて、Azure Storage はメトリックを Azure Monitor プラットフォームに統合します。 今後、Azure ポリシーに基づいて、従来のメトリックのサービスは事前に通知されたうえで終了します。 従来のストレージ メトリックに依存している場合は、メトリック情報を維持するためにサービス終了日より前に移行する必要があります。

この記事では、従来のメトリックから新しいメトリックに移行する方法について説明します。

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Azure Storage によって管理されている従来のメトリックを理解する

Azure Storage では、従来のメトリック値を収集し、集計して、同じストレージ アカウント内の $Metric テーブルに保存します。 Azure portal を使用して、監視グラフを設定することができます。 また、Azure Storage SDK を使用して、スキーマに基づく $Metric テーブルからデータを読み取ることもできます。 詳細については、「[Storage Analytics](./storage-analytics.md)」を参照してください。

従来のメトリックは、容量メトリックを Azure BLOB Storage に対してのみ提供します。 従来のメトリックは、トランザクション メトリックを BLOB Storage、Table ストレージ、Azure Files、および Queue Storage に対して提供します。

従来のメトリックは、フラット スキーマで設計されています。 このデザインでは、メトリックをトリガーするトラフィック パターンがない場合はメトリック値が 0 になります。 たとえば、ライブ トラフィックからストレージ アカウントへのサーバー タイムアウト エラーを受信しない場合でも、$Metric テーブル内の **ServerTimeoutError** 値は 0 に設定されます。

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Azure Monitor によって管理される新しいメトリックを理解する

新しいストレージ メトリックでは、Azure Storage は Azure Monitor バックエンドにメトリック データを出力します。 Azure Monitor は、ポータルやデータ インジェストからのデータを含む、統合監視エクスペリエンスを提供します。 詳しくは、こちらの[記事](../../monitoring-and-diagnostics/monitoring-overview-metrics.md)をご覧ください。

新しいメトリックは、容量メトリックとトランザクション メトリックを BLOB、Table、File、Queue、Premium Storage に対して提供します。

マルチディメンションは、Azure Monitor によって提供される機能の 1 つです。 Azure Storage では、新しいメトリック スキーマの定義にこの設計が採用されています。 メトリックでサポートされているディメンションについては、「[Azure Monitor の Azure Storage メトリック](./storage-metrics-in-azure-monitor.md)」で詳細を確認できます。 マルチディメンション設計では、インジェストの帯域幅とメトリック保存の容量の両方のコスト効率が向上します。 そのため、トラフィックによって関連メトリックがトリガーされていない場合、関連メトリック データは生成されません。 たとえば、トラフィックによってサーバー タイムアウト エラーがトリガーされていない場合、ディメンション **ResponseType** が **ServerTimeoutError** と等しいメトリック **Transactions** の値をクエリしたときに Azure Monitor はデータを返しません。

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>従来のメトリックと新しいメトリック間のメトリック マッピング

メトリック データをプログラムで読み取る場合は、プログラム内で新しいメトリック スキーマを採用する必要があります。 変更をより深く理解するために、次の表に示すマッピングを参照できます。

**容量メトリック**

| 従来のメトリック | 新しいメトリック |
| ------------------- | ----------------- |
| **Capacity**            | ディメンション **BlobType** が **BlockBlob** または **PageBlob** と等しい **BlobCapacity** |
| **ObjectCount**        | ディメンション **BlobType** が **BlockBlob** または **PageBlob** と等しい **BlobCount** |
| **ContainerCount**      | **ContainerCount** |

次のメトリックは、従来のメトリックでサポートされていない新しいサービスです。
* **TableCapacity**
* **TableCount**
* **TableEntityCount**
* **QueueCapacity**
* **QueueCount**
* **QueueMessageCount**
* **FileCapacity**
* **FileCount**
* **FileShareCount**
* **UsedCapacity**

**トランザクション メトリック**

| 従来のメトリック | 新しいメトリック |
| ------------------- | ----------------- |
| **AnonymousAuthorizationError** | ディメンション **ResponseType** が **AuthorizationError** と等しく、ディメンション **Authentication** が **Anonymous** と等しいトランザクション |
| **AnonymousClientOtherError** | ディメンション **ResponseType** が **ClientOtherError** と等しく、ディメンション **Authentication** が **Anonymous** と等しいトランザクション |
| **AnonymousClientTimeoutError** | ディメンション **ResponseType** が **ClientTimeoutError** と等しく、ディメンション **Authentication** が **Anonymous** と等しいトランザクション |
| **AnonymousNetworkError** | ディメンション **ResponseType** が **NetworkError** と等しく、ディメンション **Authentication** が **Anonymous** と等しいトランザクション |
| **AnonymousServerOtherError** | ディメンション **ResponseType** が **ServerOtherError** と等しく、ディメンション **Authentication** が **Anonymous** と等しいトランザクション |
| **AnonymousServerTimeoutError** | ディメンション **ResponseType** が **ServerTimeoutError** と等しく、ディメンション **Authentication** が **Anonymous** と等しいトランザクション |
| **AnonymousSuccess** | ディメンション **ResponseType** が **Success** と等しく、ディメンション **Authentication** が **Anonymous** と等しいトランザクション |
| **AnonymousThrottlingError** | ディメンション **ResponseType** が **ClientThrottlingError** または **ServerBusyError** と等しく、ディメンション **Authentication** が **Anonymous** と等しいトランザクション |
| **AuthorizationError** | ディメンション **ResponseType** が **AuthorizationError** と等しい Transactions |
| **可用性** | **可用性** |
| **AverageE2ELatency** | **SuccessE2ELatency** |
| **AverageServerLatency** | **SuccessServerLatency** |
| **ClientOtherError** | ディメンション **ResponseType** が **ClientOtherError** と等しい Transactions |
| **ClientTimeoutError** | ディメンション **ResponseType** が **ClientTimeoutError** と等しい Transactions |
| **NetworkError** | ディメンション **ResponseType** が **NetworkError** と等しい Transactions |
| **PercentAuthorizationError** | ディメンション **ResponseType** が **AuthorizationError** と等しい Transactions |
| **PercentClientOtherError** | ディメンション **ResponseType** が **ClientOtherError** と等しい Transactions |
| **PercentNetworkError** | ディメンション **ResponseType** が **NetworkError** と等しい Transactions |
| **PercentServerOtherError** | ディメンション **ResponseType** が **ServerOtherError** と等しい Transactions |
| **PercentSuccess** | ディメンション **ResponseType** が **Success** と等しい Transactions |
| **PercentThrottlingError** | ディメンション **ResponseType** が **ClientThrottlingError** または **ServerBusyError** と等しい Transactions |
| **PercentTimeoutError** | ディメンション **ResponseType** が **ServerTimeoutError** と等しいか、**ResponseType** が **ClientTimeoutError** と等しい Transactions |
| **SASAuthorizationError** | ディメンション **ResponseType** が **AuthorizationError** と等しく、ディメンション **Authentication** が **SAS** と等しいトランザクション |
| **SASClientOtherError** | ディメンション **ResponseType** が **ClientOtherError** と等しく、ディメンション **Authentication** が **SAS** と等しいトランザクション |
| **SASClientTimeoutError** | ディメンション **ResponseType** が **ClientTimeoutError** と等しく、ディメンション **Authentication** が **SAS** と等しいトランザクション |
| **SASNetworkError** | ディメンション **ResponseType** が **NetworkError** と等しく、ディメンション **Authentication** が **SAS** と等しいトランザクション |
| **SASServerOtherError** | ディメンション **ResponseType** が **ServerOtherError** と等しく、ディメンション **Authentication** が **SAS** と等しいトランザクション |
| **SASServerTimeoutError** | ディメンション **ResponseType** が **ServerTimeoutError** と等しく、ディメンション **Authentication** が **SAS** と等しいトランザクション |
| **SASSuccess** | ディメンション **ResponseType** が **Success** と等しく、ディメンション **Authentication** が **SAS** と等しいトランザクション |
| **SASThrottlingError** | ディメンション **ResponseType** が **ClientThrottlingError** または **ServerBusyError** と等しく、ディメンション **Authentication** が **SAS** と等しいトランザクション |
| **ServerOtherError** | ディメンション **ResponseType** が **ServerOtherError** と等しい Transactions |
| **ServerTimeoutError** | ディメンション **ResponseType** が **ServerTimeoutError** と等しい Transactions |
| **Success** | ディメンション **ResponseType** が **Success** と等しい Transactions |
| **ThrottlingError** | ディメンション **ResponseType** が **ClientThrottlingError** または **ServerBusyError** と等しい **Transactions**|
| **TotalBillableRequests** | **トランザクション** |
| **TotalEgress** | **エグレス** |
| **TotalIngress** | **イングレス** |
| **TotalRequests** | **トランザクション** |

## <a name="faq"></a>FAQ

### <a name="how-should-i-migrate-existing-alert-rules"></a>既存のアラート ルールを移行する必要はありますか?

従来のストレージ メトリックに基づいてクラシック アラート ルールを作成してある場合は、新しいメトリック スキーマに基づいて新しいアラート ルールを作成する必要があります。

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>既定では、新しいメトリック データは同じストレージ アカウントに格納されますか?

いいえ。 メトリック データをストレージ アカウントにアーカイブするには、[Azure Monitor Diagnostic Setting API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate) を使用します。

## <a name="next-steps"></a>次の手順

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Azure Monitor の Storage メトリック](./storage-metrics-in-azure-monitor.md)
