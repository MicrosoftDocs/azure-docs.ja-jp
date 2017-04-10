---
title: "チュートリアル - .NET 向け Azure Batch クライアント ライブラリを使用する | Microsoft Docs"
description: "Azure Batch の基本的な概念について説明し、.NET でシンプルなソリューションを作成します。"
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 76cb9807-cbc1-405a-8136-d1e53e66e82b
ms.service: batch
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 08b478b3108f0673f3a130612020408a0e61c815
ms.lasthandoff: 03/09/2017


---
# <a name="get-started-building-solutions-with-the-batch-client-library-for-net"></a>.NET 向け Batch クライアント ライブラリを使用してソリューション作成を開始する

> [!div class="op_single_selector"]
> * [.NET](batch-dotnet-get-started.md)
> * [Python](batch-python-tutorial.md)
>
>

この記事では、C# サンプル アプリケーションについて順を追って説明しながら、[Azure Batch][azure_batch] と [Batch .NET][net_api] ライブラリの基礎を学習します。 サンプル アプリケーションで Batch サービスを活用してクラウド内の並列ワークロードを処理する方法のほか、[Azure Storage](../storage/storage-introduction.md) とやり取りしてファイルのステージングと取得を行う方法を示します。 また、一般的な Batch アプリケーション ワークフローと、ジョブ、タスク、プール、コンピューティング ノードなど、Batch の主なコンポーネントの基本も理解できます。

![Batch ソリューションのワークフロー (基本)][11]<br/>

## <a name="prerequisites"></a>前提条件
この記事は、C# と Visual Studio に関する実用的な知識があることを前提としています。 また、以下で指定されている、Azure、Batch サービス、Storage サービスのアカウント作成要件を満たせることも前提としています。

