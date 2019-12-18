---
title: Change Tracking を使用してデータを増分コピーする
description: このチュートリアルでは、オンプレミスの SQL Server データベースにある複数のテーブルから Azure SQL データベースに差分データを増分コピーする Azure Data Factory パイプラインを作成します。
services: data-factory
ms.author: yexu
author: dearandyxu
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/22/2018
ms.openlocfilehash: de42acd9cb8ca0520db616237c23b7db9fadb77f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74923031"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-change-tracking-information"></a>変更追跡情報を使用して Azure SQL Database から Azure Blob Storage にデータを増分読み込みする 

このチュートリアルでは、ソース Azure SQL データベースから**変更追跡**情報に基づく差分データを Azure Blob Storage に読み込むパイプラインを使用して Azure Data Factory を作成します。  

このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * ソース データ ストアを準備します。
> * データ ファクトリを作成します。
> * リンクされたサービスを作成します。 
> * ソース、シンク、変更追跡の各データセットを作成します。
> * フル コピー パイプラインを作成、実行、監視します。
> * ソース テーブルのデータを追加または更新します。
> * 増分コピー パイプラインを作成、実行、監視します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概要
データ統合ソリューションでは、初回データ読み込みの後、増分データを読み込む手法が広く利用されています。 ソース データ ストアから特定の期間に変更されたデータを簡単に切り出すことができる場合もあります (LastModifyTime、CreationTime など)。 一方、前回データを処理した時点からの差分データを明示的に特定する方法がない場合もあります。 こうした差分データは、Azure SQL Database や SQL Server などのデータ ストアでサポートされる Change Tracking テクノロジを使用して特定することができます。  このチュートリアルでは、Azure Data Factory と SQL Change Tracking テクノロジを使用して、Azure SQL Database から Azure Blob Storage に差分データを増分読み込みする方法について説明します。  SQL Change Tracking テクノロジに関するより具体的な情報については、[SQL Server における変更の追跡](/sql/relational-databases/track-changes/about-change-tracking-sql-server)に関するページを参照してください。 

## <a name="end-to-end-workflow"></a>エンド ツー エンド ワークフロー
ここでは、Change Tracking テクノロジを使用してデータを増分読み込みする一般的なエンド ツー エンドのワークフロー ステップを取り上げます。

> [!NOTE]
> Azure SQL Database と SQL Server は、どちらも Change Tracking テクノロジをサポートしています。 このチュートリアルでは、Azure SQL Database をソース データ ストアとして使用します。 オンプレミスの SQL Server を使用してもかまいません。 

1. **履歴データの初回読み込みを実行する** (1 回実行)。
    1. ソース Azure SQL データベースの Change Tracking テクノロジを有効にします。
    2. Azure SQL データベースから SYS_CHANGE_VERSION の初期値をベースラインとして取得し、変更済みデータをキャプチャします。
    3. Azure SQL データベースから Azure Blob Storage にフル データを読み込みます。 
2. **スケジュールに従って差分データの増分読み込みを実行する** (データの初回読み込み後に定期的に実行)。
    1. 新旧の SYS_CHANGE_VERSION 値を取得します。
    3. 差分データを読み込みます。これは、**sys.change_tracking_tables** から得られる変更済みの行 (2 つの SYS_CHANGE_VERSION 値の間にある行) の主キーと**ソース テーブル**内のデータとを結合した後、その差分データをターゲットに移動することによって行います。
    4. 次回の差分読み込みに備えて SYS_CHANGE_VERSION を更新します。

## <a name="high-level-solution"></a>ソリューションの概略
このチュートリアルでは、次の 2 つの操作を実行する 2 つのパイプラインを作成します。  

1. **初回読み込み:** ソース データ ストア (Azure SQL Database) からターゲット データ ストア (Azure Blob Storage) にデータ全体をコピーするコピー アクティビティを含んだパイプラインを作成します。

    ![データ全体の読み込み](media/tutorial-incremental-copy-change-tracking-feature-powershell/full-load-flow-diagram.png)
