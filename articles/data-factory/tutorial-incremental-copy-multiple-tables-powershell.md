---
title: PowerShell を使用して複数のテーブルを増分コピーする
description: このチュートリアルでは、オンプレミスの SQL Server データベースにある複数のテーブルから Azure SQL データベースに差分データを増分コピーする Azure Data Factory パイプラインを作成します。
services: data-factory
ms.author: yexu
author: dearandyxu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/22/2018
ms.openlocfilehash: 46e0815ea341b732e20ebe7ffa9af355e1f35e87
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926474"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>SQL Server にある複数のテーブルから Azure SQL データベースにデータを増分読み込みする

このチュートリアルでは、オンプレミスの SQL Server にある複数のテーブルから Azure SQL データベースに差分データを読み込むパイプラインを持つ Azure Data Factory を作成します。    

このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * ソース データ ストアとターゲット データ ストアを準備します。
> * データ ファクトリを作成します。
> * セルフホステッド統合ランタイムを作成します。
> * 統合ランタイムをインストールします。 
> * リンクされたサービスを作成します。 
> * ソース データセット、シンク データセット、および基準値データセットを作成します。
> * パイプラインを作成、実行、監視します。
> * 結果を確認します。
> * ソース テーブルのデータを追加または更新します。
> * パイプラインを再実行して監視します。
> * 最終結果を確認します。

## <a name="overview"></a>概要
このソリューションを作成するための重要な手順を次に示します。 

1. **基準値列を選択する**。
    ソース データ ストアのテーブルごとに、いずれか 1 つの列を選択します。この列は、実行ごとに新しいレコードまたは更新されたレコードを特定する目的で使用されます。 通常、行が作成または更新されたときに常にデータが増える列を選択します (last_modify_time、ID など)。 この列の最大値が基準値として使用されます。

1. **基準値を格納するためのデータ ストアを準備する**。   
    このチュートリアルでは、SQL データベースに基準値を格納します。

1. **次のアクティビティを含んだパイプラインを作成する**。 
    
    a. パイプラインにパラメーターとして渡された一連のソース テーブル名を反復処理する ForEach アクティビティを作成する。 このアクティビティが、ソース テーブルごとに次のアクティビティを呼び出して各テーブルの差分読み込みを実行します。

    b. 2 つのルックアップ アクティビティを作成する。 1 つ目のルックアップ アクティビティは、前回の基準値を取得するために使用します。 2 つ目のルックアップ アクティビティは、新しい基準値を取得するために使用します。 これらの基準値は、コピー アクティビティに渡されます。

    c. コピー アクティビティを作成する。これは基準値列の値がかつての基準値より大きく、かつ新しい基準値よりも小さい行をソース データ ストアからコピーするアクティビティです。 その差分データが、ソース データ ストアから新しいファイルとして Azure BLOB ストレージにコピーされます。

    d. ストアド プロシージャ― アクティビティを作成する。これはパイプラインの次回実行に備えて基準値を更新するためのアクティビティです。 

    ソリューションの概略図を次に示します。 

    ![データの増分読み込み](media/tutorial-incremental-copy-multiple-tables-powershell/high-level-solution-diagram.png)


Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件
* **SQL Server**。 このチュートリアルでは、オンプレミスの SQL Server データベースをソース データ ストアとして使用します。 
* **Azure SQL データベース**。 シンク データ ストアとして SQL データベースを使用します。 SQL データベースがない場合の作成手順については、「[Azure SQL データベースを作成する](../sql-database/sql-database-get-started-portal.md)」を参照してください。 

### <a name="create-source-tables-in-your-sql-server-database"></a>SQL Server データベースにソース テーブルを作成する

1. SQL Server Management Studio を開き、オンプレミスの SQL Server データベースに接続します。

1. **サーバー エクスプローラー**で目的のデータベースを右クリックし、 **[新しいクエリ]** を選択します。

1. データベースに対して次の SQL コマンドを実行し、`customer_table` および `project_table` という名前のテーブルを作成します。

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Azure SQL データベースにターゲット テーブルを作成する
1. SQL Server Management Studio を開き、SQL Server データベースに接続します。

