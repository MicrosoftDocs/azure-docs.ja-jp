---
title: .NET ファイル規則ライブラリを使用して Azure Storage に出力データを保持する
description: .NET 用の Azure Batch ファイル規則ライブラリを使用して、Azure Storage にバッチ タスクとジョブの出力を保持し、Azure Portal でその出力を表示する方法を説明します。
ms.topic: how-to
ms.date: 11/14/2018
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 1a45eed421dd8d734fcef0dd452df1d4a65fd053
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88936964"
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net"></a>.NET 用の Batch ファイル規則ライブラリを使用した Azure Storage へのジョブおよびタスクのデータの保持

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

タスクのデータを保持する方法の 1 つに [.NET 用の Azure Batch ファイル規則ライブラリ][nuget_package]があります。 ファイル規則ライブラリにより、タスク出力データを Azure Storage に保存し、取得する処理が簡素化されます。 ファイル規則ライブラリはタスク コードでも、クライアント コード &mdash; でも使用できます。タスク コードではファイルを保持するために、クライアント コードではファイルを一覧で取得するために使用されます。 [タスクの依存関係](batch-task-dependencies.md)のシナリオのように、上流のタスクの出力を取得するためにタスク コードでライブラリを使用することもできます。

ファイル規則ライブラリを使用して出力ファイルを取得する際は、ファイル名や場所がわからなくても、ID と目的で出力を一覧表示することで、 特定のジョブまたはタスクのファイルを容易に特定できます。 たとえば、ファイル規則ライブラリを利用すれば、特定のタスクのすべての中間ファイルを一覧表示したり、特定のジョブのプレビュー ファイルを取得したりすることができます。

> [!TIP]
> バージョン 2017-05-01 以降では、仮想マシン構成で作成されたプール上で実行されるタスクやジョブ マネージャー タスクについて、Batch サービス API で Azure Storage への出力データの保持がサポートされています。 Batch サービス API は、タスクを作成するコード内からの出力を保持し、ファイル規則ライブラリの代わりに使用できる簡単な方法です。 Batch クライアント アプリケーションを変更して、タスクを実行中のアプリケーションを更新する必要なしに出力を保持することもできます。 詳細については、「[Persist task data to Azure Storage with the Batch service API (Batch サービス API を使用した Azure Storage へのタスクのデータの保持)](batch-task-output-files.md)」を参照してください。

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>どのような場合にファイル規則ライブラリをタスク出力に保持するか

Azure Batch は、タスクの出力を保持するために複数の方法を提供しています。 ファイル規則は以下のような場合に最適です。

- タスクを実行中のアプリケーションのコードを簡単に変更し、ファイル規則ライブラリを使用してファイルを保持したい場合。
- タスクの実行中に Azure Storage にデータをストリーミングしたい場合。
- クラウド サービス構成または仮想マシン構成のどちらで作成されたプールからもデータを保持する必要がある。
- クライアント アプリケーションや、ジョブ内の他のタスクで、ID または目的によってタスク出力ファイルを特定してダウンロードする必要がある。
- Azure ポータルでタスク出力を表示したい場合。

検討しているシナリオが上記のシナリオと異なる場合は、別のアプローチを考慮する必要があります。 タスクの出力を保持するために利用できるその他のオプションの詳細については、「[ジョブやタスクからの出力を Azure Storage に保存する](batch-task-output.md)」を参照してください。

## <a name="what-is-the-batch-file-conventions-standard"></a>Batch ファイル規則の標準

[Batch ファイル規則の標準](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files)は、出力ファイルの書き込み先となる保存先コンテナーや BLOB パスの名前付けスキームを示します。 ファイル規則の標準に従った Azure Storage にファイルを保存すると、自動的に Azure ポータルで表示できるようになります。 Azure ポータルは名前付け規則を認識するため、その規則に従ってファイルを表示することができます。

.NET 用ファイル規則ライブラリは、ファイル規則の標準に従ってストレージ コンテナーとタスク出力ファイルに自動的に名前を付けます。 また、ジョブ ID、タスク ID、または目的に従って、Azure Storage 内の出力ファイルに関するクエリを実行する手段を提供します。

