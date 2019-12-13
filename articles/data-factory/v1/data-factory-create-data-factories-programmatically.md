---
title: Azure .NET SDK を使用してデータ パイプラインを作成する
description: Data Factory SDK を使用して Azure Data Factory をプログラムによって作成、監視、管理する方法について説明します。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 9cd3cd60f5d62a0c416b0e05ea408c20483bff13
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931328"
---
# <a name="create-monitor-and-manage-azure-data-factories-using-azure-data-factory-net-sdk"></a>Azure Data Factory .NET SDK を使用した Azure Data Factory の作成、監視、および管理
> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[コピー アクティビティのチュートリアル](../quickstart-create-data-factory-dot-net.md)に関するページを参照してください。 

## <a name="overview"></a>概要
Data Factory .NET SDK を使用して Azure Data Factory をプログラムによって作成、監視、管理できます。 この記事には、Data Factory の作成と監視を行うサンプルの .NET コンソール アプリケーションを作成できるチュートリアルが含まれています。 

> [!NOTE]
> この記事では、すべての Data Factory .NET API を取り上げているわけではありません。 Data Factory の .NET API に関する完全なドキュメントについては、[Data Factory .NET API リファレンス](/dotnet/api/index?view=azuremgmtdatafactories-4.12.1)を参照してください。 

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Visual Studio 2012 または 2013 または 2015
* [Azure .NET SDK](https://azure.microsoft.com/downloads/)のダウンロードとインストール。
* Azure PowerShell。 「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/overview) 」に記載されている手順に従って、コンピューターに Azure PowerShell をインストールします。 Azure PowerShell を使用して、Azure Active Directory アプリケーションを作成します。

### <a name="create-an-application-in-azure-active-directory"></a>Azure Active Directory にアプリケーションを作成する
Azure Active Directory アプリケーションを作成し、アプリケーションのサービス プリンシパルを作成して、 **Data Factory 共同作成者** ロールに割り当てます。

1. **PowerShell**を起動します。
2. 次のコマンドを実行して、Azure ポータルへのサインインに使用するユーザー名とパスワードを入力します。

    ```powershell
    Connect-AzAccount
    ```
3. 次のコマンドを実行して、このアカウントのすべてのサブスクリプションを表示します。

    ```powershell
    Get-AzSubscription
    ```
4. 次のコマンドを実行して、使用するサブスクリプションを選択します。 **&lt;NameOfAzureSubscription**&gt; を自分の Azure サブスクリプションの名前で置き換えます。

    ```powershell
    Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
    ```

   > [!IMPORTANT]
   > このコマンドの出力から、**SubscriptionId** と **TenantId** をメモしておきます。

5. PowerShell で次のコマンドを実行して、 **ADFTutorialResourceGroup** という名前の Azure リソース グループを作成します。

    ```powershell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

    リソース グループが既に存在する場合は、それを更新するか (Y) そのまま保持するか (N) を指定します。

    異なるリソース グループを使用する場合は、このチュートリアルで ADFTutorialResourceGroup の代わりにそのリソース グループの名前を使用する必要があります。
6. Azure Active Directory アプリケーションを作成します。

    ```powershell
    $azureAdApplication = New-AzADApplication -DisplayName "ADFDotNetWalkthroughApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfdotnetwalkthroughapp.org/example" -Password "Pass@word1"
    ```

    次のエラーが表示された場合は、別の URL を指定して、コマンドをもう一度実行します。
    
    ```powershell
    Another object with the same value for property identifierUris already exists.
    ```
7. AD サービス プリンシパルを作成します。

    ```powershell
    New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    ```
8. **Data Factory 共同作成者** ロールにサービス プリンシパルを追加します。

    ```powershell
    New-AzRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    ```
9. アプリケーション ID を取得します。

    ```powershell
    $azureAdApplication 
    ```
    出力のアプリケーション ID (applicationID) をメモします。

これらの手順で、次の 4 つの値がわかりました。

* テナント ID
* サブスクリプション ID
* アプリケーション ID
* パスワード (最初のコマンドで指定)

## <a name="walkthrough"></a>チュートリアル
このチュートリアルでは、コピー アクティビティを含むパイプラインを備えたデータ ファクトリを作成します。 コピー アクティビティでは、Azure Blob Storage 内のフォルダーから同じ Blob Storage 内の別のフォルダーにデータをコピーします。 

コピー アクティビティにより、Azure Data Factory でデータ移動が実行されます。 このアクティビティは、安全で信頼性の高いスケーラブルな方法によってさまざまなデータ ストア間でデータをコピーできる、グローバルに利用可能なサービスによって動作します。 コピー アクティビティの詳細については、「 [データ移動アクティビティ](data-factory-data-movement-activities.md) 」をご覧ください。

1. Visual Studio 2012/2013/2015 を使用して、C# .NET コンソール アプリケーションを作成します。
   1. **Visual Studio** 2012/2013/2015 を起動します。
   2. **[ファイル]** をクリックし、 **[新規作成]** をポイントして、 **[プロジェクト]** をクリックします。
   3. **[テンプレート]** を展開し、 **[Visual C#]** を選択します。 このチュートリアルでは C# を使用しますが、どの .NET 言語でも使用できます。
   4. 右側にあるプロジェクトの種類の一覧から **[コンソール アプリケーション]** を選択します。
   5. [名前] に「 **DataFactoryAPITestApp** 」と入力します。
   6. [場所] で **[C:\ADFGetStarted]** を選択します。
   7. **[OK]** をクリックしてプロジェクトを作成します。
2. **[ツール]** をクリックし、 **[NuGet パッケージ マネージャー]** をポイントして、 **[パッケージ マネージャー コンソール]** をクリックします。
3. **パッケージ マネージャー コンソール**で、次の手順を実行します。
   1. 次のコマンドを実行して、Data Factory パッケージをインストールします: `Install-Package Microsoft.Azure.Management.DataFactories`
   2. 次のコマンドを実行して、Azure Active Directory パッケージをインストールします (コードで Active Directory API を使用します): `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