1. **サーバー エクスプローラー**で目的のデータベースを右クリックし、 **[新しいクエリ]** を選択します。

1. SQL データベースに対して次の SQL コマンドを実行し、`customer_table` と `project_table` という名前のテーブルを作成します。  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-the-azure-sql-database-to-store-the-high-watermark-value"></a>高基準値の格納用としてもう 1 つテーブルを Azure SQL データベースに作成する
1. SQL データベースに対して次の SQL コマンドを実行し、基準値の格納先として `watermarktable` という名前のテーブルを作成します。 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
1. 両方のソース テーブルの初期基準値を基準値テーブルに挿入します。

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Azure SQL データベースにストアド プロシージャを作成する 

次のコマンドを実行して、SQL データベースにストアド プロシージャを作成します。 パイプラインの実行後は都度、このストアド プロシージャによって基準値が更新されます。 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-the-azure-sql-database"></a>Azure SQL データベースにデータ型と新たなストアド プロシージャを作成する
次のクエリを実行して、2 つのストアド プロシージャと 2 つのデータ型を SQL データベースに作成します。 それらは、ソース テーブルから宛先テーブルにデータをマージするために使用されます。 

作業工程を簡単に始められるように、差分データをテーブル変数を介して渡すこのようなストアド プロシージャを直接使用し、それらを宛先ストアにマージします。 テーブル変数には、"多数" の差分行 (100 を超える行) が格納されることが想定されていない点に注意してください。  

多数の差分行を宛先ストアにマージする必要がある場合は、まずコピー アクティビティを使用してすべての差分データを宛先ストアの一時的な "ステージング" テーブルにコピーしてから、テーブル変数を使用せずに独自のストアド プロシージャを構築し、"ステージング" テーブルから "最終的な" テーブルにマージすることをお勧めします。 


```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE usp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE usp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

### <a name="azure-powershell"></a>Azure PowerShell
「[Azure PowerShell のインストールおよび構成](/powershell/azure/azurerm/install-azurerm-ps)」の手順に従って、最新の Azure PowerShell モジュールをインストールしてください。

## <a name="create-a-data-factory"></a>Data Factory の作成
1. 後で PowerShell コマンドで使用できるように、リソース グループ名の変数を定義します。 次のコマンド テキストを PowerShell にコピーし、[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)の名前を二重引用符で囲んで指定してコマンドを実行します。 例: `"adfrg"`。 
   
    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    リソース グループが既に存在する場合は、上書きされないようにすることができます。 `$resourceGroupName` 変数に別の値を割り当てて、コマンドをもう一度実行します。

1. データ ファクトリの場所の変数を定義します。 

    ```powershell
    $location = "East US"
    ```
1. Azure リソース グループを作成するには、次のコマンドを実行します。 

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ``` 
    リソース グループが既に存在する場合は、上書きされないようにすることができます。 `$resourceGroupName` 変数に別の値を割り当てて、コマンドをもう一度実行します。

1. データ ファクトリ名の変数を定義します。 

    > [!IMPORTANT]
    >  データ ファクトリ名は、グローバルに一意になるように更新してください。 たとえば、ADFIncMultiCopyTutorialFactorySP1127 とします。 

    ```powershell
    $dataFactoryName = "ADFIncMultiCopyTutorialFactory";
    ```
1. データ ファクトリを作成するには、次の **Set-AzDataFactoryV2** コマンドレットを実行します。 
    
    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

以下の点に注意してください。