1.  **増分読み込み:** 次のアクティビティを含んだパイプラインを作成して定期的に実行します。 
    1. **2 つのルックアップ アクティビティ**を作成します。これらのアクティビティによって Azure SQL Database から新旧の SYS_CHANGE_VERSION を取得してコピー アクティビティに渡すことになります。
    2. 2 つの SYS_CHANGE_VERSION 値の間に存在する挿入/更新/削除されたデータを Azure SQL Database から Azure Blob Storage にコピーする**コピー アクティビティを 1 つ**作成します。
    3. 次回のパイプライン実行に備えて SYS_CHANGE_VERSION の値を更新する**ストアド プロシージャ アクティビティを 1 つ**作成します。

    ![増分読み込みのフロー図](media/tutorial-incremental-copy-change-tracking-feature-powershell/incremental-load-flow-diagram.png)


Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件

* Azure PowerShell。 [Azure PowerShell のインストールと構成の方法](/powershell/azure/install-Az-ps)に関するページの手順に従って、最新の Azure PowerShell モジュールをインストールしてください。
* **Azure SQL データベース**。 **ソース** データ ストアとして使うデータベースです。 Azure SQL データベースがない場合は、[Azure SQL データベースの作成](../sql-database/sql-database-get-started-portal.md)に関する記事に書かれている手順を参照して作成してください。
* **Azure Storage アカウント**。 **シンク** データ ストアとして使用する BLOB ストレージです。 Azure ストレージ アカウントがない場合、ストレージ アカウントの作成手順については、「[ストレージ アカウントの作成](../storage/common/storage-quickstart-create-account.md)」を参照してください。 **adftutorial** という名前のコンテナーを作成します。 

### <a name="create-a-data-source-table-in-your-azure-sql-database"></a>Azure SQL データベースにデータ ソース テーブルを作成する
1. **SQL Server Management Studio** を起動し、Azure SQL Server に接続します。 
2. **サーバー エクスプローラー**で目的の**データベース**を右クリックして **[新しいクエリ]** を選択します。
3. Azure SQL データベースに対して次の SQL コマンドを実行し、`data_source_table` という名前のテーブルをソース データ ストアとして作成します。  
    
    ```sql
    create table data_source_table
    (
        PersonID int NOT NULL,
        Name varchar(255),
        Age int
        PRIMARY KEY (PersonID)
    );

    INSERT INTO data_source_table
        (PersonID, Name, Age)
    VALUES
        (1, 'aaaa', 21),
        (2, 'bbbb', 24),
        (3, 'cccc', 20),
        (4, 'dddd', 26),
        (5, 'eeee', 22);

    ```
4. 次の SQL クエリを実行して、データベースとソース テーブル (data_source_table) の **Change Tracking** 機構を有効にします。 

    > [!NOTE]
    > - &lt;your database name&gt; は、data_source_table がある実際の Azure SQL データベースの名前に置き換えてください。 
    > - 現行の例では、変更済みのデータが 2 日間維持されます。 変更済みデータを読み込む間隔を 3 日おき、またはそれ以上にした場合、変更済みデータの一部が読み込まれません。  CHANGE_RETENTION の数値を増やす必要があります。 または、変更済みデータの読み込み間隔を必ず 2 日以内としてください。 詳細については、「[データベースの変更の追跡を有効にする](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server#enable-change-tracking-for-a-database)」を参照してください。
 
    ```sql
    ALTER DATABASE <your database name>
    SET CHANGE_TRACKING = ON  
    (CHANGE_RETENTION = 2 DAYS, AUTO_CLEANUP = ON)  
  
    ALTER TABLE data_source_table
    ENABLE CHANGE_TRACKING  
    WITH (TRACK_COLUMNS_UPDATED = ON)
    ```
5. 新しいテーブルを作成し、ChangeTracking_version を既定値で格納するために、次のクエリを実行します。 

    ```sql
    create table table_store_ChangeTracking_version
    (
        TableName varchar(255),
        SYS_CHANGE_VERSION BIGINT,
    );

    DECLARE @ChangeTracking_version BIGINT
    SET @ChangeTracking_version = CHANGE_TRACKING_CURRENT_VERSION();  

    INSERT INTO table_store_ChangeTracking_version
    VALUES ('data_source_table', @ChangeTracking_version)
    ```
    
    > [!NOTE]
    > SQL Database の変更追跡を有効にした後、データが変更されていなければ、変更追跡バージョンの値は 0 になります。
6. 次のクエリを実行して、Azure SQL データベースにストアド プロシージャを作成します。 このストアド プロシージャをパイプラインで呼び出すことによって、前の手順で作成したテーブルの変更追跡バージョンを更新します。 

    ```sql
    CREATE PROCEDURE Update_ChangeTracking_Version @CurrentTrackingVersion BIGINT, @TableName varchar(50)
    AS
    
    BEGIN
    
        UPDATE table_store_ChangeTracking_version
        SET [SYS_CHANGE_VERSION] = @CurrentTrackingVersion
    WHERE [TableName] = @TableName
    
    END    
    ```

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell のインストールと構成の方法](/powershell/azure/install-Az-ps)に関するページの手順に従って、最新の Azure PowerShell モジュールをインストールしてください。

