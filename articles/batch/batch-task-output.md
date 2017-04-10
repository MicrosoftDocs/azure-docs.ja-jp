---
title: "Azure Storage へのジョブおよびタスクの出力の保持 - Azure Batch | Microsoft Docs"
description: "Azure Storage を Batch のタスク出力とジョブ出力用の永続的ストアとして使用し、それらの保持された出力を Azure ポータルで確認できるようにする方法について説明します。"
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 3b3aa18eb52993843be1feeb8e0b2a43339413c3
ms.lasthandoff: 03/09/2017


---
# <a name="persist-results-from-completed-jobs-and-tasks-to-azure-storage"></a>Azure Storage への完了したジョブおよびタスクの結果の保持

通常、Batch で実行されるタスクは出力を生成します。この出力は、格納された後、ジョブ内の他のタスクかジョブを実行したクライアント アプリケーション、またはその両方によって取得される必要があります。 この出力は、入力データを処理して作成されたファイルであるか、タスクの実行に関連付けられたログ ファイルである可能性があります。 この記事では、規則ベースの手法でこのようなタスク出力を Azure BLOB ストレージで保持する .NET クラス ライブラリを紹介し、プール、ジョブ、コンピューティング ノードを削除した後でもタスク出力を使用できるようにします。

この記事の手法を使用して、[Azure Portal][portal] の **[Saved output files (保存された出力ファイル)]** と **[Saved logs (保存されたログ)]** でタスク出力を確認することもできます。

![Saved output files and Saved logs selectors in portal][1]

> [!NOTE]
> この記事で説明されている [Azure Batch ファイル規則][nuget_package] .NET クラス ライブラリは現在プレビュー段階です。 ここで説明する機能の一部は、一般公開前に変更される可能性があります。
> 
> 

## <a name="task-output-considerations"></a>タスク出力に関する考慮事項
Batch ソリューションを設計する際は、ジョブとタスク出力に関連するいくつかの事項を考慮する必要があります。

* **コンピューティング ノードの有効期間**: コンピューティング ノードは多くの場合、一時的なものです (特に自動スケールが有効なプールの場合)。 ノード上で実行されるタスクの出力は、そのノードが存在し、タスクに設定したファイルのリテンション期間内である場合にのみ使用可能です。 つまり、タスク出力を確実に保持するには、タスク出力ファイルを Azure Storage などの永続的なストアにアップロードする必要があります。
* **出力ストレージ**: 永続的なストレージでタスク出力データを保持するために、タスクのコードで [Azure Storage SDK](../storage/storage-dotnet-how-to-use-blobs.md) を使用して、タスク出力を BLOB ストレージ コンテナーにアップロードできます。 コンテナーとファイルの名前付け規則を実装した場合、クライアント アプリケーションまたはジョブ内の他のタスクは、規則に基づいてこの出力を特定し、ダウンロードできます。
* **出力の取得**: タスク出力は、プール内のコンピューティング ノードから直接取得できるほか、タスク出力が保持されている場合は Azure Storage から取得することもできます。 タスクの出力をコンピューティング ノードから直接取得するには、ファイル名とノード上の出力場所が必要です。 Azure Storage で出力を保持する場合、下流のタスクまたはクライアント アプリケーションは、Azure Storage SDK を使用して出力をダウンロードするために Azure Storage のファイルの完全なパスを把握する必要があります。
* **出力の表示**: Azure Portal で Batch のタスクに移動し、**[ノード上のファイル]** を選択すると、目的の出力ファイルだけでなく、タスクに関連するすべてのファイルが表示されます。 繰り返しになりますが、コンピューティング ノード上のファイルは、そのノードが存在し、タスクに設定したファイルのリテンション期間内である場合のみ使用可能です。 Azure Storage で保持されているタスク出力を [Azure Storage Explorer][storage_explorer] などのアプリケーションやポータルで表示するには、出力の保存場所を確認してファイルに直接移動する必要があります。

## <a name="help-for-persisted-output"></a>保持されている出力に関するヘルプ
ジョブとタスクの出力をより簡単に保持できるようにするために、Batch チームは、一連の名前付け規則のほかに、Batch アプリケーションで使用できる .NET クラス ライブラリ ([Azure Batch ファイル規則][nuget_package]ライブラリ) を定義し、実装しています。 さらに、Azure ポータルはこれらの名前付け規則を認識しているため、ライブラリを使用すれば格納したファイルを簡単に見つけ出すことができます。