4. プロジェクトの **App.config** ファイルの内容を次の内容に置き換えます。 
    
    ```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
            <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
            <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

            <add key="ApplicationId" value="your application ID" />
            <add key="Password" value="Password you used while creating the AAD application" />
            <add key="SubscriptionId" value= "Subscription ID" />
            <add key="ActiveDirectoryTenantId" value="Tenant ID" />
        </appSettings>
    </configuration>
    ```
5. App.Config ファイルで、 **&lt;Application ID&gt;** 、 **&lt;Password&gt;** 、 **&lt;Subscription ID&gt;** 、 **&lt;tenant ID&gt;** の値を実際の値に置き換えます。
6. 次の **using** ステートメントをプロジェクト内の **Program.cs** ファイルに追加します。

    ```csharp
    using System.Configuration;
    using System.Collections.ObjectModel;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure;
    using Microsoft.Azure.Management.DataFactories;
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Common.Models;

    using Microsoft.IdentityModel.Clients.ActiveDirectory;

    ```
6. **DataPipelineManagementClient** クラスのインスタンスを作成する次のコードを **Main** メソッドに追加します。 このオブジェクトを使用して、データ ファクトリ、リンクされたサービス、入力データセットと出力データセット、およびパイプラインを作成します。 また、実行時にデータセットのスライスを監視する際にもこのオブジェクトを使用します。

    ```csharp
    // create data factory management client

    //IMPORTANT: specify the name of Azure resource group here
    string resourceGroupName = "ADFTutorialResourceGroup";

    //IMPORTANT: the name of the data factory must be globally unique.
    // Therefore, update this value. For example:APITutorialFactory05122017
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader().Result);

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > **resourceGroupName** の値を、使用する Azure リソース グループの名前で置き換えます。 リソース グループを作成するには、 [New-AzureResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドレットを使用します。
   >
   > データ ファクトリの名前 (dataFactoryName) が一意になるように更新します。 データ ファクトリの名前はグローバルに一意にする必要があります。 Data Factory アーティファクトの名前付け規則については、 [Data Factory - 名前付け規則](data-factory-naming-rules.md) に関するトピックを参照してください。
7. **データ ファクトリ**を作成する次のコードを **Main** メソッドに追加します。

    ```csharp
    // create a data factory
    Console.WriteLine("Creating a data factory");
    client.DataFactories.CreateOrUpdate(resourceGroupName,
        new DataFactoryCreateOrUpdateParameters()
        {
            DataFactory = new DataFactory()
            {
                Name = dataFactoryName,
                Location = "westus",
                Properties = new DataFactoryProperties()
            }
        }
    );
    ```
8. **Azure Storage のリンクされたサービス**を作成する次のコードを **Main** メソッドに追加します。

   > [!IMPORTANT]
   > **storageaccountname** と **accountkey** を Azure ストレージ アカウントの名前とキーで置き換えます。

    ```csharp
    // create a linked service for input data store: Azure Storage
    Console.WriteLine("Creating Azure Storage linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureStorageLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                )
            }
        }
    );
    ```
9. **入力データセットと出力データセット**を作成する次のコードを **Main** メソッドに追加します。

    入力 BLOB の **FolderPath** を **adftutorial/** に設定します。この **adftutorial** は BLOB ストレージ内のコンテナーの名前です。 Azure BLOB ストレージにこのコンテナーが存在しない場合は、**adftutorial** という名前のコンテナーを作成し、このコンテナーにテキスト ファイルをアップロードします。

    出力 BLOB の FolderPath を **adftutorial/apifactoryoutput/{Slice}** に設定します。この **Slice** は **SliceStart** (各スライスの開始日時) の値に基づいて動的に計算されます。

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "DatasetBlobSource";
    string Dataset_Destination = "DatasetBlobDestination";
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/",
                    FileName = "emp.txt"
                },
                External = true,
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },
    
                Policy = new Policy()
                {
                    Validation = new ValidationPolicy()
                    {
                        MinimumRows = 1
                    }
                }
            }
        }
    });
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Destination,
            Properties = new DatasetProperties()
            {
    
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                    PartitionedBy = new Collection<Partition>()
                    {
                        new Partition()
                        {
                            Name = "Slice",
                            Value = new DateTimePartitionValue()
                            {
                                Date = "SliceStart",
                                Format = "yyyyMMdd-HH"
                            }
                        }
                    }
                },
    
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },
            }
        }
    });
    ```