## <a name="create-a-data-factory"></a>Data Factory の作成
1. 後で PowerShell コマンドで使用できるように、リソース グループ名の変数を定義します。 次のコマンド テキストを PowerShell にコピーし、[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)の名前を二重引用符で囲んで指定し、コマンドを実行します。 (例: `"adfrg"`)。 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    リソース グループが既に存在する場合、上書きしないようお勧めします。 `$resourceGroupName` 変数に別の値を割り当てて、コマンドをもう一度実行します。
2. データ ファクトリの場所の変数を定義します。 

    ```powershell
    $location = "East US"
    ```
3. Azure リソース グループを作成するには、次のコマンドを実行します。 

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ``` 
    リソース グループが既に存在する場合、上書きしないようお勧めします。 `$resourceGroupName` 変数に別の値を割り当てて、コマンドをもう一度実行します。 
3. データ ファクトリ名の変数を定義します。 

    > [!IMPORTANT]
    >  データ ファクトリ名は、グローバルに一意となるように更新してください。  

    ```powershell
    $dataFactoryName = "IncCopyChgTrackingDF";
    ```
5. データ ファクトリを作成するには、次の **Set-AzDataFactoryV2** コマンドレットを実行します。 
    
    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

以下の点に注意してください。

* Azure Data Factory の名前はグローバルに一意にする必要があります。 次のエラーが発生した場合は、名前を変更してからもう一度実行してください。

    ```
    The specified Data Factory name 'ADFIncCopyChangeTrackingTestFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory インスタンスを作成するには、Azure へのログインに使用するユーザー アカウントが、**共同作成者**ロールまたは**所有者**ロールのメンバーであるか、Azure サブスクリプションの**管理者**である必要があります。
* 現在 Data Factory が利用できる Azure リージョンの一覧については、次のページで目的のリージョンを選択し、 **[分析]** を展開して **[Data Factory]** を探してください (「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」)。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。


## <a name="create-linked-services"></a>リンクされたサービスを作成します
データ ストアおよびコンピューティング サービスをデータ ファクトリにリンクするには、リンクされたサービスをデータ ファクトリに作成します。 このセクションでは、Azure ストレージ アカウントと Azure SQL データベースに対するリンクされたサービスを作成します。 

### <a name="create-azure-storage-linked-service"></a>Azure Storage のリンクされたサービスを作成する
この手順では、Azure ストレージ アカウントをデータ ファクトリにリンクします。

1. 以下の内容を記述した **AzureStorageLinkedService.json** という名前の JSON ファイルを **C:\ADFTutorials\IncCopyChangeTrackingTutorial** フォルダー内に作成します (フォルダーがない場合は作成します)。 ファイルを保存する前に、`<accountName>` と `<accountKey>` を Azure ストレージ アカウントの名前とキーに置き換えます。

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
2. **Azure PowerShell** で **C:\ADFTutorials\IncCopyChangeTrackingTutorial** フォルダーに切り替えます。
3. **Set-AzDataFactoryV2LinkedService** コマンドレットを実行して、リンクされたサービス **AzureStorageLinkedService** を作成します。 次の例では、**ResourceGroupName** パラメーターと **DataFactoryName** パラメーターの値を渡しています。 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    出力例を次に示します。

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-azure-sql-database-linked-service"></a>Azure SQL Database のリンクされたサービスを作成する
この手順では、Azure SQL データベースをデータ ファクトリにリンクします。

