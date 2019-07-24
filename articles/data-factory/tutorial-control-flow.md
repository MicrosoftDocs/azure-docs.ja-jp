---
title: Azure Data Factory パイプラインでの分岐 |Microsoft Docs
description: アクティビティの分岐と連鎖によって Azure Data Factory 内のデータのフローを制御する方法を説明します。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: shlo
ms.openlocfilehash: 9a03094683a973db16aa949f0610bc7f9914be45
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649222"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Data Factory パイプラインでのアクティビティの分岐と連鎖

このチュートリアルでは、いくつかの制御フロー機能を紹介する Data Factory パイプラインを作成します。 このパイプラインでは、Azure Blob Storage 内のコンテナーから同じストレージ アカウント内の別のコンテナーへの単純なコピーを行います。 コピー アクティビティが成功した場合は、成功したコピー操作の詳細 (書き込まれたデータの量など) を成功電子メールで送信します。 コピー アクティビティが失敗した場合は、コピー失敗の詳細 (エラー メッセージなど) を失敗電子メールで送信します。 チュートリアル全体を通じて、パラメーターを渡す方法が示されます。

シナリオの概要:![概要](media/tutorial-control-flow/overview.png)

このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Azure Storage のリンクされたサービスを作成します。
> * Azure BLOB データセットを作成します。
> * コピー アクティビティと Web アクティビティを含むパイプラインを作成します。
> * アクティビティの出力を後続のアクティビティに送信します。
> * パラメーターの引き渡しとシステム変数を利用します。
> * パイプラインの実行を開始します。
> * パイプラインとアクティビティの実行を監視します。

このチュートリアルでは .NET SDK を使用します。 その他のメカニズムを使用して Azure Data Factory を操作する場合は、目次から「クイック スタート」を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件

