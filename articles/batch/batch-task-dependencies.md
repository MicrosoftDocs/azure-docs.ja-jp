---
title: タスクを実行するためにタスクの依存関係を作成する
description: MapReduce に見られるようなビッグ データのワークロードを Azure Batch で処理することを目的として、他のタスクの完了に依存するタスクを作成します。
ms.topic: how-to
ms.date: 12/28/2020
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: ef05a98fffc3c0684ad0fa29f2f9f039b388f5ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97803937"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>タスクの依存関係を作成して、他のタスクに依存するタスクを実行する

Batch のタスク依存関係を使用し、コンピューティング ノードで 1 つ以上の親タスクが完了した後に実行されるようにスケジューリングされたタスクを作成します。 たとえば、並列実行される複数の独立したタスクを使って 3D ムービーの各フレームをレンダリングするジョブを作成できます。 最後のタスク ("マージ タスク") では、すべてのフレームが正常にレンダリングされた後にのみ、そのレンダリング済みのフレームをマージしてムービーを完成させます。

タスクの依存関係は、たとえば次のようなシナリオで役立ちます。

- クラウドでの MapReduce 形式のワークロード。
- 有向非巡回グラフ (DAG) としてデータ処理タスクを表すことのできるジョブ。
- 各タスクが次のタスクの開始前に完了している必要がある、レンダリング前およびレンダリング後プロセス。
- 下流のタスクが上流タスクの出力に依存する、その他すべてのジョブ。