* データ ファクトリの名前はグローバルに一意にする必要があります。 次のエラーが発生した場合は、名前を変更してからもう一度実行してください。

    ```
    The specified Data Factory name 'ADFIncMultiCopyTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory インスタンスを作成するには、Azure へのサインインに使用するユーザー アカウントが、共同作成者または所有者ロールのメンバーであるか、Azure サブスクリプションの管理者である必要があります。
* 現在 Data Factory が利用できる Azure リージョンの一覧については、次のページで目的のリージョンを選択し、 **[分析]** を展開して **[Data Factory]** を探してください。(「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」)。 データ ファクトリで使用するデータ ストア (Azure Storage、SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。

[!INCLUDE [data-factory-create-install-integration-runtime](../../includes/data-factory-create-install-integration-runtime.md)]


## <a name="create-linked-services"></a>リンクされたサービスを作成します
データ ストアおよびコンピューティング サービスをデータ ファクトリにリンクするには、リンクされたサービスをデータ ファクトリに作成します。 このセクションでは、オンプレミスの SQL Server データベースと Azure SQL データベースに対するリンクされたサービスを作成します。 

### <a name="create-the-sql-server-linked-service"></a>SQL Server のリンクされたサービスを作成する
この手順では、オンプレミス SQL Server データベースをデータ ファクトリにリンクします。

1. 次の内容を記述した **SqlServerLinkedService.json** という名前の JSON ファイルを C:\ADFTutorials\IncCopyMultiTableTutorial フォルダーに作成します。 SQL Server への接続に使用する認証に基づいて、右側のセクションを選択します。 ローカル フォルダーがまだ存在しない場合は新たに作成してください。 

    > [!IMPORTANT]
    > SQL Server への接続に使用する認証に基づいて、右側のセクションを選択します。

    SQL 認証を使用する場合は、次の JSON 定義をコピーします。

    ```json
    {  
        "name":"SqlServerLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=False;data source=<servername>;initial catalog=<database name>;user id=<username>;Password=<password>"
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    } 
   ```    
    Windows 認証を使用する場合は、次の JSON 定義をコピーします。

    ```json
    {  
        "name":"SqlServerLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=True;data source=<servername>;initial catalog=<database name>",
                "userName":"<username> or <domain>\\<username>",
                "password":{  
                    "type":"SecureString",
                    "value":"<password>"
                }
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    }
    ```
    > [!IMPORTANT]
    > - SQL Server への接続に使用する認証に基づいて、右側のセクションを選択します。
    > - &lt;integration runtime name> は、実際の統合ランタイムの名前に置き換えます。
    > - &lt;servername>、&lt;databasename>、&lt;username>、および &lt;password> を実際の SQL Server インスタンスの値に置き換えてからファイルを保存してください。
    > - ユーザー アカウントまたはサーバー名にスラッシュ文字 (`\`) を使用する必要がある場合は、エスケープ文字 (`\`) を使用します。 例: `mydomain\\myuser`。

1. PowerShell で次のコマンドを実行して、C:\ADFTutorials\IncCopyMultiTableTutorial フォルダーに切り替えます。

    ```powershell
    Set-Location 'C:\ADFTutorials\IncCopyMultiTableTutorial'
    ```

1. **Set-AzDataFactoryV2LinkedService** コマンドレットを実行して、リンクされたサービス AzureStorageLinkedService を作成します。 次の例では、*ResourceGroupName* パラメーターと *DataFactoryName* パラメーターの値を渡しています。 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerLinkedService" -File ".\SqlServerLinkedService.json"
    ```

    出力例を次に示します。

    ```json
    LinkedServiceName : SqlServerLinkedService
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerLinkedService
    ```

### <a name="create-the-sql-database-linked-service"></a>SQL データベースのリンクされたサービスを作成する
1. 次の内容を記述した **AzureSQLDatabaseLinkedService.json** という名前の JSON ファイルを C:\ADFTutorials\IncCopyMultiTableTutorial フォルダーに作成します (ADF フォルダーが存在しない場合は作成してください)。&lt;servername&gt;、&lt;database name&gt;、&lt;user name&gt;、&lt;password&gt; を実際の SQL Server データベースの名前、データベースの名前、ユーザー名、パスワードに置き換えてからファイルを保存してください。 

    ```json
    {  
        "name":"AzureSQLDatabaseLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"AzureSqlDatabase",
            "typeProperties":{  
                "connectionString":"integrated security=False;encrypt=True;connection timeout=30;data source=<servername>.database.windows.net;initial catalog=<database name>;user id=<user name>;Password=<password>;"
            }
        }
    }
    ```
1. PowerShell で **Set-AzDataFactoryV2LinkedService** コマンドレットを実行して、リンクされたサービス AzureSQLDatabaseLinkedService を作成します。 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    出力例を次に示します。

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>データセットを作成する
この手順では、データ ソース、データの宛先、および基準値の格納場所を表すデータセットを作成します。