### <a name="accounts"></a>アカウント
* **Azure アカウント**: まだ Azure サブスクリプションを持っていない場合は、[無料 Azure アカウントを作成][azure_free_account]します。
* **Batch アカウント**: Azure サブスクリプションの用意ができたら、 [Azure Batch アカウントを作成](batch-account-create-portal.md)します。
* **ストレージ アカウント**: 「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」の「[ストレージ アカウントの作成](../storage/storage-create-storage-account.md#create-a-storage-account)」を参照してください。

> [!IMPORTANT]
> 「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」の手順 5. 「[ストレージ アカウントの作成](../storage/storage-create-storage-account.md#create-a-storage-account)」で説明されているように、Batch では、現時点で**汎用**のストレージ アカウントの種類 "*のみ*" がサポートされています。
>
>

### <a name="visual-studio"></a>Visual Studio
サンプル プロジェクトをビルドするには、**Visual Studio 2015 以降**が必要です。 Visual Studio の無料試用版については、[Visual Studio 製品の概要][visual_studio]に関するページを参照してください。

### <a name="dotnettutorial-code-sample"></a>*DotNetTutorial* コード サンプル
[DotNetTutorial][github_dotnettutorial] サンプルは、GitHub の [azure-batch-samples][github_samples] リポジトリに置かれている多数の Batch コード サンプルの&1; つです。 リポジトリのホーム ページから **[Clone or download (複製またはダウンロード)]、[Download ZIP (ZIP のダウンロード)]** の順にクリックするか、[azure-batch-samples-master.zip][github_samples_zip] というダウンロード リンクを直接クリックすると、すべてのサンプルをダウンロードできます。 ZIP ファイルの内容を抽出すると、次のフォルダーでソリューションを見つけられます。

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### <a name="azure-batch-explorer-optional"></a>(省略可能) Azure Batch Explorer
[Azure Batch Explorer][github_batchexplorer] は、GitHub の [azure-batch-samples][github_samples] リポジトリにある無料ユーティリティです。 このチュートリアルには必要ありませんが、Batch ソリューションの開発とデバッグに有効活用してください。

## <a name="dotnettutorial-sample-project-overview"></a>DotNetTutorial サンプル プロジェクトの概要
*DotNetTutorial* コード サンプルは、**DotNetTutorial** と **TaskApplication** という&2; つのプロジェクトから構成される Visual Studio ソリューションです。

* **DotNetTutorial** は、Batch サービスおよび Storage サービスとやり取りして、コンピューティング ノード (仮想マシン) で並列ワークロードを実行するクライアント アプリケーションです。 DotNetTutorial はローカル ワークステーションで実行されます。
* **TaskApplication** は、Azure のコンピューティング ノードで実際の作業を行うために実行されるプログラムです。 このサンプルの `TaskApplication.exe` では、Azure Storage からダウンロードされたファイル (入力ファイル) のテキストを解析します。 次に、入力ファイル内で出現回数が多い上位&3; つの単語の一覧を含むテキスト ファイル (出力ファイル) を生成します。 出力ファイルを作成した後で、TaskApplication はそのファイルを Azure Storage にアップロードします。 これで、ファイルをクライアント アプリケーションがダウンロードできるようになります。 TaskApplication は、Batch サービスの複数のコンピューティング ノードで並列で実行されます。

次の図は、クライアント アプリケーション *DotNetTutorial* と、タスクによって実行されるアプリケーション *TaskApplication* から実行される主な処理を示します。 この基本ワークフローは、Batch で作成する多くのコンピューティング ソリューションの中でも一般的なものです。 Batch サービスで使用できるすべての機能を網羅しているわけではありませんが、同様のワークフローは、ほぼすべての Batch シナリオに含まれます。

![Batch のワークフロー例][8]<br/>

[**手順 1.**](#step-1-create-storage-containers) Azure Blob Storage で**コンテナー**を作成します。<br/>
[**手順 2.**](#step-2-upload-task-application-and-data-files) タスク アプリケーション ファイルと入力ファイルをコンテナーにアップロードします。<br/>
[**手順 3.**](#step-3-create-batch-pool) Batch **プール**を作成します。<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** プールに参加するときに、プールの **StartTask** でタスク バイナリ ファイル (TaskApplication) をノードにダウンロードします。<br/>
[**手順 4.**](#step-4-create-batch-job) Batch **ジョブ**を作成します。<br/>
[**手順 5.**](#step-5-add-tasks-to-job) ジョブに **タスク** を追加します。<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** タスクはノード上で実行されるようにスケジュールされています。<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** 各タスクは Azure Storage から入力データをダウンロードし、実行を開始します。<br/>
[**手順 6.**](#step-6-monitor-tasks) タスクを監視するします。<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** タスクの完了後に、出力データを Azure Storage にアップロードします。<br/>
[**手順 7.**](#step-7-download-task-output) Storage からタスク出力をダウンロードします。

既に述べたように、このとおりの実行手順ではない Batch ソリューションもあり、他の手順が含まれる場合もありますが、 *DotNetTutorial* サンプル アプリケーションでは、Batch ソリューションの一般的なプロセスを実行します。

## <a name="build-the-dotnettutorial-sample-project"></a>*DotNetTutorial* サンプル プロジェクトのビルド
サンプルを適切に実行するには、*DotNetTutorial* プロジェクトの `Program.cs` ファイルに Batch と Storage 両方のアカウント資格情報を指定する必要があります。 Visual Studio でソリューションを開いていない場合は、 `DotNetTutorial.sln` ソリューション ファイルをダブルクリックして Visual Studio で開きます。 **[ファイル] > [開く] > [プロジェクト/ソリューション]** メニューの順にクリックして、Visual Studio 内から開くこともできます。

*DotNetTutorial* プロジェクトで `Program.cs` を開きます。 次に、指定どおりファイルの先頭近くに資格情報を追加します。

```csharp
// Update the Batch and Storage account credential strings below with the values
// unique to your accounts. These are used when constructing connection strings
// for the Batch and Storage client objects.

// Batch account credentials
private const string BatchAccountName = "";
private const string BatchAccountKey  = "";
private const string BatchAccountUrl  = "";

// Storage account credentials
private const string StorageAccountName = "";
private const string StorageAccountKey  = "";
```

> [!IMPORTANT]
> 既に述べたとおり、現時点では、Azure Storage では **汎用** ストレージ アカウントの資格情報を指定する必要があります。 Batch アプリケーションでは、 **汎用** ストレージ アカウント内で Blob Storage が使用されます。 アカウントの種類として *[Blob Storage]* を選択して作成されたストレージ アカウントの資格情報を指定しないでください。
>
>

[Azure Portal][azure_portal] の各サービスのアカウント ブレード内に Batch および Storage アカウント資格情報があります。

![ポータルの Batch の資格情報][9]
![ポータルの Storage の資格情報][10]<br/>

資格情報を使用してプロジェクトを更新したら、ソリューション エクスプローラーでソリューションを右クリックし、 **[ソリューションのビルド]**をクリックします。 メッセージに従って NuGet パッケージの復元を確認します。

> [!TIP]
> NuGet パッケージが自動的に復元されない場合、またはパッケージの復元失敗に関するエラーが表示される場合は、[NuGet パッケージ マネージャー][nuget_packagemgr]がインストールされていることを確認します。 不足しているパッケージのダウンロードを有効にします。 パッケージのダウンロードを有効にするには、[ビルド中のパッケージ復元の有効化][nuget_restore]に関するページを参照してください。
>
>

以下のセクションでは、サンプル アプリケーションを複数の手順に分け、Batch サービスでワークロードを処理するために実行する手順ごとに詳しく説明します。 この記事ではサンプルのすべてのコード行については説明していないので、Visual Studio で未完成のソリューションを参照しながら、この記事の残りの部分に目を通すことをお勧めします。

*DotNetTutorial* プロジェクトの `Program.cs` ファイルにある `MainAsync` メソッドの一番上に移動し、手順 1. から始めてください。 以下の各手順では、ほぼ `MainAsync` のメソッドの呼び出し順に説明します。

## <a name="step-1-create-storage-containers"></a>手順 1: ストレージ コンテナーを作成する
![Azure Storage でコンテナーを作成する][1]
<br/>

Batch には、Azure Storage とやり取りするための組み込みのサポートが含まれています。 Storage アカウントのコンテナーは、Batch アカウントで実行するタスクで必要なファイルを提供します。 また、タスクによって生成される出力データを格納する場所も提供します。 *DotNetTutorial* クライアント アプリケーションで最初に実行する手順は、 [Azure Blob Storage](../storage/storage-introduction.md)で&3; つのコンテナーを作成することです。

* **application**: このコンテナーには、タスクによって実行されるアプリケーションと、DLL などの依存するファイルが格納されます。
* **input**: タスクで、 *input* コンテナーから処理対象のデータ ファイルをダウンロードします。
* **output**: タスクで入力ファイルの処理を完了した後に、結果を *output* コンテナーにアップロードします。

ストレージ アカウントを操作してコンテナーを作成するには、[.NET 用 Azure Storage クライアント ライブラリ][net_api_storage]を使用します。 [CloudStorageAccount][net_cloudstorageaccount] を使用してアカウントへの参照を作成し、それを基にして [CloudBlobClient][net_cloudblobclient] を作成します。

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format(
    "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
    StorageAccountName,
    StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to
// blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

ここでは、アプリケーション全体で `blobClient` 参照を使用し、いくつかのメソッドにパラメーターとして渡します。 この例は、上記のすぐ下のコード ブロックにあります。ここで `CreateContainerIfNotExistAsync` を呼び出して実際にコンテナーを作成します。

```csharp
// Use the blob client to create the containers in Azure Storage if they don't
// yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```csharp
private static async Task CreateContainerIfNotExistAsync(
    CloudBlobClient blobClient,
    string containerName)
{
        CloudBlobContainer container =
            blobClient.GetContainerReference(containerName);

        if (await container.CreateIfNotExistsAsync())
        {
                Console.WriteLine("Container [{0}] created.", containerName);
        }
        else
        {
                Console.WriteLine("Container [{0}] exists, skipping creation.",
                    containerName);
        }
}
```

コンテナーを作成すると、アプリケーションから、タスクで使用するファイルをアップロードできるようになります。

> [!TIP]
> [.NET から Blob Storage を使用する方法](../storage/storage-dotnet-how-to-use-blobs.md)に関するページに、Azure Storage コンテナーと BLOB の操作がわかりやすく示されています。 Batch を扱う場合は、この記事を早い段階で読むことをお勧めします。
>
>

## <a name="step-2-upload-task-application-and-data-files"></a>手順 2: タスク アプリケーションとデータ ファイルをアップロードする
![タスク アプリケーションと入力 (データ) ファイルをコンテナーにアップロードする][2]
<br/>

ファイルのアップロード操作で、*DotNetTutorial* にローカル コンピューターの **application** と **input** のファイル パスのコレクションをまず定義します。 次に、それらのファイルを前の手順で作成したコンテナーにアップロードします。

```csharp
// Paths to the executable and its dependencies that will be executed by the tasks
List<string> applicationFilePaths = new List<string>
{
    // The DotNetTutorial project includes a project reference to TaskApplication,
    // allowing us to determine the path of the task application binary dynamically
    typeof(TaskApplication.Program).Assembly.Location,
    "Microsoft.WindowsAzure.Storage.dll"
};

// The collection of data files that are to be processed by the tasks
List<string> inputFilePaths = new List<string>
{
    @"..\..\taskdata1.txt",
    @"..\..\taskdata2.txt",
    @"..\..\taskdata3.txt"
};

// Upload the application and its dependencies to Azure Storage. This is the
// application that will process the data files, and will be executed by each
// of the tasks on the compute nodes.
List<ResourceFile> applicationFiles = await UploadFilesToContainerAsync(
    blobClient,
    appContainerName,
    applicationFilePaths);

// Upload the data files. This is the data that will be processed by each of
// the tasks that are executed on the compute nodes within the pool.
List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

アップロード プロセスで使用される `Program.cs` には&2; つのメソッドがあります。

* `UploadFilesToContainerAsync`: このメソッドは、[ResourceFile][net_resourcefile] オブジェクト (後述) のコレクションを返し、内部的に `UploadFileToContainerAsync` を呼び出して、*filePaths* パラメーターで渡される各ファイルをアップロードします。
* `UploadFileToContainerAsync`: 実際にファイルのアップロードを実行し、[ResourceFile][net_resourcefile] オブジェクトを作成するメソッドです。 ファイルをアップロードすると、ファイルの Shared Access Signature (SAS) が取得され、それを表す ResourceFile オブジェクトが返されます。 ここでは、Shared Access Signature についても説明します。

```csharp
private static async Task<ResourceFile> UploadFileToContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string filePath)
{
        Console.WriteLine(
            "Uploading file {0} to container [{1}]...", filePath, containerName);

        string blobName = Path.GetFileName(filePath);

        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlockBlob blobData = container.GetBlockBlobReference(blobName);
        await blobData.UploadFromFileAsync(filePath);

        // Set the expiry time and permissions for the blob shared access signature.
        // In this case, no start time is specified, so the shared access signature
        // becomes valid immediately
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
        {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
                Permissions = SharedAccessBlobPermissions.Read
        };

        // Construct the SAS URL for blob
        string sasBlobToken = blobData.GetSharedAccessSignature(sasConstraints);
        string blobSasUri = String.Format("{0}{1}", blobData.Uri, sasBlobToken);

        return new ResourceFile(blobSasUri, blobName);
}
```

### <a name="resourcefiles"></a>ResourceFiles
[ResourceFile][net_resourcefile] は、Batch のタスクに対して、タスクの実行前にコンピューティング ノードにダウンロードする Azure Storage のファイルの URL を提供します。 [ResourceFile.BlobSource][net_resourcefile_blobsource] プロパティには、Azure Storage にあるファイルの完全な URL を指定します。 URL には、ファイルに対する安全なアクセスを提供する Shared Access Signature (SAS) も含めることができます。 Batch .NET 内のほとんどのタスクの種類には、次のように *ResourceFiles* プロパティが含まれます。

* [CloudTask][net_task]
* [StartTask][net_pool_starttask]
* [JobPreparationTask][net_jobpreptask]
* [JobReleaseTask][net_jobreltask]

DotNetTutorial サンプル アプリケーションには JobPreparationTask または JobReleaseTask というタスクの種類は使用しませんが、詳細については、「 [Azure Batch コンピューティング ノードでのジョブ準備タスクとジョブ完了タスクの実行](batch-job-prep-release.md)」を参照してください。

### <a name="shared-access-signature-sas"></a>Shared Access Signature (SAS)
Shared Access Signature (SAS) は、URL の一部に含めると、Azure Storage のコンテナーと BLOB への安全なアクセスを提供することができる文字列です。 DotNetTutorial アプリケーションは、BLOB とコンテナー両方の Shared Access Signature URL を使用し、Storage サービスからこれらの Shared Access Signature 文字列を取得する方法を示します。

* **BLOB Shared Access Signature**: DotNetTutorial 内のプールの StartTask は、Storage からアプリケーション バイナリと入力データ ファイルをダウンロードするときに BLOB の Shared Access Signature を使用します (以下の手順 3. を参照してください)。 DotNetTutorial の `Program.cs` 内の `UploadFileToContainerAsync` メソッドには、各 BLOB の Shared Access Signature を取得するコードが含まれます。 この処理を実行するために、[CloudBlob.GetSharedAccessSignature][net_sas_blob] を呼び出します。
* **コンテナー Shared Access Signature**: 各タスクでコンピューティング ノードでの処理が完了すると、出力ファイルは Azure Storage の *output* コンテナーにアップロードされます。 この処理を実行するために、TaskApplication では、ファイルをアップロードするときに、パスの一部としてコンテナーへの書き込みアクセス権を提供するコンテナー Shared Access Signature を使用します。 コンテナー Shared Access Signature の取得は、BLOB Shared Access Signature を取得するときと同様の方法で行います。 DotNetTutorial では、`GetContainerSasUrl` ヘルパー メソッドがこれを行う [CloudBlobContainer.GetSharedAccessSignature][net_sas_container] を呼び出します。 TaskApplication でコンテナー Shared Access Signature を使用する方法の詳細については、「手順 6: タスクを監視する」を参照してください。

> [!TIP]
> Storage アカウントのデータへの安全なアクセスを提供する方法については、Shared Access Signature に関する 2 つの記事、[第 1 部: SAS モデルの概要](../storage/storage-dotnet-shared-access-signature-part-1.md)と[第 2 部: BLOB ストレージでの Shared Access Signature (SAS) の作成と使用](../storage/storage-dotnet-shared-access-signature-part-2.md)に関するページをご覧ください。
>
>

## <a name="step-3-create-batch-pool"></a>手順 3: Batch プールを作成する
![Batch プールを作成する][3]
<br/>

Batch **プール**は複数のコンピューティング ノード (仮想マシン) をまとめたものです。Batch は、このプールでジョブのタスクを実行することになります。

アプリケーションとデータ ファイルを Storage アカウントにアップロードすると、 *DotNetTutorial* は Batch .NET ライブラリを使用して Batch サービスとのやり取りを開始します。 その処理のために、まず [BatchClient][net_batchclient] が作成されます。

```csharp
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(
    BatchAccountUrl,
    BatchAccountName,
    BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(cred))
{
    ...
```

次に、 `CreatePoolIfNotExistsAsync`を呼び出してコンピューティング ノードのプールが Batch アカウントに作成されます。 `CreatePoolIfNotExistsAsync` は、[BatchClient.PoolOperations.CreatePool][net_pool_create] メソッドを使用して、Batch サービス内にプールを作成します。

```csharp
private static async Task CreatePoolIfNotExistAsync(BatchClient batchClient, string poolId, IList<ResourceFile> resourceFiles)
{
    CloudPool pool = null;
    try
    {
        Console.WriteLine("Creating pool [{0}]...", poolId);

        // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(), no pool is actually created in the
        // Batch service. This CloudPool instance is therefore considered "unbound," and we can modify its properties.
        pool = batchClient.PoolOperations.CreatePool(
            poolId: poolId,
            targetDedicated: 3,                                                         // 3 compute nodes
            virtualMachineSize: "small",                                                // single-core, 1.75 GB memory, 225 GB disk
            cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));   // Windows Server 2012 R2

        // Create and assign the StartTask that will be executed when compute nodes join the pool.
        // In this case, we copy the StartTask's resource files (that will be automatically downloaded
        // to the node by the StartTask) into the shared directory that all tasks will have access to.
        pool.StartTask = new StartTask
        {
            // Specify a command line for the StartTask that copies the task application files to the
            // node's shared directory. Every compute node in a Batch pool is configured with a number
            // of pre-defined environment variables that can be referenced by commands or applications
            // run by tasks.

            // Since a successful execution of robocopy can return a non-zero exit code (e.g. 1 when one or
            // more files were successfully copied) we need to manually exit with a 0 for Batch to recognize
            // StartTask execution success.
            CommandLine = "cmd /c (robocopy %AZ_BATCH_TASK_WORKING_DIR% %AZ_BATCH_NODE_SHARED_DIR%) ^& IF %ERRORLEVEL% LEQ 1 exit 0",
            ResourceFiles = resourceFiles,
            WaitForSuccess = true
        };

        await pool.CommitAsync();
    }
    catch (BatchException be)
    {
        // Swallow the specific error code PoolExists since that is expected if the pool already exists
        if (be.RequestInformation?.BatchError != null && be.RequestInformation.BatchError.Code == BatchErrorCodeStrings.PoolExists)
        {
            Console.WriteLine("The pool {0} already existed when we tried to create it", poolId);
        }
        else
        {
            throw; // Any other exception is unexpected
        }
    }
}
```

[CreatePool][net_pool_create] を使用してプールを作成する場合は、コンピューティング ノードの数、[ノードのサイズ](../cloud-services/cloud-services-sizes-specs.md)、ノードのオペレーティング システムなど、いくつかのパラメーターを指定します。 *DotNetTutorial* では、[CloudServiceConfiguration][net_cloudserviceconfiguration] を使用して、[Cloud Services](../cloud-services/cloud-services-guestos-update-matrix.md) から Windows Server 2012 R2 を指定します。 ただし、代わりに [VirtualMachineConfiguration][net_virtualmachineconfiguration] を指定することにより、Marketplace イメージから作成されたノードのプールを作成できます。これには、Windows と Linux 両方のイメージが含まれます。詳細については、「[Azure Batch プールの Linux コンピューティング ノードのプロビジョニング](batch-linux-nodes.md)」を参照してください。

> [!IMPORTANT]
> Batch のコンピューティング リソースには料金がかかります。 コストを最小限に抑えるために、サンプルを実行する前に `targetDedicated` を 1 に引き下げることもできます。
>
>

このような物理ノードのプロパティだけでなく、プールの [StartTask][net_pool_starttask] を指定することもできます。 各ノードがプールに参加するときと、ノードの再起動のたびに、各ノードで StartTask が実行されます。 StartTask は、タスクの実行前に、コンピューティング ノードにアプリケーションをインストールする場合に特に便利です。 たとえば、Python スクリプトを使用してタスクでデータを処理する場合、StartTask を使用してコンピューティング ノードに Python をインストールすることができます。

このサンプル アプリケーションでは、StartTask が、StartTask 作業ディレクトリの Storage からダウンロードするファイル ([StartTask][net_starttask].[ResourceFiles][net_starttask_resourcefiles] プロパティを使用して指定します) を、ノードで実行される "*すべて*" のタスクからアクセスできる共有ディレクトリにコピーします。 基本的に、これはノードがプールに参加するときに各ノードの共有ディレクトリに `TaskApplication.exe` およびその依存関係をコピーし、ノードで実行するすべてのタスクがアクセスできるようにします。

> [!TIP]
> Azure Batch の **アプリケーション パッケージ** 機能では、プール内のコンピューティング ノードにアプリケーションをデプロイするもう&1; つの方法が提供されています。 詳細については、「 [Azure Batch アプリケーション パッケージを使用したアプリケーションのデプロイ](batch-application-packages.md) 」を参照してください。
>
>

また、上記のコード スニペットでは、StartTask の *CommandLine* プロパティで `%AZ_BATCH_TASK_WORKING_DIR%` と `%AZ_BATCH_NODE_SHARED_DIR%` という&2; つの環境変数を使用している点についても注目してください。 Batch プールの各コンピューティング ノードには、Batch に固有の環境変数がいくつか自動的に構成されます。 また、タスクによって実行されるプロセスは、これらの環境変数に対するアクセス権を持ちます。

> [!TIP]
> Batch プールのコンピューティング ノードで使用できる環境変数と、タスクの作業ディレクトリの詳細については、「[開発者向け Batch 機能の概要](batch-api-basics.md)」の「[タスクの環境設定](batch-api-basics.md#environment-settings-for-tasks)」と「[ファイルとディレクトリ](batch-api-basics.md#files-and-directories)」のセクションを参照してください。
>
>

## <a name="step-4-create-batch-job"></a>手順 4: Batch ジョブを作成する
![Batch ジョブを作成する][4]<br/>

基本的に、Batch **ジョブ** はタスクのコレクションであり、コンピューティング ノードのプールに関連付けられます。 ジョブに含まれる一連のタスクは、関連付けられているプールのコンピューティング ノードで実行されます。

ジョブを使うと、関連するワークロードのタスクを整理し、追跡するだけでなく、ジョブ (さらにはタスク) の最長実行時間をはじめとする一定の制限や、Batch アカウントの他のジョブと関連するジョブの優先度を設けることができます。 ただし、この例では、ジョブは手順 3 で作成したプールにのみ関連付けられています。 他のプロパティは構成されていません。

すべての Batch ジョブは、特定のプールに関連付けられています。 この関連付けはジョブのタスクがどのノードで実行されるかを示します。 これを指定するには、以下のコード スニペットに示すとおり、[CloudJob.PoolInformation][net_job_poolinfo] プロパティを使用します。

```csharp
private static async Task CreateJobAsync(
    BatchClient batchClient,
    string jobId,
    string poolId)
{
    Console.WriteLine("Creating job [{0}]...", jobId);

    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = jobId;
    job.PoolInformation = new PoolInformation { PoolId = poolId };

    await job.CommitAsync();
}
```

ジョブを作成したので、次は作業を実行するタスクを実行します。

## <a name="step-5-add-tasks-to-job"></a>手順 5: ジョブにタスクを追加する
![ジョブにタスクを追加する][5]<br/>
"*(1) タスクをジョブに追加します。(2) ノードで実行されるようにタスクをスケジュールします。(3) タスクで処理対象のデータ ファイルをダウンロードします*"

Batch の **タスク** は、コンピューティング ノードで実行される独立した作業単位です。 タスクはコマンド ラインを持ち、スクリプト (またはそのコマンド ラインに指定された実行可能ファイル) を実行します。

実際に作業を実行するには、タスクをジョブに追加する必要があります。 コマンド ラインが自動的に実行される前に、タスクによってノードにダウンロードされる [ResourceFiles][net_task_resourcefiles] (プールの StartTask と同様) とコマンド ライン プロパティを使用して、各 [CloudTask][net_task] を構成します。 *DotNetTutorial* サンプル プロジェクトでは、各タスクで処理されるファイルは&1; つだけです。 したがって、その ResourceFiles コレクションには、1 つの要素が含まれています。

```csharp
private static async Task<List<CloudTask>> AddTasksAsync(
    BatchClient batchClient,
    string jobId,
    List<ResourceFile> inputFiles,
    string outputContainerSasUrl)
{
    Console.WriteLine("Adding {0} tasks to job [{1}]...", inputFiles.Count, jobId);

    // Create a collection to hold the tasks that we'll be adding to the job
    List<CloudTask> tasks = new List<CloudTask>();

    // Create each of the tasks. Because we copied the task application to the
    // node's shared directory with the pool's StartTask, we can access it via
    // the shared directory on the node that the task runs on.
    foreach (ResourceFile inputFile in inputFiles)
    {
        string taskId = "topNtask" + inputFiles.IndexOf(inputFile);
        string taskCommandLine = String.Format(
            "cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 \"{1}\"",
            inputFile.FilePath,
            outputContainerSasUrl);

        CloudTask task = new CloudTask(taskId, taskCommandLine);
        task.ResourceFiles = new List<ResourceFile> { inputFile };
        tasks.Add(task);
    }

    // Add the tasks as a collection, as opposed to issuing a separate AddTask call
    // for each. Bulk task submission helps to ensure efficient underlying API calls
    // to the Batch service.
    await batchClient.JobOperations.AddTaskAsync(jobId, tasks);

    return tasks;
}
```

> [!IMPORTANT]
> `%AZ_BATCH_NODE_SHARED_DIR%` などの環境変数にアクセスする場合や、ノードの `PATH` にないアプリケーションを実行する場合は、タスク コマンド ラインの先頭に `cmd /c` を付ける必要があります。 これにより、明示的にコマンド インタープリターが実行され、そのコマンド インタープリターにコマンドの実行後に終了するよう指示が与えられます。 タスクでノードの `PATH` (*robocopy.exe* や *powershell.exe* など) 内にあるアプリケーションを実行し、環境変数を使用しない場合は、この要件は不要です。
>
>

上記のコード スニペットの `foreach` ループ内には、3 つのコマンド ライン引数が *TaskApplication.exe*に渡されるようにタスクのコマンド ラインが構成されています。

1. **1 つ目の引数** は処理するファイルのパスです。 これは、ノードに存在するファイルのローカル パスです。 上記で `UploadFileToContainerAsync` の ResourceFile オブジェクトを初めて作成したときに、ファイル名がこのプロパティに (ResourceFile コンストラクターのパラメーターとして) 使用されています。 そのため、ファイルが *TaskApplication.exe*と同じディレクトリにあることがわかります。
2. **2 つ目の引数** は、上位 *N* 個の単語を出力ファイルに書き出すように指定するものです。 このサンプルでは、上位&3; 個の単語が出力ファイルに書き出されるようにハードコーディングされています。
3. **3 つ目の引数**は、Azure Storage の **output** コンテナーに対する書き込みアクセス権を提供する Shared Access Signature (SAS) です。 *TaskApplication.exe* は、出力ファイルを Azure Storage にアップロードするときにこの Shared Access Signature URL を使用します。 そのコードは TaskApplication プロジェクトの `Program.cs` ファイル内の `UploadFileToContainer` メソッドにあります。

```csharp
// NOTE: From project TaskApplication Program.cs

private static void UploadFileToContainer(string filePath, string containerSas)
{
        string blobName = Path.GetFileName(filePath);

        // Obtain a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(containerSas));

        // Upload the file (as a new blob) to the container
        try
        {
                CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
                blob.UploadFromFile(filePath);

                Console.WriteLine("Write operation succeeded for SAS URL " + containerSas);
                Console.WriteLine();
        }
        catch (StorageException e)
        {

                Console.WriteLine("Write operation failed for SAS URL " + containerSas);
                Console.WriteLine("Additional error information: " + e.Message);
                Console.WriteLine();

                // Indicate that a failure has occurred so that when the Batch service
                // sets the CloudTask.ExecutionInformation.ExitCode for the task that
                // executed this application, it properly indicates that there was a
                // problem with the task.
                Environment.ExitCode = -1;
        }
}
```

## <a name="step-6-monitor-tasks"></a>手順 6: タスクを監視する
![タスクを監視する][6]<br/>
"*クライアント アプリケーションで (1) タスクの完了と成功の状態を監視し、(2) タスクから結果データを Azure Storage にアップロードします*"

タスクをジョブに追加すると、そのジョブに関連付けられたプール内のコンピューティング ノードに実行待ちとして自動的にキューに追加され、スケジュールされます。 指定した設定に基づき、Batch は、すべてのタスクのキュー、スケジュール、再試行など、タスク管理作業を処理します。

タスクの実行を監視する方法は多数ありますが、 DotNetTutorial では、完了と、タスクの失敗または成功の状態のみをレポートする簡単な例を説明します。 DotNetTutorial の `Program.cs` 内の `MonitorTasks` メソッド内には、Batch .NET の重要な&3; つの概念があります。 その概念を出現順に以下に示します。

1. **ODATADetailLevel**: Batch アプリケーションのパフォーマンスを確保するうえで、リスト操作 (ジョブのタスク リストの取得など) で [ODATADetailLevel][net_odatadetaillevel] を指定することが重要です。 Batch アプリケーション内でなんらかの状態監視を実行する予定がある場合は、「 [効率的な Azure Batch サービスのクエリ](batch-efficient-list-queries.md) 」を参考にしてください。
2. **TaskStateMonitor**: [TaskStateMonitor][net_taskstatemonitor] は、Batch .NET アプリケーションにタスクの状態を監視するためのヘルパー ユーティリティを提供します。 `MonitorTasks` で、*DotNetTutorial* はすべてのタスクが制限時間内に [TaskState.Completed][net_taskstate] に達するまで待ちます。 その後でジョブを終了します。
3. **TerminateJobAsync**: [JobOperations.TerminateJobAsync][net_joboperations_terminatejob] を使用してジョブを終了すると (または JobOperations.TerminateJob をブロックすると)、ジョブに完了とマークが付けられます。 この処理は、Batch ソリューションで [JobReleaseTask][net_jobreltask] を使用する場合に必要です。 これは、 [ジョブの準備タスクと完了タスク](batch-job-prep-release.md)に関するページで説明されている特殊なタスクです。

*DotNetTutorial* の `Program.cs` 内の `MonitorTasks` メソッドは次のとおりです。

```csharp
private static async Task<bool> MonitorTasks(
    BatchClient batchClient,
    string jobId,
    TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use
    // a detail level to  specify that only the "id" property of each task should be
    // populated. Using a detail level for all list operations helps to lower
    // response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks =
        await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...",
        timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we
    // will wait for all tasks to reach the Completed state.
    TaskStateMonitor taskStateMonitor
        = batchClient.Utilities.CreateTaskStateMonitor();

    try
    {
        await taskStateMonitor.WhenAll(tasks, TaskState.Completed, timeout);
    }
    catch (TimeoutException)
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);
        Console.WriteLine(failureMessage);
        return false;
    }

    await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

    // All tasks have reached the "Completed" state, however, this does not
    // guarantee all tasks completed successfully. Here we further check each task's
    // ExecutionInfo property to ensure that it did not encounter a scheduling error
    // or return a non-zero exit code.

    // Update the detail level to populate only the task id and executionInfo
    // properties. We refresh the tasks below, and need only this information for
    // each task.
    detail.SelectClause = "id, executionInfo";

    foreach (CloudTask task in tasks)
    {
        // Populate the task's properties with the latest info from the
        // Batch service
        await task.RefreshAsync(detail);

        if (task.ExecutionInformation.SchedulingError != null)
        {
            // A scheduling error indicates a problem starting the task on the node.
            // It is important to note that the task's state can be "Completed," yet
            // still have encountered a scheduling error.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] encountered a scheduling error: {1}",
                task.Id,
                task.ExecutionInformation.SchedulingError.Message);
        }
        else if (task.ExecutionInformation.ExitCode != 0)
        {
            // A non-zero exit code may indicate that the application executed by
            // the task encountered an error during execution. As not every
            // application returns non-zero on failure by default (e.g. robocopy),
            // your implementation of error checking may differ from this example.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] returned a non-zero exit code - this may indicate task execution or completion failure.", task.Id);
        }
    }

    if (allTasksSuccessful)
    {
        Console.WriteLine("Success! All tasks completed successfully within the specified timeout period.");
    }

    return allTasksSuccessful;
}
```

## <a name="step-7-download-task-output"></a>手順 7: タスクの出力をダウンロードする
![Storage からタスク出力をダウンロードする][7]<br/>

これでジョブが完了したので、タスクの出力を Azure Storage からダウンロードできます。 ダウンロードするには、*DotNetTutorial* の `Program.cs` 内の `DownloadBlobsFromContainerAsync` を呼び出します。

```csharp
private static async Task DownloadBlobsFromContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string directoryPath)
{
        Console.WriteLine("Downloading all files from container [{0}]...", containerName);

        // Retrieve a reference to a previously created container
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);

        // Get a flat listing of all the block blobs in the specified container
        foreach (IListBlobItem item in container.ListBlobs(
                    prefix: null,
                    useFlatBlobListing: true))
        {
                // Retrieve reference to the current blob
                CloudBlob blob = (CloudBlob)item;

                // Save blob contents to a file in the specified folder
                string localOutputFile = Path.Combine(directoryPath, blob.Name);
                await blob.DownloadToFileAsync(localOutputFile, FileMode.Create);
        }

        Console.WriteLine("All files downloaded to {0}", directoryPath);
}
```

> [!NOTE]
> *DotNetTutorial* アプリケーションでの `DownloadBlobsFromContainerAsync` の呼び出しでは、ファイルを `%TEMP%` フォルダーにダウンロードするように指定します。 この出力場所は自由に変更できます。
>
>

## <a name="step-8-delete-containers"></a>手順 8: コンテナーを削除する
Azure Storage にあるデータは課金対象なので、Batch ジョブに使用しなくなった BLOB がある場合は削除することをお勧めします。 DotNetTutorial の `Program.cs` では、ヘルパー メソッド `DeleteContainerAsync` の&3; 回の呼び出しで削除を実行します。

```csharp
// Clean up Storage resources
await DeleteContainerAsync(blobClient, appContainerName);
await DeleteContainerAsync(blobClient, inputContainerName);
await DeleteContainerAsync(blobClient, outputContainerName);
```

このメソッド自体は、コンテナーの参照を取得してから [CloudBlobContainer.DeleteIfExistsAsync][net_container_delete] を呼び出すだけです。

```csharp
private static async Task DeleteContainerAsync(
    CloudBlobClient blobClient,
    string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    if (await container.DeleteIfExistsAsync())
    {
        Console.WriteLine("Container [{0}] deleted.", containerName);
    }
    else
    {
        Console.WriteLine("Container [{0}] does not exist, skipping deletion.",
            containerName);
    }
}
```

## <a name="step-9-delete-the-job-and-the-pool"></a>手順 9: ジョブとプールを削除する
最後の手順では、DotNetTutorial アプリケーションで作成されたジョブとプールを削除するよう求められます。 ジョブとタスク自体は課金対象ではありませんが、コンピューティング ノードは "*課金対象*" です。 そのため、必要な場合にのみノードを割り当てることをお勧めします。 使用されていないプールは、メンテナンス プロセスの一環として削除できます。

BatchClient の [JobOperations][net_joboperations] と [PoolOperations][net_pooloperations] には、いずれも対応する削除メソッドがあります。このメソッドは、ユーザーが削除を確定すると呼び出されます。

```csharp
// Clean up the resources we've created in the Batch account if the user so chooses
Console.WriteLine();
Console.WriteLine("Delete job? [yes] no");
string response = Console.ReadLine().ToLower();
if (response != "n" && response != "no")
{
    await batchClient.JobOperations.DeleteJobAsync(JobId);
}

Console.WriteLine("Delete pool? [yes] no");
response = Console.ReadLine();
if (response != "n" && response != "no")
{
    await batchClient.PoolOperations.DeletePoolAsync(PoolId);
}
```

> [!IMPORTANT]
> コンピューティング リソースは課金対象であるため、使用していないプールを削除することでコストを最小限に抑えられることを覚えておいてください。 また、プールを削除すると、そのプール内のすべてのコンピューティング ノードが削除され、プールの削除後はノード上のデータを復元できなくなる点にも注意してください。
>
>

## <a name="run-the-dotnettutorial-sample"></a>*DotNetTutorial* サンプルを実行する
サンプル アプリケーションを実行すると、コンソールの出力は次のようになります。 実行中、プールのコンピューティング ノードを開始する際に、 `Awaiting task completion, timeout in 00:30:00...` で一時停止が発生します。 実行中と実行後のプール、コンピューティング ノード、ジョブ、タスクを監視するには、[Azure Portal][azure_portal] を使用します。 アプリケーションで作成された Storage リソース (コンテナーと BLOB) を表示するには、[Azure Portal][azure_portal] または [Azure ストレージ エクスプローラー][storage_explorers]を使用します。

既定の構成でアプリケーションを実行する場合、通常の実行時間は **約 5 分間** です。

```
Sample start: 1/8/2016 09:42:58 AM

Container [application] created.
Container [input] created.
Container [output] created.
Uploading file C:\repos\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial\bin\Debug\TaskApplication.exe to container [application]...
Uploading file Microsoft.WindowsAzure.Storage.dll to container [application]...
Uploading file ..\..\taskdata1.txt to container [input]...
Uploading file ..\..\taskdata2.txt to container [input]...
Uploading file ..\..\taskdata3.txt to container [input]...
Creating pool [DotNetTutorialPool]...
Creating job [DotNetTutorialJob]...
Adding 3 tasks to job [DotNetTutorialJob]...
Awaiting task completion, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Downloading all files from container [output]...
All files downloaded to C:\Users\USERNAME\AppData\Local\Temp
Container [application] deleted.
Container [input] deleted.
Container [output] deleted.

Sample end: 1/8/2016 09:47:47 AM
Elapsed time: 00:04:48.5358142

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>次のステップ
*DotNetTutorial* と *TaskApplication* を自由に変更して、さまざまなコンピューティング シナリオを試してみてください。 たとえば、[Thread.Sleep][net_thread_sleep] を使用するなどして *TaskApplication* に実行遅延を追加して、実行時間が長いタスクをシミュレートし、ポータルで監視することができます。 タスクを追加したり、コンピューティング ノード数を調整したりすることもできます。 また、既存のプールの使用を確認および許可し、実行時間を短縮するロジックを追加できます ("*ヒント*": [azure-batch-samples][github_samples] の [Microsoft.Azure.Batch.Samples.Common][github_samples_common] プロジェクトの `ArticleHelpers.cs` を確認してください)。

Batch ソリューションの基本的なワークフローを理解したところで、次は Batch サービスのその他の機能を掘り下げてみましょう。

* このサービスを初めて扱う場合は、 [Azure Batch 機能の概要](batch-api-basics.md) に関する記事を確認することをお勧めします。
* [Batch ラーニング パス][batch_learning_path]の「**開発の詳細**」にある他の Batch 開発記事をお読みください。
* [TopNWords][github_topnwords] サンプルで、Batch を使用した "上位 N 個の単語" ワークロードの処理について、さまざまな実装を確認してください。

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[github_dotnettutorial]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/DotNetTutorial
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[net_api]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[net_api_storage]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudblobclient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx
[net_cloudblobcontainer]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudserviceconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudserviceconfiguration.aspx
[net_container_delete]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteifexistsasync.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_poolinfo]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.protocol.models.cloudjob.poolinformation.aspx
[net_joboperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.joboperations
[net_joboperations_terminatejob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_jobpreptask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_jobreltask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_odatadetaillevel]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_pooloperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.pooloperations
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_resourcefile_blobsource]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.blobsource.aspx
[net_sas_blob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature.aspx
[net_sas_container]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.getsharedaccesssignature.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.resourcefiles.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_task_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.resourcefiles.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_taskstatemonitor]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskstatemonitor.aspx
[net_thread_sleep]: https://msdn.microsoft.com/library/274eh01d(v=vs.110).aspx
[net_virtualmachineconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.virtualmachineconfiguration.aspx
[nuget_packagemgr]: https://docs.nuget.org/consume/installing-nuget
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build
[storage_explorers]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/vs/

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "Azure Storage でコンテナーを作成する"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "タスク アプリケーションと入力 (データ) ファイルをコンテナーにアップロードする"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "Batch プールを作成する"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "Batch ジョブを作成する"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "ジョブにタスクを追加する"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "タスクを監視する"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "Storage からタスク出力をダウンロードする"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "Batch ソリューション ワークフロー (完全な図)"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "ポータルの Batch の資格情報"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "ポータルの Storage の資格情報"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "Batch ソリューション ワークフロー (最小限の図)"

