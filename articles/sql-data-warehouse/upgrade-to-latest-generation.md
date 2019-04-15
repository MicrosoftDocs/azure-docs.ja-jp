---
title: Azure SQL Data Warehouse の最新世代へのアップグレード | Microsoft Docs
description: Azure SQL Data Warehouse を最新世代の Azure ハードウェアとストレージ アーキテクチャにアップグレードします。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.openlocfilehash: a8bd260db7a141ce845ce7fb5b7e10f642907b82
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2019
ms.locfileid: "58851084"
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>SQL Data Warehouse をアップグレードしてパフォーマンスを最適化する

Azure SQL Data Warehouse を最新世代の Azure ハードウェアとストレージ アーキテクチャにアップグレードします。

## <a name="why-upgrade"></a>アップグレードする理由

[サポートされるリージョン](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)の Azure portal で SQL Data Warehouse のコンピューティング最適化 Gen2 階層にシームレスにアップグレードできるようになりました。 セルフアップグレードがサポートされていないリージョンの場合は、サポートされているリージョンにアップグレードするか、またはそのリージョンでセルフアップグレードが利用できるようになるまで待つことができます。 今すぐアップグレードして、最新世代の Azure ハードウェアと、高パフォーマンス、高スケーラビリティ、無制限の列指向ストレージなどの拡張されたストレージ アーキテクチャを利用してください。 

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

## <a name="applies-to"></a>適用対象

このアップグレードは、[サポートされるリージョン](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)の、コンピューティング最適化 Gen1 階層のデータ ウェアハウスに適用されます。

## <a name="before-you-begin"></a>開始する前に

1. ご自分の[リージョン](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)で GEN1 から GEN2 への移行がサポートされているかどうかをチェックしてください。 自動移行の日付に注意してください。 自動化されたプロセスとの競合を避けるため、自動化されたプロセスの開始日より前に手動移行を計画します。
2. まだサポートされていないリージョンの場合は、リージョンが追加されるまで引き続きチェックするか、またはサポートされているリージョンに[復元を使用してアップグレード](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal)してください。
3. サポートされているリージョンの場合は、[Azure portal でアップグレード](#upgrade-in-a-supported-region-using-the-azure-portal)します
4. 次のマッピングを使用して、コンピューティング最適化 Gen1 階層の現在のパフォーマンス レベルに基づいて、データ ウェアハウスの**推奨パフォーマンス レベルを選択**します。

   | コンピューティング最適化 Gen1 階層 | コンピューティング最適化 Gen2 階層 |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c            |
   |            DW600            |           DW500c            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

> [!Note]
> 推奨されるパフォーマンス レベルは、直接変換ではありません。 たとえば、DW600 から DW500c に移行することをお勧めします。

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>サポートされているリージョンで Azure portal を使用してアップグレードする

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!NOTE]
> Azure portal による Gen1 から Gen2 への移行は永続的です。 Gen1 に戻るプロセスはありません。  

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインします

