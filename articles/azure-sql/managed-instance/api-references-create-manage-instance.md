---
title: Azure SQL Managed Instance の管理 API リファレンス
description: Azure SQL Managed Instance のマネージド インスタンスの作成と構成について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 8cc2930422bf644f217737d0f0ba585c243575ee
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503006"
---
# <a name="managed-api-reference-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance のマネージド API リファレンス
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance のマネージド インスタンスは、Azure portal、PowerShell、Azure CLI、REST API、Transact-SQL を使用して作成および管理できます。 この記事では、マネージド インスタンスの作成と構成に使用できる関数と API の概要を説明します。

## <a name="azure-portal-create-a-managed-instance"></a>Azure portal:マネージド インスタンスを作成する

マネージド インスタンスの作成方法を示したクイックスタートについては、[マネージド インスタンスの作成のクイックスタート](instance-create-quickstart.md)を参照してください。

## <a name="powershell-create-and-configure-managed-instances"></a>PowerShell:マネージド インスタンスを作成して構成する

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRM モジュールのコマンドの引数は実質的に同じです。

Azure PowerShell を使用してマネージド インスタンスを作成および管理するには、次の PowerShell コマンドレットを使用します。 PowerShell をインストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関する記事を参照してください。

> [!TIP]
> PowerShell サンプル スクリプトについては、[PowerShell ライブラリを使用したマネージド インスタンスの作成のクイックスタート スクリプト](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/)を参照してください。

| コマンドレット | 説明 |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|マネージド インスタンスを作成します。 |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|マネージド インスタンスに関する情報を返します。|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|マネージド インスタンスのプロパティを設定します。|
|[Remove-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|マネージド インスタンスを削除します。|
|[New-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|SQL Managed Instance データベースを作成します。|
|[Get-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|SQL Managed Instance データベースに関する情報を返します。|
|[Remove-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|SQL Managed Instance データベースを削除します。|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|SQL Managed Instance データベースを復元します。|

## <a name="azure-cli-create-and-configure-managed-instances"></a>Azure CLI:マネージド インスタンスを作成して構成する

[Azure CLI](/cli/azure) でマネージド インスタンスを作成して構成するには、次の [SQL Managed Instance 用の Azure CLI コマンド](/cli/azure/sql/mi)を使用します。 [Azure Cloud Shell](/azure/cloud-shell/overview) を使用して CLI をブラウザーで実行することも、macOS、Linux、または Windows に[インストール](/cli/azure/install-azure-cli)することもできます。

> [!TIP]
> Azure CLI のクイックスタートについては、「[Working with SQL Managed Instance using Azure CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)」 (Azure CLI を使用した SQL Managed Instance の操作) を参照してください。

| コマンドレット | 説明 |
| --- | --- |
|[az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |マネージド インスタンスを作成します。|
|[az sql mi list](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|使用可能なマネージド インスタンスを一覧表示します。|
|[az sql mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|マネージド インスタンスの詳細を取得します。|
|[az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|マネージド インスタンスを更新します。|
|[az sql mi delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|マネージド インスタンスを削除します。|
|[az sql midb create](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |マネージド データベースを作成します。|
|[az sql midb list](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|使用可能なマネージド データベースを一覧表示します。|
|[az sql midb restore](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|マネージド データベースを復元します。|
|[az sql midb delete](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|マネージド データベースを削除します。|

## <a name="transact-sql-create-and-configure-instance-databases"></a>Transact-SQL:インスタンス データベースを作成して構成する

マネージド インスタンスの作成後、インスタンス データベースを作成して構成するには、次の T-SQL コマンドを使用します。 これらのコマンドは、Azure portal、[SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is)、[Visual Studio Code](https://code.visualstudio.com/docs)、またはサーバーに接続して Transact-SQL コマンドを渡すことができるその他のプログラムを使用して実行できます。

> [!TIP]
> Microsoft Windows で SQL Server Management Studio を使用して、マネージド インスタンスを構成して接続する方法を示したクイックスタートについては、「[クイック スタート: Azure SQL Managed Instance に接続するように Azure VM を構成する](connect-vm-instance-configure.md)」および「[クイック スタート: オンプレミスから Azure SQL Managed Instance へのポイント対サイト接続を構成する](point-to-site-p2s-configure.md)」を参照してください。

> [!IMPORTANT]
> Transact-SQL を使用して、マネージド インスタンスを作成したり、削除したりすることはできません。

| コマンド | 説明 |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|SQL Managed Instance に新しいインスタンス データベースを作成します。 新しいデータベースを作成するには、master データベースに接続している必要があります。|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |SQL Managed Instance のインスタンス データベースを変更します。|

## <a name="rest-api-create-and-configure-managed-instances"></a>REST API:マネージド インスタンスを作成して構成する

マネージド インスタンスを作成して構成するには、以下の REST API 要求を使用します。

| コマンド | 説明 |
| --- | --- |
|[Managed Instances - Create Or Update](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|マネージド インスタンスを作成または更新します。|
|[Managed Instances - Delete](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|マネージド インスタンスを削除します。|
|[Managed Instances - Get](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|マネージド インスタンスを取得します。|
|[Managed Instances - List](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|サブスクリプション内のマネージド インスタンスの一覧を返します。|
|[Managed Instances - List By Resource Group](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|リソース グループ内のマネージド インスタンスの一覧を取得します。|
|[Managed Instances - Update](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|マネージド インスタンスを更新します。|

## <a name="next-steps"></a>次のステップ

- SQL Server データベースを Azure に移行する方法については、「[Azure SQL Database に移行](../database/migrate-to-database-from-sql-server.md)」を参照してください。
- サポートされている機能については、[機能](../database/features-comparison.md)に関する記事をご覧ください。
