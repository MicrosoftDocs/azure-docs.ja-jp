---
title: PowerShell を使用してテーブルを増分コピーする
description: このチュートリアルでは、Azure SQL データベースから Azure BLOB ストレージにデータを増分コピーする Azure Data Factory パイプラインを作成します。
services: data-factory
author: dearandyxu
ms.author: yexu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-dt-2019
ms.date: 01/22/2018
ms.openlocfilehash: d6b41f06f9e5d64532b5684e6bc40d017a8c4434
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74925217"
---
# <a name="incrementally-load-data-from-an-azure-sql-database-to-azure-blob-storage-using-powershell"></a>PowerShell を使用して Azure SQL データベースから Azure Blob Storage にデータを増分読み込みする

このチュートリアルでは、Azure SQL データベース内のテーブルから Azure BLOB ストレージに差分データを読み込むパイプラインを使用して Azure Data Factory を作成します。 

このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * 基準値を格納するためのデータ ストアを準備します。
> * データ ファクトリを作成します。
> * リンクされたサービスを作成します。 
> * ソース データセット、シンク データセット、および基準値データセットを作成します。
> * パイプラインを作成します。
> * パイプラインを実行します。
> * パイプラインの実行を監視します。 

## <a name="overview"></a>概要
ソリューションの概略図を次に示します。 

![データの増分読み込み](media/tutorial-Incrementally-copy-powershell/incrementally-load.png)

このソリューションを作成するための重要な手順を次に示します。 

1. **基準値列を選択する**。
    ソース データ ストアのいずれか 1 つの列を選択します。実行ごとに新しいレコードまたは更新されたレコードを切り分ける目的で使用されます。 通常、行が作成または更新されたときに常にデータが増える列を選択します (last_modify_time、ID など)。 この列の最大値が基準値として使用されます。

2. **基準値を格納するためのデータ ストアを準備する**。   
    このチュートリアルでは、SQL データベースに基準値を格納します。
    
3. **次のワークフローを含んだパイプラインを作成する**。 
    
    このソリューションのパイプラインには、次のアクティビティがあります。
  
    * 2 つのルックアップ アクティビティを作成する。 1 つ目のルックアップ アクティビティは、前回の基準値を取得するために使用します。 2 つ目のルックアップ アクティビティは、新しい基準値を取得するために使用します。 これらの基準値は、コピー アクティビティに渡されます。 
    * コピー アクティビティを作成する。これは基準値列の値がかつての基準値より大きく、かつ新しい基準値よりも小さい行をソース データ ストアからコピーするアクティビティです。 その差分データが、ソース データ ストアから新しいファイルとして BLOB ストレージにコピーされます。 
    * ストアド プロシージャ― アクティビティを作成する。これはパイプラインの次回実行に備えて基準値を更新するためのアクティビティです。 


Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure SQL データベース**。 ソース データ ストアとして使うデータベースです。 SQL データベースがない場合の作成手順については、「[Azure SQL データベースを作成する](../sql-database/sql-database-get-started-portal.md)」を参照してください。
* **Azure Storage**。 シンク データ ストアとして使用する BLOB ストレージです。 Azure ストレージ アカウントがない場合の作成手順については、「[ストレージ アカウントの作成](../storage/common/storage-quickstart-create-account.md)」を参照してください。 adftutorial という名前のコンテナーを作成します。 
* **Azure PowerShell**。 「[Azure PowerShell のインストールおよび構成](/powershell/azure/install-Az-ps)」に記載されている手順に従います。

### <a name="create-a-data-source-table-in-your-sql-database"></a>SQL データベースにデータ ソース テーブルを作成する
1. SQL Server Management Studio を開きます。 **サーバー エクスプローラー**で目的のデータベースを右クリックし、 **[新しいクエリ]** を選択します。

2. SQL データベースに対して次の SQL コマンドを実行し、`data_source_table` という名前のテーブルをデータ ソース ストアとして作成します。 
    
    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    このチュートリアルでは、LastModifytime を基準値列として使用します。 データ ソース ストアに格納されているデータを次の表に示します。

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>高基準値の格納用としてもう 1 つテーブルを SQL データベースに作成する
1. SQL データベースに対して次の SQL コマンドを実行し、基準値の格納先として `watermarktable` という名前のテーブルを作成します。  
    
    ```sql
    create table watermarktable
    (
    
    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. ソース データ ストアのテーブル名と組み合わせて高基準値の既定値を設定します。 このチュートリアルでは、テーブル名は data_source_table です。

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. `watermarktable` テーブル内のデータを確認します。
    
    ```sql
    Select * from watermarktable
    ```
    出力: 

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>SQL データベースにストアド プロシージャを作成する 

次のコマンドを実行して、SQL データベースにストアド プロシージャを作成します。

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN
    
    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName
    
END
```