10. **パイプラインを作成してアクティブにする**次のコードを **Main** メソッドに追加します。 このパイプラインには、ソースとして **BlobSource**、シンクとして **BlobSink** を使用する **CopyActivity** があります。

    コピー アクティビティにより、Azure Data Factory でデータ移動が実行されます。 このアクティビティは、安全で信頼性の高いスケーラブルな方法によってさまざまなデータ ストア間でデータをコピーできる、グローバルに利用可能なサービスによって動作します。 コピー アクティビティの詳細については、「 [データ移動アクティビティ](data-factory-data-movement-activities.md) 」をご覧ください。

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
    string PipelineName = "PipelineBlobSample";
    
    client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new PipelineCreateOrUpdateParameters()
    {
        Pipeline = new Pipeline()
        {
            Name = PipelineName,
            Properties = new PipelineProperties()
            {
                Description = "Demo Pipeline for data transfer between blobs",
    
                // Initial value for pipeline's active period. With this, you won't need to set slice status
                Start = PipelineActivePeriodStartTime,
                End = PipelineActivePeriodEndTime,
    
                Activities = new List<Activity>()
                {
                    new Activity()
                    {
                        Name = "BlobToBlob",
                        Inputs = new List<ActivityInput>()
                        {
                            new ActivityInput()
                {
                                Name = Dataset_Source
                            }
                        },
                        Outputs = new List<ActivityOutput>()
                        {
                            new ActivityOutput()
                            {
                                Name = Dataset_Destination
                            }
                        },
                        TypeProperties = new CopyActivity()
                        {
                            Source = new BlobSource(),
                            Sink = new BlobSink()
                            {
                                WriteBatchSize = 10000,
                                WriteBatchTimeout = TimeSpan.FromMinutes(10)
                            }
                        }
                    }
    
                },
            }
        }
    });
    ```
12. 次のコードを **Main** メソッドに追加して、出力データセットのデータ スライスのステータスを取得します。 この例で予想される 1 つのスライスのみが存在します。

    ```csharp
    // Pulling status within a timeout threshold
    DateTime start = DateTime.Now;
    bool done = false;
    
    while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
    {
        Console.WriteLine("Pulling the slice status");
        // wait before the next status check
        Thread.Sleep(1000 * 12);
    
        var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
            new DataSliceListParameters()
            {
                DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
            });
    
        foreach (DataSlice slice in datalistResponse.DataSlices)
        {
            if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
            {
                Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                done = true;
                break;
            }
            else
            {
                Console.WriteLine("Slice status is: {0}", slice.State);
            }
        }
    }
    ```
13. **(省略可能)** データ スライスの実行の詳細を取得するために次のコードを **Main** メソッドに追加します。

    ```csharp
    Console.WriteLine("Getting run details of a data slice");
    
    // give it a few minutes for the output slice to be ready
    Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
    Console.ReadKey();
    
    var datasliceRunListResponse = client.DataSliceRuns.List(
        resourceGroupName,
        dataFactoryName,
        Dataset_Destination,
        new DataSliceRunListParameters()
        {
            DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
        });
    
    foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
    {
        Console.WriteLine("Status: \t\t{0}", run.Status);
        Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
        Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
        Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
        Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
        Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
        Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
    }
    
    Console.WriteLine("\nPress any key to exit.");
    Console.ReadKey();
    ```
14. **Main** メソッドで使用される次のヘルパー メソッドを **Program** クラスに追加します。 このメソッドは、Azure Portal へのログインに使用する**ユーザー名**と**パスワード**の入力が可能なダイアログ ボックスを表示します。

    ```csharp
    public static async Task<string> GetAuthorizationHeader()
    {
        AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
        ClientCredential credential = new ClientCredential(
            ConfigurationManager.AppSettings["ApplicationId"],
            ConfigurationManager.AppSettings["Password"]);
        AuthenticationResult result = await context.AcquireTokenAsync(
            resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
            clientCredential: credential);

        if (result != null)
            return result.AccessToken;

        throw new InvalidOperationException("Failed to acquire token");
    }
    ```

15. ソリューション エクスプローラーで、**DataFactoryAPITestApp** プロジェクトを展開し、 **[参照]** を右クリックして **[参照の追加]** をクリックします。 `System.Configuration` アセンブリのチェック ボックスをオンにし、 **[OK]** をクリックします。
15. コンソール アプリケーションをビルドします。 メニューから **[ビルド]** をクリックし、 **[ソリューションのビルド]** をクリックします。
16. Azure BLOB ストレージ内の adftutorial コンテナーに少なくとも 1 つのファイルが存在することを確認します。 存在しない場合は、以下の内容を記述した Emp.txt ファイルをメモ帳で作成し、これを adftutorial コンテナーにアップロードします。

    ```
    John, Doe
    Jane, Doe
    ```
17. メニューの **[デバッグ]**  ->  **[デバッグの開始]** の順にクリックして、サンプルを実行します。 **[Getting run details of a data slice (データ スライスの実行の詳細を取得中)]** が表示されている場合は、数分待機して、**Enter** を押します。
18. Azure ポータルを使用して、データ ファクトリの **APITutorialFactory** が次のアーティファクトで作成されることを確認します。
    * リンクされたサービス: **AzureStorageLinkedService**
    * データセット: **DatasetBlobSource** と **DatasetBlobDestination**。
    * パイプライン: **PipelineBlobSample**
19. **adftutorial** コンテナーの **apifactoryoutput** フォルダーに出力ファイルが作成されることを確認します。

## <a name="get-a-list-of-failed-data-slices"></a>失敗したデータ スライスの一覧を取得する 

```csharp
// Parse the resource path
var ResourceGroupName = "ADFTutorialResourceGroup";
var DataFactoryName = "DataFactoryAPITestApp";