* **Azure Storage アカウント**。 BLOB ストレージを**ソース** データ ストアとして使用します。 Azure ストレージ アカウントがない場合、ストレージ アカウントの作成手順については、「[ストレージ アカウントの作成](../storage/common/storage-quickstart-create-account.md)」を参照してください。
* **Azure SQL データベース**。 データベースを**シンク** データ ストアとして使用します。 Azure SQL Database がない場合は、[Azure SQL データベースの作成](../sql-database/sql-database-get-started-portal.md)に関する記事に書かれている手順を参照して作成してください。
* **Visual Studio** 2013、2015、または 2017。 この記事のチュートリアルでは、Visual Studio 2017 を使用します。
* **[Azure .NET SDK](https://azure.microsoft.com/downloads/) をダウンロードしてインストールします**。
* [これらの手順](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)に従って、**Azure Active Directory にアプリケーションを作成します**。 **アプリケーション ID**、**認証キー**、および**テナント ID** の値をメモしておいてください。後の手順で使用します。 同じ記事の手順に従って、アプリケーションを "**共同作成者**" ロールに割り当てます。

### <a name="create-blob-table"></a>BLOB テーブルを作成する

1. メモ帳を起動します。 次のテキストをコピーし、**input.txt** ファイルとしてディスクに保存します。

    ```
    John|Doe
    Jane|Doe
    ```

2. [Azure Storage Explorer](https://storageexplorer.com/) などのツールを使用して **adfv2branch** コンテナーを作成し、このコンテナーに **input.txt** ファイルをアップロードします。

## <a name="create-visual-studio-project"></a>Visual Studio プロジェクトの作成

Visual Studio 2015/2017 を使用して、C# .NET コンソール アプリケーションを作成します。

1. **Visual Studio** を起動します。
2. **[ファイル]** をクリックし、**[新規作成]** をポイントして、**[プロジェクト]** をクリックします。 .NET バージョン 4.5.2 以降が必要です。
3. 右側にあるプロジェクトの種類の一覧で、**[Visual C#]** -> **[コンソール アプリ (.NET Framework)]** の順に選択します。
4. [名前] には「**ADFv2BranchTutorial**」と入力します。
5. **[OK]** をクリックしてプロジェクトを作成します。

## <a name="install-nuget-packages"></a>NuGet パッケージのインストール

1. **[ツール]** -> **[NuGet パッケージ マネージャー]** -> **[パッケージ マネージャー コンソール]** の順にクリックします。
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

2. 次の静的変数を **Program クラス**に追加します。 プレースホルダーは実際の値に置き換えてください。 現在 Data Factory が利用できる Azure リージョンの一覧については、次のページで目的のリージョンを選択し、**[分析]** を展開して **[Data Factory]** を探してください。(「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」)。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。

    ```csharp
        // Set variables
        static string tenantID = "<tenant ID>";
        static string applicationId = "<application ID>";
        static string authenticationKey = "<Authentication key for your application>";
        static string subscriptionId = "<Azure subscription ID>";
        static string resourceGroup = "<Azure resource group name>";

        static string region = "East US";
        static string dataFactoryName = "<Data factory name>";

        // Specify the source Azure Blob information
        static string storageAccount = "<Azure Storage account name>";
        static string storageKey = "<Azure Storage account key>";
        // confirm that you have the input.txt file placed in th input folder of the adfv2branch container. 
        static string inputBlobPath = "adfv2branch/input";
        static string inputBlobName = "input.txt";
        static string outputBlobPath = "adfv2branch/output";
        static string emailReceiver = "<specify email address of the receiver>";

        static string storageLinkedServiceName = "AzureStorageLinkedService";
        static string blobSourceDatasetName = "SourceStorageDataset";
        static string blobSinkDatasetName = "SinkStorageDataset";
        static string pipelineName = "Adfv2TutorialBranchCopy";

        static string copyBlobActivity = "CopyBlobtoBlob";
        static string sendFailEmailActivity = "SendFailEmailActivity";
        static string sendSuccessEmailActivity = "SendSuccessEmailActivity";
    
    ```

3. **DataFactoryManagementClient** クラスのインスタンスを作成する次のコードを **Main** メソッドに追加します。 このオブジェクトを使用して、データ ファクトリ、リンクされたサービス、データセット、およびパイプラインを作成します。 また、このオブジェクトを使用して、パイプラインの実行の詳細を監視します。

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Data Factory を作成する。

Program.cs ファイルで "CreateOrUpdateDataFactory" 関数を作成します。

```csharp
static Factory CreateOrUpdateDataFactory(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating data factory " + dataFactoryName + "...");
    Factory resource = new Factory
    {
        Location = region
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));

    Factory response;
    {
        response = client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, resource);
    }

    while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
    {
        System.Threading.Thread.Sleep(1000);
    }
    return response;
}
```



**データ ファクトリ**を作成する次のコードを **Main** メソッドに追加します。 

```csharp
Factory df = CreateOrUpdateDataFactory(client);
```

## <a name="create-an-azure-storage-linked-service"></a>Azure Storage のリンクされたサービスを作成する

Program.cs ファイルで "StorageLinkedServiceDefinition" 関数を作成します。

```csharp
static LinkedServiceResource StorageLinkedServiceDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");
    AzureStorageLinkedService storageLinkedService = new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
    LinkedServiceResource linkedService = new LinkedServiceResource(storageLinkedService, name:storageLinkedServiceName);
    return linkedService;
}
```

**Azure Storage のリンクされたサービス**を作成する次のコードを **Main** メソッドに追加します。 サポートされるプロパティと詳細については、[Azure BLOB のリンクされたサービスのプロパティ](connector-azure-blob-storage.md#linked-service-properties)に関するページを参照してください。

```csharp
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
```

## <a name="create-datasets"></a>データセットを作成する

このセクションでは、ソース用とシンク用の 2 つのデータセットを作成します。 

### <a name="create-a-dataset-for-source-azure-blob"></a>ソース Azure BLOB のためのデータセットを作成する

**Azure BLOB データセット**を作成する次のコードを **Main** メソッドに追加します。 サポートされるプロパティと詳細については、[Azure BLOB データセットのプロパティ](connector-azure-blob-storage.md#dataset-properties)に関するページを参照してください。

Azure BLOB 内のソース データを表すデータセットを定義します。 この BLOB データセットは、前の手順で作成した Azure Storage のリンクされたサービスを参照し、次の内容を記述します。

- コピー元 BLOB の場所:**FolderPath** と **FileName**。
- FolderPath ではパラメーターが使用されていることに注意してください。 "sourceBlobContainer" はパラメーターの名前であり、この式は実行パイプラインに渡された値に置き換えられます。 パラメーターを定義する構文は `@pipeline().parameters.<parameterName>` です。

Program.cs ファイルで "SourceBlobDatasetDefinition" 関数を作成します。

```csharp
static DatasetResource SourceBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSourceDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    { 
        FolderPath = new Expression { Value = "@pipeline().parameters.sourceBlobContainer" },
        FileName = inputBlobName,
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name:blobSourceDatasetName);
    return dataset;
}
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>シンク Azure BLOB のためのデータセットを作成する

Program.cs ファイルで "SourceBlobDatasetDefinition" 関数を作成します。

```csharp
static DatasetResource SinkBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSinkDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    {
        FolderPath = new Expression { Value = "@pipeline().parameters.sinkBlobContainer" },
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name: blobSinkDatasetName);
    return dataset;
}
```

Azure BLOB ソースおよびシンク データセットを作成する次のコードを **Main** メソッドに追加します。 

```csharp
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
```

## <a name="create-a-c-class-emailrequest"></a>C# クラスを作成する:EmailRequest

C# プロジェクトで、**EmailRequest** という名前のクラスを作成します。 これは、パイプラインで電子メールが送信されるときに要求本文で送信されるプロパティを定義します。 このチュートリアルでは、パイプラインから電子メールに次の 4 つのプロパティが送信されます。

- **Message**: 電子メールの本文。 成功したコピーでは、このプロパティには実行の詳細 (書き込まれたデータの数) が含まれています。 失敗したコピーでは、このプロパティにはエラーの詳細が含まれています。
- **Data factory name**: データ ファクトリの名前
- **Pipeline name**: パイプラインの名前
- **Receiver**:渡されるパラメーター。 このプロパティは、電子メールの受信者を指定します。

```csharp
    class EmailRequest
    {
        [Newtonsoft.Json.JsonProperty(PropertyName = "message")]
        public string message;

        [Newtonsoft.Json.JsonProperty(PropertyName = "dataFactoryName")]
        public string dataFactoryName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "pipelineName")]
        public string pipelineName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "receiver")]
        public string receiver;

        public EmailRequest(string input, string df, string pipeline, string receiverName)
        {
            message = input;
            dataFactoryName = df;
            pipelineName = pipeline;
            receiver = receiverName;
        }
    }