## <a name="create-a-data-factory"></a>Data Factory の作成
1. 後で PowerShell コマンドで使用できるように、リソース グループ名の変数を定義します。 次のコマンド テキストを PowerShell にコピーし、[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)の名前を二重引用符で囲んで指定してコマンドを実行します。 例: `"adfrg"`。 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    リソース グループが既に存在する場合は、上書きされないようにすることができます。 `$resourceGroupName` 変数に別の値を割り当てて、コマンドをもう一度実行します。

2. データ ファクトリの場所の変数を定義します。 

    ```powershell
    $location = "East US"
    ```
3. Azure リソース グループを作成するには、次のコマンドを実行します。 

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ``` 
    リソース グループが既に存在する場合は、上書きされないようにすることができます。 `$resourceGroupName` 変数に別の値を割り当てて、コマンドをもう一度実行します。

4. データ ファクトリ名の変数を定義します。 

    > [!IMPORTANT]
    >  データ ファクトリ名は、グローバルに一意になるように更新してください。 たとえば、ADFTutorialFactorySP1127 とします。 

    ```powershell
    $dataFactoryName = "ADFIncCopyTutorialFactory";
    ```
5. データ ファクトリを作成するには、次の **Set-AzDataFactoryV2** コマンドレットを実行します。 
    
    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

以下の点に注意してください。

* データ ファクトリの名前はグローバルに一意にする必要があります。 次のエラーが発生した場合は、名前を変更してからもう一度実行してください。

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Data Factory インスタンスを作成するには、Azure へのサインインに使用するユーザー アカウントが、共同作成者または所有者ロールのメンバーであるか、Azure サブスクリプションの管理者である必要があります。
* 現在 Data Factory が利用できる Azure リージョンの一覧については、次のページで目的のリージョンを選択し、 **[分析]** を展開して **[Data Factory]** を探してください。[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/) データ ファクトリで使用するデータ ストア (Storage、SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。


## <a name="create-linked-services"></a>リンクされたサービスを作成します
データ ストアおよびコンピューティング サービスをデータ ファクトリにリンクするには、リンクされたサービスをデータ ファクトリに作成します。 このセクションでは、ストレージ アカウントと SQL データベースに対するリンクされたサービスを作成します。 

### <a name="create-a-storage-linked-service"></a>ストレージのリンクされたサービスを作成するには
1. 次の内容を記述した AzureStorageLinkedService.json という名前の JSON ファイルを C:\ADF フォルダーに作成します (ADF フォルダーが存在しない場合は作成してください)。`<accountName>` と `<accountKey>` を、使用するストレージ アカウントの名前とキーに置き換えてからファイルを保存してください。

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. PowerShell で ADF フォルダーに切り替えます。

3. **Set-AzDataFactoryV2LinkedService** コマンドレットを実行して、リンクされたサービス AzureStorageLinkedService を作成します。 次の例では、*ResourceGroupName* パラメーターと *DataFactoryName* パラメーターの値を渡しています。 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    出力例を次に示します。

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-a-sql-database-linked-service"></a>SQL Database のリンクされたサービスを作成する
1. 次の内容を記述した AzureSQLDatabaseLinkedService.json という名前の JSON ファイルを C:\ADF フォルダーに作成します (ADF フォルダーが存在しない場合は作成してください)。&lt;server&gt;、&lt;database&gt;、&lt;user id&gt;、&lt;password&gt; を実際のサーバーの名前、データベース、ユーザー ID、パスワードに置き換えてからファイルを保存してください。 

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "value": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database>; Persist Security Info=False; User ID=<user> ; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. PowerShell で ADF フォルダーに切り替えます。

3. **Set-AzDataFactoryV2LinkedService** コマンドレットを実行して、リンクされたサービス AzureSQLDatabaseLinkedService を作成します。 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    出力例を次に示します。

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ProvisioningState :
    ```

## <a name="create-datasets"></a>データセットを作成する
この手順では、ソース データとシンク データを表すデータセットを作成します。 

### <a name="create-a-source-dataset"></a>ソース データセットを作成する

