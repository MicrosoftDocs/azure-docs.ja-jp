---
title: Azure Data Factory パイプラインでの分岐
description: アクティビティの分岐と連鎖によって Azure Data Factory 内のデータのフローを制御する方法を説明します。
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 9/27/2019
ms.openlocfilehash: 83bc90ca251fddd498dd365f1a4b00689a471475
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785667"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Data Factory パイプラインでのアクティビティの分岐と連鎖

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

このチュートリアルでは、いくつかの制御フロー機能を紹介する Data Factory パイプラインを作成します。 このパイプラインでは、Azure Blob Storage 内のコンテナーから同じストレージ アカウント内の別のコンテナーへのコピーを行います。 コピー アクティビティが成功した場合は、成功したコピー操作の詳細がパイプラインによってメールで送信されます。 この情報には、書き込まれたデータの量が含まれる場合があります。 コピー アクティビティが失敗した場合は、エラー メッセージなどのコピー失敗の詳細がメールで送信されます。 チュートリアル全体を通じて、パラメーターを渡す方法が示されます。

次の図は、シナリオの概要を示しています。

![コピー対象の Azure Blob Storage を示す図。成功した場合は詳細を含む電子メールを送信し、失敗した場合はエラーの詳細を含む電子メールを送信する。](media/tutorial-control-flow/overview.png)

このチュートリアルでは、以下のタスクの実行方法を説明します。

> [!div class="checklist"]
> * Data Factory の作成
> * Azure Storage のリンクされたサービスを作成する
> * Azure BLOB データセットを作成します。
> * コピー アクティビティと Web アクティビティを含むパイプラインを作成します。
> * アクティビティの出力を後続のアクティビティに送信します。
> * パラメーターの引き渡しとシステム変数を使用する
> * パイプラインの実行を開始します。
> * パイプラインとアクティビティの実行を監視します。

このチュートリアルでは .NET SDK を使用します。 Azure Data Factory の操作にはその他のメカニズムを使用することもできます。 Data Factory のクイックスタートについては、[5 分間のクイックスタート](./quickstart-create-data-factory-portal.md)を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="prerequisites"></a>前提条件