var parameters = new ActivityWindowsByDataFactoryListParameters(ResourceGroupName, DataFactoryName);
parameters.WindowState = "Failed";
var response = dataFactoryManagementClient.ActivityWindows.List(parameters);
do
{
    foreach (var activityWindow in response.ActivityWindowListResponseValue.ActivityWindows)
    {
        var row = string.Join(
            "\t",
            activityWindow.WindowStart.ToString(),
            activityWindow.WindowEnd.ToString(),
            activityWindow.RunStart.ToString(),
            activityWindow.RunEnd.ToString(),
            activityWindow.DataFactoryName,
            activityWindow.PipelineName,
            activityWindow.ActivityName,
            string.Join(",", activityWindow.OutputDatasets));
        Console.WriteLine(row);
    }

    if (response.NextLink != null)
    {
        response = dataFactoryManagementClient.ActivityWindows.ListNext(response.NextLink, parameters);
    }
    else
    {
        response = null;
    }
}
while (response != null);
```

## <a name="next-steps"></a>次の手順
Azure Blob Storage から Azure SQL データベースにデータをコピーする .NET SDK を使用してパイプラインを作成するには、次の例を確認してください。 

- [Blob Storage から SQL Database にデータをコピーするパイプラインを作成する](data-factory-copy-activity-tutorial-using-dotnet-api.md)