```

## <a name="create-email-workflow-endpoints"></a>電子メール ワークフロー エンドポイントを作成する

電子メール送信をトリガーするには、[Logic Apps](../logic-apps/logic-apps-overview.md) を使用してワークフローを定義します。 ロジック アプリ ワークフローの作成の詳細については、[ロジック アプリを作成する方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。 

### <a name="success-email-workflow"></a>成功電子メールのワークフロー 

`CopySuccessEmail` という名前のロジック アプリ ワークフローを作成します。 ワークフロー トリガーを `When an HTTP request is received` として定義し、`Office 365 Outlook – Send an email` のアクションを追加します。

![成功電子メールのワークフロー](media/tutorial-control-flow/success-email-workflow.png)

要求トリガー用に、`Request Body JSON Schema` に次の JSON を入力します。

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

これは、前のセクションで作成した **EmailRequest** クラスに合わせたものです。 

要求はロジック アプリ デザイナーで次のようになります。

![ロジック アプリ デザイナー - 要求](media/tutorial-control-flow/logic-app-designer-request.png)

**[電子メールの送信]** アクション用に、要求本文の JSON スキーマで渡されるプロパティを利用して、電子メールを書式設定する方法をカスタマイズします。 たとえば次のようになります。

![ロジック アプリ デザイナー - 電子メールの送信アクション](media/tutorial-control-flow/send-email-action.png)

成功電子メール ワークフローの HTTP Post 要求 URL をメモしておきます。

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="fail-email-workflow"></a>失敗電子メールのワークフロー 

**CopySuccessEmail** を複製し、**CopyFailEmail** という別の Logic Apps ワークフローを作成します。 要求トリガー内の `Request Body JSON schema` は同じです。 `Subject` のように単純に電子メールの書式を変更し、失敗電子メールになるように調整します。 たとえば次のようになります。

![ロジック アプリ デザイナー - 失敗電子メールのワークフロー](media/tutorial-control-flow/fail-email-workflow.png)

失敗電子メール ワークフローの HTTP Post 要求 URL をメモしておきます。

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

2 つのワークフローの URL を取得できました。

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-pipeline"></a>パイプラインを作成する。

コピー アクティビティと dependsOn プロパティが含まれているパイプラインを作成する次のコードを Main メソッドに追加します。 このチュートリアルのパイプラインに含まれる 1 つのアクティビティ (コピー アクティビティ) では、ソースおよびシンクとして別個の BLOB データセットが取り込まれます。 コピー アクティビティの成功と失敗に応じて、異なる電子メール タスクが呼び出されます。

このパイプラインでは、次の機能を使用します。

- parameters
- Web アクティビティ
- アクティビティの依存関係
- アクティビティからの出力を後続のアクティビティへの入力として使用する

次のパイプラインをセクションごとに分割してみましょう。

```csharp

