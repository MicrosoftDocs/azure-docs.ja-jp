---
title: クイック スタート:Azure SQL Data Warehouse でのコンピューティングの一時停止と再開 - PowerShell | Microsoft Docs
description: PowerShell を使用して、Azure SQL Data Warehouse でのコンピューティングを一時停止してコストを節約します。 データ ウェアハウスを使用する準備ができたら、コンピューティングを再開します。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 03/20/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: fe9cd6c951f9eba73cee1bea66df88f3143859b9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58846841"
---
# <a name="quickstart-pause-and-resume-compute-in-azure-sql-data-warehouse-with-powershell"></a>クイック スタート:PowerShell による Azure SQL Data Warehouse でのコンピューティングの一時停止と再開

PowerShell を使用して、Azure SQL Data Warehouse でのコンピューティングを一時停止してコストを節約します。 データ ウェアハウスを使用する準備ができたら、[コンピューティングを再開](sql-data-warehouse-manage-compute-overview.md)します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

このクイック スタートでは、一時停止して再開できる SQL Data Warehouse が既に用意されていることを前提とします。 作成する必要がある場合は、[Azure Portal での作成と接続](create-data-warehouse-portal.md)に関する記事に従って、**mySampleDataWarehouse** という名前のデータ ウェアハウスを作成できます。

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
2. Azure Portal の左側のページで **[SQL データベース]** を選択します。
3. **[SQL データベース]** ページから **[mySampleDataWarehouse]** を選択します。 データ ウェアハウスが開きます。

    ![サーバー名とリソース グループ](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. データ ウェアハウスの名前 (データベース名) を書き留めます。 サーバー名とリソース グループも書き留めます。
6. サーバーが foo.database.windows.net である場合は、PowerShell コマンドレットでサーバー名として最初の部分のみを使用します。 前の図では、サーバーの完全名は newserver-20171113.database.windows.net です。 サフィックスを削除し、PowerShell コマンドレットのサーバー名として **newserver-20171113** を使用します。

## <a name="pause-compute"></a>コンピューティングの一時停止

コストを節約するために、オンデマンドでコンピューティング リソースを一時停止および再開できます。 たとえば、夜間と週末にデータベースを使用しない場合、その期間にデータベースを一時停止して、日中に再開することができます。 データベースが一時停止されている間、コンピューティング リソースへの課金は行われません。 ただし、ストレージに対する課金は引き続き行われます。

データベースを一時停止するには、[Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase) コマンドレットを使用します。 次の例は、**newserver-20171113** という名前のサーバーでホストされている **mySampleDataWarehouse** という名前のデータ ウェアハウスを一時停止します。 このサーバーは、**myResourceGroup** という名前の Azure リソース グループ内にあります。


```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
```

バリエーションの 1 つとして、次の例では $database オブジェクトにデータベースを取り込みます。 オブジェクトは [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase) にパイプ処理されます。 結果は、オブジェクト resultDatabase に格納されます。 最後のコマンドは結果を表示します。

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```


## <a name="resume-compute"></a>コンピューティングの再開

データベースを開始するには、[Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase) コマンドレットを使用します。 次の例は、newserver-20171113 という名前のサーバーでホストされている mySampleDataWarehouse という名前のデータベースを開始します。 このサーバーは、myResourceGroup という名前の Azure リソース グループ内にあります。

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" -DatabaseName "mySampleDataWarehouse"
```

バリエーションの 1 つとして、次の例では $database オブジェクトにデータベースを取り込みます。 オブジェクトは [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase) にパイプ処理され、結果が $resultDatabase に格納されます。 最後のコマンドは結果を表示します。

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-data-warehouse-operation"></a>データ ウェアハウスの操作の状態の確認

データ ウェアハウスの状態を確認するには、[Get-AzSqlDatabaseActivity](https://docs.microsoft.com/powershell/module/az.sql/Get-AzSqlDatabaseActivity#description) コマンドレットを使用します。

```
Get-AzSqlDatabaseActivity -ResourceGroupName "ResourceGroup01" -ServerName "Server01" -DatabaseName "Database02"
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

データ ウェアハウス ユニットとデータ ウェアハウスに格納されているデータに対して課金されます。 これらのコンピューティングとストレージのリソースは別々に請求されます。

- データをストレージ内に保持する場合は、コンピューティングを一時停止します。
- それ以上課金されないようにする場合は、データ ウェアハウスを削除できます。

必要に応じて、以下の手順でリソースをクリーンアップします。

1. [Azure Portal](https://portal.azure.com) にサインインし、データ ウェアハウスをクリックします。

    ![リソースのクリーンアップ](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. コンピューティング リソースを一時停止するには、**[一時停止]** ボタンをクリックします。 データ ウェアハウスが一時停止すると、ボタンの表示が **[開始]** になります。  コンピューティング リソースを再開するには、**[開始]** をクリックします。

3. コンピューティング リソースやストレージに課金されないようにデータ ウェアハウスを削除するには、**[削除]** をクリックします。

4. 作成した SQL Server を削除するには、**mynewserver-20171113.database.windows.net** をクリックした後、**[削除]** をクリックします。  サーバーを削除すると、サーバーに割り当てられているすべてのデータベースが削除されるので、削除には注意してください。

5. リソース グループを削除するには、**myResourceGroup** をクリックして、**[リソース グループの削除]** をクリックします。


## <a name="next-steps"></a>次の手順

データ ウェアハウスに対するコンピューティングの一時停止と再開を行いました。 Azure SQL Data Warehouse の詳細については、データの読み込みに関するチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [SQL Data Warehouse にデータを読み込む](load-data-from-azure-blob-storage-using-polybase.md)
