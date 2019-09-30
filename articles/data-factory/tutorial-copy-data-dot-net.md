---
title: Azure Blob Storage から SQL Database にデータをコピーする | Microsoft Docs
description: このチュートリアルでは、Azure Blob Storage から Azure SQL Database にデータをコピーするための詳細な手順を説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jingwang
ms.openlocfilehash: 49b5b03356790bd45b2ad29897a57b746af1abe1
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70140683"
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Azure Data Factory を使用して Azure BLOB から Azure SQL Database にデータをコピーする

このチュートリアルでは、Azure Blob Storage から Azure SQL Database にデータをコピーする Data Factory パイプラインを作成します。 このチュートリアルの構成パターンは、ファイルベースのデータ ストアからリレーショナル データ ストアへのコピーに適用されます。 ソースおよびシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する表を参照してください。

このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Azure Storage と Azure SQL Database のリンクされたサービスを作成します。
> * Azure BLOB と Azure SQL Database のデータセットを作成します。
> * コピー アクティビティが含まれているパイプラインを作成します。
> * パイプラインの実行を開始します。
> * パイプラインとアクティビティの実行を監視します。

このチュートリアルでは .NET SDK を使用します。 その他のメカニズムを使用して Azure Data Factory を操作する場合は、「クイックスタート」の下のサンプルを参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件