### <a name="create-a-source-dataset"></a>ソース データセットを作成する

1. 以下の内容を記述した **SourceDataset.json** という名前の JSON ファイルを同じフォルダー内に作成します。 

    ```json
    {  
        "name":"SourceDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"SqlServerLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  
    
            ],
            "type":"SqlServerTable",
            "schema":[  
    
            ]
        }
    }
   
    ```

    このパイプライン内のコピー アクティビティは、テーブル全体を読み込むことはせずに、SQL クエリを使用してデータを読み込みます。

1. **Set-AzDataFactoryV2Dataset** コマンドレットを実行して、データセット SourceDataset を作成します。
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    このコマンドレットの出力例を次に示します。
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-sink-dataset"></a>シンク データセットを作成する

1. 次の内容を記述した **SinkDataset.json** という名前の JSON ファイルを同じフォルダー内に作成します。 tableName 要素は、実行時にパイプラインによって動的に設定されます。 パイプライン内の ForEach アクティビティは、一連のテーブル名を反復処理しながら、各イテレーションの中でこのデータセットにテーブル名を渡します。 

    ```json
    {  
        "name":"SinkDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"AzureSQLDatabaseLinkedService",
                "type":"LinkedServiceReference"
            },
            "parameters":{  
                "SinkTableName":{  
                    "type":"String"
                }
            },
            "annotations":[  
    
            ],
            "type":"AzureSqlTable",
            "typeProperties":{  
                "tableName":{  
                    "value":"@dataset().SinkTableName",
                    "type":"Expression"
                }
            }
        }
    }
    ```

1. **Set-AzDataFactoryV2Dataset** コマンドレットを実行して、データセット SinkDataset を作成します。
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    このコマンドレットの出力例を次に示します。
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-a-watermark"></a>基準値用のデータセットを作成する
この手順では、高基準値を格納するためのデータセットを作成します。 

1. 以下の内容を記述した **WatermarkDataset.json** という名前の JSON ファイルを同じフォルダー内に作成します。 

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
1. **Set-AzDataFactoryV2Dataset** コマンドレットを実行して、データセット WatermarkDataset を作成します。
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    このコマンドレットの出力例を次に示します。
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>パイプラインを作成する
このパイプラインは、一連のテーブル名をパラメーターとして受け取ります。 **ForEach アクティビティ**は、一連のテーブル名を反復処理しながら、次の操作を実行します。 

1. **ルックアップ アクティビティ**を使用して古い基準値 (初期値または前回のイテレーションで使用された値) を取得します。

1. **ルックアップ アクティビティ**を使用して新しい基準値 (ソース テーブルの基準値列の最大値) を取得します。

1. **コピー アクティビティ**を使用して、この 2 つの基準値の間に存在するデータをソース データベースからターゲット データベースにコピーします。

1. **ストアド プロシージャ アクティビティ**を使用して古い基準値を更新します。この値が、次のイテレーションの最初のステップで使用されます。 

