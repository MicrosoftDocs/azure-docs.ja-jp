---
title: "Azure .NET SDK を使用したデータ パイプラインの作成 | Microsoft Docs"
description: "Data Factory SDK を使用して Azure Data Factory をプログラムによって作成、監視、管理する方法について説明します。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: b0a357be-3040-4789-831e-0d0a32a0bda5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 6a76c399e626ea85581d5f8fb863da878bdbf50b
ms.lasthandoff: 03/09/2017


---
# <a name="create-monitor-and-manage-azure-data-factories-using-azure-data-factory-net-sdk"></a>Azure Data Factory .NET SDK を使用した Azure Data Factory の作成、監視、および管理
## <a name="overview"></a>概要
Data Factory .NET SDK を使用して Azure Data Factory をプログラムによって作成、監視、管理できます。 この記事には、Data Factory の作成と監視を行うサンプルの .NET コンソール アプリケーションを作成できるチュートリアルが含まれています。 Data Factory .NET SDK の詳細については、 [Data Factory クラス ライブラリ リファレンス](https://msdn.microsoft.com/library/mt415893.aspx) に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件
* Visual Studio 2012 または 2013 または 2015
* [Azure .NET SDK](http://azure.microsoft.com/downloads/)のダウンロードとインストール。
* Azure Active Directory にネイティブ クライアント アプリケーションを追加します。 アプリケーションを追加する手順については、「 [Azure Active Directory とアプリケーションの統合](../active-directory/active-directory-integrating-applications.md) 」を参照してください。 **[構成]** ページの**クライアント ID** と**リダイレクト URI** をメモしておきます。
* Azure の**サブスクリプション ID** と**テナント ID** を入手します。 手順については、「 [Azure サブスクリプションとテナント ID を入手する](#get-azure-subscription-and-tenant-ids) 」を参照してください。
* Azure Data Factory の NuGet パッケージをダウンロードしてインストールします。 手順はこのチュートリアルにあります。

## <a name="walkthrough"></a>チュートリアル
1. Visual Studio 2012 または 2013 を使用して、C# .NET コンソール アプリケーションを作成します。
   1. **Visual Studio 2012/2013/2015**を起動します。
   2. **[ファイル]** をクリックし、**[新規作成]** をポイントして、**[プロジェクト]** をクリックします。
   3. **[テンプレート]** を展開し、**[Visual C#]** を選択します。 このチュートリアルでは C# を使用しますが、どの .NET 言語でも使用できます。
   4. 右側にあるプロジェクトの種類の一覧から **[コンソール アプリケーション]** を選択します。
   5. **[名前]** に「**DataFactoryAPITestApp**」と入力します。
   6. **[場所]** は **[C:\ADFGetStarted]** を選択します。
   7. **[OK]** をクリックしてプロジェクトを作成します。
2. **[ツール]** をクリックし、**[NuGet パッケージ マネージャー]** をポイントして、**[パッケージ マネージャー コンソール]** をクリックします。
3. **[パッケージ マネージャー コンソール]**で、次のコマンドを&1; つずつ実行します。

    ```
    Install-Package Microsoft.Azure.Management.DataFactories
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
    ```
4. 次の **appSetttings** セクションを **App.config** ファイルに追加します。 これらの構成値は、 **GetAuthorizationHeader** メソッドで使用されます。

   > [!IMPORTANT]
   > **AdfClientId**、**RedirectUri**、**SubscriptionId**、**ActiveDirectoryTenantId** の値は、実際の値で置き換えてください。

    ```XML
    <appSettings>
        <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
        <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
        <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
    
        <!-- Replace the following values with your own -->
        <add key="AdfClientId" value="Your AAD application ID" />
        <add key="RedirectUri" value="Your AAD application's redirect URI" />
        <add key="SubscriptionId" value="your subscription ID" />
        <add key="ActiveDirectoryTenantId" value="your tenant ID" />
    </appSettings>
    ```
5. 次の **using** ステートメントをプロジェクト内のソース ファイル (Program.cs) に追加します。

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
    string resourceGroupName = "resourcegroupname";
    string dataFactoryName = "APITutorialFactorySP";
    
    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
        GetAuthorizationHeader().Result);
    
    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);
    
    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!NOTE]
   > **resourcegroupname** は Azure リソース グループの名前に置き換えます。 リソース グループを作成するには、 [New-AzureResourceGroup](https://msdn.microsoft.com/library/Dn654594.aspx) コマンドレットを使用します。
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
8. **リンクされたサービス**を作成する次のコードを **Main** メソッドに追加します。

   > [!NOTE]
   > **ConnectionString** には、Azure ストレージ アカウントの**アカウント名**と**アカウント キー**を使用します。

    ```csharp
    // create a linked service
    Console.WriteLine("Creating a linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "LinkedService-AzureStorage",
                Properties = new LinkedServiceProperties
                (
                    new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>")
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
                LinkedServiceName = "LinkedService-AzureStorage",
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
    
                LinkedServiceName = "LinkedService-AzureStorage",
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
11. **Main** メソッドで使用される次のヘルパー メソッドを **Program** クラスに追加します。 このメソッドは、Azure Portal へのログインに使用する**ユーザー名**と**パスワード**の入力が可能なダイアログ ボックスを表示します。

    ```csharp
    public static async Task<string> GetAuthorizationHeader()
    {
        var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
        AuthenticationResult result = await context.AcquireTokenAsync(
            resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
            clientId: ConfigurationManager.AppSettings["AdfClientId"],
            redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
            promptBehavior: PromptBehavior.Always);

        if (result != null)
            return result.AccessToken;

        throw new InvalidOperationException("Failed to acquire token");
    }
    ```
12. 次のコードを **Main** メソッドに追加して、出力データセットのデータ スライスのステータスを取得します。 この例で予想される&1; つのスライスのみが存在します。

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
14. ソリューション エクスプローラーで、プロジェクト (**DataFactoryAPITestApp**) を展開し、**[参照]** を右クリックして **[参照の追加]** をクリックします。 `System.Configuration` アセンブリのチェック ボックスをオンにし、 **[OK]**をクリックします。
15. コンソール アプリケーションをビルドします。 メニューから **[ビルド]** をクリックし、**[ソリューションのビルド]** をクリックします。
16. Azure BLOB ストレージ内の adftutorial コンテナーに少なくとも&1; つのファイルが存在することを確認します。 存在しない場合は、以下の内容を記述した Emp.txt ファイルをメモ帳で作成し、これを adftutorial コンテナーにアップロードします。

    ```
    John, Doe
    Jane, Doe
    ```
17. メニューの **[デバッグ]** -> **[デバッグの開始]** の順にクリックして、サンプルを実行します。 **[Getting run details of a data slice (データ スライスの実行の詳細を取得中)]** が表示されている場合は、数分待機して、**Enter** を押します。
18. Azure ポータルを使用して、データ ファクトリの **APITutorialFactory** が次のアーティファクトで作成されることを確認します。
    * リンクされたサービス: **LinkedService_AzureStorage**
    * データセット: **DatasetBlobSource** と **DatasetBlobDestination**
    * パイプライン: **PipelineBlobSample**
19. **adftutorial** コンテナーの **apifactoryoutput** フォルダーに出力ファイルが作成されることを確認します。

## <a name="log-in-without-popup-dialog-box"></a>ポップアップ ダイアログ ボックスなしでのログイン
このチュートリアルのサンプル コードでは、Azure 資格情報を入力するためのダイアログ ボックスが起動されます。 ダイアログ ボックスを使用せずにプログラムでサインインする必要がある場合は、[Azure Resource Manager でのサービス プリンシパルの認証](../azure-resource-manager/resource-group-authenticate-service-principal.md)に関するページを参照してください。

> [!IMPORTANT]
> Web アプリケーションを Azure Active Directory に追加し、アプリケーションのクライアント ID とクライアント シークレットを書き留めてください。
>
>

### <a name="example"></a>例
GetAuthorizationHeaderNoPopup メソッドを作成します。

```csharp
public static async Task<string> GetAuthorizationHeaderNoPopup()
{
    var authority = new Uri(new Uri("https://login.windows.net"), ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
    var context = new AuthenticationContext(authority.AbsoluteUri);
    var credential = new ClientCredential(
        ConfigurationManager.AppSettings["AdfClientId"],
    ConfigurationManager.AppSettings["AdfClientSecret"]);
    
    AuthenticationResult result = await context.AcquireTokenAsync(
        ConfigurationManager.AppSettings["WindowsManagementUri"],
    credential);

    if (result != null)
        return result.AccessToken;

    throw new InvalidOperationException("Failed to acquire token");
}
```

**Main** 関数の **GetAuthorizationHeader** の呼び出しを **GetAuthorizationHeaderNoPopup** の呼び出しで置き換えます。

```csharp
TokenCloudCredentials aadTokenCredentials =
    new TokenCloudCredentials(
    ConfigurationManager.AppSettings["SubscriptionId"],
    GetAuthorizationHeaderNoPopup().Result);
```

次に、Active Directory アプリケーションとサービス プリンシパルを作成した後、それを Data Factory 共同作成者ロールに割り当てる方法を示します。

1. AD アプリケーションを作成します。

    ```PowerShell
    $azureAdApplication = New-AzureRmADApplication -DisplayName "MyADAppForADF" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.myadappforadf.org/example" -Password "Pass@word1"
    ```
2. AD サービス プリンシパルを作成します。

    ```PowerShell
    New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    ```
3. Data Factory 共同作成者ロールにサービス プリンシパルを追加します。

    ```PowerShell
    New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    ```
4. アプリケーション ID を取得します。

    ```PowerShell
    $azureAdApplication
    ```

アプリケーション ID とパスワード (クライアント シークレット) を書き留めて、チュートリアルで使用します。

## <a name="get-azure-subscription-and-tenant-ids"></a>Azure サブスクリプションとテナント ID を入手する
コンピューターに PowerShell の最新バージョンをインストールしていない場合は、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs) 」に記載されている手順に従ってインストールします。

1. Azure PowerShell を起動し、次のコマンドを実行します。
2. 次のコマンドを実行して、Azure ポータルへのサインインに使用するユーザー名とパスワードを入力します。

    ```PowerShell
    Login-AzureRmAccount
    ```

    このアカウントに関連付けられている Azure サブスクリプションを&1; つのみをお持ちの場合は、次の&2; つの手順を実行する必要はありません。
3. 次のコマンドを実行して、このアカウントのすべてのサブスクリプションを表示します。

    ```PowerShell
    Get-AzureRmSubscription
    ```
4. 次のコマンドを実行して、使用するサブスクリプションを選択します。 **NameOfAzureSubscription** を自分の Azure サブスクリプションの名前で置き換えます。

    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName NameOfAzureSubscription | Set-AzureRmContext
    ```PowerShell

Note down the **SubscriptionId** and **TenantId** values.

