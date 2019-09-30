---
title: SQL Database マネージド インスタンス - ポイントインタイム リストア | Microsoft Docs
description: SQL マネージド インスタンス内のデータベースを以前の特定の時点に復元する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: 67f13d16dcf6bbe4fa13fe3a6e78d3e4d61e1999
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861998"
---
# <a name="restore-a-sql-managed-instance-database-to-a-previous-point-in-time"></a>SQL マネージド インスタンス データベースを以前の特定の時点に復元する

ポイントインタイム リストア (PITR) を使用すると、データベースを、過去のある時点における別のデータベースのコピーとして作成することができます。 この記事では、マネージド インスタンス内のデータベースについてポイントインタイム リストアを実行する方法について説明します。

ポイントインタイム リストアは、エラーによって発生したインシデント、データの誤った読み込み、重要なデータの削除、およびその他の問題などの復旧のシナリオで使用できるほか、テストまたは監査の目的でのみ使用することもできます。 データベースの設定によって異なりますが、バックアップ ファイルは 7 日から 35 日の期間保持されます。

ポイントインタイム リストアを使用すると、次のことができます。

- 既存のデータベースからデータベースを復元する。
- 削除されたデータベースからデータベースを復元する。

さらに、マネージド インスタンスでは、ポイントインタイム リストアを使用して次のことを行うことができます。 

- 同じマネージド インスタンスにデータベースを復元する。
- 別のマネージド インスタンスにデータベースを復元する。


> [!NOTE]
> マネージド インスタンス全体のポイントインタイム リストアは実行できません。 実行できるのは、マネージド インスタンス上でホストされているデータベースのポイントインタイム リストアであり、この記事では、それについて説明します。


## <a name="limitations"></a>制限事項

別のマネージド インスタンスに復元する場合は、両方のインスタンスが同じサブスクリプションとリージョン内に存在する必要があります。 リージョン間およびサブスクリプション間での復元は、現在、サポートされていません。

> [!WARNING]
> マネージド インスタンスのストレージ サイズに注意してください。復元するデータのサイズによっては、インスタンス ストレージが不足する可能性があります。 データを復元するための領域が不足している場合は、別の方法を使用します。

次の表に、マネージド インスタンスにおける特定の時点への復旧シナリオを示します。

|           |既存の DB の復元| 既存の DB の復元|削除された DB の復元| 削除された DB の復元|
|:----------|:----------|:----------|:----------|:----------|
|Destination| 同じ MI|別の MI |同じ MI|別の MI |
|Azure ポータル| はい|いいえ |いいえ|いいえ|
|Azure CLI|はい |はい |いいえ|いいえ|
|PowerShell| はい|はい |はい|はい|


## <a name="restore-existing-database"></a>既存のデータベースを復元する