### <a name="create-the-pipeline"></a>パイプラインを作成する
1. 次の内容を記述した **IncrementalCopyPipeline.json** という名前の JSON ファイルを同じフォルダー内に作成します。 

    ```json
    {  
        "name":"IncrementalCopyPipeline",
        "properties":{  
            "activities":[  
                {  
                    "name":"IterateSQLTables",
                    "type":"ForEach",
                    "dependsOn":[  
    
                    ],
                    "userProperties":[  
    
                    ],
                    "typeProperties":{  
                        "items":{  
                            "value":"@pipeline().parameters.tableList",
                            "type":"Expression"
                        },
                        "isSequential":false,
                        "activities":[  
                            {  
                                "name":"LookupOldWaterMarkActivity",
                                "type":"Lookup",
                                "dependsOn":[  
    
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"AzureSqlSource",
                                        "sqlReaderQuery":{  
                                            "value":"select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'",
                                            "type":"Expression"
                                        }
                                    },
                                    "dataset":{  
                                        "referenceName":"WatermarkDataset",
                                        "type":"DatasetReference"
                                    }
                                }
                            },
                            {  
                                "name":"LookupNewWaterMarkActivity",
                                "type":"Lookup",
                                "dependsOn":[  
    
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"SqlServerSource",
                                        "sqlReaderQuery":{  
                                            "value":"select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}",
                                            "type":"Expression"
                                        }
                                    },
                                    "dataset":{  
                                        "referenceName":"SourceDataset",
                                        "type":"DatasetReference"
                                    },
                                    "firstRowOnly":true
                                }
                            },
                            {  
                                "name":"IncrementalCopyActivity",
                                "type":"Copy",
                                "dependsOn":[  
                                    {  
                                        "activity":"LookupOldWaterMarkActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    },
                                    {  
                                        "activity":"LookupNewWaterMarkActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    }
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"SqlServerSource",
                                        "sqlReaderQuery":{  
                                            "value":"select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'",
                                            "type":"Expression"
                                        }
                                    },
                                    "sink":{  
                                        "type":"AzureSqlSink",
                                        "sqlWriterStoredProcedureName":{  
                                            "value":"@{item().StoredProcedureNameForMergeOperation}",
                                            "type":"Expression"
                                        },
                                        "sqlWriterTableType":{  
                                            "value":"@{item().TableType}",
                                            "type":"Expression"
                                        },
                                        "storedProcedureTableTypeParameterName":{  
                                            "value":"@{item().TABLE_NAME}",
                                            "type":"Expression"
                                        },
                                        "disableMetricsCollection":false
                                    },
                                    "enableStaging":false
                                },
                                "inputs":[  
                                    {  
                                        "referenceName":"SourceDataset",
                                        "type":"DatasetReference"
                                    }
                                ],
                                "outputs":[  
                                    {  
                                        "referenceName":"SinkDataset",
                                        "type":"DatasetReference",
                                        "parameters":{  
                                            "SinkTableName":{  
                                                "value":"@{item().TABLE_NAME}",
                                                "type":"Expression"
                                            }
                                        }
                                    }
                                ]
                            },
                            {  
                                "name":"StoredProceduretoWriteWatermarkActivity",
                                "type":"SqlServerStoredProcedure",
                                "dependsOn":[  
                                    {  
                                        "activity":"IncrementalCopyActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    }
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "storedProcedureName":"[dbo].[usp_write_watermark]",
                                    "storedProcedureParameters":{  
                                        "LastModifiedtime":{  
                                            "value":{  
                                                "value":"@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}",
                                                "type":"Expression"
                                            },
                                            "type":"DateTime"
                                        },
                                        "TableName":{  
                                            "value":{  
                                                "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}",
                                                "type":"Expression"
                                            },
                                            "type":"String"
                                        }
                                    }
                                },
                                "linkedServiceName":{  
                                    "referenceName":"AzureSQLDatabaseLinkedService",
                                    "type":"LinkedServiceReference"
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters":{  
                "tableList":{  
                    "type":"array"
                }
            },
            "annotations":[  
    
            ]
        }
    }
    ```
1. **Set-AzDataFactoryV2Pipeline** コマンドレットを実行して、パイプライン IncrementalCopyPipeline を作成します。
    
   ```powershell
   Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   出力例を次に示します。 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```
 
## <a name="run-the-pipeline"></a>パイプラインを実行する

1. 次の内容を記述した **Parameters.json** という名前のパラメーター ファイルを同じフォルダーに作成します。

    ```json
    {
        "tableList": 
        [
            {
                "TABLE_NAME": "customer_table",
                "WaterMark_Column": "LastModifytime",
                "TableType": "DataTypeforCustomerTable",
                "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
            },
            {
                "TABLE_NAME": "project_table",
                "WaterMark_Column": "Creationtime",
                "TableType": "DataTypeforProjectTable",
                "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
            }
        ]
    }
    ```