既定では、依存タスクは、親タスクが正常に完了した後にのみ実行されるようにスケジューリングされます。 既定の動作をオーバーライドし、親タスクが失敗したときにタスクを実行する、[依存関係アクション](#dependency-actions)を任意で指定できます。

## <a name="task-dependencies-with-batch-net"></a>Batch .NET でのタスクの依存関係

この記事では、[Batch .NET](/dotnet/api/microsoft.azure.batch) ライブラリを使用したタスクの依存関係の構成方法について説明します。 まず、ジョブで[タスクの依存関係を有効にする](#enable-task-dependencies)方法を説明した後、[依存関係を伴うタスクを構成する](#create-dependent-tasks)方法を紹介します。 親が失敗した場合に依存タスクを実行する、依存関係アクションを指定する方法についても説明します。 最後に、Batch でサポートされる [依存関係のシナリオ](#dependency-scenarios) について取り上げます。

## <a name="enable-task-dependencies"></a>タスクの依存関係を有効にする

Batch アプリケーションでタスクの依存関係を使用するには、まず、タスクの依存関係を使用するジョブを構成する必要があります。 Batch .NET では、[CloudJob](/dotnet/api/microsoft.azure.batch.cloudjob) の [UsesTaskDependencies](/dotnet/api/microsoft.azure.batch.cloudjob.usestaskdependencies) プロパティを `true` に設定することによって有効にします。

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

前のコード スニペットでは、"batchClient" は、[BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) クラスのインスタンスです。

## <a name="create-dependent-tasks"></a>依存タスクの作成

1 つ以上の親タスクの完了に依存するタスクを作成するには、タスクが他のタスクに "依存" するよう指定します。 Batch .NET では、[TaskDependencies](/dotnet/api/microsoft.azure.batch.taskdependencies) クラスのインスタンスを使って [CloudTask.DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) プロパティを構成します。

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

このコード スニペットでは、"Flowers" というタスク ID の依存タスクを作成しています。 "Flowers" タスクは "Rain" タスクと "Sun" タスクに依存します。 "Flowers" タスクは、"Rain" タスクと "Sun" タスクが正常に完了した後にのみコンピューティング ノードで実行されるようにスケジューリングされます。

> [!NOTE]
> 既定では、タスクは completed 状態になり、その終了コードが `0` であるときに正常に完了したと見なされます。 Batch .NET では、[CloudTask.State](/dotnet/api/microsoft.azure.batch.cloudtask.state) プロパティの値が `Completed` で、なおかつ CloudTask の [TaskExecutionInformation.ExitCode](/dotnet/api/microsoft.azure.batch.taskexecutioninformation.exitcode) プロパティの値が `0` であることを意味します。 これを変更する方法については、「[依存関係アクション](#dependency-actions)」セクションを参照してください。

## <a name="dependency-scenarios"></a>依存関係のシナリオ

Azure Batch で利用できる基本的なタスクの依存関係には、一対一、一対多、タスク ID 範囲という 3 つのシナリオがあります。 これら 3 つのシナリオを組み合わせることで、第 4 のシナリオ (多対多) を実現することもできます。

| シナリオ&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 例 | 図 |
|:---:| --- | --- |
|  [一対一](#one-to-one) |*taskB* は *taskA* に依存 <p/> *taskB* は、*taskA* が正常に完了するまで実行されないようにスケジューリングされる |:::image type="content" source="media/batch-task-dependency/01_one_to_one.png" alt-text="一対一のタスク依存関係シナリオを示す図。"::: |
|  [[一対多]](#one-to-many) |*taskC* は *taskA* と *taskB* の両方に依存 <p/> *taskC* は、*taskA* と *taskB* の両方が正常に完了するまで実行されないようにスケジューリングされる |:::image type="content" source="media/batch-task-dependency/02_one_to_many.png" alt-text="一対多のタスク依存関係シナリオを示す図。"::: |
|  [タスク ID の範囲](#task-id-range) |*taskD* は、一連のタスク範囲に依存 <p/> *taskD* は、ID *1* ～ *10* のタスクが正常に完了するまで実行されないようにスケジューリングされる |:::image type="content" source="media/batch-task-dependency/03_task_id_range.png" alt-text="タスク ID 範囲のタスク依存関係シナリオを示す図。"::: |

> [!TIP]
> タスク C、D、E、F がそれぞれタスク A と B に依存するような **多対多** の関係を作成できます。たとえば下流の複数のタスクが上流にある複数のタスクの出力に依存するような、前処理を並列実行する状況で有効活用できます。
> 
> このセクションの例では、依存タスクは親タスクが正常に完了した後にのみ実行されます。 この動作は、依存タスクの既定の動作です。 既定の動作をオーバーライドする[依存関係アクション](#dependency-actions)を指定すると、親タスクが失敗した後に依存タスクを実行できます。

### <a name="one-to-one"></a>一対一

一対一の依存関係では、タスクは 1 つの親タスクの正常な完了に依存します。 この依存関係を作成するには、[CloudTask.DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) プロパティに値を設定する際、[TaskDependencies.OnId](/dotnet/api/microsoft.azure.batch.taskdependencies.onid) 静的メソッドに 1 つのタスク ID を渡します。

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>一対多

一対多の依存関係では、タスクは複数の親タスクの完了に依存します。 この依存関係を作成するには、[CloudTask.DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) プロパティに値を設定する際、[TaskDependencies.OnIds](/dotnet/api/microsoft.azure.batch.taskdependencies.onids) 静的メソッドにタスク ID のコレクションを渡します。

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

### <a name="task-id-range"></a>タスク ID の範囲

親タスクの範囲への依存関係では、タスクは ID が特定の範囲内にあるタスクの完了に依存します。
この依存関係を作成するには、[CloudTask.DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) プロパティに値を設定するときに、その範囲の最初のタスク ID と最後のタスク ID を [TaskDependencies](/dotnet/api/microsoft.azure.batch.taskdependencies.onidrange) 静的メソッドに渡します。

> [!IMPORTANT]
> 依存関係にタスク ID の範囲を使用した場合、その範囲によって選択されるのは、整数値を表す ID を持つタスクだけです。 たとえば、範囲 `1..10` ではタスク `3` と `7` が選択されますが、`5flamingoes` は選択されません。
>
> 範囲の依存関係を評価するときに先頭のゼロは重要ではないため、`4`、`04`、`004` の文字列識別子を持つタスクはすべて範囲 *内* になり、それらはすべてタスク `4` として扱われます。そのため、最初に完了したものが依存関係を満たします。
>
> 範囲内のすべてのタスクが、正常に完了するか、または **Satisfy** に設定された [依存関係アクション](#dependency-actions)に指定されているエラーで終了することによって、依存関係を満たしている必要があります。

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="dependency-actions"></a>依存関係アクション

既定では、依存タスクまたは一連の依存タスク は、親タスクが正常に完了した後にのみ実行されます。 シナリオによっては、親タスクが失敗した後でも依存タスクを実行したい場合があります。 依存関係アクションを指定すると、既定の動作をオーバーライドできます。

依存関係アクションは、親タスクの成功または失敗に基づいて、依存タスクを実行対象にするかどうかを指定します。 たとえば、依存タスクが、上流タスクの完了によって得られるデータを待機しているとします。 上流タスクが失敗しても、古いデータを使用して依存タスクを実行できる場合があります。 その場合は、依存関係アクションで、親タスクの失敗にかかわらずタスクを実行対象にするよう指定できます。

依存関係アクションは、親タスクの終了条件に基づきます。 次のどの終了条件に対しても依存関係アクションを指定できます。

- 前処理エラーが発生したとき。
- ファイルのアップロード エラーが発生したとき。 **exitCodes** または **exitCodeRanges** で指定された終了コードでタスクが終了し、その後ファイルのアップロード エラーが発生した場合、その終了コードにより指定されたアクションが優先されます。
- **ExitCodes** プロパティで定義された終了コードでタスクが終了したとき。
- **ExitCodeRanges** プロパティで定義された範囲内の終了コードでタスクが終了したとき。
- 既定のケースで、**ExitCodes** および **ExitCodeRanges** で定義されていない終了コードでタスクが終了した、または前処理エラーでタスクが終了し、**PreProcessingError** プロパティが設定されていない、またはファイルのアップロード エラーでタスクが失敗し、**FileUploadError** プロパティが設定されていない場合。 

.NET の場合、これらの条件の詳細については、[ExitConditions](/dotnet/api/microsoft.azure.batch.exitconditions) クラスを参照してください。

.NET で依存関係アクションを指定するには、終了条件の [ExitOptions.DependencyAction](/dotnet/api/microsoft.azure.batch.exitoptions.dependencyaction) プロパティを次のいずれかに設定します。

- **Satisfy**:指定したエラーで親タスクが終了した場合に依存タスクが実行対象になります。
- **ブロック**: 依存タスクは実行できないことを示します。

**DependencyAction** プロパティの既定の設定は、終了コード 0 に対しては **Satisfy**、その他のすべての終了条件に対しては **Block** です。

次のコード スニペットでは、親タスクの **DependencyAction** プロパティを設定します。 親タスクが前処理エラーで終了した場合、または指定したエラー コードで終了した場合は、依存タスクがブロックされます。 親タスクがその他の 0 以外のエラーで終了した場合は、依存タスクが実行対象となります。

```csharp
// Task A is the parent task.
new CloudTask("A", "cmd.exe /c echo A")
{
    // Specify exit conditions for task A and their dependency actions.
    ExitConditions = new ExitConditions
    {
        // If task A exits with a pre-processing error, block any downstream tasks (in this example, task B).
        PreProcessingError = new ExitOptions
        {
            DependencyAction = DependencyAction.Block
        },
        // If task A exits with the specified error codes, block any downstream tasks (in this example, task B).
        ExitCodes = new List<ExitCodeMapping>
        {
            new ExitCodeMapping(10, new ExitOptions() { DependencyAction = DependencyAction.Block }),
            new ExitCodeMapping(20, new ExitOptions() { DependencyAction = DependencyAction.Block })
        },
        // If task A succeeds or fails with any other error, any downstream tasks become eligible to run 
        // (in this example, task B).
        Default = new ExitOptions
        {
            DependencyAction = DependencyAction.Satisfy
        }
    }
},
// Task B depends on task A. Whether it becomes eligible to run depends on how task A exits.
new CloudTask("B", "cmd.exe /c echo B")
{
    DependsOn = TaskDependencies.OnId("A")
},
```

## <a name="code-sample"></a>コード サンプル

GitHub の [TaskDependencies](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies) サンプル プロジェクトは次を示します。

- ジョブでタスクの依存関係を有効にする方法。
- 他のタスクに依存するタスクを作成する方法。
- コンピューティング ノードのプールでそれらのタスクを実行する方法

## <a name="next-steps"></a>次のステップ

- Batch の [アプリケーション パッケージ](batch-application-packages.md)機能の詳細を確認してください。コンピューティング ノード上でタスクを通じて実行するアプリケーションのデプロイとバージョン管理が簡単になります。
- [ジョブとタスクのエラー チェック](batch-job-task-error-checking.md)の詳細を確認してください。