1. 以下の内容を記述した SourceDataset.json という名前の JSON ファイルを同じフォルダー内に作成します。 

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "data_source_table"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
   
    ```
    このチュートリアルでは、data_source_table というテーブル名を使用します。 別の名前のテーブルを使用する場合は、その名前に置き換えてください。

2. **Set-AzDataFactoryV2Dataset** コマンドレットを実行して、データセット SourceDataset を作成します。
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    このコマンドレットの出力例を次に示します。
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>シンク データセットを作成する

1. 以下の内容を記述した SinkDataset.json という名前の JSON ファイルを同じフォルダー内に作成します。 

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incrementalcopy",
                "fileName": "@CONCAT('Incremental-', pipeline().RunId, '.txt')", 
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }   
    ```

    > [!IMPORTANT]
    > このスニペットは、BLOB ストレージに adftutorial という名前の BLOB コンテナーがあることを前提としています。 このコンテナーが存在しない場合は作成するか、既存のコンテナーの名前を設定してください。 出力フォルダー `incrementalcopy` は、コンテナーに存在しない場合は自動的に作成されます。 このチュートリアルでは、`@CONCAT('Incremental-', pipeline().RunId, '.txt')` という式を使ってファイル名が動的に生成されます。

2. **Set-AzDataFactoryV2Dataset** コマンドレットを実行して、データセット SinkDataset を作成します。
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    このコマンドレットの出力例を次に示します。
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset    
    ```

## <a name="create-a-dataset-for-a-watermark"></a>基準値用のデータセットを作成する
この手順では、高基準値を格納するためのデータセットを作成します。 

1. 以下の内容を記述した WatermarkDataset.json という名前の JSON ファイルを同じフォルダー内に作成します。 

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
2.  **Set-AzDataFactoryV2Dataset** コマンドレットを実行して、データセット WatermarkDataset を作成します。
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    このコマンドレットの出力例を次に示します。
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>パイプラインを作成する
このチュートリアルでは、2 つのルックアップ アクティビティ、1 つのコピー アクティビティ、そして 1 つのストアド プロシージャ アクティビティを 1 つに連結したパイプラインを作成します。 


1. 以下の内容を記述した IncrementalCopyPipeline.json という名前の JSON ファイルを同じフォルダー内に作成します。 

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "LookupOldWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from watermarktable"
                        },
    
                        "dataset": {
                        "referenceName": "WatermarkDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                {
                    "name": "LookupNewWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select MAX(LastModifytime) as NewWatermarkvalue from data_source_table"
                        },
    
                        "dataset": {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                
                {
                    "name": "IncrementalCopyActivity",
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupNewWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        },
                        {
                            "activity": "LookupOldWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ],
    
                    "inputs": [
                        {
                            "referenceName": "SourceDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "SinkDataset",
                            "type": "DatasetReference"
                        }
                    ]
                },
    
                {
                    "name": "StoredProceduretoWriteWatermarkActivity",
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
    
                        "storedProcedureName": "usp_write_watermark",
                        "storedProcedureParameters": {
                            "LastModifiedtime": {"value": "@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}", "type": "datetime" },
                            "TableName":  { "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}", "type":"String"}
                        }
                    },
    
                    "linkedServiceName": {
                        "referenceName": "AzureSQLDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
    
                    "dependsOn": [
                        {
                            "activity": "IncrementalCopyActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ]
                }
            ]
            
        }
    }
    ```
    

2. **Set-AzDataFactoryV2Pipeline** コマンドレットを実行して、パイプライン IncrementalCopyPipeline を作成します。
    
   ```powershell
   Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   出力例を次に示します。 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Activities        : {LookupOldWaterMarkActivity, LookupNewWaterMarkActivity, IncrementalCopyActivity, StoredProceduretoWriteWatermarkActivity}
    Parameters        :
   ```
 
## <a name="run-the-pipeline"></a>パイプラインを実行する

1. **Invoke-AzDataFactoryV2Pipeline** コマンドレットを使って IncrementalCopyPipeline パイプラインを実行します。 プレースホルダーはそれぞれ実際のリソース グループとデータ ファクトリ名に置き換えてください。

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroupName $resourceGroupName -dataFactoryName $dataFactoryName
    ``` 
2. **Get-AzDataFactoryV2ActivityRun** コマンドレットを実行し、すべてのアクティビティが正常に実行されている状態になるまでパイプラインの状態をチェックします。 *RunStartedAfter* パラメーターと *RunStartedBefore* パラメーターのプレースホルダーを、適切な時刻に置き換えてください。 このチュートリアルでは、 *-RunStartedAfter "2017/09/14"* と *-RunStartedBefore "2017/09/15"* を使用します。

    ```powershell
    Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    出力例を次に示します。
 
    ```json
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:42:50 AM
    DurationInMs      : 7777
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:43:07 AM
    DurationInMs      : 25437
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:10 AM
    ActivityRunEnd    : 9/14/2017 7:43:29 AM
    DurationInMs      : 19769
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:32 AM
    ActivityRunEnd    : 9/14/2017 7:43:47 AM
    DurationInMs      : 14467
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```