1. **Invoke-AzDataFactoryV2Pipeline** コマンドレットを使って IncrementalCopyPipeline パイプラインを実行します。 プレースホルダーはそれぞれ実際のリソース グループとデータ ファクトリ名に置き換えてください。

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"        
    ``` 

## <a name="monitor-the-pipeline"></a>パイプラインの監視

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. **[すべてのサービス]** を選択し、キーワード "*データ ファクトリ*" で検索して、 **[データ ファクトリ]** を選択します。 

1. データ ファクトリの一覧から**目的のデータ ファクトリ**を探して選択し、[データ ファクトリ] ページを開きます。 

1. **[データ ファクトリ]** ページの **[Author & Monitor]\(作成と監視\)** を選択して、別のタブで Azure Data Factory を起動します。

1. **[始めましょう]** ページで、左側の **[監視]** を選択します。 
![パイプラインの実行](media/doc-common-process/get-started-page-monitor-button.png)    

1. すべてのパイプラインの実行とその状態を確認できます。 次の例では、パイプラインの実行が、**成功**状態であることに注目してください。 パイプラインに渡されたパラメーターを確認するには、 **[パラメーター]** 列のリンクを選択します。 エラーが発生した場合は、 **[エラー]** 列にリンクが表示されます。

    ![パイプライン実行](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-pipeline-runs-4.png)    
1. **[アクション]** 列のリンクを選択すると、そのパイプラインに関するすべてのアクティビティの実行が表示されます。 

1. 再度**パイプラインの実行**ビューに移動するには、 **[すべてのパイプラインの実行]** を選択します。 

## <a name="review-the-results"></a>結果の確認
SQL Server Management Studio からターゲット SQL データベースに対して次のクエリを実行し、ソース テーブルからターゲット テーブルにデータがコピーされていることを確かめます。 

**クエリ** 
```sql
select * from customer_table
```

**Output**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**クエリ**

```sql
select * from project_table
```

**Output**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**クエリ**

```sql
select * from watermarktable
```

**Output**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

2 つのテーブルの基準値が更新されたことがわかります。 

## <a name="add-more-data-to-the-source-tables"></a>ソース テーブルにデータを追加する

コピー元の SQL Server データベースに次のクエリを実行して、customer_table 内の既存の行を更新します。 project_table に新しい行を挿入します。 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>パイプラインを再実行する

1. 今度は、次の PowerShell コマンドを実行してパイプラインを再実行します。

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupname -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"
    ```
1. 「[パイプラインの監視](#monitor-the-pipeline)」セクションの手順に従ってパイプラインの実行を監視します。 パイプラインが**進行中**の状態にあるとき、 **[アクション]** には、パイプラインの実行をキャンセルするためのアクション リンクが別途表示されています。 

1. パイプラインの実行に成功するまで、 **[最新の情報に更新]** を選択して一覧を更新します。 

1. 必要に応じて、 **[アクション]** の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクを選択すると、このパイプラインの実行に関連付けられているアクティビティの実行がすべて表示されます。 

## <a name="review-the-final-results"></a>最終結果を確認する
SQL Server Management Studio からターゲット データベースに対して次のクエリを実行し、更新されたデータや新しいデータがソース テーブルからターゲット テーブルにコピーされていることを確かめます。 

**クエリ** 
```sql
select * from customer_table
```

**Output**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**PersonID** 3 を見ると、**Name** と **LastModifytime** が新しい値であることがわかります。 

**クエリ**

```sql
select * from project_table
```

**Output**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

project_table に **NewProject** というエントリが追加されていることがわかります。 

**クエリ**

```sql
select * from watermarktable
```

**Output**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

2 つのテーブルの基準値が更新されたことがわかります。
     
## <a name="next-steps"></a>次の手順
このチュートリアルでは、以下の手順を実行しました。 

> [!div class="checklist"]
> * ソース データ ストアとターゲット データ ストアを準備します。
> * データ ファクトリを作成します。
> * 自己ホスト型統合ランタイム (IR) を作成します。
> * 統合ランタイムをインストールします。
> * リンクされたサービスを作成します。 
> * ソース データセット、シンク データセット、および基準値データセットを作成します。
> * パイプラインを作成、実行、監視します。
> * 結果を確認します。
> * ソース テーブルのデータを追加または更新します。
> * パイプラインを再実行して監視します。
> * 最終結果を確認します。

次のチュートリアルに進み、Azure 上の Spark クラスターを使ってデータを変換する方法について学習しましょう。

> [!div class="nextstepaction"]
>[Change Tracking テクノロジを使用して Azure SQL Database から Azure BLOB ストレージにデータを増分読み込みする](tutorial-incremental-copy-change-tracking-feature-powershell.md)


