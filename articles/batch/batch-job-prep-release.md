---
title: コンピューティング ノードでジョブを準備しジョブを完了するタスクの作成 - Azure Batch | Microsoft Docs
description: Azure Batch コンピューティング ノード間のデータ転送を最小にするにはジョブ レベルの準備タスクを使用し、ジョブの完了時にノードをクリーンアップするには解放タスクを使用します。
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 517ac0f612b9e5fc5909a7f0fe2ce088c9b367d9
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548699"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Batch コンピューティング ノードでのジョブ準備タスクとジョブ解放タスクの実行

 Azure Batch ジョブでは、タスクの実行前に何らかのセットアップが必要になることがよくあります。また、タスクが完了した後に、ジョブ実行後のメンテナンスが必要になることもよくあります。 場合によっては、タスクに共通する入力データをコンピューティング ノードにダウンロードしたり、ジョブの完了後にタスクの出力データを Azure Storage にアップロードしたりする必要があります。 **ジョブの準備**タスクと**ジョブの解放**タスクを使用して、これらの操作を実行できます。

## <a name="what-are-job-preparation-and-release-tasks"></a>ジョブの準備タスクと解放タスク
ジョブのタスクが実行される前に、1 つ以上のタスクの実行がスケジュールされているすべてのコンピューティング ノードで、ジョブの準備タスクが実行されます。 ジョブが完了すると、少なくとも 1 つのタスクを実行したプールの各ノードでジョブ解放タスクが実行されます。 通常の Batch タスクと同様に、ジョブの準備タスクまたは解放タスクが実行されるときに呼び出されるコマンド ラインを指定できます。

ジョブの準備タスクとジョブの解放タスクは、ファイルのダウンロード ([リソース ファイル][net_job_prep_resourcefiles])、管理者特権での実行、カスタム環境変数、最大実行期間、再試行回数、ファイルのリテンション期間などの使い慣れた Batch タスク機能を提供します。

以下のセクションでは、[Batch .NET][api_net] ライブラリの [JobPreparationTask][net_job_prep] クラスと [JobReleaseTask][net_job_release] クラスの使用方法について説明します。

> [!TIP]
> ジョブの準備タスクと解放タスクは、"共有プール" 環境で特に役に立ちます。この環境では、コンピューティング ノードのプールが異なるジョブの実行間で維持され、多くのジョブによって使用されます。
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>ジョブ準備タスクおよびジョブ解放タスクを使用するのに適した状況
ジョブの準備タスクと解放タスクは、次のような状況に適しています。

**共通のタスク データのダウンロード**

Batch ジョブでは、ジョブのタスクに対する入力として共通のデータ セットが必要になることがよくあります。 たとえば、毎日のリスク分析の計算では、市場データはジョブ固有ですが、そのジョブのすべてのタスクに共通です。 このような市場データはサイズが数ギガバイトになることも多く、各コンピューティング ノードに 1 回だけダウンロードして、そのノードで実行する各タスクがそれを使用できるようにする必要があります。 **ジョブの準備タスク** を使用して、ジョブの他のタスクの実行前に各ノードにこのデータをダウンロードします。

**ジョブとタスクの出力の削除**

ジョブ間でプールのコンピューティング ノードが使用停止されない "共有プール" 環境では、実行と実行の間でジョブ データの削除が必要になる場合があります。 場合によっては、ノードのディスク領域を節約したり、組織のセキュリティ ポリシーを満たしたりする必要があるためです。 ジョブの準備タスクによってダウンロードされたデータ、またはタスクの実行中に生成されたデータを削除するには、 **ジョブの解放タスク** を使用します。

**ログのリテンション期間**

タスクによって生成されるログ ファイルのコピーや、障害が発生したアプリケーションによって生成される可能性があるクラッシュ ダンプ ファイルの保持が必要な場合があります。 そのような場合は、**ジョブ解放タスク**を使用してこのデータを圧縮し、[Azure Storage][azure_storage] アカウントにアップロードします。

> [!TIP]
> ログ ファイルのほか、他のジョブやタスクの出力データを保持するもう 1 つの方法は、 [Azure Batch ファイル規則](batch-task-output.md) ライブラリを使用することです。
> 
> 

## <a name="job-preparation-task"></a>ジョブの準備タスク
ジョブのタスクを実行する前に、タスクの実行がスケジュールされている各コンピューティング ノードで、Batch によってジョブの準備タスクが実行されます。 既定では、Batch サービスはジョブの準備タスクが完了するまで待機してから、ノードでスケジュールされているタスクを実行します。 ただし、完了を待たないようにサービスを構成することもできます。 ノードが再起動するとジョブの準備タスクも再度実行されますが、この動作を無効にすることもできます。

ジョブの準備タスクは、タスクを実行するようにスケジュールされているノードでのみ実行されます。 これにより、ノードにタスクが割り当てられていない場合は、準備タスクが無駄に実行されることがなくなります。 これは、ジョブのタスク数がプール内のノード数より少ない場合に発生する可能性があります。 また、[同時実行タスクの実行](batch-parallel-node-tasks.md)が有効になっている場合も当てはまります。この場合、タスク数が同時実行可能なタスクの総数より少ないと、一部のノードがアイドル状態のままになります。 アイドル状態のノードでジョブ準備タスクを実行しないことで、データ転送の費用を削減できます。

