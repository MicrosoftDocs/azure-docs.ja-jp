---
title: 専用 SQL プール (旧称 SQL DW) 用の REST API による一時停止、再開、スケーリング
description: REST API を使用して、Azure Synapse Analytics で専用 SQL プール (旧称 SQL DW) のコンピューティング能力を管理します。
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/29/2019
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: c04f61aaef5f5072ce0fb39ff111ba07ee151700
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375903"
---
# <a name="rest-apis-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure Synapse Analytics での専用 SQL プール (旧称 SQL DW) 用の REST API

Azure Synapse Analytics で専用 SQL プール (旧称 SQL DW) のコンピューティングを管理するための REST API。

## <a name="scale-compute"></a>コンピューティングのスケーリング

Data Warehouse ユニットを変更するには、[データベースの作成または更新](/rest/api/sql/databases/createorupdate) REST API を使います。 次の例では、MyServer サーバーにホストされているデータベース MySQLDW の Data Warehouse ユニットを DW1000 に設定します。 サーバーは "ResourceGroup1" という名前の Asure リソース グループ内にあります。

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2020-08-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    location: "West Central US",
    "sku": {
    "name": "DW200c"
    }
}
```

## <a name="pause-compute"></a>コンピューティングの一時停止

データベースを一時停止するには、[データベースの一時停止](/rest/api/sql/databases/pause) REST API を使います。 次の例では、"Server01" という名前のサーバーにホストされている "Database02" という名前のデータベースが一時停止されます。 サーバーは "ResourceGroup1" という名前の Asure リソース グループ内にあります。

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2020-08-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>コンピューティングの再開

データベースを開始するには、[データベースの再開](/rest/api/sql/databases/resume) REST API を使います。 次の例では、"Server01" という名前のサーバーにホストされている "Database02" という名前のデータベースが開始されます。 サーバーは "ResourceGroup1" という名前の Asure リソース グループ内にあります。

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2020-08-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>データベース状態の確認

> [!NOTE]
> 現在、"データベース状態の確認" では、データベースがオンライン ワークフローを完了している間に "オンライン" が返され、その結果として接続エラーが発生する場合があります。 この API 呼び出しを使用して接続試行をトリガーする場合は、アプリケーション コードに 2 分から 3 分の遅延を追加する必要がある可能性があります。

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/servers/{serverName}/databases/{databaseName}?api-version=2020-08-01-preview
```

## <a name="get-maintenance-schedule"></a>メンテナンス スケジュールの取得

専用 SQL プール (旧称 SQL DW) に設定されているメンテナンス スケジュールを確認します。

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>メンテナンス スケジュールの設定

既存の専用 SQL プール (旧称 SQL DW) のメンテナンス スケジュールを設定および更新します。

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": "Saturday",
                                "startTime": "00:00",
                                "duration": "08:00",
                },
                {
                                "dayOfWeek": "Wednesday",
                                "startTime": "00:00",
                                "duration": "08:00",
                }
                ]
    }
}

```

## <a name="next-steps"></a>次のステップ

詳細については、[コンピューティングの管理](sql-data-warehouse-manage-compute-overview.md)に関するページを参照してください。