static PipelineResource PipelineDefinition(DataFactoryManagementClient client)
        {
            Console.WriteLine("Creating pipeline " + pipelineName + "...");
            PipelineResource resource = new PipelineResource
            {
                Parameters = new Dictionary<string, ParameterSpecification>
                {
                    { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                    { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                    { "receiver", new ParameterSpecification { Type = ParameterType.String } }

                },
                Activities = new List<Activity>
                {
                    new CopyActivity
                    {
                        Name = copyBlobActivity,
                        Inputs = new List<DatasetReference>
                        {
                            new DatasetReference
                            {
                                ReferenceName = blobSourceDatasetName
                            }
                        },
                        Outputs = new List<DatasetReference>
                        {
                            new DatasetReference
                            {
                                ReferenceName = blobSinkDatasetName
                            }
                        },
                        Source = new BlobSource { },
                        Sink = new BlobSink { }
                    },
                    new WebActivity
                    {
                        Name = sendSuccessEmailActivity,
                        Method = WebActivityMethod.POST,
                        Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/00000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000000",
                        Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                        DependsOn = new List<ActivityDependency>
                        {
                            new ActivityDependency
                            {
                                Activity = copyBlobActivity,
                                DependencyConditions = new List<String> { "Succeeded" }
                            }
                        }
                    },
                    new WebActivity
                    {
                        Name = sendFailEmailActivity,
                        Method =WebActivityMethod.POST,
                        Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000",
                        Body = new EmailRequest("@{activity('CopyBlobtoBlob').error.message}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                        DependsOn = new List<ActivityDependency>
                        {
                            new ActivityDependency
                            {
                                Activity = copyBlobActivity,
                                DependencyConditions = new List<String> { "Failed" }
                            }
                        }
                    }
                }
            };
            Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));
            return resource;
        }
```

パイプラインを作成する次のコードを **Main** メソッドに追加します。

```
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
```

### <a name="parameters"></a>parameters

パイプラインの最初のセクションでは、パラメーターを定義します。 

- sourceBlobContainer - ソース BLOB データセットによって使用されるパイプライン内のパラメーターです。
- sinkBlobContainer – シンク BLOB データセットによって使用されるパイプライン内のパラメーターです。
- receiver – このパラメーターは、このパラメーターによって電子メール アドレスが指定されている受信者に成功または失敗の電子メールを送信するパイプライン内の 2 つの Web アクティビティによって使用されます。


```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```

### <a name="web-activity"></a>Web アクティビティ

Web アクティビティでは、任意の REST エンドポイントを呼び出すことができます。 このアクティビティの詳細については、[Web アクティビティ](control-flow-web-activity.md)に関する記事を参照してください。 このパイプラインでは、Web アクティビティを使用して Logic Apps 電子メール ワークフローを呼び出します。 2 つの Web アクティビティを作成します。1 つは **CopySuccessEmail** ワークフローを呼び出し、もう 1 つは **CopyFailWorkFlow** を呼び出します。

```csharp
        new WebActivity
        {
            Name = sendCopyEmailActivity,
            Method = WebActivityMethod.POST,
            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/12345",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Succeeded" }
                }
            }
        }
