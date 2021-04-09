---
title: Media Services .NET SDK を使用するアセットと関連エンティティの管理
description: Media Services SDK for .NET を使用してアセットと関連エンティティを管理する方法について説明します。
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1bd8fd42-7306-463d-bfe5-f642802f1906
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: b859ff49edb495be15cdf76719c3aaaf1ac72a6e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013773"
---
# <a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Media Services .NET SDK を使用するアセットと関連エンティティの管理

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-manage-entities.md)
> * [REST](media-services-rest-manage-entities.md)
> 
> 

> [!NOTE]
> Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](../latest/index.yml) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-v-2-v-3-migration-introduction.md)を参照してください。

このトピックでは、.NET で Azure Media Services エンティティを管理する方法を説明します。

2017 年 4 月 1 日からは、レコードの合計数が最大クォータより小さい場合でも、アカウント内の 90 日前より古いすべてのジョブ レコードが、関連付けられているタスク レコードと共に自動的に削除されます。 たとえば、2017 年 4 月 1 日には、アカウント内の 2016 年 12 月 31 日より古いジョブ レコードはすべて、自動的に削除されます。 ジョブやタスクの情報をアーカイブする必要がある場合は、このトピックで説明するコードを使うことができます。

## <a name="prerequisites"></a>前提条件

「[.NET を使用した Media Services 開発](media-services-dotnet-how-to-use.md)」の説明に従って、開発環境をセットアップし、app.config ファイルに接続情報を指定します。 

## <a name="get-an-asset-reference"></a>アセット参照を取得する
頻繁に実行するタスクでは、Media Services の既存のアセットへの参照を取得します。 次のコード例では、アセット ID に基づいて、サーバー コンテキスト オブジェクトで Assets コレクションからアセット参照を取得する方法を示します。次のコード例では、Linq クエリを使用して、既存の IAsset オブジェクトへの参照を取得します。

```csharp
    static IAsset GetAsset(string assetId)
    {
        // Use a LINQ Select query to get an asset.
        var assetInstance =
            from a in _context.Assets
            where a.Id == assetId
            select a;
        // Reference the asset as an IAsset.
        IAsset asset = assetInstance.FirstOrDefault();

        return asset;
    }
```

## <a name="list-all-assets"></a>すべてのアセットを一覧表示する
ストレージに保持しているアセットの数が増えていくと、アセットの一覧表示が役立ちます。 次のコード例では、サーバー コンテキスト オブジェクトで Assets コレクションを反復処理する方法を示します。 またこのコード例では、各アセットについて、そのプロパティ値の一部をコンソールに出力します。 たとえば、各アセットには多数のメディア ファイルが含まれることがあります。 このコード例では、各アセットに関連付けられているすべてのファイルを書き出します。

```csharp
    static void ListAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IAsset asset in _context.Assets)
        {
            // Display the collection of assets.
            builder.AppendLine("");
            builder.AppendLine("******ASSET******");
            builder.AppendLine("Asset ID: " + asset.Id);
            builder.AppendLine("Name: " + asset.Name);
            builder.AppendLine("==============");
            builder.AppendLine("******ASSET FILES******");

            // Display the files associated with each asset. 
            foreach (IAssetFile fileItem in asset.AssetFiles)
            {
                builder.AppendLine("Name: " + fileItem.Name);
                builder.AppendLine("Size: " + fileItem.ContentFileSize);
                builder.AppendLine("==============");
            }
        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="get-a-job-reference"></a>ジョブ参照を取得する

Media Services のコードで処理タスクを使用するときは、多くの場合、ID に基づいて既存のジョブへの参照を取得する必要があります。次のコード例では、Jobs コレクションから IJob オブジェクトへの参照を取得する方法を示します。

実行時間の長いエンコード ジョブを開始するときは、ジョブ参照を取得し、スレッドのジョブの状態を確認する必要があります。 このような場合は、メソッドがスレッドから返されるときに、更新されたジョブ参照を取得する必要があります。

```csharp
    static IJob GetJob(string jobId)
    {
        // Use a Linq select query to get an updated 
        // reference by Id. 
        var jobInstance =
            from j in _context.Jobs
            where j.Id == jobId
            select j;
        // Return the job reference as an Ijob. 
        IJob job = jobInstance.FirstOrDefault();

        return job;
    }