* Azure ストレージ アカウント。 BLOB ストレージをソース データ ストアとして使用します。 Azure ストレージ アカウントがない場合は、「[ストレージ アカウントの作成](../storage/common/storage-account-create.md)」を参照してください。
* Azure Storage Explorer. このツールをインストールするには、「[Azure Storage Explorer](https://storageexplorer.com/)」を参照してください。
* Azure SQL Database。 データベースをシンク データ ストアとして使用します。 Azure SQL Database のデータベースがない場合は、[Azure SQL Database のデータベースの作成](../azure-sql/database/single-database-create-quickstart.md)に関するページを参照してください。
* 見ることができます。 この記事では、Visual Studio 2019 を使用します。
* Azure .NET SDK。 [Azure .NET SDK](https://azure.microsoft.com/downloads/) をダウンロードしてインストールします。

現在 Data Factory が利用可能な Azure リージョンの一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」を参照してください。 データ ストアとコンピューティングは、別のリージョンに配置できます。 ストアには、Azure Storage と Azure SQL Database が含まれます。 コンピューティングには、Data Factory で使用される HDInsight が含まれます。

「[Azure Active Directory アプリケーションを作成する](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)」の説明に従って、アプリケーションを作成します。 同じ記事の手順に従って、このアプリケーションを **共同作成者** ロールに割り当てます。 このチュートリアルの後の方で、**アプリケーション (クライアント) ID** や **ディレクトリ (テナント) ID** など、いくつかの値が必要になります。

### <a name="create-a-blob-table"></a>BLOB テーブルを作成する

1. テキスト エディターを開きます。 次のテキストをコピーし、*input.txt* としてローカルに保存します。

   ```
   Ethel|Berg
   Tamika|Walsh
   ```

1. Azure ストレージ エクスプローラーを開きます。 お使いのストレージ アカウントを展開します。 **[BLOB コンテナー]** を右クリックし、 **[BLOB コンテナーの作成]** を選択します。
1. 新しいコンテナーに *adfv2branch* という名前を付け、 **[アップロード]** を選択して、そのコンテナーに *input.txt* ファイルを追加します。

## <a name="create-visual-studio-project"></a>Visual Studio プロジェクトを作成する<a name="create-visual-studio-project"></a>

C# .NET コンソール アプリケーションを作成します。

1. Visual Studio を起動し、 **[新しいプロジェクトの作成]** を選択します。
1. **[新しいプロジェクトの作成]** で、C# の **[コンソール アプリ (.NET Framework)]** を選択してから、 **[次へ]** を選択します。
1. プロジェクトに *ADFv2BranchTutorial* という名前を付けます。
1. **.NET バージョン 4.5.2** 以降を選択し、 **[作成]** を選択します。

### <a name="install-nuget-packages"></a>NuGet パッケージのインストール

1. **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に選択します。
1. **パッケージ マネージャー コンソール** で、次のコマンドを実行してパッケージをインストールします。 詳細については、[Microsoft.Azure.Management.DataFactory nuget パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/)を参照してください。

   ```powershell
   Install-Package Microsoft.Azure.Management.DataFactory
   Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

### <a name="create-a-data-factory-client"></a>データ ファクトリ クライアントを作成する

1. *Program.cs* を開き、次のステートメントを追加します。

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

1. `Program` クラスに次の静的変数を追加します。 プレースホルダーは実際の値に置き換えてください。

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

1. `Main` メソッドに次のコードを追加します。 このコードによって、`DataFactoryManagementClient` クラスのインスタンスが作成されます。 その後、このオブジェクトを使用して、データ ファクトリ、リンクされたサービス、データセット、パイプラインを作成します。 また、このオブジェクトを使用して、パイプラインの実行の詳細を監視することもできます。

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
   ```

### <a name="create-a-data-factory"></a>Data Factory の作成

1. *Program.cs* ファイルに `CreateOrUpdateDataFactory` メソッドを追加します。

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

1. データ ファクトリを作成する次の行を `Main` メソッドに追加します。

   ```csharp
   Factory df = CreateOrUpdateDataFactory(client);
   ```

## <a name="create-an-azure-storage-linked-service"></a>Azure Storage のリンクされたサービスを作成する

1. *Program.cs* ファイルに `StorageLinkedServiceDefinition` メソッドを追加します。

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

1. Azure Storage のリンクされたサービスを作成する次の行を `Main` メソッドに追加します。

   ```csharp
   client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
   ```

サポートされているプロパティと詳細については、「[リンクされたサービスのプロパティ](connector-azure-blob-storage.md#linked-service-properties)」を参照してください。

## <a name="create-datasets"></a>データセットを作成する

このセクションでは、ソース用とシンク用に 1 つずつ、2 つのデータセットを作成します。

### <a name="create-a-dataset-for-a-source-azure-blob"></a>ソース Azure BLOB 用のデータセットの作成

*Azure BLOB データセット* を作成するメソッドを追加します。 サポートされているプロパティと詳細については、[Azure BLOB データセットのプロパティ](connector-azure-blob-storage.md#dataset-properties)に関するページを参照してください。

*Program.cs* ファイルに `SourceBlobDatasetDefinition` メソッドを追加します。

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

Azure BLOB 内のソース データを表すデータセットを定義します。 この BLOB データセットは、前の手順でサポートされていた Azure Storage のリンクされたサービスを参照します。 BLOB データセットには、コピー元の BLOB の場所が記述されます。*FolderPath* と *FileName* が使用されます。

*FolderPath* ではパラメーターが使用されていることに注意してください。 `sourceBlobContainer` はパラメーターの名前であり、この式はパイプラインの実行で渡された値に置き換えられます。 パラメーターを定義する構文は `@pipeline().parameters.<parameterName>` です。

### <a name="create-a-dataset-for-a-sink-azure-blob"></a>シンク Azure BLOB 用のデータセットの作成

1. *Program.cs* ファイルに `SourceBlobDatasetDefinition` メソッドを追加します。

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

1. Azure BLOB のソース データセットとシンク データセット両方を作成する次のコードを `Main` メソッドに追加します。

   ```csharp
   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
   ```

## <a name="create-a-c-class-emailrequest"></a>C# クラスを作成する:EmailRequest

C# プロジェクトで、`EmailRequest` という名前のクラスを作成します。 このクラスは、パイプラインがメールを送信するときに要求本文で送信するプロパティを定義します。 このチュートリアルでは、パイプラインから電子メールに次の 4 つのプロパティが送信されます。

* Message. メールの本文。 コピーが成功した場合、このプロパティには、書き込まれたデータの量が含まれます。 コピーが失敗した場合、このプロパティには、エラーの詳細が含まれます。
* データ ファクトリ名。 データ ファクトリの名前。
* パイプライン名。 パイプラインの名前。
* 受信者。 パススルーされるパラメーター。 このプロパティは、電子メールの受信者を指定します。

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

電子メール送信をトリガーするには、[Logic Apps](../logic-apps/logic-apps-overview.md) を使用してワークフローを定義します。 Logic Apps ワークフローの作成の詳細については、[ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

### <a name="success-email-workflow"></a>成功電子メールのワークフロー

[Azure portal](https://portal.azure.com) で、*CopySuccessEmail* という名前の Logic Apps ワークフローを作成します。 ワークフロー トリガーを `When an HTTP request is received` として定義します。 要求トリガー用に、`Request Body JSON Schema` に次の JSON を入力します。

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

ワークフローは次の例のようになります。

![成功電子メールのワークフロー](media/tutorial-control-flow/success-email-workflow-trigger.png)

この JSON コンテンツは、前のセクションで作成した `EmailRequest` クラスに合わせたものです。

`Office 365 Outlook – Send an email` のアクションを追加します。 **[Send an email]\(メールの送信\)** アクションでは、要求 **本文** の JSON スキーマで渡されるプロパティを使用して、メールの書式設定方法をカスタマイズします。 次に例を示します。

![ロジック アプリ デザイナー - メールの送信アクション](media/tutorial-control-flow/customize-send-email-action.png)

ワークフローを保存した後、トリガーの **[HTTP POST の URL]** の値をコピーして保存します。

## <a name="fail-email-workflow"></a>失敗電子メールのワークフロー

**CopySuccessEmail** を、別の Logic Apps ワークフローとして *CopyFailEmail* という名前で複製します。 要求トリガー内の `Request Body JSON schema` は同じです。 `Subject` のように電子メールの書式を変更し、失敗電子メールになるように調整します。 たとえば次のようになります。

![ロジック アプリ デザイナー - 失敗メールのワークフロー](media/tutorial-control-flow/fail-email-workflow.png)

ワークフローを保存した後、トリガーの **[HTTP POST の URL]** の値をコピーして保存します。

これで、次の例のように、2 つのワークフローの URL を取得できたはずです。

```csharp
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-pipeline"></a>パイプラインを作成する

Visual Studio でプロジェクトに戻ります。 ここで、コピー アクティビティと `DependsOn` プロパティを含むパイプラインを作成する次のコードを追加します。 このチュートリアルでは、パイプラインに 1 つのアクティビティ (コピー アクティビティ) が含まれています。このアクティビティは、ソースとして BLOB データセットを取り込み、シンクとして別の BLOB データセットを取り込みます。 このコピー アクティビティが成功したか失敗したかに応じて、異なるメール タスクが呼び出されます。

このパイプラインでは、次の機能を使用します。

* パラメーター
* Web アクティビティ
* アクティビティの依存関係
* あるアクティビティからの出力を別のアクティビティへの入力として使用する

1. このメソッドをプロジェクトに追加します。 以降のセクションで、さらに詳しく説明します。

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

1. パイプラインを作成する次の行を `Main` メソッドに追加します。

   ```csharp
   client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
   ```

### <a name="parameters"></a>パラメーター

パイプライン コードの最初のセクションでは、パラメーターを定義します。

* `sourceBlobContainer`. ソース BLOB データセットは、パイプラインでこのパラメーターを使用します。
* `sinkBlobContainer`. シンク BLOB データセットは、パイプラインでこのパラメーターを使用します。
* `receiver`. 成功または失敗のメールを受信者に送信する、パイプライン内の 2 つの Web アクティビティがこのパラメーターを使用します。

```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```

### <a name="web-activity"></a>Web アクティビティ

Web アクティビティを使用すると、任意の REST エンドポイントを呼び出すことができます。 このアクティビティの詳細については、「[Azure Data Factory の Web アクティビティ](control-flow-web-activity.md)」を参照してください。 このパイプラインでは、Web アクティビティを使用して Logic Apps のメール ワークフローを呼び出します。 `CopySuccessEmail` ワークフローを呼び出す Web アクティビティと `CopyFailWorkFlow` を呼び出す Web アクティビティの 2 つ作成します。

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

`Url` プロパティには、Logic Apps ワークフローの **HTTP POST URL** エンドポイントを貼り付けます。 `Body` プロパティで、`EmailRequest` クラスのインスタンスを渡します。 この電子メール要求には次のプロパティが含まれます。

* Message. `@{activity('CopyBlobtoBlob').output.dataWritten` の値を渡します。 前のコピー アクティビティのプロパティにアクセスし、`dataWritten` の値を渡します。 失敗の場合、`@{activity('CopyBlobtoBlob').error.message` の代わりにエラー出力を渡します。
* データ ファクトリ名。 `@{pipeline().DataFactory}` の値を渡します。このシステム変数を使用すると、対応するデータ ファクトリ名にアクセスできます。 システム変数の一覧については、[システム変数](control-flow-system-variables.md)に関するページを参照してください。
* パイプライン名。 `@{pipeline().Pipeline}` の値を渡します。 このシステム変数を使用すると、対応するパイプライン名にアクセスできます。
* 受信者。 `"@pipeline().parameters.receiver"` の値を渡します。 パイプライン パラメーターにアクセスします。

このコードにより、前のコピー アクティビティに依存する、アクティビティの依存関係が新しく作成されます。

## <a name="create-a-pipeline-run"></a>パイプラインの実行を作成する

パイプラインの実行をトリガーする次のコードを `Main` メソッドに追加します。

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

最終的な `Main` メソッドは次のようになります。

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

プログラムをビルドして実行し、パイプラインの実行をトリガーしてください。

## <a name="monitor-a-pipeline-run"></a>パイプラインの実行を監視する

1. 次のコードを `Main` メソッドに追加します。

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

    このコードは、データのコピーが完了するまで、実行の状態を継続的に確認します。

1. コピー アクティビティの実行の詳細 (たとえば、読み書きされたデータのサイズ) を取得する次のコードを `Main` メソッドに追加します。

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

このアプリケーションでは、データ ファクトリ、リンクされたサービス、データセット、パイプライン、およびパイプラインの実行の作成の進捗状況が表示されます。 その後、パイプラインの実行状態が確認されます。 コピー アクティビティの実行の詳細と、データの読み取り/書き込みのサイズが表示されるまで待ちます。 次に、Azure Storage Explorer などのツールを使用して、変数で指定したように BLOB が *inputBlobPath* から *outputBlobPath* にコピーされたことを確認します。

出力は次のサンプルのようになります。

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
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

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次のタスクを実行しました。

> [!div class="checklist"]
> * Data Factory の作成
> * Azure Storage のリンクされたサービスを作成する
> * Azure BLOB データセットを作成します。
> * コピー アクティビティと Web アクティビティを含むパイプラインを作成します。
> * アクティビティの出力を後続のアクティビティに送信します。
> * パラメーターの引き渡しとシステム変数を使用する
> * パイプラインの実行を開始します。
> * パイプラインとアクティビティの実行を監視します。

これで、概念セクションに進むと、Azure Data Factory の詳細を確認できるようになりました。
> [!div class="nextstepaction"]
>[パイプラインとアクティビティ](concepts-pipelines-activities.md)