Azure portal、PowerShell、または Azure CLI を使用して、既存のデータベースを同じインスタンスに復元することができます。 PowerShell または Azure CLI を使用して、ターゲット マネージド インスタンスとリソース グループ プロパティを指定することにより、別のインスタンスにデータベースを復元することができます。 これらのパラメーターを指定しないと、既定では、データベースが既存のインスタンスに復元されます。 別のインスタンスへの復元は、現在、Azure portal ではサポートされていません。 

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com) にサインインします。 
1. ご利用のマネージド インスタンスに移動し、復元するデータベースを選択します。 
1. データベース ページで **[復元]** を選択します。 

    ![既存のデータベースを復元する](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

1. **[復元]** ページで、データベースの復元先とする履歴内の日付と時刻のポイントを選択します。
1. **[確認]** を選択して、ご利用のデータベースを復元します。 これにより、復元プロセスが開始されます。このプロセスでは、新しいデータベースが作成され、目的の時点における元のデータベースのデータが入力されます。 復旧プロセスの詳細については、「[復旧時間](sql-database-recovery-using-backups.md#recovery-time)」を参照してください。 

1. マネージド インスタンスを検索します。
1. 復元するデータベースを選択します。
1. データベース画面で、[復元] 操作をクリックします。
1. [復元] 画面で、データベースの復元先とする履歴のポイントを示す日付と時刻を選択します。
1. 確定すると、復元プロセスが開始され、データベースのサイズに応じて、新しいデータベースが作成され、目的の時点における元のデータベースのデータが入力されます。 復元プロセスの期間については、バックアップを使用した復旧に関する記事を確認してください。


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell がまだインストールされていない場合は、「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps)」を参照してください。

PowerShell を使用してデータベースを復元するには、ご利用になる値でパラメーターを更新し、次のコマンドを実行します。

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"
 
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId
 
Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                               -InstanceName $managedInstanceName `
                               -Name $databaseName `
                               -PointInTime $pointInTime `
                               -TargetInstanceDatabaseName $targetDatabase `
```

データベースを別のマネージド インスタンスに復元するには、ターゲット リソース グループ名とターゲット マネージド インスタンス名を設定します。  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

詳細については、[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) を参照してください。


# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI をまだインストールしていない場合は、「[Azure CLI のインストール](/cli/azure/install-azure-cli?view=azure-cli-latest)」を参照してください。

Azure CLI を使用してデータベースを復元するには、ご利用になる値でパラメーターを更新し、次のコマンドを実行します。


```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```


データベースを別のマネージド インスタンスに復元するには、ターゲット リソース グループ名とターゲット マネージド インスタンス名を設定します。  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

使用可能なパラメーターの詳細については、[マネージド インスタンス CLI](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore) に関するページを参照してください。 

---

## <a name="restore-a-deleted-database"></a>削除されたデータベースの復元 
 
削除されたデータベースの復元は、PowerShell でのみ行うことができます。 データベースは、同じインスタンスまたは別のインスタンスに復元できます。 

削除されたデータベースを PowerShell を使用して復元するには、ご利用になる値でパラメーターを更新し、次のコマンドを実行します。

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"

$deleted_db = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
            -InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName 

$pointInTime = "2018-06-27T08:51:39.3882806Z"
$properties = New-Object System.Object
$properties | Add-Member -type NoteProperty -name CreateMode -Value "PointInTimeRestore"
$properties | Add-Member -type NoteProperty -name RestorePointInTime -Value $pointInTime
$properties | Add-Member -type NoteProperty -name RestorableDroppedDatabaseId -Value $deleted_db.Id
```

削除されたデータベースを別のインスタンスに復元するには、リソース グループ名とマネージド インスタンス名を変更します。

location パラメーターは、リソース グループおよびマネージド インスタンスの場所と一致する必要があります。

```powershell-interactive
$resourceGroupName = "<Second resource group name>"
$managedInstanceName = "<Second managed instance name>"

$location = "West Europe"

$restoredDBName = "WorldWideImportersPITR"
$resource_id = "subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$managedInstanceName/databases/$restoredDBName"

New-AzResource -Location $location -Properties $properties `
        -ResourceId $resource_id -ApiVersion "2017-03-01-preview" -Force
```

## <a name="overwrite-existing-database"></a>既存のデータベースを上書きする 
 
既存のデータベースを上書きするには、次のことも行う必要があります。

1. 上書きする既存のデータベースを削除します。
1. ポイントインタイム リストアされたデータベースの名前を、削除したデータベースの名前に変更します。 


### <a name="drop-original-database"></a>元のデータベースを削除する 
 
データベースは、Azure portal、PowerShell、または Azure CLI を使用して削除することができます。 

また、マネージド インスタンスに直接接続し、SQL Server Management Studio (SSMS) を起動し、次の Transact-SQL (T-SQL) コマンドを実行してで、データベースを削除することもできます。

```sql
DROP DATABASE WorldWideImporters;
```

マネージド インスタンス データベースに接続するには、次のいずれかの方法を使用します。 

- [SQL 仮想マシン](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [ポイント対サイト](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [パブリック エンドポイント](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

Azure portal で、マネージド インスタンスからデータベースを選択し、 **[削除]** を選択します。

   ![既存のデータベースを復元する](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

次の PowerShell コマンドを使用して、マネージド インスタンスから既存のデータベースを削除します。 

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

次の Azure CLI コマンドを使用して、マネージド インスタンスから既存のデータベースを削除します。 

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---


### <a name="alter-new-database-name-to-original"></a>新しいデータベース名を元の名前に変更する

マネージド インスタンスに直接接続し、SQL Server Management Studio を起動してから、次の Transact-SQL (T-SQL) クエリを実行することで、復元されたデータベースの名前を、上書きの対象とした削除済みのデータベースの名前に変更します。 


```sql
ALTER WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```


マネージド インスタンス データベースに接続するには、次のいずれかの方法を使用します。 

- [SQL 仮想マシン](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [ポイント対サイト](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [パブリック エンドポイント](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>次の手順

[長期保有](sql-database-long-term-retention.md)と[自動バックアップ](sql-database-automated-backups.md)について説明します。 
