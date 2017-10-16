---
title: "タスクを状態別にカウントすることでジョブの進行状況を監視する - Azure Batch | Microsoft Docs"
description: "Get Task Counts 操作を呼び出してジョブのタスクをカウントすることで、ジョブの進行状況を監視します。 アクティブなタスク、実行中のタスク、完了したタスク、成功したタスク、および失敗したタスクの数を取得できます。"
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 08/02/2017
ms.author: tamram
ms.openlocfilehash: ceff59d7063b60a1344a47489d3d73e0e8ee07df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="count-tasks-by-state-to-monitor-a-jobs-progress-preview"></a>タスクを状態別にカウントしてジョブの進行状況を監視する (プレビュー)

Azure Batch には、タスクの実行時にジョブの進行状況を監視するための効率的な方法が用意されています。 [Get Task Counts][rest_get_task_counts] 操作を呼び出して、状態がアクティブ、実行中、または完了であるタスクの数と、成功または失敗したタスクの数を検出できます。 各状態のタスクの数をカウントすることによって、ジョブの進行状況をより簡単にユーザーに表示したり、ジョブに影響を与える可能性がある予期しない遅延や障害を検出したりできます。

> [!IMPORTANT]
> Get Task Counts 操作は現在プレビュー段階であり、Azure Government、Azure China、および Azure Germany ではまだ利用できません。 
>
>

## <a name="how-tasks-are-counted"></a>タスクのカウント方法

Get Task Counts 操作は、次に示すように、タスクを状態別にカウントします。

- キューに置かれ、実行可能であるが、その時点でコンピューティング ノードに割り当てられていないタスクは、**アクティブ**としてカウントされます。 まだ完了していない親タスクに依存しているタスクも、**アクティブ**としてカウントされます。 タスクの依存関係の詳細については、「[タスクの依存関係を作成して、他のタスクに依存するタスクを実行する](batch-task-dependencies.md)」を参照してください。 
- コンピューティング ノードに割り当てられているが、まだ完了していないタスクは、**実行中**としてカウントされます。 [Get information about a task][rest_get_task] 操作で指示される状態が `preparing` または `running` のいずれかであるタスクは、**実行中**としてカウントされます。
- 実行される資格がなくなったタスクは、**完了**としてカウントされます。 **完了**としてカウントされるタスクは、通常は、正常に完了しているか、失敗したが再試行の上限を超えているタスクです。 

Get Task Counts 操作は、成功または失敗したタスクの数も報告します。 Batch は、[executionInfo][https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task#executionInfo] プロパティの **result** プロパティをチェックすることで、タスクが成功したか失敗したかを判断します。

    - タスクは、タスクの実行結果が `success` の場合に **成功**としてカウントされます。
    - タスクは、タスクの実行結果が `failure` の場合に **失敗**としてカウントされます。

タスクの状態の詳細については、[タスクについての情報の取得][rest_get_task]に関する記事を参照してください。

次の .NET コード サンプルは、状態別のタスクの数を取得する方法を示しています。 

```csharp
var taskCounts = await batchClient.JobOperations.GetTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
Console.WriteLine("ValidationStatus: {0}", taskCounts.ValidationStatus);
```

> [!NOTE]
> REST やその他のサポートされている言語で同様のパターンを使用して、ジョブのタスクの数を取得できます。 
> 
> 

## <a name="consistency-checking-for-task-counts"></a>タスク数の整合性チェック

Batch サービスは、非同期分散システムの複数の部分からデータを収集することでタスクの数を集計します。 タスクの数が正確であることを保証するため、Batch では、システムの複数のコンポーネントに対して整合性チェックを実行することで、状態数の追加検証を行っています。 Batch は、ジョブのタスク数が 200,000 未満である限り、整合性チェックを実行します。 めったにありませんが、整合性チェックでエラーが検出された場合、Batch は、整合性チェックの結果に基づいて Get Tasks Counts 操作の結果を修正します。 整合性チェックは、ソリューションが必要とする正確な情報を Get Task Counts 操作で確実に取得できるようにするための追加手段です。

応答内の **ValidationStatus** プロパティは、Batch が整合性チェックを実行しているかどうかを示します。 Batch がシステムに保持されている実際の状態数との照合を実行できなかった場合、**validationStatus** プロパティは `unvalidated` に設定されます。 パフォーマンス上の理由から、ジョブに 200,000 以上のタスクが含まれている場合は、整合性チェックは実行されません。このため、**validationStatus** プロパティが `unvalidated` に設定される可能性があります。 ただし、この場合でも、データ損失の可能性は非常に低く、発生した場合で失われるデータは非常にわずかであるため、タスクの数は必ずしも間違っているわけではありません。 

タスクの状態が変わると、集計パイプラインが数秒以内にその変更を処理します。 Get Task Counts 操作は、更新されたタスクの数をその期間内に反映します。 ただし、集計パイプラインがタスクの状態の変更を見逃した場合、その変更は次回の検証パスまで登録されることはありません。 この期間中、見逃されたイベントのせいでタスクの数はわずかに不正確になる可能性がありますが、それは次回の検証パスで修正されます。

## <a name="best-practices-for-counting-a-jobs-tasks"></a>ジョブのタスクをカウントするためのベスト プラクティス

Get Task Counts 操作の呼び出しは、ジョブの状態別のタスクの基本的なカウントを返す最も効率的な方法です。 Batch サービスのバージョン 2017-06-01.5.1 を使用している場合は、Get Task Counts を使用するコードを記述するか、使用するようにコードを更新することをお勧めします。

Get Task Counts 操作は 2017-06-01.5.1.1 より前のバージョンの Batch サービスでは使用できません。 古いバージョンのサービスを使用している場合は、代わりにリスト クエリを使用してジョブ内のタスクをカウントしてください。 詳細については、「[効率的に Batch リソースを一覧表示するクエリを作成する](batch-efficient-list-queries.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* Batch サービスの概念と機能の詳細については、[Batch 機能の概要](batch-api-basics.md)に関するページを参照してください。 記事では、プール、コンピューティング ノード、ジョブ、タスクなどの主要な Batch リソースと、サービスの機能の概要について説明しています。
* [Batch .NET クライアント ライブラリ](batch-dotnet-get-started.md)または [Python](batch-python-tutorial.md) を使用した Batch 対応アプリケーションの開発に関する基本事項を確認してください。 これらの入門記事では、Batch サービスを使用して複数のコンピューティング ノードでワークロードを実行する実用アプリケーションの開発手順を説明しています。


[rest_get_task_counts]: https://docs.microsoft.com/rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_task]: https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task
[rest_list_tasks]: https://docs.microsoft.com/rest/api/batchservice/list-the-tasks-associated-with-a-job