[Azure Portal](https://portal.azure.com/) にサインインします。

1. アップグレードするコンピューティング最適化 Gen1 階層のデータ ウェアハウスが一時停止している場合は、[データ ウェアハウスを再開](pause-and-resume-compute-portal.md)します。

   > [!NOTE]
   > Gen2 に移行するには、Azure SQL Data Warehouse が実行されている必要があります。

2. 数分間のダウンタイムに備えます。 

3. コンピューティング最適化 Gen1 パフォーマンス レベルに対するすべてのコード参照を識別し、同等のコンピューティング最適化 Gen2 パフォーマンス レベルに変更します。 アップグレードする前にコード参照を更新する必要がある 2 つの例を以下に示します。

   元の Gen1 PowerShell コマンド:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   変更後:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE] 
   > -RequestedServiceObjectiveName "DW300" が -RequestedServiceObjectiveName "DW300**c**" に変更されています
   >

   元の Gen1 T-SQL コマンド:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   変更後:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ; 
   ```
   > [!NOTE] 
   > SERVICE_OBJECTIVE = 'DW300' が SERVICE_OBJECTIVE = 'DW300**c**' に変更されています

## <a name="start-the-upgrade"></a>アップグレードを開始する

1. Azure portal でコンピューティング最適化 Gen1 階層のデータ ウェアハウスに移動します。 アップグレードするコンピューティング最適化 Gen1 階層のデータ ウェアハウスが一時停止している場合は、[データ ウェアハウスを再開](pause-and-resume-compute-portal.md)します。 
2. [タスク] タブで **[Gen2 にアップグレードします]** カードを選択します。![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)
    
    > [!NOTE]
    > [タスク] タブに **[Gen2 にアップグレードします]** カードが表示されない場合は、お使いのサブスクリプションの種類が現在のリージョンで制限されています。
    > [サポート チケットを提出](sql-data-warehouse-get-started-create-support-ticket.md)し、サブスクリプションをホワイトリストに登録してもらってください。

3. アップグレードの前に、ワークロードの実行が完了し、休止していることを確認します。 データ ウェアハウスがコンピューティング最適化 Gen2 階層のデータ ウェアハウスとしてオンラインに戻る前に、数分間のダウンタイムが発生します。 **[アップグレード] を選択します**。

   ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. Azure Portal で状態を確認して**アップグレードを監視**します。

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)

   アップグレード プロセスの最初の手順では、すべてのセッションが強制終了されるスケール操作 ("アップグレード中 - オフライン") が実行され、接続が切断されます。 

   アップグレード プロセスの 2 番目の手順は、データの移行 ("アップグレード中 - オンライン") です。 データの移行は、少量のオンライン バックグラウンド プロセスです。 このプロセスでは、列指向のデータが、以前のストレージ アーキテクチャから、ローカル SSD キャッシュを使用して、新しいストレージ アーキテクチャにゆっくり移行されます。 この期間中、データ ウェアハウスはクエリと読み込みを行うためにオンラインになります。 移行されたかどうかに関係なく、データをクエリに使用できます。 データの移行速度は、データ サイズ、パフォーマンス レベル、列ストア セグメントの数に応じて変化します。 

5. **オプションの推奨事項:** スケーリング操作が完了すると、データ移行バックグラウンド プロセスの速度を上げることができます。 大規模な SLO とリソース クラスにあるクエリ対象のすべてのプライマリ列ストア テーブルに対して [Alter Index rebuild](sql-data-warehouse-tables-index.md) を実行して、データ移動を強制実行できます。 この操作は、少量のバックグラウンド プロセスに比べて**オフライン**であり、テーブルのサイズと数によっては完了に何時間もかかることがあります。 ただし、完了した後は、高品質の行グループを含む新しい拡張ストレージ アーキテクチャのため、データの移行ははるかに速くなります。
 
> [!NOTE]
> Alter Index rebuild はオフライン操作であり、再構築が完了するまでテーブルは使用できなくなります。

次のクエリでは、データの移行を短縮するために必要な Alter Index Rebuild コマンドが生成されます。

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON [' 
       + Schema_name(tbl.schema_id) + '].[' 
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE 
                                                         WHEN ( 
                                                     (SELECT Count(*) 
                                                      FROM   sys.partitions 
                                                             part2 
                                                      WHERE  part2.index_id 
                                                             = idx.index_id 
                                                             AND 
                                                     idx.object_id = 
                                                     part2.object_id) 
                                                     > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              ELSE '' 
                                                       END ) + '; SELECT ''[' + 
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' + 
              Object_name(idx.object_id) + '] ' + ( 
              CASE 
                WHEN ( (SELECT Count(*) 
                        FROM   sys.partitions 
                               part2 
                        WHERE 
                     part2.index_id = 
                     idx.index_id 
                     AND idx.object_id 
                         = part2.object_id) > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              + ' completed'';' 
              ELSE ' completed'';' 
                                                    END ) 
FROM   sys.indexes idx 
       INNER JOIN sys.tables tbl 
               ON idx.object_id = tbl.object_id 
       LEFT OUTER JOIN sys.partitions part 
                    ON idx.index_id = part.index_id 
                       AND idx.object_id = part.object_id 
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE'; 
```

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Azure portal で復元を使用して Azure の地理的リージョンからアップグレードする

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Azure portal を使用してユーザー定義の復元ポイントを作成する

1. [Azure Portal](https://portal.azure.com/) にサインインします。

2. 復元ポイントを作成する対象の SQL データ ウェアハウスに移動します。

3. [概要] セクションの上部にある **[+ 新しい復元ポイント]** を選択します。

    ![新しい復元ポイント](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_0.png)

4. 復元ポイントの名前を指定します。

    ![復元ポイントの名前](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Azure portal を使用してアクティブまたは一時停止中のデータベースを復元する

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. 復元元の SQL データ ウェアハウスに移動します。
3. [概要] セクションの上部にある **[復元]** を選択します。

    ![ 復元の概要](./media/sql-data-warehouse-restore-database-portal/restoring_0.png)

4. **[自動復元ポイント]** または **[ユーザー定義の復元ポイント]** を選択します。

    ![自動復元ポイント](./media/sql-data-warehouse-restore-database-portal/restoring_1.png)

5. ユーザー定義の復元ポイントの場合は、**復元ポイントを選択**するか、または**新しいユーザー定義の復元ポイントを作成**します。 Gen2 でサポートされている地理的リージョンで、サーバーを選択します。 

    ![ユーザー定義の復元ポイント](./media/sql-data-warehouse-restore-database-portal/restoring_2_udrp.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>PowerShell を使用して Azure 地理的リージョンから復元する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

データベースを復旧するには、[Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) コマンドレットを使用します。

> [!NOTE]
> Gen2 への geo リストアを行うことができます。 そのためには、省略可能なパラメーターとして Gen2 の ServiceObjectiveName (例: DW1000**c**) を指定します。

1. Windows PowerShell を開きます。
2. Azure アカウントに接続して、アカウントに関連付けられているすべてのサブスクリプションを一覧表示します。
3. 復元するデータベースを含むサブスクリプションを選択します。
4. 復旧するデータベースを取得します。
5. Gen2 ServiceObjectiveName を指定して、データベースの復旧要求を作成します。
6. geo リストアされたデータベースの状態を確認します。

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>" -RequestedServiceObjectiveName "DW300c"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> 復元が完了した後にデータベースを構成する方法については、「 [復旧後のデータベースの構成](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery)」を参照してください。

ソース データベースの TDE が有効な場合、復旧したデータベースも TDE が有効になります。


データ ウェアハウスで問題が発生した場合は、[サポート リクエスト](sql-data-warehouse-get-started-create-support-ticket.md)を作成し、考えられる原因を "Gen2 アップグレード" としてください。

## <a name="next-steps"></a>次の手順

アップグレードしたデータ ウェアハウスはオンラインです。 拡張アーキテクチャを利用するには、[ワークロード管理のためのリソース クラス](resource-classes-for-workload-management.md)に関する記事をご覧ください。
