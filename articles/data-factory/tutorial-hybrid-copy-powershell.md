---
title: "Azure Data Factory を使ってオンプレミスのデータをクラウドにコピーする | Microsoft Docs"
description: "Azure Data Factory 内のセルフホステッド統合ランタイムを使って、オンプレミスのデータ ストアから Azure クラウドにデータをコピーする方法について説明します。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/14/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: 60641ddfef7846f0e8b5d850e716b2652bf62367
ms.contentlocale: ja-jp
ms.lasthandoff: 09/26/2017

---

# <a name="copy-data-between-on-premises-and-cloud"></a>オンプレミスとクラウドの間でのデータ コピー
Azure Data Factory は、データドリブン型のワークフローをクラウドに作成することでデータの移動と変換を制御し、自動化することができるクラウドベースのデータ統合サービスです。 Azure Data Factory を使えば、データ主導型のワークフロー (パイプライン) を作成し、スケジューリングできます。具体的には、各種データ ストアからデータを取り込む、そのデータを各種コンピューティング サービス (Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics、Azure Machine Learning など) で処理/変換する、データ ストア (Azure SQL Data Warehouse など) に出力データを公開して、それを利用するビジネス インテリジェンス (BI) アプリケーションに提供するという一連の処理を行えるワークフローです。 

このチュートリアルでは、オンプレミスの SQL Server データベースから Azure BLOB ストレージにデータをコピーする Data Factory パイプラインを Azure PowerShell を使って作成します。 Azure Data Factory のセルフホステッド統合ランタイム (IR) を作成し、使用することで、オンプレミス データ ストアとクラウド データ ストアの統合が可能となります。  その他のツールまたは SDK を使ってデータ ファクトリを作成する方法については、[クイックスタート](quickstart-create-data-factory-dot-net.md)を参照してください。 

このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * セルフホステッド統合ランタイムを作成します。
> * オンプレミス SQL Server のリンクされたサービスをセルフホステッド統合ランタイムに作成して暗号化します。
> * Azure Storage のリンクされたサービスを作成します。
> * SQL Server と Azure Storage のデータセットを作成します。
> * コピー アクティビティでデータを移動するパイプラインを作成します。
> * パイプラインの実行を開始します。
> * パイプラインとアクティビティの実行を監視します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件

