---
title: クイック スタート:Azure SQL Data Warehouse のコンピューティングのスケールアウト - PowerShell | Microsoft Docs
description: PowerShell で Azure SQL Data Warehouse のコンピューティングをスケーリングします。 コンピューティングをスケールアウトしてパフォーマンスを向上させます。または、コンピューティングをスケールバックしてコストを削減します。
services: sql-data-warehouse
author: Antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: Anthony.vanGemert
ms.reviewer: igorstan
ms.openlocfilehash: ddf33c927054512d1807d1c9e3429edaa5de25b9
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479244"
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-powershell"></a>クイック スタート:PowerShell で Azure SQL Data Warehouse のコンピューティングをスケーリングします。

PowerShell で Azure SQL Data Warehouse のコンピューティングをスケーリングします。 [コンピューティングをスケールアウト](sql-data-warehouse-manage-compute-overview.md)してパフォーマンスを向上させます。または、コンピューティングをスケールバックしてコストを削減します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

このクイックスタートでは、スケーリングできる SQL データ ウェアハウスが既に用意されていることを前提とします。 作成する必要がある場合は、[ポータルでの作成と接続](create-data-warehouse-portal.md)に関する記事に従って、**mySampleDataWarehouse** という名前のデータ ウェアハウスを作成してください。

## <a name="log-in-to-azure"></a>Azure にログインする

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```powershell
Connect-AzAccount
```

使用しているサブスクリプションを確認するには、[Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) を実行します。

```powershell
Get-AzSubscription
```

既定ではない別のサブスクリプションを使用する必要がある場合は、[Set-AzContext](/powershell/module/az.accounts/set-azcontext) を実行します。

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>データ ウェアハウスの情報を調べる

一時停止して再開する予定のデータ ウェアハウスのデータベース名、サーバー名、およびリソース グループを見つけます。

次の手順に従って、データ ウェアハウスの場所の情報を検索します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. Azure portal の左側のページで **[SQL データ ウェアハウス]** をクリックします。
3. **[SQL データ ウェアハウス]** ページで **mySampleDataWarehouse** を選びます。 これにより、データ ウェアハウスが開きます。

    ![サーバー名とリソース グループ](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. データベース名として使用される、データ ウェアハウスの名前を書き留めます。 データ ウェアハウスはデータベースの一種であることに注意してください。 サーバー名とリソース グループも書き留めます。 これらは、一時停止コマンドと再開コマンドで使用します。
5. サーバーが foo.database.windows.net である場合は、PowerShell コマンドレットでサーバー名として最初の部分のみを使用します。 前の図では、サーバーの完全名は newserver-20171113.database.windows.net です。 PowerShell コマンドレットのサーバー名として **newserver-20180430** を使用します。

## <a name="scale-compute"></a>コンピューティングのスケーリング

SQL Data Warehouse では、Data Warehouse ユニットを調整してコンピューティング リソースを増減させることができます。 [ポータルでの作成と接続](create-data-warehouse-portal.md)では、**mySampleDataWarehouse** を作成し、それを 400 DWU で初期化しました。 次の手順では、**mySampleDataWarehouse** の DWU を調整します。

Data Warehouse ユニットを変更するには、[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) PowerShell コマンドレットを使用します。 次の例では、サーバー **mynewserver-20180430** 上のリソース グループ **myResourceGroup** においてホストされているデータベース **mySampleDataWarehouse** の Data Warehouse ユニットを DW300c に設定します。

```Powershell
Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
```

## <a name="check-data-warehouse-state"></a>データ ウェアハウスの状態の確認

データ ウェアハウスの現在の状態を確認するには、[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) PowerShell コマンドレットを使用します。 これを使用すると、リソース グループ **myResourceGroup** とサーバー **mynewserver-20180430.database.windows.net** 内のデータベース **mySampleDataWarehouse** の状態が取得されます。

```powershell
$database = Get-AzSqlDatabase -ResourceGroupName myResourceGroup -ServerName mynewserver-20171113 -DatabaseName mySampleDataWarehouse
$database
```

次のような結果が表示されます。

```powershell
ResourceGroupName             : myResourceGroup
ServerName                    : mynewserver-20171113
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
                                resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mynewserver-20171113/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

出力には、データベースの**状態**が表示されます。 このケースでは、データベースがオンラインであることが確認できます。  このコマンドを実行すると、状態を表す値としてオンライン、一時停止中、再開中、スケーリング、一時停止のいずれかが表示されます。

状態のみを表示するには、次のコマンドを使用します。

```powershell
$database | Select-Object DatabaseName,Status
```

## <a name="next-steps"></a>次の手順
ここでは、データ ウェアハウスのコンピューティングをスケーリングする方法について学習しました。 Azure SQL Data Warehouse の詳細については、データの読み込みに関するチュートリアルを参照してください。

> [!div class="nextstepaction"]
>[SQL Data Warehouse にデータを読み込む](load-data-from-azure-blob-storage-using-polybase.md)
