---
title: Azure Blob Storage から Azure SQL Database にコピーする
description: このチュートリアルでは、Azure Blob Storage から Azure SQL Database にデータをコピーするための詳細な手順を説明します。
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: jingwang
ms.openlocfilehash: 2a9e19f24c4889a194c85e33317860db1d3a68d2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101736561"
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Azure Data Factory を使用して Azure BLOB から Azure SQL Database にデータをコピーする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

このチュートリアルでは、Azure Blob Storage から Azure SQL Database にデータをコピーする Data Factory パイプラインを作成します。 このチュートリアルの構成パターンは、ファイルベースのデータ ストアからリレーショナル データ ストアへのコピーに適用されます。 ソースおよびシンクとしてサポートされているデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。

このチュートリアルでは、次の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Azure Storage と Azure SQL Database のリンクされたサービスを作成します。
> * Azure BLOB と Azure SQL Database のデータセットを作成します。
> * コピー アクティビティが含まれているパイプラインを作成します。
> * パイプラインの実行を開始します。
> * パイプラインとアクティビティの実行を監視します。

このチュートリアルでは .NET SDK を使用します。 その他のメカニズムを使用して Azure Data Factory を操作する場合は、 **「クイックスタート」** の下のサンプルを参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="prerequisites"></a>前提条件

* *Azure Storage アカウント*。 BLOB ストレージを *ソース* データ ストアとして使用します。 Azure ストレージ アカウントがない場合は、[汎用ストレージ アカウントの作成](../storage/common/storage-account-create.md)に関するページを参照してください。
* *Azure SQL データベース*。 データベースを *シンク* データ ストアとして使用します。 Azure SQL Database のデータベースがない場合は、[Azure SQL Database のデータベースの作成](../azure-sql/database/single-database-create-quickstart.md)に関するページを参照してください。
* *Visual Studio*. この記事のチュートリアルでは、Visual Studio 2019 を使用します。
* *[Azure SDK for .NET](/dotnet/azure/dotnet-tools)* .
* *Azure Active Directory アプリケーション*。 Azure Active Directory アプリケーションをお持ちでない場合は、「[Azure Active Directory アプリケーションを作成する](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)」セクションを、[ポータルを使用して Azure AD アプリケーションを作成する方法](../active-directory/develop/howto-create-service-principal-portal.md)に関するページから参照してください。 以降の手順で使用するために、次の値をコピーします。**アプリケーション (クライアント) ID**、**認証キー**、および **ディレクトリ (テナント) ID** です。 同じ記事の手順に従って、アプリケーションを **[共同作成者]** ロールに割り当てます。

### <a name="create-a-blob-and-a-sql-table"></a>BLOB と SQL テーブルを作成する

ここでは、ソース ブログとシンク SQL テーブルを作成して、チュートリアルのために Azure BLOB と Azure SQL Database を準備します。

#### <a name="create-a-source-blob"></a>ソース BLOB を作成する

最初に、コンテナーを作成し、そこに入力テキスト ファイルをアップロードして、ソース BLOB を作成します。

1. メモ帳を開きます。 次のテキストをコピーし、*inputEmp.txt* というファイル名でローカルに保存します。

    ```inputEmp.txt
    John|Doe
    Jane|Doe
    ```

2. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) などのツールを使用して *adfv2tutorial* コンテナーを作成し、そのコンテナーに *inputEmp.txt* ファイルをアップロードします。

#### <a name="create-a-sink-sql-table"></a>シンク SQL テーブルを作成する

次に、シンク SQL テーブルを作成します。

1. 次の SQL スクリプトを使用して、*dbo.emp* テーブルを Azure SQL Database に作成します。

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

