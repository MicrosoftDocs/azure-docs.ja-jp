---
title: ポイントインタイム リストア (PITR)
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance 上のデータベースを以前の時点に復元します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: 407d56c209f64d350906a17c0746b1c43f969d43
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708663"
---
# <a name="restore-a-database-in-azure-sql-managed-instance-to-a-previous-point-in-time"></a>Azure SQL Managed Instance のデータベースを以前の時点に復元します
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

ポイントインタイム リストア (PITR) を使用して、データベースを、過去のある時点における別のデータベースのコピーとして作成します。 この記事では、Azure SQL Managed Instance 内のデータベースについて、ポイントインタイム リストアを実行する方法について説明します。

ポイントインタイム リストアは、エラーによって発生したインシデント、データの誤った読み込み、重要なデータの削除などの復旧のシナリオで役に立ちます。 単にテストや監査のために使用することもできます。 データベースの設定によって異なりますが、バックアップ ファイルは 7 日から 35 日間保持されます。

ポイントインタイム リストアでは、次のようにデータベースを復元できます。

- 既存のデータベースから。
- 削除されたデータベースから。
- 同じ SQL Managed Instance、または別の SQL Managed Instance に対して。 

## <a name="limitations"></a>制限事項

SQL Managed Instance に対するポイントインタイム リストアには、次の制限があります。

- SQL Managed Instance のインスタンス間で復元する場合は、両方のインスタンスが同じサブスクリプションおよびリージョン内に存在する必要があります。 リージョン間およびサブスクリプション間での復元は、現在、サポートされていません。
- SQL Managed Instance 全体のポイントインタイム リストアは実行できません。 この記事では、実行できる内容 (SQL Managed Instance 上でホストされているデータベースのポイントインタイム リストア) についてのみ説明します。

> [!WARNING]
> SQL Managed Instance のストレージ サイズに注意してください。 復元するデータのサイズによっては、インスタンス ストレージが不足する可能性があります。 復元されたデータ用の領域が不足している場合は、別の方法を使用してください。

次の表に、SQL Managed Instance におけるポイントインタイム リストアのシナリオを示します。

|           |既存の DB を同じ SQL Managed Instance のインスタンスに復元する| 既存の DB を別の SQL Managed Instance に復元する|削除された DB を同じ SQL Managed Instance に復元する|削除された DB を別の SQL Managed Instance に復元する|
|:----------|:----------|:----------|:----------|:----------|
|**Azure Portal**| はい|いいえ |はい|いいえ|
|**Azure CLI**|はい |はい |いいえ|いいえ|
|**PowerShell**| はい|はい |はい|はい|

## <a name="restore-an-existing-database"></a>既存のデータベースを復元する

Azure portal、PowerShell、または Azure CLI を使用して、既存のデータベースを同じ SQL Managed Instance に復元します。 データベースを別の SQL Managed Instance に復元するには、ターゲットの SQL Managed Instance とリソース グループのプロパティを指定できるように、PowerShell または Azure CLI を使用します。 これらのパラメーターを指定しない場合、既定ではデータベースは既存の SQL Managed Instance に復元されます。 Azure portal では、現在のところ別の SQL Managed Instance への復元はサポートされていません。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com) にサインインします。 
2. ご利用の SQL Managed Instance に移動し、復元するデータベースを選択します。
3. データベース ページで **[復元]** を選択します。

    ![Azure portal を使用してデータベースを復元する](./media/point-in-time-restore/restore-database-to-mi.png)