1. 以下の内容を記述した **AzureSQLDatabaseLinkedService.json** という名前の JSON ファイルを **C:\ADFTutorials\IncCopyChangeTrackingTutorial** フォルダー内に作成します。 **&lt;server&gt;、&lt;database name **、&lt;user id&gt;、&lt;password&gt;** を実際の Azure SQL サーバーの名前、データベースの名前、ユーザー ID、パスワードに置き換えてからファイルを保存してください。 

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "value": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database name>; Persist Security Info=False; User ID=<user name>; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. **Azure PowerShell** で、**Set-AzDataFactoryV2LinkedService** コマンドレットを実行して、リンクされたサービス**AzureSQLDatabaseLinkedService** を作成します。 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    出力例を次に示します。

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>データセットを作成する
この手順では、データのコピー元とコピー先、 SYS_CHANGE_VERSION の格納場所を表すデータセットを作成します。

### <a name="create-a-source-dataset"></a>ソース データセットを作成する
この手順では、ソース データを表すデータセットを作成します。 

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

2.  Set-AzDataFactoryV2Dataset コマンドレットを実行して、データセットSourceDataset を作成します。
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    このコマンドレットの出力例を次に示します。
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>シンク データセットを作成する
この手順では、ソース データ ストアからコピーされたデータを表すデータセットを作成します。 

1. 以下の内容を記述した SinkDataset.json という名前の JSON ファイルを同じフォルダー内に作成します。 

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incchgtracking",
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

    前提条件の 1 つとして adftutorial コンテナーを Azure Blob Storage に作成します。 このコンテナーが存在しない場合は作成するか、または既存のコンテナーの名前に設定してください。 このチュートリアルでは、@CONCAT('Incremental-', pipeline().RunId, '.txt') という式を使って出力ファイル名が動的に生成されます。
2.  Set-AzDataFactoryV2Dataset コマンドレットを実行して、データセットSinkDataset を作成します。
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    このコマンドレットの出力例を次に示します。
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

### <a name="create-a-change-tracking-dataset"></a>変更追跡データセットを作成する
この手順では、変更追跡バージョンを格納するためのデータセットを作成します。  

1. 以下の内容を記述した ChangeTrackingDataset.json という名前の JSON ファイルを同じフォルダー内に作成します。 

    ```json
    {
        "name": " ChangeTrackingDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "table_store_ChangeTracking_version"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
    ```

    前提条件の 1 つとして table_store_ChangeTracking_version テーブルを作成します。
2.  Set-AzDataFactoryV2Dataset コマンドレットを実行して、データセットChangeTrackingDataset
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "ChangeTrackingDataset" -File ".\ChangeTrackingDataset.json"
    ```

    このコマンドレットの出力例を次に示します。
    
    ```json
    DatasetName       : ChangeTrackingDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

## <a name="create-a-pipeline-for-the-full-copy"></a>完全コピーのパイプラインを作成する
この手順では、ソース データ ストア (Azure SQL Database) からターゲット データ ストア (Azure Blob Storage) にデータ全体をコピーするコピー アクティビティを含んだパイプラインを作成します。

1. JSON ファイル FullCopyPipeline.json を同じフォルダー内に作成します。次の内容を記述してください。 

    ```json
    {
        "name": "FullCopyPipeline",
        "properties": {
            "activities": [{
                "name": "FullCopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
    
                "inputs": [{
                    "referenceName": "SourceDataset",
                    "type": "DatasetReference"
                }],
                "outputs": [{
                    "referenceName": "SinkDataset",
                    "type": "DatasetReference"
                }]
            }]
        }
    }
    ```
