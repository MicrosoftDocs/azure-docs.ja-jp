---
title: Synapse SQL プールのコンピューティングをスケーリングする (Azure PowerShell)
description: Azure PowerShell を使用して、Synapse SQL プール (データ ウェアハウス) のコンピューティングをスケーリングできます。
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.openlocfilehash: 044b1b145f95e81b2aa2474950d4961131c3c88a
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079729"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-azure-powershell"></a>クイック スタート:Azure PowerShell を使用して Synapse SQL プールのコンピューティングをスケーリングする

Azure PowerShell を使用して、Synapse SQL プール (データ ウェアハウス) のコンピューティングをスケーリングできます。 [コンピューティングをスケールアウト](sql-data-warehouse-manage-compute-overview.md)してパフォーマンスを向上させます。または、コンピューティングをスケールバックしてコストを削減します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

このクイックスタートでは、スケーリングできる SQL プールが既に用意されていることを前提とします。 作成する必要がある場合は、[ポータルでの作成と接続](create-data-warehouse-portal.md)に関する記事に従って、**mySampleDataWarehouse** という名前の SQL プールを作成してください。

## <a name="log-in-to-azure"></a>Azure にログインする

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```powershell
Connect-AzAccount
```

使用しているサブスクリプションを確認するには、[Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) を実行します。

```powershell
Get-AzSubscription
```

既定ではない別のサブスクリプションを使用する必要がある場合は、[Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) を実行します。

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>データ ウェアハウスの情報を調べる

一時停止して再開する予定のデータ ウェアハウスのデータベース名、サーバー名、およびリソース グループを見つけます。

次の手順に従って、データ ウェアハウスの場所の情報を検索します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. Azure portal の左側のナビゲーション ページで **[Azure Synapse Analytics (以前の SQL DW)]** をクリックします。
3. **[Azure Synapse Analytics (以前の SQL DW)]** ページから **[mySampleDataWarehouse]** を選択してデータ ウェアハウスを開きます。

    ![サーバー名とリソース グループ](./media/quickstart-scale-compute-powershell/locate-data-warehouse-information.png)

4. データベース名として使用される、データ ウェアハウスの名前を書き留めます。 データ ウェアハウスはデータベースの一種であることに注意してください。 サーバー名とリソース グループも書き留めます。 このサーバー名とリソース グループ名は、一時停止コマンドと再開コマンドで使用します。
5. PowerShell コマンドレットで使用するのは、サーバー名の最初の部分のみです。 前の図では、サーバーの完全名は sqlpoolservername.database.windows.net です。 PowerShell コマンドレットでは、サーバー名として **sqlpoolservername** を使用します。

## <a name="scale-compute"></a>コンピューティングのスケーリング

SQL プールでは、Data Warehouse ユニットを調整してコンピューティング リソースを増減させることができます。 [ポータルでの作成と接続](create-data-warehouse-portal.md)では、**mySampleDataWarehouse** を作成し、それを 400 DWU で初期化しました。 次の手順では、**mySampleDataWarehouse** の DWU を調整します。

Data Warehouse ユニットを変更するには、[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell コマンドレットを使用します。 次の例では、サーバー **sqlpoolservername** 上のリソース グループ **resourcegroupname** においてホストされているデータベース **mySampleDataWarehouse** の Data Warehouse ユニットを DW300c に設定します。

```Powershell
Set-AzSqlDatabase -ResourceGroupName "resourcegroupname" -DatabaseName "mySampleDataWarehouse" -ServerName "sqlpoolservername" -RequestedServiceObjectiveName "DW300c"
```

## <a name="check-data-warehouse-state"></a>データ ウェアハウスの状態の確認

データ ウェアハウスの現在の状態を確認するには、[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell コマンドレットを使用します。 このコマンドレットを実行すると、リソース グループ **resourcegroupname** とサーバー **sqlpoolservername.database.windows.net** 内のデータベース **mySampleDataWarehouse** の状態が表示されます。

```powershell
$database = Get-AzSqlDatabase -ResourceGroupName resourcegroupname -ServerName sqlpoolservername -DatabaseName mySampleDataWarehouse
```

次のような結果が表示されます。

```powershell
ResourceGroupName             : resourcegroupname
ServerName                    : sqlpoolservername
DatabaseName                  : mySampleDataWarehouse
Location                      : North Europe
DatabaseId                    : 34d2ffb8-b70a-40b2-b4f9-b0a39833c974
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1_General_CP1_CI_AS
CatalogCollation              :
MaxSizeBytes                  : 263882790666240
Status                        : Online
CreationDate                  : 11/20/2017 9:18:12 PM
CurrentServiceObjectiveId     : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
CurrentServiceObjectiveName   : DW300c
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/resourcegroupname/providers/Microsoft.Sql/servers/sqlpoolservername/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

出力には、データベースの**状態**が表示されます。 このケースでは、データベースがオンラインであることが確認できます。  このコマンドを実行すると、状態を表す値としてオンライン、一時停止中、再開中、スケーリング、一時停止のいずれかが表示されます。

状態のみを表示するには、次のコマンドを使用します。

```powershell
$database | Select-Object DatabaseName,Status
```

## <a name="next-steps"></a>次のステップ

ここでは、SQL プールのコンピューティングをスケーリングする方法について説明しました。 SQL プールに関する理解をさらに深めるために、データの読み込みに関するチュートリアルに進んでください。

> [!div class="nextstepaction"]
>[SQL プールにデータを読み込む](load-data-from-azure-blob-storage-using-polybase.md)
