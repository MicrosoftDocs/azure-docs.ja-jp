---
title: クイック スタート:Azure SQL Data Warehouse のコンピューティングをスケールアウトする - T-SQL | Microsoft Docs
description: T-SQL と SQL Server Management Studio (SSMS) を使用して、Azure SQL Data Warehouse のコンピューティングをスケーリングします。 コンピューティングをスケールアウトしてパフォーマンスを向上させます。または、コンピューティングをスケールバックしてコストを削減します。
services: sql-data-warehouse
author: Antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: Anthony.vanGemert
ms.reviewer: igorstan
ms.openlocfilehash: b5f3eb8a8e323add287dba8d9c590e89ea4e1fa7
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479230"
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-using-t-sql"></a>クイック スタート:T-SQL を使用して Azure SQL Data Warehouse のコンピューティングをスケーリングする

T-SQL と SQL Server Management Studio (SSMS) を使用して、Azure SQL Data Warehouse のコンピューティングをスケーリングします。 [コンピューティングをスケールアウト](sql-data-warehouse-manage-compute-overview.md)してパフォーマンスを向上させます。または、コンピューティングをスケールバックしてコストを削減します。 

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="before-you-begin"></a>開始する前に

最新バージョンの [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) をダウンロードしてインストールします。
 
## <a name="create-a-data-warehouse"></a>データ ウェアハウスの作成

[ポータルでの作成と接続に関するクイックスタート](create-data-warehouse-portal.md)を使用して、**mySampleDataWarehouse** という名前のデータ ウェアハウスを作成します。 クイックスタートを完了して、ファイアウォール規則があり、SQL Server Management Studio 内からデータ ウェアハウスに接続できることを確認します。

## <a name="connect-to-the-server-as-server-admin"></a>サーバー管理者としてサーバーに接続する

このセクションでは、[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) を使って、Azure SQL Server に対する接続を確立します。

1. SQL Server Management Studio を開きます。

2. **[サーバーへの接続]** ダイアログ ボックスで、次の情報を入力します。

   | Setting       | 推奨値 | 説明 | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | サーバーの種類 | データベース エンジン | この値は必須です |
   | サーバー名 | 完全修飾サーバー名 | たとえば、**mynewserver-20171113.database.windows.net** です。 |
   | Authentication | SQL Server 認証 | このチュートリアルで構成した認証の種類は "SQL 認証" のみです。 |
   | ログイン | サーバー管理者アカウント | サーバーの作成時に指定したアカウントです。 |
   | パスワード | サーバー管理者アカウントのパスワード | これは、サーバーの作成時に指定したパスワードです。 |

    ![[サーバーへの接続]](media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

4. **[接続]** をクリックします。 SSMS でオブジェクト エクスプローラー ウィンドウが開きます。 

5. オブジェクト エクスプローラーで、 **[データベース]** を展開します。 **mySampleDatabase** を展開して、新しいデータベースのオブジェクトを表示します。

    ![データベース オブジェクト](media/create-data-warehouse-portal/connected.png) 

## <a name="view-service-objective"></a>サービス目標の表示
サービス目標設定には、データ ウェアハウスの Data Warehouse ユニットの数が含まれています。 

データ ウェアハウスの現在の Data Warehouse ユニットを表示するには:

1. **mynewserver-20171113.database.windows.net** への接続で、**System Databases** を展開します。
2. **master** を右クリックし、 **[新しいクエリ]** を選択します。 新しいクエリ ウィンドウが開きます。
3. 次のクエリを実行して、sys.database_service_objectives 動的管理ビューから選択します。 

    ```sql
    SELECT
        db.name [Database]
    ,   ds.edition [Edition]
    ,   ds.service_objective [Service Objective]
    FROM
        sys.database_service_objectives ds
    JOIN
        sys.databases db ON ds.database_id = db.database_id
    WHERE 
        db.name = 'mySampleDataWarehouse'
    ```

4. 次の結果は、**mySampleDataWarehouse** のサービス目標が DW400 であることを示しています。 

    ![現在の DWU の表示](media/quickstart-scale-compute-tsql/view-current-dwu.png)


## <a name="scale-compute"></a>コンピューティングのスケーリング
SQL Data Warehouse では、Data Warehouse ユニットを調整してコンピューティング リソースを増減させることができます。 [ポータルでの作成と接続](create-data-warehouse-portal.md)では、**mySampleDataWarehouse** を作成し、それを 400 DWU で初期化しました。 次の手順では、**mySampleDataWarehouse** の DWU を調整します。

Data Warehouse ユニットを変更するには:

1. **master** を右クリックし、 **[新しいクエリ]** を選択します。
2. [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) T-SQL ステートメントを使用して、サービス目標を変更します。 次のクエリを実行して、サービス目標を DW300 に変更します。 

    ```Sql
    ALTER DATABASE mySampleDataWarehouse
    MODIFY (SERVICE_OBJECTIVE = 'DW300c')
    ;
    ```

## <a name="monitor-scale-change-request"></a>スケール変更要求の監視
以前の変更要求の進行状況を確認するには、`WAITFORDELAY` T-SQL 構文を使用して sys.dm_operation_status 動的管理ビュー (DMV) をポーリングします。

サービス オブジェクトの変更状態をポーリングするには:

1. **master** を右クリックし、 **[新しいクエリ]** を選択します。
2. 次のクエリを実行して、sys.dm_operation_status DMV をポーリングします。

    ```sql
    WHILE 
    (
        SELECT TOP 1 state_desc
        FROM sys.dm_operation_status
        WHERE 
            1=1
            AND resource_type_desc = 'Database'
            AND major_resource_id = 'MySampleDataWarehouse'
            AND operation = 'ALTER DATABASE'
        ORDER BY
            start_time DESC
    ) = 'IN_PROGRESS'
    BEGIN
        RAISERROR('Scale operation in progress',0,0) WITH NOWAIT;
        WAITFOR DELAY '00:00:05';
    END
    PRINT 'Complete';
    ```
3. 結果の出力には、状態のポーリングのログが表示されます。

    ![操作の状態](media/quickstart-scale-compute-tsql/polling-output.png)

## <a name="check-data-warehouse-state"></a>データ ウェアハウスの状態の確認

データ ウェアハウスが一時停止すると、T-SQL で接続することはできません。 データ ウェアハウスの現在の状態を確認する場合は、PowerShell コマンドレットを使用できます。 例については、[データ ウェアハウスの状態の確認 - Powershell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) に関するページを参照してください。 

## <a name="check-operation-status"></a>操作の状態の確認

SQL Data Warehouse に対するさまざまな管理操作に関する情報を返すには、[sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) DMV で次のクエリを実行します。 たとえば、操作と操作の状態 (IN_PROGRESS または COMPLETED) が返されます。

```sql
SELECT *
FROM
    sys.dm_operation_status
WHERE
    resource_type_desc = 'Database'
AND 
    major_resource_id = 'MySampleDataWarehouse'
```


## <a name="next-steps"></a>次の手順
ここでは、データ ウェアハウスの計算をスケーリングする方法について学習しました。 Azure SQL Data Warehouse の詳細については、データの読み込みに関するチュートリアルを参照してください。

> [!div class="nextstepaction"]
>[SQL Data Warehouse にデータを読み込む](load-data-from-azure-blob-storage-using-polybase.md)