## <a name="using-the-file-conventions-library"></a>ファイル規則ライブラリの使用
[Azure Batch ファイル規則][nuget_package]は .NET クラス ライブラリです。Batch .NET アプリケーションでこれを使用すれば、Azure Storage との間でタスク出力の格納と出力を簡単に行えます。 これはタスク コードでも、クライアント コードでも使用できます。タスク コードではファイルを保持するために、クライアント コードではファイルを一覧で取得するために使用されます。 [タスクの依存関係](batch-task-dependencies.md)のシナリオのように、上流のタスクの出力を取得するためにタスクでライブラリを使用することもできます。

規則ライブラリを利用すれば、ストレージ コンテナーとタスク出力ファイルの名前が規則に従って設定され、それらが Azure Storage で保持される際に正しい場所にアップロードされるようになります。 出力を取得する際は、ファイル名や Storage 内での場所がわからなくても、ID と目的で出力を一覧表示または取得することで、特定のジョブまたはタスクの出力を容易に特定できます。

たとえば、ファイル名やストレージ アカウント内での場所がわからなくても、このライブラリを利用すれば、"タスク 7 のすべての中間ファイルの一覧表示" や " *mymovie*ジョブのサムネイルのプレビューの閲覧" などの目的を果たすことができます。

### <a name="get-the-library"></a>ライブラリの入手
新しいクラスが含まれ、[NuGet][nuget_package] からの新しいメソッドによって [CloudJob][net_cloudjob] クラスと [CloudTask][net_cloudtask] クラスが拡張されているライブラリを入手できます。 このライブラリは [NuGet ライブラリ パッケージ マネージャー][nuget_manager]を使用して Visual Studio のプロジェクトに追加できます。

> [!TIP]
> Azure Batch ファイル規則ライブラリの[ソース コード][github_file_conventions]は、GitHub の Microsoft Azure SDK for .NET リポジトリ内にあります。
> 
> 