.NET 以外の開発言語を使用する場合、ファイル規則の標準を独自にアプリケーションに実装できます。 詳細については、「[Batch ファイル規則の標準を実装する](batch-task-output.md#implement-the-batch-file-conventions-standard)」を参照してください。

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Azure Storage アカウントを Batch アカウントにリンクする

ファイル規則ライブラリを使用して Azure Storage への出力データを保持するには、先に Azure Storage アカウントを Batch アカウントにリンクする必要があります。 まだリンクしていない場合は、[Azure Portal](https://portal.azure.com) で Storage アカウントを Batch アカウントにリンクします。その方法は以下のとおりです。

1. Azure Portal の Batch アカウントに移動します。
1. **[設定]** で **[ストレージ アカウント]** を選択します。
1. まだ Storage アカウントを Batch アカウントに関連付けていない場合は、 **[Storage Account (None)]\(ストレージ アカウント (なし)\)** をクリックします。
1. サブスクリプションの一覧から Storage アカウントを選択します。 最適なパフォーマンスを得るには、タスクを実行している Batch アカウントと同じリージョンにある Azure Storage アカウントを使用します。

## <a name="persist-output-data"></a>出力データの保持

ファイル規則ライブラリを利用してジョブとタスク出力のデータを保存する場合、Azure Storage にコンテナーを作成し、そのコンテナーに出力を保存します。 タスク出力をコンテナーにアップロードするには、タスク コードで [.NET 用 Azure Storage クライアント ライブラリ](https://www.nuget.org/packages/WindowsAzure.Storage)を使用します。

Azure Storage でのコンテナーと BLOB の操作の詳細については、「[.NET を使用して Azure Blob Storage を使用する](../storage/blobs/storage-quickstart-blobs-dotnet.md)」を参照してください。

> [!WARNING]
> ファイル規則ライブラリを使用したジョブとタスクの出力はすべて同じコンテナーに格納されるため、 大量のタスクで同時にファイルを保持しようとすると、Azure Storage の調整の制限が適用される場合があります。 調整の制限の詳細については、「[BLOB ストレージのパフォーマンスとスケーラビリティのチェックリスト](../storage/blobs/storage-performance-checklist.md)」を参照してください。

### <a name="create-storage-container"></a>ストレージ コンテナーの作成

Azure Storage にタスク出力を保持するには、まず [CloudJob][net_cloudjob].[PrepareOutputStorageAsync][net_prepareoutputasync] を呼び出してコンテナーを作成します。 この拡張メソッドは [CloudStorageAccount][net_cloudstorageaccount] オブジェクトをパラメーターとして受け取り、 コンテナーを作成します。このコンテナーには、Azure portal またはこれ以降に説明する取得メソッドで内容を検出できるように、ファイル規則の標準に従って名前が付けられます。

通常、クライアント アプリケーション &mdash; (プール、ジョブ、タスクを作成するアプリケーション) でコンテナーを作成するために以下のコードを配置します。

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

これで Azure Storage にコンテナーを準備できたので、ファイル規則ライブラリの [TaskOutputStorage][net_taskoutputstorage] クラスを使用して、タスク出力をコンテナーに保存できます。

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

保持されたファイルは [TaskOutputStorage](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage).[SaveAsync](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync#overloads) メソッドの `kind` パラメーターによって分類されます。 [TaskOutputKind][net_taskoutputkind] として、事前に定義された 4 つの種類が存在します。`TaskOutput`、`TaskPreview`、`TaskLog`、`TaskIntermediate.` です。カスタム出力カテゴリを定義することもできます。

これらの出力の種類を使用すれば、後で Batch に対してクエリを実行し、特定のタスクの保持された出力を取得するときに、どの種類の出力を一覧表示するかを指定することができます。 つまり、タスク出力を一覧表示したとき、出力の種類に基づいて一覧をフィルター処理することができます。 たとえば、"タスク *109* の出力の "*プレビュー*" を閲覧する" といったことが可能です。 出力の一覧表示と取得については、この記事の後ろのセクションの「出力の取得」で詳しく説明します。

> [!TIP]
> 出力の種類によって、特定のファイルが Azure portal のどこに表示されるかも指定されます。*TaskOutput* に分類されたファイルは **[Task output files]\(タスク出力ファイル\)** に表示され、 *TaskLog* に分類されたファイルは **[Task logs]\(タスク ログ\)** に表示されます。

### <a name="store-job-outputs"></a>ジョブの出力の格納

タスク出力を格納するだけでなく、ジョブ全体に関連付けられている出力も格納できます。 たとえば、ムービー レンダリング ジョブのマージ タスクで、完全にレンダリングされたムービーをジョブの出力として保持できます。 ジョブが完了したら、クライアント アプリケーションでジョブの出力を一覧表示して取得することが可能になります。個々のタスクを照会する必要はありません。

ジョブ出力を格納するには、[JobOutputStorage][net_joboutputstorage].[SaveAsync][net_joboutputstorage_saveasync] メソッドを呼び出し、[JobOutputKind][net_joboutputkind] とファイル名を指定します。

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

タスク出力のときの **TaskOutputKind** と同様に、種類 [JobOutputKind][net_joboutputkind] で、ジョブの保持されたファイルを分類します。 このパラメーターにより、後で指定の種類の出力を照会 (または一覧表示) できるようになります。 **JobOutputKind** は出力とプレビューの両カテゴリを含み、カスタマイズしたカテゴリの作成をサポートします。

### <a name="store-task-logs"></a>タスクのログの格納

タスクまたはジョブの完了時に、永続的なストレージでファイルを保持するだけでなく、タスクの実行中に更新されたファイルの保持も必要になる可能性があります。たとえば、&mdash; ログ ファイル、`stdout.txt`、`stderr.txt` などです。 Azure Batch ファイル規則ライブラリでは、この目的で使用する [TaskOutputStorage][net_taskoutputstorage].[SaveTrackedAsync][net_savetrackedasync] メソッドを提供しています。 [SaveTrackedAsync][net_savetrackedasync] では、ノード上のファイルの更新を (指定した間隔で) 追跡し、それを Azure Storage で保持できます。

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

コマンド セクション `Code to process data and produce output file(s)` は、タスクが通常実行するコードのプレースホルダーです。 たとえば、Azure Storage からデータをダウンロードして何らかの変換や計算を行うコードがあるとします。 このスニペットの重要な役割は、ファイルを定期的に [SaveTrackedAsync][net_savetrackedasync] で更新するために、そのようなコードを `using` ブロックにラップする方法を示すことです。

ノード エージェントは、プール内の各ノードで実行されるプログラムで、ノードと Batch サービスとの間でコマンドと制御の仲立ちとしての役割を果たします。 ノード上の stdout.txt ファイルに対する標準出力の内容をノード エージェントがフラッシュするための時間を確保するために、この `using` ブロックの最後には `Task.Delay` の呼び出しが必要です。 この待機時間を設けなかった場合、最後の数秒間分の出力が失われる可能性があります。 ファイルによっては、この待機時間が不要である場合もあります。

> [!NOTE]
> **SaveTrackedAsync** でファイルの追跡を有効にすると、追跡されているファイルへの "*追加分*" だけが Azure Storage で保持されます。 このメソッドは、ローテーションのないログ ファイルの追跡か、ファイルの末尾への追加操作によって書き込まれる他のファイルの追跡のみに使用してください。

## <a name="retrieve-output-data"></a>出力データの取得

保持された出力を Azure Batch ファイル規則ライブラリを使用して取得する際は、タスクまたはジョブ中心の方法を使用します。 Azure Storage ストレージ内でのパス、さらにはファイル名を把握していなくても、 タスクまたはジョブの ID で特定のタスクまたはジョブの出力を要求することができます。

次のコード スニペットでは、ジョブのタスクを反復処理し、タスクの一部の出力ファイル情報を出力して、Storage からそのファイルをダウンロードします。

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (OutputFileReference output in
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

## <a name="view-output-files-in-the-azure-portal"></a>Azure Portal での出力ファイルの表示

Azure Portal では、[Batch ファイル規則の標準](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files)を使って、リンク済みの Azure Storage アカウントに保持されているタスク出力ファイルとログが表示されます。 これらの規則を好きな言語で実装できるほか、自分の .NET アプリケーションのファイル規則ライブラリを使用することもできます。

ポータルで出力ファイルの表示を有効にするには、次の要件を満たす必要があります。

1. Azure ストレージ アカウントを Batch アカウントにリンクします。
1. 出力を保持する際は、ストレージ コンテナーとファイルの事前定義された名前付け規則に準拠します。 これらの規則の定義は、ファイル規則ライブラリの [README][github_file_conventions_readme] に記載されています。 出力の保持に [Azure Batch ファイル規則][nuget_package]ライブラリを使用した場合、ファイルはファイル規則の標準に従って保持されます。

Azure Portal でタスク出力ファイルとログを表示するには、目的の出力を行ったタスクに移動し、 **[保存された出力ファイル]** または **[保存されたログ]** をクリックします。 次の図は、IDが "007" のタスクの **保存された出力ファイル** を示しています。

![Task outputs blade in the Azure portal][2]

## <a name="code-sample"></a>コード サンプル

[PersistOutputs][github_persistoutputs] サンプル プロジェクトは、GitHub にある [Azure Batch コード サンプル][github_samples]の 1 つです。 この Visual Studio ソリューションは、Azure Batch ファイル規則ライブラリを使用して永続的なストレージでタスク出力を保持する方法を示しています。 サンプルを実行するには、次の手順に従います。

1. **Visual Studio 2019** でプロジェクトを開きます。
2. Microsoft.Azure.Batch.Samples.Common プロジェクトの **AccountSettings.settings** に、Batch と Storage の **アカウント資格情報** を追加します。
3. **ビルド** します (ただし実行はしないでください)。 NuGet パッケージの復元を求められた場合は、復元します。
4. Azure ポータルを使用して、 [アプリケーション パッケージ](batch-application-packages.md) を **PersistOutputsTask** としてアップロードします。 `PersistOutputsTask.exe` とそれに依存するアセンブリを .zip パッケージに含め、アプリケーション ID を "PersistOutputsTask"、アプリケーション パッケージのバージョンを "1.0" に設定します。
5. **PersistOutputs** プロジェクトを **開始** (実行) します。
6. サンプルの実行に使用する保持テクノロジを選択するよう求められた場合は、「**1**」を入力し、ファイル規則ライブラリをタスク出力の保持に使用してサンプルを実行します。 

## <a name="next-steps"></a>次のステップ

### <a name="get-the-batch-file-conventions-library-for-net"></a>.NET 用 Batch ファイル規則ライブラリの入手

.NET 用 Batch ファイル規則ライブラリは [NuGet][nuget_package] にあります。 このライブラリでは、新しいメソッドによって [CloudJob][net_cloudjob] クラスと [CloudTask][net_cloudtask] クラスが拡張されています。 ファイル規則ライブラリについての[リファレンス ドキュメント](/dotnet/api/microsoft.azure.batch.conventions.files)も参照してください。

ファイル規則ライブラリの[ソース コード][github_file_conventions]は、GitHub の Microsoft Azure SDK for .NET リポジトリ内にあります。 

### <a name="explore-other-approaches-for-persisting-output-data"></a>出力データを保持するその他のアプローチ

- タスクおよびジョブのデータ保持の概要については、「[Azure Storage への完了したジョブおよびタスクの結果の保持](batch-task-output.md)」を参照してください。
- Batch サービス API を使用して出力データを保持する方法については、「[Persist task data to Azure Storage with the Batch service API](batch-task-output-files.md)」(Batch サービス API を使用した Azure Storage へのタスク データの保持) を参照してください。

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: /dotnet/api/microsoft.azure.batch.batchclient
[net_cloudjob]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_cloudstorageaccount]: /java/api/com.microsoft.azure.storage.cloudstorageaccount
[net_cloudtask]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files/README.md
[net_joboutputkind]: /dotnet/api/microsoft.azure.batch.conventions.files.joboutputkind
[net_joboutputstorage]: /dotnet/api/microsoft.azure.batch.conventions.files.joboutputstorage
[net_joboutputstorage_saveasync]: /dotnet/api/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync
[net_msdn]: /dotnet/api/microsoft.azure.batch
[net_prepareoutputasync]: /dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync
[net_saveasync]: /dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync
[net_savetrackedasync]: /dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync
[net_taskoutputkind]: /dotnet/api/microsoft.azure.batch.conventions.files.taskoutputkind
[net_taskoutputstorage]: /dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Saved output files and Saved logs selectors in portal"
[2]: ./media/batch-task-output/task-output-02.png "Task outputs blade in the Azure portal"