> [!NOTE]
> [JobPreparationTask][net_job_prep_cloudjob] と [CloudPool.StartTask][pool_starttask] は異なります。JobPreparationTask が各ジョブの開始時に実行されるのに対し、StartTask はコンピューティング ノードが初めてプールに追加されたとき、または再起動したときにのみ実行されます。
> 
> 

## <a name="job-release-task"></a>ジョブの解放タスク
ジョブが完了とマークされると、少なくとも 1 つのタスクを実行したプールの各ノードでジョブ解放タスクが実行されます。 ジョブを完了済みとして指定するには、終了要求を発行します。 それに対し、Batch サービスはジョブの状態を " *終了中*" に設定し、ジョブに関連付けられているアクティブなタスクまたは実行中のタスクを終了してから、ジョブの解放タスクを実行します。 その後、ジョブは *完了* 状態に移行します。

> [!NOTE]
> ジョブを削除した場合もジョブ解放タスクが実行されます。 ただし、ジョブが既に終了している場合は、その後でジョブを削除しても解放タスクが再度実行されることはありません。
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Batch .NET でのジョブ準備タスクとジョブ解放タスク
ジョブの準備タスクを使用するには、[JobPreparationTask][net_job_prep] オブジェクトをジョブの [CloudJob.JobPreparationTask][net_job_prep_cloudjob] プロパティに割り当てます。 同様に、ジョブの解放タスクを設定するには、[JobReleaseTask][net_job_release] を初期化し、それをジョブの [CloudJob.JobReleaseTask][net_job_prep_cloudjob] プロパティに割り当てます。

次のコード スニペットで、`myBatchClient` は [BatchClient][net_batch_client] のインスタンス、`myPool` は Batch アカウント内の既存のプールです。

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobReleaseTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

前に述べたとおり、解放タスクはジョブの終了時または削除時に実行されます。 [JobOperations.TerminateJobAsync][net_job_terminate] を使用してジョブを終了します。 [JobOperations.DeleteJobAsync][net_job_delete] を使用してジョブを削除します。 通常は、タスクが完了したときか、定義したタイムアウトに達したときに、ジョブを終了または削除します。

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>GitHub 上のサンプル コード
ジョブの準備タスクとジョブの解放タスクの動作を確認するには、GitHub の [JobPrepRelease][job_prep_release_sample] サンプル プロジェクトをご覧ください。 このコンソール アプリケーションは次のことを行います。

1. 2 つのノードを含むプールを作成します。
2. ジョブ準備タスク、ジョブ解放タスク、標準タスクを含むジョブを作成します。
3. ノードの "共有" ディレクトリ内のテキスト ファイルに最初にノード ID を書き込むジョブ準備タスクを実行します。
4. 同じテキスト ファイルにタスク ID を書き込むタスクを各ノードで実行します。
5. すべてのタスクが完了したら (またはタイムアウトに達したら)、各ノードのテキスト ファイルの内容をコンソールに出力します。
6. ジョブが完了したら、ジョブ解放タスクを実行してノードからファイルを削除します。
7. タスクを実行した各ノードのジョブ準備タスクとジョブ解放タスクの終了コードを出力します。
8. ジョブまたはプールの削除を確認できるように実行を一時停止します。

サンプル アプリケーションからの出力は次のようになります。

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

> [!NOTE]
> 新しいプールにノードが作成されて起動されるタイミングは不定である (一部のノードだけ他のノードよりも早く、タスクを処理できる状態になることがある) ため、出力結果が異なる場合があります。 たとえばタスクの実行には時間がかからないので、ジョブのすべてのタスクがプール内のいずれか 1 つのノードによって実行される可能性もあります。 このとき、タスクを実行しなかったノードについては、ジョブの準備タスクとジョブの解放タスクが存在しません。
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Azure Portal でのジョブの準備タスクと解放タスクの確認
サンプル アプリケーションを実行する際、[Azure Portal][portal] を使用して、ジョブとそのタスクのプロパティを表示したり、ジョブのタスクによって変更された共有テキスト ファイルをダウンロードしたりできます。

以下のスクリーンショットは、サンプル アプリケーション実行後の Azure Portal の **[準備タスク]** ブレードです。 タスクの完了後 (ただし、ジョブとプールが削除される前)、*JobPrepReleaseSampleJob* プロパティに移動し、**[準備タスク]** または **[リリース タスク]** をクリックして、それらのプロパティを表示します。

![Job preparation properties in Azure portal][1]

## <a name="next-steps"></a>次の手順
### <a name="application-packages"></a>アプリケーション パッケージ
タスクの実行に使用するコンピューティング ノードは、ジョブの準備タスクのほか、Batch の [アプリケーション パッケージ](batch-application-packages.md) 機能を使用して準備することもできます。 この機能は特に、インストーラーの実行を必要としないアプリケーションや、多数 (100 個超) のファイルを含んだアプリケーション、厳密なバージョン管理が要求されるアプリケーションをデプロイする場合に利便性を発揮します。

### <a name="installing-applications-and-staging-data"></a>アプリケーションとステージング データのインストール
この MSDN フォーラムの投稿では、タスクの実行に使用するノードの準備方法の概要をいくつか示しています。

[Installing Applications and staging data on Batch compute nodes (Batch コンピューティング ノードでのアプリケーションとステージング データのインストール)][forum_post]

Azure Batch チームのメンバーによって投稿されたもので、コンピューティング ノードへのアプリケーションとデータのデプロイに使用できるいくつかの手法について説明しています。

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_prep_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.resourcefiles.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png