2. Azure サービスに SQL Database へのアクセスを許可します。 Data Factory サービスから SQL Database にデータを書き込むことができるように、サーバー上で Azure サービスへのアクセスを許可していることを確認します。 この設定を確認して有効にするには、次の手順を実行します。

    1. [Azure portal](https://portal.azure.com) に移動して、SQL サーバーを管理します。 **[SQL サーバー]** を探して選択します。

    2. ご自身のサーバーを選択します。

    3. SQL サーバー メニューの **[セキュリティ]** の見出しの下にある **[ファイアウォールと仮想ネットワーク]** を選択します。

    4. **[ファイアウォールと仮想ネットワーク]** ページの **[Azure サービスおよびリソースにこのサーバーへのアクセスを許可する]** の下で、 **[ON]** を選択します。

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

Visual Studio を使用して、C# .NET コンソール アプリケーションを作成します。

1. Visual Studio を開きます。
2. **[開始]** ウィンドウで、 **[新しいプロジェクトの作成]** を選択します。
3. **[新しいプロジェクトの作成]** ウィンドウで、 **[コンソール アプリ (.NET Framework)]** の C# バージョンを、プロジェクトの種類の一覧から選択します。 **[次へ]** を選択します。
4. **[新しいプロジェクトの構成]** ウィンドウで、 **[プロジェクト名]** に「*ADFv2Tutorial*」と入力します。 **[場所]** では、プロジェクトを保存するディレクトリを参照または作成します。 **[作成]** を選択します。 Visual Studio IDE に新しいプロジェクトが表示されます。

## <a name="install-nuget-packages"></a>NuGet パッケージのインストール

次に、NuGet パッケージ マネージャーを使用して、必要なライブラリ パッケージをインストールします。

1. メニュー バーで、 **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に選択します。
2. **[パッケージ マネージャー コンソール]** ウィンドウで、次のコマンドを実行してパッケージをインストールします。 Azure Data Factory NuGet パッケージの詳細については、[Microsoft.Azure.Management.DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/) を参照してください。

    ```package manager console
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -PreRelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>データ ファクトリ クライアントを作成する

データ ファクトリ クライアントを作成するには、次の手順に従います。

1. *Program.cs* を開き、既存の `using` ステートメントを次のコードに置き換えて、名前空間への参照を追加します。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. 変数を設定する次のコードを、`Main` メソッドに追加します。 14 のプレースホルダーを実際の値に置き換えます。

    現在 Data Factory が利用可能な Azure リージョンの一覧を確認するには、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」を参照してください。 **[製品]** ドロップダウン リストの下で、 **[参照]**  >  **[分析]**  >  **[Data Factory]** の順に選択します。 次に、 **[リージョン]** ドロップダウン リストで、目的のリージョンを選択します。 選択したリージョンでの Data Factory 製品の利用可否の状態が、グリッドに表示されます。

    > [!NOTE]
    > Data Factory で使用されるデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は Data Factory 用に選択したリージョン以外のリージョンに配置できます。

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    string region = "<location to create the data factory in, such as East US>";
    string dataFactoryName = "<name of data factory to create (must be globally unique)>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString =
        "Server=tcp:<your server name>.database.windows.net,1433;" +
        "Database=<your database name>;" +
        "User ID=<your username>@<your server name>;" +
        "Password=<your password>;" +
        "Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. `DataFactoryManagementClient` クラスのインスタンスを作成する次のコードを、`Main` メソッドに追加します。 このオブジェクトを使用して、データ ファクトリ、リンクされたサービス、データセット、パイプラインを作成します。 また、このオブジェクトを使用して、パイプラインの実行の詳細を監視します。

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync(
        "https://management.azure.com/", cc
    ).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Data Factory の作成

"*データ ファクトリ*" を作成する次のコードを、`Main` メソッドに追加します。

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};

client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings)
);

while (
    client.Factories.Get(
        resourceGroup, dataFactoryName
    ).ProvisioningState == "PendingCreation"
)
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-linked-services"></a>リンクされたサービスを作成します

このチュートリアルでは、ソース用とシンク用に、それぞれリンクされた 2 つのサービスを作成します。

### <a name="create-an-azure-storage-linked-service"></a>Azure Storage のリンクされたサービスを作成する

"*Azure Storage のリンクされたサービス*" を作成する次のコードを、`Main` メソッドに追加します。 サポートされているプロパティと詳細については、[Azure BLOB のリンクされたサービスのプロパティ](connector-azure-blob-storage.md#linked-service-properties)に関するセクションを参照してください。

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString(
            "DefaultEndpointsProtocol=https;AccountName=" + storageAccount +
            ";AccountKey=" + storageKey
        )
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings)
);
```

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database のリンクされたサービスを作成する

"*Azure SQL Database のリンクされたサービス*" を作成する次のコードを、`Main` メソッドに追加します。 サポートされているプロパティと詳細については、[Azure SQL Database のリンクされたサービスのプロパティ](connector-azure-sql-database.md#linked-service-properties)に関するセクションを参照してください。

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings)
);
```

## <a name="create-datasets"></a>データセットを作成する

このセクションでは、2 つのデータセットを作成します。1 つはソース用、もう 1 つはシンク用です。

### <a name="create-a-dataset-for-source-azure-blob"></a>ソース Azure BLOB のためのデータセットを作成する

"*Azure BLOB データセット*" を作成する次のコードを、`Main` メソッドに追加します。 サポートされているプロパティと詳細については、[Azure BLOB データセットのプロパティ](connector-azure-blob-storage.md#dataset-properties)に関するページを参照してください。

Azure BLOB 内のソース データを表すデータセットを定義します。 この BLOB データセットは、前の手順で作成した Azure Storage のリンクされたサービスを参照し、次の内容を記述します。

- コピー元の BLOB の場所: `FolderPath` および `FileName`
- 内容の解析方法を示す BLOB 形式: `TextFormat` とその設定 (列区切り記号など)。
- 列名とデータ型を含むデータ構造。この例では、シンク SQL テーブルにマップされます

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement { Name = "FirstName", Type = "String" },
            new DatasetDataElement { Name = "LastName", Type = "String" }
        }
    }
);

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, blobDatasetName, blobDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings)
);
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>シンク Azure SQL Database のためのデータセットを作成する

"*Azure SQL Database データセット*" を作成する次のコードを、`Main` メソッドに追加します。 サポートされているプロパティと詳細については、[Azure SQL Database データセットのプロパティ](connector-azure-sql-database.md#dataset-properties)に関するセクションを参照してください。

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

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings)
);
```