* **Azure Storage アカウント**。 BLOB ストレージを**ソース** データ ストアとして使用します。 Azure ストレージ アカウントがない場合、ストレージ アカウントの作成手順については、「[ストレージ アカウントの作成](../storage/common/storage-quickstart-create-account.md)」を参照してください。
* **Azure SQL データベース**。 データベースを**シンク** データ ストアとして使用します。 Azure SQL データベースがない場合は、[Azure SQL データベースの作成](../sql-database/sql-database-get-started-portal.md)に関する記事に書かれている手順を参照して作成してください。
* **Visual Studio** 2015 または 2017。 この記事のチュートリアルでは、Visual Studio 2017 を使用します。
* **[Azure .NET SDK](https://azure.microsoft.com/downloads/) をダウンロードしてインストールします**。
* [この手順](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)に従って、**Azure Active Directory にアプリケーションを作成します**。 **アプリケーション ID**、**認証キー**、**テナント ID** の値をメモしておいてください。後の手順で使用します。 同じ記事の手順に従って、アプリケーションを "**共同作成者**" ロールに割り当てます。

### <a name="create-a-blob-and-a-sql-table"></a>BLOB と SQL テーブルを作成する

ここでは、次の手順を実行して、チュートリアルで使用する Azure BLOB と Azure SQL Database を準備します。

#### <a name="create-a-source-blob"></a>ソース BLOB を作成する

1. メモ帳を起動します。 次のテキストをコピーし、**inputEmp.txt** ファイルとしてディスクに保存します。

    ```
    John|Doe
    Jane|Doe
    ```

2. [Azure Storage Explorer](https://storageexplorer.com/) などのツールを使用して **adfv2tutorial** コンテナーを作成し、このコンテナーに **inputEmp.txt** ファイルをアップロードします。

#### <a name="create-a-sink-sql-table"></a>シンク SQL テーブルを作成する

1. 次の SQL スクリプトを使用して、**dbo.emp** テーブルを Azure SQL データベースに作成します。

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Azure サービスに SQL サーバーへのアクセスを許可します。 Data Factory サービスから Azure SQL サーバーにデータを書き込むことができるように、Azure SQL サーバーで **[Azure サービスへのアクセスを許可]** の設定が**オン**になっていることを確認します。 この設定を確認して有効にするには、次の手順を実行します。

    1. 左側にある **[More services (その他のサービス)]** ハブをクリックし、 **[SQL サーバー]** をクリックします。
    2. サーバーを選択し、 **[設定]** の **[ファイアウォール]** をクリックします。
    3. **[ファイアウォールの設定]** ページの **[Azure サービスへのアクセスを許可]** で **[オン]** をクリックします。


## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

Visual Studio 2015/2017 を使用して、C# .NET コンソール アプリケーションを作成します。

1. **Visual Studio** を起動します。
2. **[ファイル]** をクリックし、 **[新規作成]** をポイントして、 **[プロジェクト]** をクリックします。
3. 右側にあるプロジェクトの種類の一覧で、 **[Visual C#]**  ->  **[コンソール アプリ (.NET Framework)]** の順に選択します。 .NET バージョン 4.5.2 以降が必要です。
4. 名前として「**ADFv2Tutorial**」と入力します。
5. **[OK]** をクリックしてプロジェクトを作成します。

## <a name="install-nuget-packages"></a>NuGet パッケージのインストール

1. **[ツール]**  ->  **[NuGet パッケージ マネージャー]**  ->  **[パッケージ マネージャー コンソール]** の順にクリックします。
2. **パッケージ マネージャー コンソール**で、次のコマンドを実行してパッケージをインストールします。 詳細については、[Microsoft.Azure.Management.DataFactory nuget パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/)を参照してください。

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>データ ファクトリ クライアントを作成する

1. **Program.cs** を開き、次のステートメントを挿入して、名前空間への参照を追加します。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

    
2. 変数を設定する次のコードを **Main** メソッドに追加します。 プレースホルダーは実際の値に置き換えてください。 現在 Data Factory が利用できる Azure リージョンの一覧については、次のページで目的のリージョンを選択し、 **[分析]** を展開して **[Data Factory]** を探してください。(「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」)。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    string region = "East US";
    string dataFactoryName = "<specify the name of a data factory to create. It must be globally unique.>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString = "Server=tcp:<your server name>.database.windows.net,1433;Database=<your database name>;User ID=<your username>@<your server name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. **DataFactoryManagementClient** クラスのインスタンスを作成する次のコードを **Main** メソッドに追加します。 このオブジェクトを使用して、データ ファクトリ、リンクされたサービス、データセット、パイプラインを作成します。 また、このオブジェクトを使用して、パイプラインの実行の詳細を監視します。

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Data Factory の作成

**データ ファクトリ**を作成する次のコードを **Main** メソッドに追加します。

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()

};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-linked-services"></a>リンクされたサービスを作成します

このチュートリアルでは、ソース用とシンク用に、リンクされたサービスを 2 つ作成します。

### <a name="create-an-azure-storage-linked-service"></a>Azure Storage のリンクされたサービスを作成する

**Azure Storage のリンクされたサービス**を作成する次のコードを **Main** メソッドに追加します。 サポートされるプロパティと詳細については、[Azure BLOB のリンクされたサービスのプロパティ](connector-azure-blob-storage.md#linked-service-properties)に関するページを参照してください。

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
```

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database のリンクされたサービスを作成する

**Azure SQL Database のリンクされたサービス**を作成する次のコードを **Main** メソッドに追加します。 サポートされるプロパティと詳細については、[Azure SQL Database のリンクされたサービスのプロパティ](connector-azure-sql-database.md#linked-service-properties)に関するページを参照してください。

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings));
```

## <a name="create-datasets"></a>データセットを作成する

このセクションでは、ソース用とシンク用の 2 つのデータセットを作成します。 

### <a name="create-a-dataset-for-source-azure-blob"></a>ソース Azure BLOB のためのデータセットを作成する

**Azure BLOB データセット**を作成する次のコードを **Main** メソッドに追加します。 サポートされるプロパティと詳細については、[Azure BLOB データセットのプロパティ](connector-azure-blob-storage.md#dataset-properties)に関するページを参照してください。

Azure BLOB 内のソース データを表すデータセットを定義します。 この BLOB データセットは、前の手順で作成した Azure Storage のリンクされたサービスを参照し、次の内容を記述します。

- コピー元 BLOB の場所:**FolderPath** と **FileName**。
- コンテンツの解析方法を示す BLOB 形式:**TextFormat** とその設定 (たとえば、列区切り記号)。
- 列名とデータ型を含むデータ構造。ここでは、シンク SQL テーブルにマップされます。

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement
            {
                Name = "FirstName",
                Type = "String"
            },
            new DatasetDataElement
            {
                Name = "LastName",
                Type = "String"
            }
        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>シンク Azure SQL Database のためのデータセットを作成する

**Azure SQL Database データセット**を作成する次のコードを **Main** メソッドに追加します。 サポートされるプロパティと詳細については、[Azure SQL Database データセットのプロパティ](connector-azure-sql-database.md#dataset-properties)に関するページを参照してください。

Azure SQL Database 内のシンク データを表すデータセットを定義します。 このデータセットは、前の手順で作成した Azure SQL Database のリンクされたサービスを参照します。 また、コピーされたデータを保持する SQL テーブルも指定します。 

```csharp
// Create an Azure SQL Database dataset
Console.WriteLine("Creating dataset " + sqlDatasetName + "...");
DatasetResource sqlDataset = new DatasetResource(
    new AzureSqlTableDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = sqlDbLinkedServiceName
        },
        TableName = azureSqlTableName
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>パイプラインを作成する。

**コピー アクティビティが含まれているパイプライン**を作成する次のコードを **Main** メソッドに追加します。 このチュートリアルでは、このパイプラインに 1 つのアクティビティ (コピー アクティビティ) が含まれています。このアクティビティは、BLOB データセットをソースとして受け取り、SQL データセットをシンクとして受け取ります。 コピー アクティビティの詳細については、[コピー アクティビティの概要](copy-activity-overview.md)に関するページを参照してください。

```csharp
// Create a pipeline with copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToSQL",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = sqlDatasetName
                }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>パイプラインの実行を作成する

**パイプラインの実行をトリガーする**次のコードを **Main** メソッドに追加します。

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>パイプラインの実行を監視する

1. データのコピーが完了するまでパイプラインの実行の状態を継続的にチェックする次のコードを **Main** メソッドに追加します。

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. コピー アクティビティの実行の詳細 (たとえば、読み書きされたデータのサイズ) を取得する次のコードを **Main** メソッドに追加します。

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
 
    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
    }
    else
        Console.WriteLine(activityRuns.First().Error);
    
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>コードの実行

アプリケーションをビルドして起動し、パイプラインの実行を確認します。

コンソールに、データ ファクトリ、リンクされたサービス、データセット、パイプライン、およびパイプラインの実行の作成の進捗状況が表示されます。 その後、パイプラインの実行状態が確認されます。 コピー アクティビティの実行の詳細と、データの読み取り/書き込みのサイズが表示されるまで待ちます。 次に、SSMS (SQL Server Management Studio) や Visual Studio などのツールを使用して、コピー先の Azure SQL データベースに接続し、指定したテーブルにデータがコピーされているかどうかを確認します。

### <a name="sample-output"></a>サンプル出力

```json
Creating a data factory AdfV2Tutorial...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
      }
    }
  }
}
Creating linked service AzureSqlDbLinkedService...
{
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": "adfv2tutorial/",
      "fileName": "inputEmp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "|"
      }
    },
    "structure": [
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
      "type": "LinkedServiceReference",
      "referenceName": "AzureStorageLinkedService"
    }
  }
}
Creating dataset SqlDataset...
{
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": "dbo.emp"
    },
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureSqlDbLinkedService"
    }
  }
}
Creating pipeline Adfv2TutorialBlobToSqlCopy...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "BlobDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SqlDataset"
          }
        ],
        "name": "CopyFromBlobToSQL"
      }
    ]
  }
}
Creating pipeline run...
Pipeline run ID: 1cd03653-88a0-4c90-aabc-ae12d843e252
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 18,
  "dataWritten": 28,
  "rowsCopied": 2,
  "copyDuration": 2,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
  "usedDataIntegrationUnits": 2,
  "billedDuration": 2
}

Press any key to exit...
```


## <a name="next-steps"></a>次の手順

このサンプルのパイプラインは、Azure BLOB ストレージ内のある場所から別の場所にデータをコピーするものです。 以下の方法について学習しました。 

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Azure Storage と Azure SQL Database のリンクされたサービスを作成します。
> * Azure BLOB と Azure SQL Database のデータセットを作成します。
> * コピー アクティビティが含まれているパイプラインを作成します。
> * パイプラインの実行を開始します。
> * パイプラインとアクティビティの実行を監視します。


次のチュートリアルに進んで、オンプレミスからクラウドにデータをコピーする方法について学習しましょう。 

> [!div class="nextstepaction"]
>[オンプレミスからクラウドにデータをコピーする](tutorial-hybrid-copy-powershell.md)
