---
title: Storage Analytics メトリックから Azure Monitor メトリックに移動する | Microsoft Docs
description: Storage Analytics メトリック (クラシック メトリック) から Azure Monitor のメトリックに移行する方法について説明します。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: ff4c3c5ae8629879fca6e9e683a9c77d0e2f144b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100596109"
---
# <a name="transition-to-metrics-in-azure-monitor"></a>Azure Monitor のメトリックに移行する

**2023 年 8 月 31 日** をもって、Storage Analytics メトリック ("*クラシック メトリック*" とも呼ばれます) は廃止されます。 詳細については、[公式告知](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/)を参照してください。 クラシック メトリックを使用している場合は、その日より前に Azure Monitor のメトリックに移行するようにしてください。 この記事は、移行を行う際に役立ちます。 

## <a name="steps-to-complete-the-transition"></a>移行を行うための手順

Azure Monitor のメトリックに移行するには、次の方法をお勧めします。

1. クラシック メトリックと Azure Monitor のメトリックの間の[重要な違い](#key-differences-between-classic-metrics-and-metrics-in-azure-monitor)について確認します。 

2. 現在使用しているクラシック メトリックの一覧をまとめます。

3. 現在使用しているメトリックと同じデータが提供される [Azure Monitor のメトリック](#metrics-mapping-between-old-metrics-and-new-metrics)を特定します。 
   
4. メトリック データを表示するための[グラフ](/learn/modules/gather-metrics-blob-storage/2-viewing-blob-metrics-in-azure-portal)または[ダッシュボード](/learn/modules/gather-metrics-blob-storage/4-using-dashboards-in-the-azure-portal)を作成します。

   > [!NOTE]
   > Azure Monitor のメトリックは既定で有効になるので、メトリックのキャプチャを開始するために行う必要があることはありません。 ただし、それらのメトリックを表示するには、グラフまたはダッシュボードを作成する必要があります。 
 
5. クラシック ストレージ メトリックに基づく警告ルールを作成してある場合は、Azure Monitor のメトリックに基づいて[警告ルールを作成](../../azure-monitor/alerts/alerts-overview.md)します。 

6. Azure Monitor のすべてのメトリックを表示できるようになったら、クラシック ログをオフにすることができます。 

<a id="key-differences-between-classic-metrics-and-metrics-in-azure-monitor"></a>

## <a name="classic-metrics-vs-metrics-in-azure-monitor"></a>クラシック メトリックと Azure Monitor のメトリックの比較

ここでは、これら 2 つのメトリック プラットフォームの重要な違いについて説明します。

主な違いは、メトリックの管理方法です。 クラシック メトリックは Azure Storage によって管理されますが、Azure Monitor のメトリックは Azure Monitor によって管理されます。 クラシック メトリックを使用すると、Azure Storage によってメトリックの値が収集されて集計され、ストレージ アカウント内にあるテーブルに格納されます。 Azure Monitor のメトリックでは、Azure Storage によって Azure Monitor バックエンドにメトリック データが送信されます。 Azure Monitor で提供される統合監視エクスペリエンスには、Azure portal からのデータや、取り込まれたデータが含まれます。 

クラシック メトリックは Azure ストレージ アカウントに送信され、保管されます。 Azure Monitor メトリックは複数の場所に送信できます。 ストレージ アカウントはいずれかの場所にすることができますが、必須ではありません。  

メトリックでサポートされている限り、クラシック メトリックの **容量** メトリックは、Azure Blob Storage に対してのみ提供されます。 Azure Monitor のメトリックでは、BLOB、Table、File、Queue、Premium Storage に対して容量メトリックが提供されます。 クラシック メトリックの **トランザクション** メトリックは、BLOB、Table、Azure File、Queue Storage に対して提供されます。 Azure Monitor のメトリックは、その一覧に Premium Storage が追加されます。

アカウントのアクティビティによってメトリックがトリガーされない場合、クラシック メトリックではそのメトリックに対してゼロ (0) という値が表示されます。 Azure Monitor のメトリックの場合は、データが完全に除外され、レポートがわかりやすくなります。 たとえば、クラシック メトリックでは、サーバー タイムアウト エラーが報告されない場合、メトリック テーブルの `ServerTimeoutError` の値は 0 に設定されます。 Azure Monitor では、ディメンション `ResponseType` が `ServerTimeoutError` に等しいメトリック `Transactions` の値に対してクエリを実行すると、データは返されません。 

Azure Monitor のメトリックの詳細については、「[Azure Monitor のメトリック](../../azure-monitor/essentials/data-platform-metrics.md)」を参照してください。

<a id="metrics-mapping-between-old-metrics-and-new-metrics"></a>

## <a name="map-classic-metrics-to-metrics-in-azure-monitor"></a>クラシック メトリックを Azure Monitor のメトリックにマップする

 現在使用しているメトリックと同じデータが提供される Azure Monitor のメトリックを確認するには、次の表を使用します。 

**容量メトリック**

| クラシック メトリック | Azure Monitor のメトリック |
| ------------------- | ----------------- |
| `Capacity`            | ディメンション `BlobType` が `BlockBlob` または `PageBlob` と等しい `BlobCapacity` |
| `ObjectCount`        | ディメンション `BlobType` が `BlockBlob` または `PageBlob` と等しい `BlobCount` |
| `ContainerCount`      | `ContainerCount` |

> [!NOTE]
> また、クラシック メトリックでは使用できなかった新しい容量メトリックもいくつかあります。 完全な一覧を見るには、「[メトリック](../blobs/monitor-blob-storage-reference.md#metrics)」を参照してください。

**トランザクション メトリック**

| クラシック メトリック | Azure Monitor のメトリック |
| ------------------- | ----------------- |
| `AnonymousAuthorizationError` | ディメンション `ResponseType` が `AuthorizationError` に等しく、ディメンション `Authentication` が `Anonymous` に等しいトランザクション |
| `AnonymousClientOtherError` | ディメンション `ResponseType` が `ClientOtherError` に等しく、ディメンション `Authentication` が `Anonymous` に等しいトランザクション |
| `AnonymousClientTimeoutError` | ディメンション `ResponseType` が `ClientTimeoutError` に等しく、ディメンション `Authentication` が `Anonymous` に等しいトランザクション |
| `AnonymousNetworkError` | ディメンション `ResponseType` が `NetworkError` に等しく、ディメンション `Authentication` が `Anonymous` に等しいトランザクション |
| `AnonymousServerOtherError` | ディメンション `ResponseType` が `ServerOtherError` に等しく、ディメンション `Authentication` が `Anonymous` に等しいトランザクション |
| `AnonymousServerTimeoutError` | ディメンション `ResponseType` が `ServerTimeoutError` に等しく、ディメンション `Authentication` が `Anonymous` に等しいトランザクション |
| `AnonymousSuccess` | ディメンション `ResponseType` が `Success` に等しく、ディメンション `Authentication` が `Anonymous` に等しいトランザクション |
| `AnonymousThrottlingError` | ディメンション `ResponseType` が `ClientThrottlingError` または `ServerBusyError` に等しく、ディメンション `Authentication` が `Anonymous` に等しいトランザクション |
| `AuthorizationError` | ディメンション `ResponseType` が `AuthorizationError` に等しいトランザクション |
| `Availability` | `Availability` |
| `AverageE2ELatency` | `SuccessE2ELatency` |
| `AverageServerLatency` | `SuccessServerLatency` |
| `ClientOtherError` | ディメンション `ResponseType` が `ClientOtherError` に等しいトランザクション |
| `ClientTimeoutError` | ディメンション `ResponseType` が `ClientTimeoutError` に等しいトランザクション |
| `NetworkError` | ディメンション `ResponseType` が `NetworkError` に等しいトランザクション |
| `PercentAuthorizationError` | ディメンション `ResponseType` が `AuthorizationError` に等しいトランザクション |
| `PercentClientOtherError` | ディメンション `ResponseType` が `ClientOtherError` に等しいトランザクション |
| `PercentNetworkError` | ディメンション `ResponseType` が `NetworkError` に等しいトランザクション |
| `PercentServerOtherError` | ディメンション `ResponseType` が `ServerOtherError` に等しいトランザクション |
| `PercentSuccess` | ディメンション `ResponseType` が `Success` に等しいトランザクション |
| `PercentThrottlingError` | ディメンション `ResponseType` が `ClientThrottlingError` または `ServerBusyError` に等しいトランザクション |
| `PercentTimeoutError` | ディメンション `ResponseType` が `ServerTimeoutError` に等しいか、または `ResponseType` が `ClientTimeoutError` に等しいトランザクション |
| `SASAuthorizationError` | ディメンション `ResponseType` が `AuthorizationError` に等しく、ディメンション `Authentication` が `SAS` に等しいトランザクション |
| `SASClientOtherError` | ディメンション `ResponseType` が `ClientOtherError` に等しく、ディメンション `Authentication` が `SAS` に等しいトランザクション |
| `SASClientTimeoutError` | ディメンション `ResponseType` が `ClientTimeoutError` に等しく、ディメンション `Authentication` が `SAS` に等しいトランザクション |
| `SASNetworkError` | ディメンション `ResponseType` が `NetworkError` に等しく、ディメンション `Authentication` が `SAS` に等しいトランザクション |
| `SASServerOtherError` | ディメンション `ResponseType` が `ServerOtherError` に等しく、ディメンション `Authentication` が `SAS` に等しいトランザクション |
| `SASServerTimeoutError` | ディメンション `ResponseType` が `ServerTimeoutError` に等しく、ディメンション `Authentication` が `SAS` に等しいトランザクション |
| `SASSuccess` | ディメンション `ResponseType` が `Success` に等しく、ディメンション `Authentication` が `SAS` に等しいトランザクション |
| `SASThrottlingError` | ディメンション `ResponseType` が `ClientThrottlingError` または `ServerBusyError` に等しく、ディメンション `Authentication` が `SAS` に等しいトランザクション |
| `ServerOtherError` | ディメンション `ResponseType` が `ServerOtherError` に等しいトランザクション |
| `ServerTimeoutError` | ディメンション `ResponseType` が `ServerTimeoutError` に等しいトランザクション |
| `Success` | ディメンション `ResponseType` が `Success` に等しいトランザクション |
| `ThrottlingError` | ディメンション `ResponseType` が `ClientThrottlingError` または `ServerBusyError` と等しい `Transactions`|
| `TotalBillableRequests` | `Transactions` |
| `TotalEgress` | `Egress` |
| `TotalIngress` | `Ingress` |
| `TotalRequests` | `Transactions` |

## <a name="next-steps"></a>次のステップ

* [Azure Monitor](../../azure-monitor/overview.md)