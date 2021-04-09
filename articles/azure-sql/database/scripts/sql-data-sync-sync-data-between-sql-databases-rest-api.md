---
title: 'REST API: 複数のデータベース間での同期'
description: 複数のデータベース間で同期するには、REST API のサンプル スクリプトを使用します。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: REST API
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: ef2823b870f76922dd0dc157341aea9b502fb4bb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564561"
---
# <a name="use-rest-api-to-sync-data-between-multiple-databases"></a>REST API を使用して複数のデータベース間でデータを同期する 

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

この REST API の例は、複数のデータベース間でデータを同期する SQL データ同期を構成します。

SQL データ同期の概要については、[Azure の SQL データ同期を使用した複数のクラウドおよびオンプレミス データベース間でのデータの同期](../sql-data-sync-data-sql-server-sql-database.md)に関する記事を参照してください。

> [!IMPORTANT]
> 現時点では、SQL データ同期で Azure SQL Managed Instance はサポートされていません。

## <a name="create-sync-group"></a>同期グループを作成する

同期グループを作成するには、[作成または更新](https://docs.microsoft.com/rest/api/sql/syncgroups/createorupdate)テンプレートを使用します。
 
同期グループを作成するときには、同期スキーマ (table\column) を渡さず、masterSyncMemberName を渡さないでください。これは現時点では、同期グループには table\column 情報がまだ含まれていないためです。

同期グループ作成の要求の例: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

同期グループ作成の応答の例:

状態コード:200
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

状態コード: 201
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

## <a name="create-sync-member"></a>同期メンバーを作成する

同期メンバーを作成するには、[作成または更新](https://docs.microsoft.com/rest/api/sql/syncmembers/createorupdate)テンプレートを使用します。

同期メンバー作成の要求の例:

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```
同期メンバー作成の応答の例:

状態コード: 200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

状態コード: 201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="refresh-schema"></a>スキーマを更新する

同期グループが正常に作成されたら、次のテンプレートを使用してスキーマを更新します。

ハブ データベースのスキーマを更新するには、[ハブ スキーマの更新](https://docs.microsoft.com/rest/api/sql/syncgroups/refreshhubschema)テンプレートを使用します。 

ハブ データベース スキーマ更新の要求の例: 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/refreshHubSchema?api-version=2015-05-01-preview
```

ハブ データベース スキーマ更新の応答の例: 

状態コード:200

状態コード: 202

ハブ データベース スキーマの一覧を表示するには、[ハブ スキーマの一覧表示](https://docs.microsoft.com/rest/api/sql/syncgroups/listhubschemas)テンプレートを使用します。 

メンバー データベース スキーマを更新するには、[メンバー スキーマの更新](https://docs.microsoft.com/rest/api/sql/syncmembers/refreshmemberschema)テンプレートを使用します。 

メンバー データベース スキーマの一覧を表示するには、[メンバー スキーマの一覧表示](https://docs.microsoft.com/rest/api/sql/syncmembers/listmemberschemas)テンプレートを使用します。 

スキーマが正常に更新された場合のみ、次の手順に進みます。 

## <a name="update-sync-group"></a>同期グループを更新する 

同期グループを更新するには、[作成または更新](https://docs.microsoft.com/rest/api/sql/syncgroups/createorupdate)テンプレートを使用します。

同期スキーマを指定して、同期グループを更新します。 スキーマと masterSyncMemberName を含めます。これは、使用するスキーマが格納されている名前です。 

同期グループ更新の要求の例: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

同期グループ更新の応答の例: 

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```
## <a name="update-sync-member"></a>同期メンバーを更新する

同期メンバーを更新するには、[作成または更新](https://docs.microsoft.com/rest/api/sql/syncmembers/createorupdate)テンプレートを使用します。

同期メンバー更新の要求の例: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```

同期メンバー更新の応答の例: 

状態コード:200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

状態コード: 201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="trigger-sync"></a>同期 をトリガーする

同期操作をトリガーするには、[同期のトリガー](https://docs.microsoft.com/rest/api/sql/syncgroups/triggersync) テンプレートを使用します。

同期操作トリガーの要求の例: 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/triggerSync?api-version=2015-05-01-preview
```

同期操作トリガーの応答の例: 

状態コード:200

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。

その他の SQL Database 用の PowerShell サンプル スクリプトは、[Azure SQL Database 用の PowerShell スクリプト](../powershell-script-content-guide.md)のページにあります。

SQL データ同期の詳細については、以下を参照してください。

- 概要 - [Azure の SQL データ同期を使用して複数のクラウドおよびオンプレミス データベース間でデータを同期する](../sql-data-sync-data-sql-server-sql-database.md)
- データ同期の設定
    - Azure portal の使用 - [チュートリアル: Azure SQL Database と SQL Server の間でデータを同期するように SQL データ同期を設定する](../sql-data-sync-sql-server-configure.md)
    - PowerShell の使用 - [PowerShell を使用して Azure SQL Database と SQL Server のデータベース間でデータを同期する](sql-data-sync-sync-data-between-azure-onprem.md)
- データ同期エージェント - [Azure の SQL データ同期の Data Sync Agent](../sql-data-sync-agent-overview.md)
- ベスト プラクティス - [Azure の SQL データ同期のベスト プラクティス](../sql-data-sync-best-practices.md)
- 監視 - [Azure Monitor ログによる SQL データ同期の監視](../monitor-tune-overview.md)
- トラブルシューティング - [Azure の SQL データ同期に関する問題のトラブルシューティング](../sql-data-sync-troubleshoot.md)
- 同期スキーマの更新
    - Transact-SQL の使用 - [Azure の SQL データ同期内でスキーマ変更のレプリケートを自動化する](../sql-data-sync-update-sync-schema.md)
    - PowerShell の使用 - [PowerShell を使用して、既存の同期グループの同期スキーマを更新する](update-sync-schema-in-sync-group.md)

SQL Database の詳細については、以下をご覧ください。

- [SQL Database の概要](../sql-database-paas-overview.md)
- [データベースのライフサイクル管理](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
