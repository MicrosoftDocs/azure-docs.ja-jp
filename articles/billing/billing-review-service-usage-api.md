---
title: REST API を使用して Azure サービスのリソースの使用状況を確認する | Microsoft Docs
description: Azure REST API を使用して Azure サービスのリソースの使用状況を確認する方法を説明します。
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: banders
ms.openlocfilehash: 47e19fae26d6e3bc465799980c587d7bb7ed5e92
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443062"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>REST API を使用して Azure リソースの使用状況を確認する

Azure Cost Management API を使用すると、Azure リソースの消費量を確認して管理できます。

この記事では、1 時間ごとの使用情報を含むコンマ区切り値ドキュメントを生成する日次レポートを作成する方法、および Azure リソース グループ内の仮想マシン、データベース、タグ付けされたリソースの使用状況を照会できるようにフィルターでレポートをカスタマイズする方法を説明します。

>[!NOTE]
> Cost Management API は、現在プライベート プレビュー段階にあります。

## <a name="create-a-basic-cost-management-report"></a>基本的なコスト管理レポートを作成する

Cost Management API の `reports` 操作を使用して、コスト レポートの生成方法と、レポートの発行先を定義します。

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

`{subscriptionGuid}` パラメーターは必須であり、API トークンで提供された資格情報を使用して読み取ることができるサブスクリプション ID を含む必要があります。 `{reportName}`

次のヘッダーは必須です｡ 

|要求ヘッダー|説明|  
|--------------------|-----------------|  
|*Content-Type:*| 必須。 `application/json` を設定します。 |  
|*Authorization:*| 必須。 有効な `Bearer` トークンを設定します。 |

HTTP 要求本文でレポートのパラメーターを構成します。 次の例のレポートは、アクティブのときは毎日生成し、Azure Storage BLOB コンテナーに書き込まれる CSV ファイルであり、リソース グループ `westus` 内のすべてのリソースに対する 1 時間ごとのコスト情報を含むように、設定されています。

```json
{
    "properties": {
        "schedule": {
            "status": "Inactive",
            "recurrence": "Daily",
            "recurrencePeriod": {
                "from": "2018-08-21",
                "to": "2019-10-31"
            }
        },
        "deliveryInfo": {
            "destination": {
                "resourceId": "/subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
                "container": "MyReportContainer",
                "rootFolderPath": "MyScheduleTest"
            }
        },
        "format": "Csv",
        "definition": {
            "type": "Usage",
            "timeframe": "MonthToDate",
            "dataSet": {
                "granularity": "Hourly",
                "filter": {
                    "dimensions": {
                        "name": "ResourceLocation",
                        "operator": "In",
                        "values": [
                            "westus"
                        ]
                    }
                }
            }
        }
    }
}
```

その

## <a name="filtering-reports"></a>レポートのフィルター処理

レポートを使用するときに、要求本文の `filter` および `dimensions` セクションを使用して、特定のリソースの種類のコストだけに絞り込むことができます。 前記の要求本文では、リージョンのすべてのリソースでフィルター処理する方法が示されています。 

### <a name="get-all-compute-usage"></a>すべてのコンピューティングの使用状況を取得する

`ResourceType` ディメンションを使用して、すべてのリージョンのサブスクリプションにおける Azure 仮想マシンのコストを報告します。

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.ClassicCompute/virtualMachines", 
                "Microsoft.Compute/virtualMachines"
        ] 
    }
}
```

### <a name="get-all-database-usage"></a>すべてのデータベースの使用状況を取得する

`ResourceType` ディメンションを使用して、すべてのリージョンのサブスクリプションにおける Azure SQL Database のコストを報告します。

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.Sql/servers"
        ] 
    }
}
```

### <a name="report-on-specific-instances"></a>特定のインスタンスについて報告する

`Resource` ディメンションを使用すると、特定のリソースに対するコストを報告できます。

```json
"filter": {
    "dimensions": {
        "name": "Resource",
        "operator": "In",
        "values": [
            "subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroup}/providers/Microsoft.ClassicCompute/virtualMachines/{ResourceName}"
        ]
    }
}
```

### <a name="changing-timeframes"></a>期間を変更する

`timeframe` の定義を `Custom` に設定して、期間を曜日および日付の組み込みオプションの範囲外に設定します。

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>次の手順
- [Azure Rest API の開始](https://docs.microsoft.com/rest/api/azure/)   