## <a name="create-a-pipeline"></a>パイプラインを作成する

"*コピー アクティビティが含まれているパイプライン*" を作成する次のコードを、`Main` メソッドに追加します。 このチュートリアルでは、このパイプラインに 1 つのアクティビティ (`CopyActivity`) が含まれています。これは、BLOB データセットをソースとして受け取り、SQL データセットをシンクとして受け取ります。 コピー アクティビティの詳細については、「[Azure Data Factory のコピー アクティビティ](copy-activity-overview.md)」を参照してください。

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
                new DatasetReference() { ReferenceName = blobDatasetName }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference { ReferenceName = sqlDatasetName }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings)
);
```

## <a name="create-a-pipeline-run"></a>パイプラインの実行を作成する

"*パイプラインの実行をトリガーする*" 次のコードを、`Main` メソッドに追加します。

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>パイプラインの実行を監視する

次に、パイプラインの実行状態を確認するコードを挿入し、コピー アクティビティの実行に関する詳細を取得します。

1. データのコピーが完了するまでパイプラインの実行の状態を継続的にチェックする次のコードを、`Main` メソッドに追加します。

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(
            resourceGroup, dataFactoryName, runResponse.RunId
        );
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. コピー アクティビティの実行の詳細 (読み取りおよび書き込みされたデータのサイズなど) を取得する次のコードを、`Main` メソッドに追加します。

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    RunFilterParameters filterParams = new RunFilterParameters(
        DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)
    );

    ActivityRunsQueryResponse queryResponse = client.ActivityRuns.QueryByPipelineRun(
        resourceGroup, dataFactoryName, runResponse.RunId, filterParams
    );

    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(queryResponse.Value.First().Output);
    }
    else
        Console.WriteLine(queryResponse.Value.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>コードの実行

**[ビルド]**  >  **[ソリューションのビルド]** を選択して、アプリケーションをビルドします。 次に、 **[デバッグ]**  >  **[デバッグの開始]** の順に選択してアプリケーションを起動し、パイプラインの実行を検証します。

コンソールに、データ ファクトリ、リンクされたサービス、データセット、パイプライン、およびパイプラインの実行の作成の進捗状況が表示されます。 その後、パイプラインの実行状態が確認されます。 コピー アクティビティの実行の詳細と、データの読み取り/書き込みのサイズが表示されるまで、待機します。 次に、SQL Server Management Studio (SSMS) や Visual Studio などのツールを使用して、宛先の Azure SQL Database に接続して、指定した宛先テーブルにコピーされたデータが含まれているかを確認できます。

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

## <a name="next-steps"></a>次のステップ

このサンプルのパイプラインは、Azure BLOB ストレージ内のある場所から別の場所にデータをコピーするものです。 以下の方法を学習しました。

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