## <a name="requirement-linked-storage-account"></a>要件: リンクされたストレージ アカウント
ファイル規則ライブラリを使用して永続的なストレージに出力を格納し、それを Azure ポータルで確認するには、Batch アカウントに [Azure ストレージ アカウントをリンクする](batch-application-packages.md#link-a-storage-account) 必要があります。 まだリンクされていない場合は、Azure ポータルを使用して Batch アカウントにストレージ アカウントをリンクします。

**[Batch アカウント]** ブレード、**[設定]** > **[ストレージ アカウント]** > **[ストレージ アカウント]** (なし) の順にクリックし、自分のサブスクリプションのストレージ アカウントを選択します。

ストレージ アカウントのリンクに関するさらに詳細なチュートリアルについては、 [「Azure Batch アプリケーション パッケージを使用したアプリケーションのデプロイ」](batch-application-packages.md)を参照してください。

## <a name="persist-output"></a>出力の保持
ファイル規則ライブラリを利用してジョブとタスク出力を保存する場合、主なアクションが&2; つあります。ストレージ コンテナーの作成と、コンテナーへの出力の保存です。

> [!WARNING]
> ジョブとタスクの出力はすべて同じコンテナーに格納されるため、大量のタスクで同時にファイルを保持しようとすると、 [ストレージのスロットルの制限](../storage/storage-performance-checklist.md#blobs) が適用される場合があります。
> 
> 

### <a name="create-storage-container"></a>ストレージ コンテナーの作成
ストレージでのタスク出力の保持を始める前に、出力のアップロード先となる Blob Storage コンテナーを作成する必要があります。 これは [CloudJob][net_cloudjob].[PrepareOutputStorageAsync][net_prepareoutputasync] を呼び出すことで作成できます。 この拡張メソッドは [CloudStorageAccount][net_cloudstorageaccount] オブジェクトをパラメーターとして受け取り、コンテナーを作成します。このコンテナーには、Azure Portal またはこれ以降に説明する取得メソッドで内容を検出できるように、決まった方法で名前が付けられます。

通常、このコードはクライアント アプリケーション (プール、ジョブ、タスクを作成するアプリケーション) に配置します。

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>タスク出力の格納
これで Blob Storage にコンテナーを準備できたので、ファイル規則ライブラリの [TaskOutputStorage][net_taskoutputstorage] クラスを使用して、タスク出力をコンテナーに保存できます。

タスク コードでは、最初に [TaskOutputStorage][net_taskoutputstorage] オブジェクトを作成し、そのタスクが完了したら [TaskOutputStorage][net_taskoutputstorage].[SaveAsync][net_saveasync] メソッドを呼び出して、出力を Azure Storage に保存します。

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

保持されたファイルは "出力の種類" パラメーターによって分類されます。 [TaskOutputKind][net_taskoutputkind] として、事前に定義された&4; つの種類が存在します。"TaskOutput"、"TaskPreview"、"TaskLog"、"TaskIntermediate" です。 カスタムの種類を定義して、ワークフローで活用することもできます。

これらの出力の種類を使用すれば、後で Batch に対してクエリを実行し、特定のタスクの保持された出力を取得するときに、どの種類の出力を一覧表示するかを指定することができます。 つまり、タスク出力を一覧表示したとき、出力の種類に基づいて一覧をフィルター処理することができます。 たとえば、"タスク *109* の出力の "*プレビュー*" を閲覧する" といったことが可能です。 一覧の表示と出力の取得については、この記事の後ろのセクションの「 [出力の取得](#retrieve-output) 」で詳しく説明します。

> [!TIP]
> 出力の種類によって、特定のファイルが Azure Portal のどこに表示されるかも指定されます。*TaskOutput* に分類されたファイルは [Task output files (タスク出力ファイル)] に表示され、*TaskLog* に分類されたファイルは [Task logs (タスクのログ)] に表示されます。
> 
> 

### <a name="store-job-outputs"></a>ジョブの出力の格納
タスク出力を格納するだけでなく、ジョブ全体に関連付けられている出力も格納できます。 たとえば、ムービー レンダリング ジョブのマージ タスクで、完全にレンダリングされたムービーをジョブの出力として保持できます。 ジョブが完了したら、クライアント アプリケーションで単純にジョブの出力を一覧表示して取得することが可能になります。個々のタスクを照会する必要はありません。

ジョブ出力を格納するには、[JobOutputStorage][net_joboutputstorage].[SaveAsync][net_joboutputstorage_saveasync] メソッドを呼び出し、[JobOutputKind ][net_joboutputkind] とファイル名を指定します。

```
CloudJob job = await batchClient.JobOperations.GetJobAsync(jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

タスク出力のときの TaskOutputKind と同様に、[JobOutputKind][net_joboutputkind] パラメーターで、ジョブの保持されたファイルを分類します。 このパラメーターにより、後で指定の種類の出力を照会 (または一覧表示) できるようになります。 JobOutputKind は出力とプレビューの両種類を含み、カスタマイズした種類の作成をサポートします。

### <a name="store-task-logs"></a>タスクのログの格納
タスクまたはジョブの完了時に、永続的なストレージでファイルを保持するだけでなく、タスクの実行中に更新されたファイルの保持も必要になる可能性があります。たとえば、ログ ファイル、`stdout.txt`、`stderr.txt` などです。 Azure Batch ファイル規則ライブラリでは、この目的で使用する [TaskOutputStorage][net_taskoutputstorage].[SaveTrackedAsync][net_savetrackedasync] メソッドを提供しています。 [SaveTrackedAsync][net_savetrackedasync] では、ノード上のファイルの更新を (指定した間隔で) 追跡し、それを Azure Storage で保持できます。

次のコード スニペットでは、タスクの実行中、15 秒ごとに Azure Storage で `stdout.txt` を更新するために [SaveTrackedAsync][net_savetrackedasync] を使用しています。

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
     await Task.Delay(stdoutFlushDelay);
}
```

`Code to process data and produce output file(s)` は、タスクが通常実行するコードの単なるプレースホルダーです。 たとえば、Azure Storage からデータをダウンロードして何らかの変換や計算を行うコードがあるとします。 このスニペットの重要な役割は、ファイルを定期的に [SaveTrackedAsync][net_savetrackedasync] で更新するために、そのようなコードを `using` ブロックにラップする方法を示すことです。

ノード上の stdout.txt ファイルに対する標準出力の内容をノード エージェントがフラッシュするための時間を確保するために、この `using` ブロックの最後には `Task.Delay` が必要です (ノード エージェントは、プール内の各ノードで実行されるプログラムで、ノードと Batch サービスとの間でコマンドと制御の仲立ちとしての役割を果たします)。 この待機時間を設けなかった場合、最後の数秒間分の出力が失われる可能性があります。 ファイルによっては、この待機時間が不要である場合もあります。

> [!NOTE]
> SaveTrackedAsync でファイルの追跡を有効にすると、追跡されているファイルへの "*追加分*" だけが Azure Storage で保持されます。 このメソッドは、ローテーションのないログ ファイルの追跡か、データが追加される他のファイルの追跡のみに使用してください。更新の際のデータはファイルの末尾に追加されるだけだからです。
> 
> 

## <a name="retrieve-output"></a>出力の取得
保持された出力を Azure Batch ファイル規則ライブラリを使用して取得する際は、タスクまたはジョブ中心の方法を使用します。 BLOB ストレージ内でのパス、さらにはファイル名を把握していなくても、特定のタスクまたはジョブの出力を要求することができます。 "タスク *109*の出力ファイルを閲覧する" だけとも言えます。

次のコード スニペットでは、すべてのジョブのタスクを反復処理し、タスクの一部の出力ファイル情報を出力して、Storage からそのファイルをダウンロードします。

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (TaskOutputStorage output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="task-outputs-and-the-azure-portal"></a>タスク出力と Azure ポータル
Azure Portal では、[Azure Batch ファイル規則の README][github_file_conventions_readme] 内の名前付け規則を使って、リンク済みの Azure Storage アカウントに保持されているタスク出力とログが表示されます。 これらの規則を好きな言語で実装できるほか、自分の .NET アプリケーションのファイル規則ライブラリを使用することもできます。

### <a name="enable-portal-display"></a>ポータルの表示の有効化
ポータルで出力の表示を有効にするには、次の要件を満たす必要があります。

1. [Link an Azure Storage account](#requirement-linked-storage-account) 必要があります。
2. 出力を保持する際は、ストレージ コンテナーとファイルの事前定義された名前付け規則に準拠します。 これらの規則の定義は、ファイル規則ライブラリの [README][github_file_conventions_readme] に記載されています。 出力の保持に [Azure Batch ファイル規則][nuget_package]ライブラリを使用する場合、この要件は満たされています。

### <a name="view-outputs-in-the-portal"></a>ポータルでの出力の表示
Azure Portal でタスク出力とログを表示するには、目的の出力を行ったタスクに移動し、**[保存された出力ファイル]** または **[保存されたログ]** をクリックします。 次の図は、IDが "007" のタスクの **保存された出力ファイル** を示しています。

![Task outputs blade in the Azure portal][2]

## <a name="code-sample"></a>サンプル コード
[PersistOutputs][github_persistoutputs] サンプル プロジェクトは、GitHub にある [Azure Batch コード サンプル][github_samples]の&1; つです。 この Visual Studio ソリューションは、Azure Batch ファイル規則ライブラリを使用して永続的なストレージでタスク出力を保持する方法を示しています。 サンプルを実行するには、次の手順に従います。

1. **Visual Studio 2015 以降**でプロジェクトを開きます。
2. Microsoft.Azure.Batch.Samples.Common プロジェクトの **AccountSettings.settings** に、Batch と Storage の**アカウント資格情報**を追加します。
3. **ビルド** します (ただし実行はしないでください)。 NuGet パッケージの復元を求められた場合は、復元します。
4. Azure ポータルを使用して、 [アプリケーション パッケージ](batch-application-packages.md) を **PersistOutputsTask**としてアップロードします。 `PersistOutputsTask.exe` とそれに依存するアセンブリを .zip パッケージに含め、アプリケーション ID を "PersistOutputsTask"、アプリケーション パッケージのバージョンを "1.0" に設定します。
5. **PersistOutputs** プロジェクトを**開始** (実行) します。

## <a name="next-steps"></a>次のステップ
### <a name="application-deployment"></a>アプリケーションのデプロイ
コンピューティング ノード上でタスクを通じて実行するアプリケーションのデプロイとバージョン管理は、どちらも Batch の [アプリケーション パッケージ](batch-application-packages.md) 機能を使って簡単に実現できます。

### <a name="installing-applications-and-staging-data"></a>アプリケーションとステージング データのインストール
Azure Batch フォーラムの「[Installing applications and staging data on Batch compute nodes (Batch コンピューティング ノードへのアプリケーションとステージング データのインストール)][forum_post]」という投稿に、タスクの実行に使用するノードを準備するさまざまな方法が簡潔に説明されています。 この投稿記事は、Azure Batch チームのメンバーによって書かれたものです。コンピューティング ノードにファイル (アプリケーションとタスクの入力データを含む) を展開する各種の方法や、その方法ごとの特別な考慮事項がわかりやすく解説されています。

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Saved output files and Saved logs selectors in portal"
[2]: ./media/batch-task-output/task-output-02.png "Task outputs blade in the Azure portal"