4. **[復元]** ページで、データベースの復元先とする日付と時刻のポイントを選択します。
5. **[確認]** を選択して、ご利用のデータベースを復元します。 このアクションにより、復元プロセスが開始されます。このプロセスでは、新しいデータベースが作成され、指定した時点における元のデータベースのデータが入力されます。 復旧プロセスの詳細については、「[復旧時間](../database/recovery-using-backups.md#recovery-time)」を参照してください。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell がまだインストールされていない場合は、「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps)」を参照してください。

PowerShell を使用してデータベースを復元するには、次のコマンドでパラメーターの値を指定します。 次に、次のコマンドを実行します。

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
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

データベースを別の SQL Managed Instance に復元するには、ターゲット リソース グループとターゲット SQL Managed Instance の名前も指定します。  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target SQL Managed Instance>"
$targetInstanceName = "<Target SQL Managed Instance name>"

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI をまだインストールしていない場合は、「[Azure CLI のインストール](/cli/azure/install-azure-cli?view=azure-cli-latest)」を参照してください。

Azure CLI を使用してデータベースを復元するには、次のコマンドでパラメーターの値を指定します。 次に、次のコマンドを実行します。

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

データベースを別の SQL Managed Instance に復元するには、ターゲット リソース グループと SQL Managed Instance の名前も指定します。  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

使用可能なパラメーターの詳細については、[SQL Managed Instance でのデータベースの復元に関する CLI のドキュメント](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore)を参照してください。

---

## <a name="restore-a-deleted-database"></a>削除されたデータベースの復元

削除されたデータベースの復元は、PowerShell または Azure portal を使用して実行できます。 削除されたデータベースを同じインスタンスに復元するには、Azure portal または PowerShell を使用します。 削除されたデータベースを別のインスタンスに復元するには、PowerShell を使用します。 

### <a name="portal"></a>ポータル 


Azure portal を使用してマネージド データベースを復旧するには、SQL Managed Instance の概要ページを開き、 **[削除されたデータベース]** を選択します。 復元する削除されたデータベースを選択し、バックアップから復元されたデータを使用して作成される新しいデータベースの名前を入力します。

  ![削除された Azure SQL インスタンス データベースの復元のスクリーンショット](./media/point-in-time-restore/restore-deleted-sql-managed-instance-annotated.png)

../../sql-database

### <a name="powershell"></a>PowerShell

同じインスタンスにデータベースを復元するには、パラメーターの値を更新し、次の PowerShell コマンドを実行します。 

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$deletedDatabaseName = "<Source database name>"
$targetDatabaseName = "<target database name>"

$deletedDatabase = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
-InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName
```

データベースを別の SQL Managed Instance に復元するには、ターゲット リソース グループとターゲット SQL Managed Instance の名前も指定します。

```powershell-interactive
$targetResourceGroupName = "<Resource group of target SQL Managed Instance>"
$targetInstanceName = "<Target SQL Managed Instance name>"

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName `
   -TargetResourceGroupName $targetResourceGroupName `
   -TargetInstanceName $targetInstanceName 
```

## <a name="overwrite-an-existing-database"></a>既存のデータベースを上書きする

既存のデータベースを上書きするには、次のことを行う必要があります。

1. 上書きする既存のデータベースを削除します。
2. ポイントインタイム リストアされたデータベースの名前を、削除したデータベースの名前に変更します。

### <a name="drop-the-original-database"></a>元のデータベースを削除する

Azure portal、PowerShell、または Azure CLI を使って、データベースを削除できます。

また、SQL Managed Instance に直接接続し、SQL Server Management Studio (SSMS) を起動し、次の Transact-SQL (T-SQL) コマンドを実行して、データベースを削除することもできます。

```sql
DROP DATABASE WorldWideImporters;
```

SQL Managed Instance 内のデータベースに接続するには、次のいずれかの方法を使用します。

- [Azure 仮想マシンを使用した SSMS/Azure Data Studio](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [ポイント対サイト](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [パブリック エンドポイント](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal で、SQL Managed Instance からデータベースを選択し、 **[削除]** を選択します。

   ![Azure portal を使ってデータベースを削除する](./media/point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

次の PowerShell コマンドを使用して、SQL Managed Instance から既存のデータベースを削除します。

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

次の Azure CLI コマンドを使用して、SQL Managed Instance から既存のデータベースを削除します。

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>元のデータベース名と一致するように新しいデータベース名を変更する

SQL Managed Instance に直接接続し、SQL Server Management Studio を起動します。 その後、次の Transact-SQL (T-SQL) クエリを実行します。 このクエリは、復元されたデータベースの名前を、上書きする予定の削除されたデータベースの名前に変更します。

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

SQL Managed Instance 内のデータベースに接続するには、次のいずれかの方法を使用します。

- [Azure 仮想マシン](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [ポイント対サイト](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [パブリック エンドポイント](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>次のステップ

[自動化されたバックアップ](../database/automated-backups-overview.md)について確認します。