2. Set-AzDataFactoryV2Pipeline コマンドレットを実行して、パイプラインFullCopyPipeline を作成します。
    
   ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "FullCopyPipeline" -File ".\FullCopyPipeline.json"
   ``` 

   出力例を次に示します。 

   ```json
    PipelineName      : FullCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Activities        : {FullCopyActivity}
    Parameters        :
   ```
 
### <a name="run-the-full-copy-pipeline"></a>フル コピー パイプラインを実行する
パイプライン **FullCopyPipeline** を、**Invoke-AzDataFactoryV2Pipeline** コマンドレットを使って実行します。 

```powershell
Invoke-AzDataFactoryV2Pipeline -PipelineName "FullCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName        
``` 

### <a name="monitor-the-full-copy-pipeline"></a>フル コピー パイプラインを監視する

1. [Azure Portal](https://portal.azure.com) にログインします。
2. **[すべてのサービス]** をクリックし、キーワード「`data factories`」で検索して、 **[データ ファクトリ]** を選択します。 

    ![[データ ファクトリ] メニュー](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-data-factories-menu-1.png)
3. データ ファクトリの一覧から**目的のデータ ファクトリ**を探して選択し、[データ ファクトリ] ページを起動します。 

    ![目的のデータ ファクトリの検索](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-search-data-factory-2.png)
4. [データ ファクトリ] ページの **[監視と管理]** タイルをクリックします。 

    ![Monitor & Manage tile](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-monitor-manage-tile-3.png)    
5. 独立したタブで**データ統合アプリケーション**が起動します。すべての**パイプラインの実行**とその状態を確認できます。 次の例では、パイプラインの実行が、**成功**状態であることに注目してください。 **[パラメーター]** 列のリンクをクリックすると、パイプラインに渡されたパラメーターを確認できます。 エラーが発生した場合は、 **[エラー]** 列にリンクが表示されます。 **[アクション]** 列のリンクをクリックします。 

    ![パイプライン実行](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-pipeline-runs-4.png)    
6. **[アクション]** 列のリンクをクリックすると、以下のページが開いて、そのパイプラインに関するすべての**アクティビティの実行**が表示されます。 

    ![アクティビティの実行](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-activity-runs-5.png)
7. 再度**パイプラインの実行**ビューに切り替えるには、この画像に示した **[パイプライン]** をクリックします。 


### <a name="review-the-results"></a>結果の確認
`adftutorial` コンテナーの `incchgtracking` フォルダーに、`incremental-<GUID>.txt` という名前のファイルが確認できます。 

![フル コピーからの出力ファイル](media/tutorial-incremental-copy-change-tracking-feature-powershell/full-copy-output-file.png)

このファイルには、Azure SQL データベースからのデータが存在します。

```
1,aaaa,21
2,bbbb,24
3,cccc,20
4,dddd,26
5,eeee,22
```

## <a name="add-more-data-to-the-source-table"></a>ソース テーブルにデータを追加する

Azure SQL データベースに次のクエリを実行して行の追加と更新を行います。 

```sql
INSERT INTO data_source_table
(PersonID, Name, Age)
VALUES
(6, 'new','50');


UPDATE data_source_table
SET [Age] = '10', [name]='update' where [PersonID] = 1