```

"Url" プロパティには、Logic Apps ワークフローから対応する要求 URL エンドポイントを貼り付けます。 "Body" プロパティには、"EmailRequest" クラスのインスタンスを渡します。 この電子メール要求には次のプロパティが含まれます。

- Message – `@{activity('CopyBlobtoBlob').output.dataWritten` から渡される値。 前のコピー アクティビティのプロパティにアクセスし、dataWritten の値を渡します。 失敗の場合、`@{activity('CopyBlobtoBlob').error.message` の代わりにエラー出力を渡します。
- Data Factory Name – `@{pipeline().DataFactory}` から渡される値。これはシステム変数であり、対応するデータ ファクトリ名へのアクセスを可能にします。 サポートされているシステム変数の一覧については、「[システム変数](control-flow-system-variables.md)」の記事を参照してください。
- Pipeline Name – `@{pipeline().Pipeline}` から渡される値。 これもシステム変数であり、対応するパイプライン名へのアクセスを可能にします。 
- Receiver – "\@pipeline().parameters.receiver") から渡される値。 パイプライン パラメーターにアクセスします。
 
このコードでは、前のコピー アクティビティの成功に応じて、新しいアクティビティの依存関係を作成します。

## <a name="create-a-pipeline-run"></a>パイプラインの実行を作成する

**パイプラインの実行をトリガーする**次のコードを **Main** メソッドに追加します。

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};
 
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="main-class"></a>Main クラス 

最終的な Main メソッドは、次のようになります。 プログラムをビルドして実行し、パイプラインの実行をトリガーしてください。

```csharp
// Authenticate and create a data factory management client
var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };

Factory df = CreateOrUpdateDataFactory(client);

client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));

Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
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
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>コードの実行

アプリケーションをビルドして起動し、パイプラインの実行を確認します。
コンソールに、データ ファクトリ、リンクされたサービス、データセット、パイプライン、およびパイプラインの実行の作成の進捗状況が表示されます。 その後、パイプラインの実行状態が確認されます。 コピー アクティビティの実行の詳細と、データの読み取り/書き込みのサイズが表示されるまで待ちます。 次に、Azure Storage Explorer などのツールを使用して、変数で指定したように BLOB が "inputBlobPath" から "outputBlobPath" にコピーされていることを確認します。

**サンプル出力:**

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": {
      "type": "SecureString",
      "value": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
    }
  }
}
Creating dataset SourceStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sourceBlobContainer"
    },
    "fileName": "input.txt"
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating dataset SinkStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sinkBlobContainer"
    }
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating pipeline Adfv2TutorialBranchCopy...
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
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SourceStorageDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SinkStorageDataset"
          }
        ],
        "name": "CopyBlobtoBlob"
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendSuccessEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Succeeded"
            ]
          }
        ]
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendFailEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Failed"
            ]
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      },
      "receiver": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 00000000-0000-0000-0000-0000000000000
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 20,
  "dataWritten": 20,
  "copyDuration": 4,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
}
{}

Press any key to exit...
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の手順を実行しました。 

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Azure Storage のリンクされたサービスを作成します。
> * Azure BLOB データセットを作成します。
> * コピー アクティビティと Web アクティビティを含むパイプラインを作成します。
> * アクティビティの出力を後続のアクティビティに送信します。
> * パラメーターの引き渡しとシステム変数を利用します。
> * パイプラインの実行を開始します。
> * パイプラインとアクティビティの実行を監視します。

これで、「概念」セクションに進み、Azure Data Factory の詳細について学習できるようになりました。
> [!div class="nextstepaction"]
>[パイプラインとアクティビティ](concepts-pipelines-activities.md)