```

## <a name="list-jobs-and-assets"></a>ジョブとアセットを一覧表示する
重要な関連タスクとして、Media Services のアセットと関連付けられているジョブを一覧表示することがあります。 次のコード例では、各 IJob オブジェクトを一覧表示し、各ジョブについて、ジョブに関するプロパティ、すべての関連するタスク、すべての入力アセット、およびすべての出力アセットを表示する方法を示します。 この例のコードは、他の多数のタスクに役立てることができます。 たとえば、これまでに実行した 1 つまたは複数のエンコード ジョブの出力アセットを一覧表示する場合は、このコードから出力アセットへのアクセス方法が分かります。 出力アセットへの参照が分かっている場合は、コンテンツをダウンロードまたは URL を提供することで、他のユーザーやアプリケーションにコンテンツを配信できます。 

アセットを配信するためのオプションの詳細については、「 [Media Services SDK for .NET によるアセットの配信](media-services-deliver-streaming-content.md)」をご覧ください。

```csharp
    // List all jobs on the server, and for each job, also list 
    // all tasks, all input assets, all output assets.

    static void ListJobsAndAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IJob job in _context.Jobs)
        {
            // Display the collection of jobs on the server.
            builder.AppendLine("");
            builder.AppendLine("******JOB*******");
            builder.AppendLine("Job ID: " + job.Id);
            builder.AppendLine("Name: " + job.Name);
            builder.AppendLine("State: " + job.State);
            builder.AppendLine("Order: " + job.Priority);
            builder.AppendLine("==============");


            // For each job, display the associated tasks (a job  
            // has one or more tasks). 
            builder.AppendLine("******TASKS*******");
            foreach (ITask task in job.Tasks)
            {
                builder.AppendLine("Task Id: " + task.Id);
                builder.AppendLine("Name: " + task.Name);
                builder.AppendLine("Progress: " + task.Progress);
                builder.AppendLine("Configuration: " + task.Configuration);
                if (task.ErrorDetails != null)
                {
                    builder.AppendLine("Error: " + task.ErrorDetails);
                }
                builder.AppendLine("==============");
            }

            // For each job, display the list of input media assets.
            builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
            foreach (IAsset inputAsset in job.InputMediaAssets)
            {

                if (inputAsset != null)
                {
                    builder.AppendLine("Input Asset Id: " + inputAsset.Id);
                    builder.AppendLine("Name: " + inputAsset.Name);
                    builder.AppendLine("==============");
                }
            }

            // For each job, display the list of output media assets.
            builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
            foreach (IAsset theAsset in job.OutputMediaAssets)
            {
                if (theAsset != null)
                {
                    builder.AppendLine("Output Asset Id: " + theAsset.Id);
                    builder.AppendLine("Name: " + theAsset.Name);
                    builder.AppendLine("==============");
                }
            }

        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="list-all-access-policies"></a>すべてのアクセス ポリシーを一覧表示する
Media Services では、アセットまたはそのファイルに関するアクセス ポリシーを定義できます。 アクセス ポリシーで、ファイルやアセットに対するアクセス許可 (アクセスの種類や期間) を定義します。 Media Services のコードでアクセス ポリシーを定義するには、通常は IAccessPolicy オブジェクトを作成して既存のアセットに関連付けます。 次に、ILocator オブジェクトを作成します。このオブジェクトにより、Media Services のアセットに直接アクセスできるようになります。 このドキュメント シリーズ付属の Visual Studio プロジェクトには、アクセス ポリシーとロケーターを作成してアセットに割り当てる方法を示すコード例がいくつか含まれています。

次のコード例では、サーバーのすべてのアクセス ポリシーを一覧表示し、それぞれに関連付けられている権限の種類を表示する方法を示します。 アクセス ポリシーを表示するもう 1 つの便利な方法は、サーバーのすべての ILocator オブジェクトを一覧表示することです。各ロケーターについて、関連付けられているアクセス ポリシーを一覧表示するには、AccessPolicy プロパティを使用します。

```csharp
    static void ListAllPolicies()
    {
        foreach (IAccessPolicy policy in _context.AccessPolicies)
        {
            Console.WriteLine("");
            Console.WriteLine("Name:  " + policy.Name);
            Console.WriteLine("ID:  " + policy.Id);
            Console.WriteLine("Permissions: " + policy.Permissions);
            Console.WriteLine("==============");

        }
    }
```
    
## <a name="limit-access-policies"></a>アクセス ポリシーを制限する 

>[!NOTE]
> さまざまな AMS ポリシー (ロケーター ポリシーや ContentKeyAuthorizationPolicy など) に 1,000,000 ポリシーの制限があります。 常に同じ日数、アクセス許可などを使う場合は、同じポリシー ID を使う必要があります (たとえば、長期間存在するように意図されたロケーターのポリシー (非アップロード ポリシー))。 

たとえば、アプリケーションで 1 回だけ実行する次のコードで、汎用的なポリシーのセットを作成できます。 後で使うために、ID をログ ファイルに記録できます。

```csharp
    double year = 365.25;
    double week = 7;
    IAccessPolicy policyYear = _context.AccessPolicies.Create("One Year", TimeSpan.FromDays(year), AccessPermissions.Read);
    IAccessPolicy policy100Year = _context.AccessPolicies.Create("Hundred Years", TimeSpan.FromDays(year * 100), AccessPermissions.Read);
    IAccessPolicy policyWeek = _context.AccessPolicies.Create("One Week", TimeSpan.FromDays(week), AccessPermissions.Read);

    Console.WriteLine("One year policy ID is: " + policyYear.Id);
    Console.WriteLine("100 year policy ID is: " + policy100Year.Id);
    Console.WriteLine("One week policy ID is: " + policyWeek.Id);
```

その後、次のようなコードで既存の ID を使うことができます。

```csharp
    const string policy1YearId = "nb:pid:UUID:2a4f0104-51a9-4078-ae26-c730f88d35cf";


    // Get the standard policy for 1 year read only
    var tempPolicyId = from b in _context.AccessPolicies
                       where b.Id == policy1YearId
                       select b;
    IAccessPolicy policy1Year = tempPolicyId.FirstOrDefault();

    // Get the existing asset
    var tempAsset = from a in _context.Assets
                where a.Id == assetID
                select a;
    IAsset asset = tempAsset.SingleOrDefault();

    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
        policy1Year,
        DateTime.UtcNow.AddMinutes(-5));
    Console.WriteLine("The locator base path is " + originLocator.BaseUri.ToString());
```

## <a name="list-all-locators"></a>すべてのロケーターを一覧表示する
ロケーターは、アセットに直接アクセスできる直接パス、およびロケーターの関連付けられているアクセス ポリシーの定義に従ってアセットへのアクセス許可を提供する URL です。 各アセットの Locators プロパティは、関連付けられている ILocator オブジェクトのコレクションを持つことがあります。 サーバー コンテキストも、すべてのロケーターが含まれる Locators コレクションを持ちます。

次のコード例では、サーバーのすべてのロケーターを一覧表示します。 各ロケーターについて、関連するアセットの ID とアクセス ポリシーを表示します。 また、アクセス許可の種類、有効期限、およびアセットの完全パスも表示します。

アセットのロケーター パスがアセットの唯一のベース URL であることに注意してください。 ユーザーまたはアプリケーションが参照することができる個々のファイルへの直接パスを作成するには、コードで、ロケーター パスに個別のファイルのパスを追加する必要があります。 個別のファイル パスを追加する方法の詳細については、「 [Media Services SDK for .NET によるアセットの配信](media-services-deliver-streaming-content.md)」をご覧ください。

```csharp
    static void ListAllLocators()
    {
        foreach (ILocator locator in _context.Locators)
        {
            Console.WriteLine("***********");
            Console.WriteLine("Locator Id: " + locator.Id);
            Console.WriteLine("Locator asset Id: " + locator.AssetId);
            Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
            Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
            Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
            // The locator path is the base or parent path (with included permissions) to access  
            // the media content of an asset. To create a full URL to a specific media file, take 
            // the locator path and then append a file name and info as needed.  
            Console.WriteLine("Locator base path: " + locator.Path);
            Console.WriteLine("");
        }
    }
```

## <a name="enumerating-through-large-collections-of-entities"></a>大規模なコレクションのエンティティの列挙
パブリック REST v2 では、クエリ結果が 1000 件に制限されているため、エンティティを照会するときには、一度に返されるエンティティが 1000 個に制限されます。 大規模なコレクションのエンティティを列挙するときは、Skip と Take を使用する必要があります。 

次の関数は、指定された Media Services アカウントのすべてのジョブをループします。 Media Services は、Jobs コレクションの 1000 個のジョブを返します。 この関数は、Skip と Take を使用してすべてのジョブが確実に列挙されるようにします (アカウントに 1,000 個を超えるジョブがある場合)。

```csharp
    static void ProcessJobs()
    {
        try
        {

            int skipSize = 0;
            int batchSize = 1000;
            int currentBatch = 0;

            while (true)
            {
                // Loop through all Jobs (1000 at a time) in the Media Services account
                IQueryable _jobsCollectionQuery = _context.Jobs.Skip(skipSize).Take(batchSize);
                foreach (IJob job in _jobsCollectionQuery)
                {
                    currentBatch++;
                    Console.WriteLine("Processing Job Id:" + job.Id);
                }

                if (currentBatch == batchSize)
                {
                    skipSize += batchSize;
                    currentBatch = 0;
                }
                else
                {
                    break;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }
```

## <a name="delete-an-asset"></a>アセットを削除する
次の例では、アセットを削除します。

```csharp
    static void DeleteAsset( IAsset asset)
    {
        // delete the asset
        asset.Delete();

        // Verify asset deletion
        if (GetAsset(asset.Id) == null)
            Console.WriteLine("Deleted the Asset");

    }
```

## <a name="delete-a-job"></a>ジョブを削除する
ジョブを削除するには、State プロパティで指定されているジョブの状態を確認する必要があります。 終了または取り消し済みのジョブは削除できますが、キューに登録済み、スケジュール済み、処理中などの他の状態のジョブは、取り消さないと削除できません。

次のコード例では、ジョブを削除する方法を示します。この例では、ジョブの状態をチェックして、状態が終了または取り消し済みであるときに削除します。 このコードは、ジョブ参照の取得については、このトピックの前述のセクション「ジョブ参照を取得する」に依存します。

```csharp
    static void DeleteJob(string jobId)
    {
        bool jobDeleted = false;

        while (!jobDeleted)
        {
            // Get an updated job reference.  
            IJob job = GetJob(jobId);

            // Check and handle various possible job states. You can 
            // only delete a job whose state is Finished, Error, or Canceled.   
            // You can cancel jobs that are Queued, Scheduled, or Processing,  
            // and then delete after they are canceled.
            switch (job.State)
            {
                case JobState.Finished:
                case JobState.Canceled:
                case JobState.Error:
                    // Job errors should already be logged by polling or event 
                    // handling methods such as CheckJobProgress or StateChanged.
                    // You can also call job.DeleteAsync to do async deletes.
                    job.Delete();
                    Console.WriteLine("Job has been deleted.");
                    jobDeleted = true;
                    break;
                case JobState.Canceling:
                    Console.WriteLine("Job is cancelling and will be deleted "
                        + "when finished.");
                    Console.WriteLine("Wait while job finishes canceling...");
                    Thread.Sleep(5000);
                    break;
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    job.Cancel();
                    Console.WriteLine("Job is scheduled or processing and will "
                        + "be deleted.");
                    break;
                default:
                    break;
            }

        }
    }
```


## <a name="delete-an-access-policy"></a>アクセス ポリシーを削除する
次のコード例では、ポリシー ID に基づいてアクセス ポリシーへの参照を取得し、ポリシーを削除する方法を示します。

```csharp
    static void DeleteAccessPolicy(string existingPolicyId)
    {
        // To delete a specific access policy, get a reference to the policy.  
        // based on the policy Id passed to the method.
        var policyInstance =
                from p in _context.AccessPolicies
                where p.Id == existingPolicyId
                select p;
        IAccessPolicy policy = policyInstance.FirstOrDefault();

        policy.Delete();

    }
```


## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