``` 

## <a name="create-a-pipeline-for-the-delta-copy"></a>差分コピーのパイプラインを作成する
この手順では、次のアクティビティを含んだパイプラインを作成して定期的に実行します。 **ルックアップ アクティビティ**は、Azure SQL Database から新旧の SYS_CHANGE_VERSION を取得してコピー アクティビティに渡します。 **コピー アクティビティ**は、2 つの SYS_CHANGE_VERSION 値の間に存在する挿入/更新/削除されたデータを Azure SQL Database から Azure Blob Storage にコピーします。 **ストアド プロシージャ アクティビティ**は、次回のパイプライン実行に備えて SYS_CHANGE_VERSION の値を更新します。

1. JSON ファイル IncrementalCopyPipeline.json を同じフォルダー内に作成します。次の内容を記述してください。 

    ```json
    {
            "name": "IncrementalCopyPipeline",
            "properties": {
                "activities": [
                {
                        "name": "LookupLastChangeTrackingVersionActivity",
                        "type": "Lookup",
                        "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from table_store_ChangeTracking_version"
                            },
        
                            "dataset": {
                            "referenceName": "ChangeTrackingDataset",
                            "type": "DatasetReference"
                            }
                        }
                    },
                    {
                        "name": "LookupCurrentChangeTrackingVersionActivity",
                        "type": "Lookup",
                        "typeProperties": {
                            "source": {
                                "type": "SqlSource",
                                "sqlReaderQuery": "SELECT CHANGE_TRACKING_CURRENT_VERSION() as CurrentChangeTrackingVersion"
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
                                "sqlReaderQuery": "select data_source_table.PersonID,data_source_table.Name,data_source_table.Age, CT.SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION from data_source_table RIGHT OUTER JOIN CHANGETABLE(CHANGES data_source_table, @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.SYS_CHANGE_VERSION}) as CT on data_source_table.PersonID = CT.PersonID where CT.SYS_CHANGE_VERSION <= @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}"
                            },
                            "sink": {
                                "type": "BlobSink"
                            }
                        },
                        "dependsOn": [
                            {
                                "activity": "LookupLastChangeTrackingVersionActivity",
                                "dependencyConditions": [
                                    "Succeeded"
                                ]
                            },
                            {
                                "activity": "LookupCurrentChangeTrackingVersionActivity",
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
                        "name": "StoredProceduretoUpdateChangeTrackingActivity",
                        "type": "SqlServerStoredProcedure",
                        "typeProperties": {
    
                            "storedProcedureName": "Update_ChangeTracking_Version",
                            "storedProcedureParameters": {
                            "CurrentTrackingVersion": {"value": "@{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}", "type": "INT64" },
                                "TableName":  { "value":"@{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.TableName}", "type":"String"}
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
2. Set-AzDataFactoryV2Pipeline コマンドレットを実行して、パイプラインFullCopyPipeline を作成します。
    
   ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   出力例を次に示します。 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Activities        : {LookupLastChangeTrackingVersionActivity, LookupCurrentChangeTrackingVersionActivity, IncrementalCopyActivity, StoredProceduretoUpdateChangeTrackingActivity}
    Parameters        :
   ```

### <a name="run-the-incremental-copy-pipeline"></a>増分コピー パイプラインを実行する
パイプライン **IncrementalCopyPipeline** を、**Invoke-AzDataFactoryV2Pipeline** コマンドレットを使って実行します。 

```powershell
Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName     
``` 


### <a name="monitor-the-incremental-copy-pipeline"></a>増分コピー パイプラインを監視する
1. **データ統合アプリケーション**で、**パイプラインの実行**ビューを最新の情報に更新します。 その一覧に IncrementalCopyPipeline があることを確認してください。 **[アクション]** 列のリンクをクリックします。  

    ![パイプライン実行](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-pipeline-runs-6.png)    
2. **[アクション]** 列のリンクをクリックすると、以下のページが開いて、そのパイプラインに関するすべての**アクティビティの実行**が表示されます。 

    ![アクティビティの実行](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-activity-runs-7.png)
3. 再度**パイプラインの実行**ビューに切り替えるには、この画像に示した **[パイプライン]** をクリックします。 

### <a name="review-the-results"></a>結果の確認
`adftutorial` コンテナーの `incchgtracking` フォルダーにもう 1 つファイルが確認できます。 

![増分コピーからの出力ファイル](media/tutorial-incremental-copy-change-tracking-feature-powershell/incremental-copy-output-file.png)

このファイルに含まれているのは、Azure SQL データベースからの差分データのみです。 `U` と記録されているレコードはデータベース内の更新された行で、`I` は追加された行です。 

```
1,update,10,2,U
6,new,50,1,I
```
最初の 3 つの列は、data_source_table 内の変更済みデータです。 最後の 2 つの列は、変更追跡システム テーブルからのメタデータです。 4 つ目の列は、変更済みの各行の SYS_CHANGE_VERSION です。 5 つ目の列は実行された操作で、U は更新 (Update) を、I は挿入 (Insert) を表します。  変更追跡情報の詳細については、「[CHANGETABLE](/sql/relational-databases/system-functions/changetable-transact-sql)」を参照してください。 

```
==================================================================
PersonID Name    Age    SYS_CHANGE_VERSION    SYS_CHANGE_OPERATION
==================================================================
1        update  10     2                     U
6        new     50     1                     I
```

    
## <a name="next-steps"></a>次の手順
次のチュートリアルに進んで、LastModifiedDate に基づいて新規ファイルおよび変更されたファイルのみをコピーする方法について学習してください。

> [!div class="nextstepaction"]
>[lastmodifieddate を基準に新しいファイルをコピーする](tutorial-incremental-copy-lastmodified-copy-data-tool.md)