* **SQL Server**。 このチュートリアルでは、オンプレミスの SQL Server データベースを**ソース** データ ストアとして使用します。 
* **Azure Storage アカウント**。 このチュートリアルでは、Azure BLOB ストレージを**コピー先/シンク** データ ストアとして使用します。 Azure ストレージ アカウントがない場合、ストレージ アカウントの作成手順については、「 [ストレージ アカウントの作成](../storage/common/storage-create-storage-account.md#create-a-storage-account) 」をご覧ください。
* **Azure PowerShell**。 [Azure PowerShell のインストールと構成の方法](/powershell/azure/install-azurerm-ps)に関するページに記載されている手順に従います。

## <a name="create-a-data-factory"></a>Data Factory を作成する。

1. **PowerShell**を起動します。 Azure PowerShell は、このチュートリアルが終わるまで開いたままにしておいてください。 Azure PowerShell を閉じて再度開いた場合は、これらのコマンドをもう一度実行する必要があります。

    次のコマンドを実行して、Azure Portal へのサインインに使用するユーザー名とパスワードを入力します。
    ```powershell
    Login-AzureRmAccount
    ```
    次のコマンドを実行して、このアカウントのすべてのサブスクリプションを表示します。

    ```powershell
    Get-AzureRmSubscription
    ```
    次のコマンドを実行して、使用するサブスクリプションを選択します。 **SubscriptionId** は、実際の Azure サブスクリプションの ID に置き換えてください。

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. **Set-AzureRmDataFactoryV2** コマンドレットを実行してデータ ファクトリを作成します。 各プレースホルダーを実際の値に置き換えてからコマンドを実行してください。

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    $df = Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    以下の点に注意してください。

    * Azure Data Factory の名前はグローバルに一意にする必要があります。 次のエラーが発生した場合は、名前を変更してからもう一度実行してください。

        ```
        Data factory name "<data factory name>" is not available.
        ```

    * Data Factory インスタンスを作成するには、Azure サブスクリプションの共同作成者または管理者である必要があります。
    * 現在、データ ファクトリを作成できるリージョンは米国東部と米国東部 2 だけです。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。

## <a name="create-a-self-hosted-ir"></a>セルフホステッド IR を作成する

このセクションでは、セルフホステッド統合ランタイムを作成し、オンプレミス ノード (マシン) に関連付けます。

1. セルフホステッド統合ランタイムを作成します。 他の統合ランタイムと競合しない一意の名前を使用してください。

   ```powershell
   $integrationRuntimeName = "<your integration runtime name>"
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   出力例を次に示します。

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. 次のコマンドを実行して、作成された統合ランタイムの状態を取得します。

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   出力例を次に示します。

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption : 
   Version                   : 
   Capabilities              : {}
   ScheduledUpdateDate       : 
   UpdateDelayOffset         : 
   LocalTimeZoneOffset       : 
   AutoUpdate                : 
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

3. 次のコマンドを実行して、クラウドの Data Factory サービスにセルフホステッド統合ランタイムを登録するための認証キーを取得します。 セルフホステッド統合ランタイムの登録に使用するいずれかのキーをコピーします。

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   出力例を次に示します。 

   ```json
   {
       "AuthKey1":  "IR@8437c862-d6a9-4fb3-87dd-7d4865a9e845@ab1@eu@VDnzgySwUfaj3pfSUxpvfsXXXXXXx4GHiyF4wboad0Y=",
       "AuthKey2":  "IR@8437c862-d6a9-4fb3-85dd-7d4865a9e845@ab1@eu@sh+k/QNJGBltXL46vXXXXXXXXOf/M1Gne5aVqPtbweI="
   }
   ```

4. セルフホステッド統合ランタイムをローカルの Windows マシンに[ダウンロード](https://www.microsoft.com/download/details.aspx?id=39717)し、前の手順で取得した認証キーを使って、セルフホステッド統合ランタイムを手動で登録します。 

   ![統合ランタイムの登録](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

   セルフホステッド統合ランタイムが正常に登録されると、次のメッセージが表示されます。 

   ![正常に登録](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

   ノードがクラウド サービスに接続されると、次のページが表示されます。 
    
   ![ノード接続済み](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

## <a name="create-linked-services"></a>リンクされたサービスを作成します 

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Azure Storage のリンクされたサービスを作成する (コピー先/シンク)

1. 次の内容を記述した **AzureStorageLinkedService.json** という名前の JSON ファイルを **C:\ADFv2Tutorial** フォルダーに作成します。 ADFv2Tutorial フォルダーがまだ存在しない場合は作成してください。  &lt;accountname&gt; と &lt;accountkey&gt; を Azure ストレージ アカウントの名前とキーに置き換えます。

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": { 
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```

2. **Azure PowerShell** で **ADFv2Tutorial** フォルダーに切り替えます。

   **Set-AzureRmDataFactoryV2LinkedService** コマンドレットを実行して、リンクされたサービス **AzureStorageLinkedService** を作成します。 このチュートリアルで使用しているコマンドレットは、**ResourceGroupName** パラメーターと **DataFactoryName** パラメーターの値を受け取ります。 または、コマンドレットを実行するたびに ResourceGroupName と DataFactoryName を入力することなく、Set-AzureRmDataFactoryV2 コマンドレットから返された **DataFactory** オブジェクトを渡すことができます。

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   出力例を次に示します。

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>SQL Server のリンクされたサービスを作成して暗号化する (ソース)

1. **C:\ADFv2Tutorial** フォルダーに、**SqlServerLinkedService.json** という名前で以下の内容の JSON ファイルを作成します。**&lt;servername>**、**&lt;databasename>**、**&lt;username>**、**&lt;servername>**、**&lt;password>** を実際の SQL Server の値に置き換えてからファイルを保存してください。 **&lt;integration** **runtime** **name>** は、実際の統合ランタイムの名前に置き換えます。 

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```
2. JSON ペイロードからの機密データを暗号化して、オンプレミスのセルフホステッド統合ランタイムに格納するには、**New-AzureRmDataFactoryV2LinkedServiceEncryptCredential** を実行して、上記の JSON ペイロードを渡します。 この暗号化によって、資格情報がデータ保護 API (DPAPI) を使って暗号化され、ローカルのセルフホステッド統合ランタイム ノードに格納されます。 暗号化された資格情報が含まれる出力ペイロードは別の JSON ファイル (この場合は "encryptedLinkedService.json") にリダイレクトできます。 

    **&lt;integration runtime name&gt;** を実際の統合ランタイムの名前に置き換えてからコマンドを実行してください。

   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName <integration runtime name> -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. 前の手順で作成した JSON を使って次のコマンドを実行し、**SqlServerLinkedService** を作成します。

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json" 
   ```


## <a name="create-datasets"></a>データセットを作成する

### <a name="prepare-an-on-premises-sql-server-for-the-tutorial"></a>チュートリアル用にオンプレミスの SQL Server を用意する

このステップでは、コピー操作 (オンプレミスの SQL Server Database => Azure BLOB ストレージ) の入力および出力データを表す入出力データ セットを作成します。 データセットを作成する前に、以下の手順を実行します (詳細な手順はリストの後にあります)。

- リンクされたサービスとしてデータ ファクトリに追加した SQL Server Database 内に "**emp**" という名前のテーブルを作成し、このテーブルにサンプル エントリをいくつか挿入します。
- Data Factory にリンクされたサービスとして追加した Azure BLOB ストレージ アカウントに **adftutorial** という名前の BLOB コンテナーを作成します。


1. オンプレミスの SQL Server リンク サービス (**SqlServerLinkedService**) 用に指定したデータベースで、次の SQL スクリプトを使用して、データベースに **emp** テーブルを作成します。

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. テーブルにサンプルをいくつか挿入します。

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```



### <a name="create-a-dataset-for-source-sql-database"></a>ソース SQL Database のデータセットを作成する

1. 以下の内容を記述した **SqlServerDataset.json** という名前の JSON ファイルを **C:\ADFv2Tutorial** フォルダー内に作成します。  

    ```json
    {
       "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dbo.emp"
            },
            "structure": [
                 {
                    "name": "ID",
                    "type": "String"
                },
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "linkedServiceName": {
                "referenceName": "EncryptedSqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "SqlServerDataset"
    }
    ```

2. データセット **SqlServerDataset** を作成するには、**Set-AzureRmDataFactoryV2Dataset** コマンドレットを実行します。

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    出力例を次に示します。

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         : {"name": "ID" "type": "String", "name": "FirstName" "type": "String", "name": "LastName" "type": "String"}
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-sink-azure-blob-storage"></a>シンク Azure Blob Storage のデータセットを作成する

1. 以下の内容を記述した **AzureBlobDataset.json** という名前の JSON ファイルを **C:\ADFv2Tutorial** フォルダー内に作成します。 

    > [!IMPORTANT]
    > このサンプル コードは、Azure BLOB ストレージに **adftutorial** という名前のコンテナーがあることを前提としています。

    ```json
    {
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/fromonprem",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "AzureBlobDataset"
    }
    ```

2. データセット **AzureBlobDataset** を作成するには、**Set-AzureRmDataFactoryV2Dataset** コマンドレットを実行します。

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    出力例を次に示します。

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-pipelines"></a>パイプラインを作成する

### <a name="create-the-pipeline-sqlservertoblobpipeline"></a>パイプライン "SqlServerToBlobPipeline" を作成する

1. 以下の内容を記述した **SqlServerToBlobPipeline.json** という名前の JSON ファイルを **C:\ADFv2Tutorial** フォルダー内に作成します。

    ```json
    {
       "name": "SQLServerToBlobPipeline",
        "properties": {
            "activities": [       
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource"
                        },
                        "sink": {
                            "type":"BlobSink"
                        }
                    },
                    "name": "CopySqlServerToAzureBlobActivity",
                    "inputs": [
                        {
                            "referenceName": "SqlServerDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "AzureBlobDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ]
        }
    }
    ```

2. パイプライン **SQLServerToBlobPipeline** を作成するには、**Set-AzureRmDataFactoryV2Pipeline** コマンドレットを実行します。

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    出力例を次に示します。

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```



## <a name="start-and-monitor-a-pipeline-run"></a>パイプラインの実行を開始して監視する

1. "SQLServerToBlobPipeline" パイプラインの実行を開始し、後で監視できるようパイプライン実行 ID をキャプチャします。  

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
    ```

2. 次のスクリプトを実行して、パイプライン "**SQLServerToBlobPipeline**" の状態を常時チェックし、最終的な結果を出力します。

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    } 
    ```

    サンプル実行の出力結果を次に示します。

    ```jdon
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : copy
    PipelineRunId     : 4ec8980c-62f6-466f-92fa-e69b10f33640
    PipelineName      : SQLServerToBlobPipeline
    Input             :  
    Output            :  
    LinkedServiceName : 
    ActivityRunStart  : 9/13/2017 1:35:22 PM
    ActivityRunEnd    : 9/13/2017 1:35:42 PM
    DurationInMs      : 20824
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. 次のようにして、パイプライン "**SQLServerToBlobPipeline**" の実行 ID を取得し、詳細なアクティビティの実行結果をチェックすることができます。

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    サンプル実行の出力結果を次に示します。

    ```json
    {
      "dataRead": 36,
      "dataWritten": 24,
      "rowsCopied": 2,
      "copyDuration": 4,
      "throughput": 0.01,
      "errors": []
    }
    ```
4. シンク Azure BLOB ストレージに接続し、Azure SQL Database から正しくデータがコピーされていることを確認します。

## <a name="next-steps"></a>次のステップ
このサンプルのパイプラインは、Azure BLOB ストレージ内のある場所から別の場所にデータをコピーするものです。 以下の方法について学習しました。 

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * セルフホステッド統合ランタイムを作成します。
> * オンプレミス SQL Server のリンクされたサービスをセルフホステッド統合ランタイムに作成して暗号化します。
> * Azure Storage のリンクされたサービスを作成します。
> * SQL Server と Azure Storage のデータセットを作成します。
> * コピー アクティビティでデータを移動するパイプラインを作成します。
> * パイプラインの実行を開始します。
> * パイプラインとアクティビティの実行を監視します。

Azure Data Factory でサポートされるソース データ ストアおよびシンク データ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事を参照してください。

次のチュートリアルに進んで、ソースからコピー先にデータを一括コピーする方法について学習しましょう。

> [!div class="nextstepaction"]
>[データを一括コピーする](tutorial-bulk-copy.md)