## <a name="review-the-results"></a>結果の確認

1. BLOB ストレージ (シンク ストア) で、SinkDataset で定義したファイルにデータがコピーされたことを確認します。 このチュートリアルでは、`Incremental- d4bf3ce2-5d60-43f3-9318-923155f61037.txt` がそのファイル名に該当します。 このファイルを開くと、SQL データベース内のデータと同じレコードがあることを確認できます。

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ``` 
2. `watermarktable` の最新の値をチェックします。 基準値が更新されたことを確認できます。

    ```sql
    Select * from watermarktable
    ```
    
    出力例を次に示します。
 
    TableName | WatermarkValue
    --------- | --------------
    data_source_table | 2017-09-05  8:06:00.000

### <a name="insert-data-into-the-data-source-store-to-verify-delta-data-loading"></a>データ ソース ストアにデータを挿入して差分データの読み込みを検証する

1. SQL データベース (データ ソース ストア) に新しいデータを挿入します。

    ```sql
    INSERT INTO data_source_table
    VALUES (6, 'newdata','9/6/2017 2:23:00 AM')
    
    INSERT INTO data_source_table
    VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
    ``` 

    SQL データベース内の更新されたデータは次のとおりです。

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    6 | newdata | 2017-09-06 02:23:00.000
    7 | newdata | 2017-09-07 09:01:00.000
    ```
2. **Invoke-AzDataFactoryV2Pipeline** コマンドレットを使って IncrementalCopyPipeline パイプラインを再実行します。 プレースホルダーはそれぞれ実際のリソース グループとデータ ファクトリ名に置き換えてください。

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroupName $resourceGroupName -dataFactoryName $dataFactoryName
    ```
3. **Get-AzDataFactoryV2ActivityRun** コマンドレットを実行し、すべてのアクティビティが正常に実行されている状態になるまでパイプラインの状態をチェックします。 *RunStartedAfter* パラメーターと *RunStartedBefore* パラメーターのプレースホルダーを、適切な時刻に置き換えてください。 このチュートリアルでは、 *-RunStartedAfter "2017/09/14"* と *-RunStartedBefore "2017/09/15"* を使用します。

    ```powershell
    Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    出力例を次に示します。
 
    ```json
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:58 AM
    DurationInMs      : 31758
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:52 AM
    DurationInMs      : 25497
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:00 AM
    ActivityRunEnd    : 9/14/2017 8:53:20 AM
    DurationInMs      : 20194
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:23 AM
    ActivityRunEnd    : 9/14/2017 8:53:41 AM
    DurationInMs      : 18502
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```
4. BLOB ストレージで、別のファイルが作成されたことを確認します。 このチュートリアルでは、`Incremental-2fc90ab8-d42c-4583-aa64-755dba9925d7.txt` が新しいファイルの名前になります。 このファイルを開くと、2 行のレコードが確認できます。

5. `watermarktable` の最新の値をチェックします。 基準値が再び更新されたことを確認できます。

    ```sql
    Select * from watermarktable
    ```
    サンプル出力: 
    
    TableName | WatermarkValue
    --------- | ---------------
    data_source_table | 2017-09-07 09:01:00.000

     
## <a name="next-steps"></a>次の手順
このチュートリアルでは、以下の手順を実行しました。 

> [!div class="checklist"]
> * 基準値を格納するためのデータ ストアを準備します。 
> * データ ファクトリを作成します。
> * リンクされたサービスを作成します。 
> * ソース データセット、シンク データセット、および基準値データセットを作成します。
> * パイプラインを作成します。
> * パイプラインを実行します。
> * パイプラインの実行を監視します。 

このチュートリアルでは、パイプラインで SQL データベース内の単一のテーブルから BLOB ストレージにデータをコピーしました。 次のチュートリアルに進んで、オンプレミスの SQL Server データベースにある複数のテーブルから SQL データベースにデータをコピーする方法について学習しましょう。 

> [!div class="nextstepaction"]
>[SQL Server にある複数のテーブルから Azure SQL Database にデータを増分読み込みする](tutorial-incremental-copy-multiple-tables-powershell.md)



