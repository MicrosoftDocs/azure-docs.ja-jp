---
title: 効率的なリスト クエリの設計
description: Batch のリソース (プール、ジョブ、タスク、コンピューティング ノードなど) に関する情報を要求する際のクエリにフィルターを適用することでパフォーマンスを高めます。
ms.topic: how-to
ms.date: 06/18/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 3a767cc8ae3c8c48e1e40e0735c33fa807ba0015
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88933516"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>効率的に Batch リソースを一覧表示するクエリを作成する

ほぼすべての Batch アプリケーションでは、Batch サービスに対して問い合わせを行う操作 (各種の監視など) が定期的に、それもかなりの頻度で必要となります。 たとえば、ジョブのキューにタスクが残っているかどうかを調べるためには、ジョブ内のすべてのタスクに関するデータを取得する必要があります。 プール内のノードの状態を調べるためには、そのプールに存在するすべてのノードのデータを取得する必要があります。 この記事では、このようなクエリを最も効率的な方法で実行する方法について説明します。

[Batch .NET](/dotnet/api/microsoft.azure.batch) ライブラリを使ってジョブ、タスク、コンピューティング ノード、その他のリソースを照会するときにサービスから返されるデータの量を減らすことで、Azure Batch アプリケーションのパフォーマンスを向上させることができます。

> [!NOTE]
> Batch サービスは、一般的なシナリオ (ジョブ内のタスクのカウントと Batch プール内のコンピューティング ノードのカウント) 用の API をサポートしています。 これらに対してリスト クエリを使用する代わりに、[Get Task Counts](/rest/api/batchservice/job/gettaskcounts) 操作と [List Pool Node Counts](/rest/api/batchservice/account/listpoolnodecounts) 操作を呼び出すことができます。 これらの操作は、リスト クエリよりも効率的ですが、常に最新であるとは限らない、より限定された情報を返します。 詳細については、[状態ごとのタスクとコンピューティング ノードのカウント](batch-get-resource-counts.md)に関するページを参照してください。

## <a name="specify-a-detail-level"></a>詳細レベルを指定する

運用環境の Batch アプリケーションでは、ジョブ、タスク、コンピューティング ノードのようなエンティティは数千単位になることがあります。 これらのリソースに関する情報を要求する場合、クエリのたびに Batch サービスからアプリケーションに大量のデータが送信される可能性があります。 クエリによって返される情報の項目数と種類を制限することで、クエリの時間を短縮し、それによってアプリケーションのパフォーマンスを向上させることができます。

次の [Batch .NET](/dotnet/api/microsoft.azure.batch) API コード スニペットは、ジョブに関連付けられている "*すべて*" のタスクと、各タスクの "*すべて*" のプロパティを取得します。

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

ただしリスト クエリは、"詳細レベル" を適用した方が、はるかに効率的に実行できます。 具体的には、[ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) オブジェクトを [JobOperations.ListTasks](/dotnet/api/microsoft.azure.batch.joboperations) メソッドに指定します。 次のスニペットでは、完了したタスクの ID、コマンド ライン、コンピューティング ノード情報のプロパティだけが返されます。

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

この例のシナリオでジョブに何千ものタスクがある場合、通常は 2 つ目のクエリの結果は最初のものより速く返されます。 Batch .NET API を使って項目をリストするときの ODATADetailLevel の使い方については、 [以下](#efficient-querying-in-batch-net)で詳しく説明しています。

> [!IMPORTANT]
> アプリケーションの最大限の効率とパフォーマンスを確保するために、.NET API リスト呼び出しには常に ODATADetailLevel オブジェクトを指定することを強くお勧めします。 詳細レベルを指定することによって、Batch サービスの応答時間の短縮、ネットワーク使用率の改善、クライアント アプリケーションによるメモリ使用量の最小化といった効果が期待できます。

## <a name="filter-select-and-expand"></a>filter、select、expand

[Batch .NET](/dotnet/api/microsoft.azure.batch) API と [Batch REST](/rest/api/batchservice/) API には、リストとして返される項目の数と、クエリごとに返される情報の量を減らす機能が用意されています。 これを行うには、リスト クエリの実行時に **filter**、**select**、**expand 文字列** を指定します。

### <a name="filter"></a>Assert

filter 文字列は、返される項目の数を減らす式です。 たとえば、あるジョブの実行中のタスクのみ、またはタスクの実行準備が完了しているコンピューティング ノードのみをリストできます。

filter 文字列は、プロパティ名、演算子、値で構成される 1 つ以上の式から成ります。 指定できるプロパティは、クエリする各エンティティ型に固有です。各エンティティでサポートされる演算子も同様です。  論理演算子の `and` と `or` を使用して、複数の式を結合できます。

たとえば、実行中の "レンダリング" タスクのみをリストする場合の filter 文字列は `(state eq 'running') and startswith(id, 'renderTask')`となります。

### <a name="select"></a>Select

select 文字列は、各項目に対して返されるプロパティの値を制限します。 コンマで区切られたプロパティ名の一覧を指定すると、項目に対してそれらのプロパティ値のみがクエリ結果で返されます。照会するエンティティ型の任意のプロパティを指定できます。

たとえば、各タスクで 3 つのプロパティのみを返すように指定する場合の select 文字列は `id, state, stateTransitionTime`になります。

### <a name="expand"></a>expand

expand 文字列は、特定の情報を取得するために必要な API 呼び出しの数を減らします。 expand 文字列を使用すると、1 つの API 呼び出しで各項目の情報をより多く取得できます。 最初にエンティティの一覧を取得し、次に一覧の各項目の情報を要求する代わりに、expand 文字列を使用して、1 つの API 呼び出しで同じ情報を取得します。これにより、API 呼び出しの数が減るため、パフォーマンスの向上につながります。

select 文字列と同様に、expand 文字列はリスト クエリ結果に特定のデータを含めるかどうかを制御します。 すべてのプロパティが必要なときに select 文字列が指定されていない場合は、expand 文字列を使用して統計情報を取得する *必要があります* 。 select 文字列を使用してプロパティのサブセットを取得する場合は、 `stats` を select 文字列に指定でき、expand 文字列を指定する必要はありません。

expand 文字列がサポートされるのは、ジョブ、ジョブのスケジュール、タスク、およびプールをリストする場合のみです。 現在サポートされているのは統計情報のみです。

たとえば、一覧の各項目の統計情報が返されるように指定する場合、expand 文字列は `stats`のようになります。

> [!NOTE]
> 3 種類のクエリ文字列 (filter、select、expand) のいずれかを組み立てる際に、プロパティ名および大文字と小文字が REST API 要素と一致することを確認する必要があります。 たとえば、.NET [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) クラスを使用する場合は、.NET プロパティが [CloudTask.State](/dotnet/api/microsoft.azure.batch.cloudtask.state#Microsoft_Azure_Batch_CloudTask_State) であっても、**State** の代わりに **state** を指定する必要があります。 .NET と REST API 間のプロパティ マッピングについては、以下の表を参照してください。

### <a name="rules-for-filter-select-and-expand-strings"></a>filter、select、expand 文字列の規則

- filter、select、および expand 文字列に指定するプロパティ名は、[Batch .NET](/dotnet/api/microsoft.azure.batch) または他の Batch SDK シリーズのいずれかを使用している場合でも、[Batch REST](/rest/api/batchservice/) API の場合と同じように表記する必要があります。
- すべてのプロパティ名では大文字と小文字が区別されますが、プロパティの値は大文字と小文字を区別しません。
- 日付/時刻文字列は、次の 2 つの形式のいずれかを使用でき、前に `DateTime`を付ける必要があります。
  
  - W3C-DTF 形式の例: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  - RFC 1123 形式の例: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
- ブール文字列は、`true` と `false` のいずれかです。
- 無効なプロパティまたは演算子を指定すると、 `400 (Bad Request)` エラーが発生します。

## <a name="efficient-querying-in-batch-net"></a>Batch .NET の効率的クエリ

[Batch .NET](/dotnet/api/microsoft.azure.batch) API 内で、[ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) クラスを使用して、リスト操作に対して filter、select、expand 文字列を指定します。 ODataDetailLevel クラスには、3 つのパブリック文字列プロパティがあり、それらをコンストラクターで指定するか、オブジェクトに直接設定できます。 そのうえで、[ListPools](/dotnet/api/microsoft.azure.batch.pooloperations)、[ListJobs](/dotnet/api/microsoft.azure.batch.joboperations)、[ListTasks](/dotnet/api/microsoft.azure.batch.joboperations) などの各種リスト操作のパラメーターとして、ODataDetailLevel オブジェクトを渡します。

- [ODATADetailLevel.FilterClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.filterclause): 返される項目の数を制限します。
- [ODATADetailLevel.SelectClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.selectclause): 各項目で返されるプロパティ値を指定します。
- [ODATADetailLevel.ExpandClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.expandclause): 項目ごとに個別の呼び出しではなく、1 つの API 呼び出しですべての項目のデータを取得します。

次のコード スニペットでは Batch .NET API を使用して、特定のプール セットの統計値を Batch サービスに効率的に問い合わせます。 このシナリオでは、Batch ユーザーにはテスト プールと運用プールの両方が与えられています。 テスト プールの ID 接頭辞は "test" であり、運用プールの ID 接頭辞は "prod" です。 このスニペットで、 *myBatchClient* は [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) クラスの適切に初期化されたインスタンスです。

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [!TIP]
> Select 句と Expand 句で構成される [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) のインスタンスも、返されるデータの量を制限するために、[PoolOperations.GetPool](/dotnet/api/microsoft.azure.batch.pooloperations.getpool#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__) などの適切な Get メソッドに渡すことができます。

## <a name="batch-rest-to-net-api-mappings"></a>Batch REST から NET API へのマッピング

filter、select、および expand 文字列のプロパティ名は、REST API の対応部分の名前および大文字と小文字の両方を反映している必要があります。 次の表では、.NET および REST API の対応部分の間のマッピングを示します。

### <a name="mappings-for-filter-strings"></a>filter 文字列のマッピング

- **.NET リスト メソッド**:この列の各 .NET API メソッドは、[ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) オブジェクトをパラメーターとして受け付けます。
- **REST リスト要求**:この列のリンク先となる各 REST API ページには、*filter* 文字列で許可されるプロパティと操作を指定する表が含まれています。 [ODATADetailLevel.FilterClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.filterclause) 文字列を構築するときに、これらのプロパティ名と操作を使用します。

| .NET リスト メソッド | REST リスト要求 |
| --- | --- |
| [CertificateOperations.ListCertificates](/dotnet/api/microsoft.azure.batch.certificateoperations) |[アカウントの証明書を一覧表示する](/rest/api/batchservice/certificate/list) |
| [CloudTask.ListNodeFiles](/dotnet/api/microsoft.azure.batch.cloudtask) |[タスクに関連付けられているファイルを一覧表示する](/rest/api/batchservice/file/listfromtask) |
| [JobOperations.ListJobPreparationAndReleaseTaskStatus](/dotnet/api/microsoft.azure.batch.joboperations) |[ジョブのジョブ準備タスクとジョブ リリース タスクの状態を一覧表示する](/rest/api/batchservice/job/listpreparationandreleasetaskstatus) |
| [JobOperations.ListJobs](/dotnet/api/microsoft.azure.batch.joboperations) |[アカウントのジョブを一覧表示する](/rest/api/batchservice/job/list) |
| [JobOperations.ListNodeFiles](/dotnet/api/microsoft.azure.batch.joboperations) |[ノードのファイルを一覧表示する](/rest/api/batchservice/file/listfromcomputenode) |
| [JobOperations.ListTasks](/dotnet/api/microsoft.azure.batch.joboperations) |[ジョブに関連付けられているタスクを一覧表示する](/rest/api/batchservice/task/list) |
| [JobScheduleOperations.ListJobSchedules](/dotnet/api/microsoft.azure.batch.jobscheduleoperations) |[アカウントのジョブ スケジュールを一覧表示する](/rest/api/batchservice/jobschedule/list) |
| [JobScheduleOperations.ListJobs](/dotnet/api/microsoft.azure.batch.jobscheduleoperations) |[ジョブ スケジュールに関連付けられているジョブを一覧表示する](/rest/api/batchservice/job/listfromjobschedule) |
| [PoolOperations.ListComputeNodes](/dotnet/api/microsoft.azure.batch.pooloperations) |[プールのコンピューティング ノードを一覧表示する](/rest/api/batchservice/computenode/list) |
| [PoolOperations.ListPools](/dotnet/api/microsoft.azure.batch.pooloperations) |[アカウントのプールを一覧表示する](/rest/api/batchservice/pool/list) |

### <a name="mappings-for-select-strings"></a>select 文字列のマッピング

- **Batch .NET の型**:Batch .NET API の型です。
- **REST API のエンティティ**:この列内の各ページには、その型の REST API プロパティ名を列挙した表が 1 つ以上含まれています。 *select* 文字列を構築する際に、これらのプロパティ名が使用されます。 [ODATADetailLevel.SelectClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.selectclause) 文字列を構築するときに、これらの同じプロパティ名を使用します。

| Batch .NET の型 | REST API のエンティティ |
| --- | --- |
| [[MSSQLSERVER のプロトコルのプロパティ]](/dotnet/api/microsoft.azure.batch.certificate) |[証明書に関する情報を取得する](/rest/api/batchservice/certificate/get) |
| [CloudJob](/dotnet/api/microsoft.azure.batch.cloudjob) |[ジョブに関する情報を取得する](/rest/api/batchservice/job/get) |
| [CloudJobSchedule](/dotnet/api/microsoft.azure.batch.cloudjobschedule) |[ジョブ スケジュールに関する情報を取得する](/rest/api/batchservice/jobschedule/get) |
| [ComputeNode](/dotnet/api/microsoft.azure.batch.computenode) |[ノードに関する情報を取得する](/rest/api/batchservice/computenode/get) |
| [CloudPool](/dotnet/api/microsoft.azure.batch.cloudpool) |[プールに関する情報を取得する](/rest/api/batchservice/pool/get) |
| [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) |[タスクに関する情報を取得する](/rest/api/batchservice/task/get) |

## <a name="example-construct-a-filter-string"></a>例: filter 文字列の構築

[ODATADetailLevel.FilterClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.filterclause) の filter 文字列を構築する場合は、「filter 文字列のマッピング」に示した表を参照して、実行するリスト操作に対応する REST API のドキュメント ページを見つけます。 そのページの最初の複数行の表に、フィルター可能なプロパティとサポートされている演算子が表示されます。 たとえば、終了コードがゼロ以外のタスクをすべて取得する場合は、[ジョブに関連付けられているタスクの一覧表示](/rest/api/batchservice/task/list)に関するページのこの行で、適用可能なプロパティ文字列と許可される演算子を指定します。

| プロパティ | 許可される操作 | Type |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

したがって、終了コードがゼロ以外のタスクをすべて一覧表示するための filter 文字列は次のようになります。

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>例: select 文字列の構築

[ODATADetailLevel.SelectClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.selectclause) を構築する場合は、「select 文字列のマッピング」に示した表を参照して、一覧表示するエンティティのタイプに対応する REST API ページに移動します。 そのページの最初の複数行の表に、選択可能なプロパティとサポートされている演算子が表示されます。 たとえば、リストの各タスクの ID とコマンド ラインのみを取得する場合は、[タスクに関する情報の取得](/rest/api/batchservice/task/get)に関するページの該当する表でこれらの行を見つけます。

| プロパティ | Type | Notes |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

リストされている各タスクの ID とコマンド ラインのみを含めるための select 文字列は次のようになります。

`id, commandLine`

## <a name="code-samples"></a>コード サンプル

### <a name="efficient-list-queries-code-sample"></a>効率的なリスト クエリ コードのサンプル

GitHub の [EfficientListQueries](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries) サンプル プロジェクトは、効率的なリスト クエリがアプリケーションのパフォーマンスにどのように影響するかを示しています。 この C# コンソール アプリケーションは、大量のタスクを作成してジョブに追加します。 その後、[JobOperations.ListTasks](/dotnet/api/microsoft.azure.batch.joboperations) メソッドを何度か呼び出します。その際に渡す [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) オブジェクトの各種プロパティの値を設定で変更することによって、取得するデータ量を変化させています。 次のような出力が表示されます。

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

経過時間を見るとわかるように、プロパティと返される項目の数を制限することで、クエリの応答時間を大幅に短縮できます。 このサンプル プロジェクトと他のサンプル プロジェクトは、GitHub の [azure-batch-samples](https://github.com/Azure-Samples/azure-batch-samples) リポジトリにあります。

### <a name="batchmetrics-library-and-code-sample"></a>BatchMetrics ライブラリとコード サンプル

上記の EfficientListQueries コード サンプルに加えて、[BatchMetrics](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/BatchMetrics) サンプル プロジェクトには、Batch API を使って Azure Batch ジョブの進行状況を効率的に監視する方法が紹介されています。

[BatchMetrics](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/BatchMetrics) サンプルには、独自のプロジェクトに組み込むことのできる .NET クラス ライブラリ プロジェクトのほか、ライブラリの使い方を実践的に紹介する簡単なコマンドライン プログラムが含まれています。

プロジェクト内のサンプル アプリケーションでは、次の操作が示されています。

1. 必要なプロパティだけをダウンロードするために特定の属性を選択します。
2. 前回のクエリ以降に行われた変更のみをダウンロードするために状態遷移の時刻を条件としてフィルタリングします。

たとえば BatchMetrics ライブラリには、以下のメソッドが存在します。 このメソッドは、照会したエンティティの `id` プロパティと `state` プロパティのみを取得するよう指定する ODATADetailLevel を返します。 この ODATADetailLevel には、パラメーターに指定された `DateTime` 以降に状態が変化したエンティティのみを取得するための指定も含まれています。

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>次のステップ

- [コンカレント ノード タスクで Azure Batch コンピューティング リソースの使用率を最大にする](batch-parallel-node-tasks.md)方法を学習する。 ワークロードの種類によっては、より大規模でより少数のコンピューティング ノードで並列タスクを実行することによってメリットが得られる場合があります。 同記事の「 [サンプル シナリオ](batch-parallel-node-tasks.md#example-scenario) 」で、そのようなシナリオについて詳しく説明されています。
- [タスクとノードを状態別にカウントして、Batch ソリューションを監視する](batch-get-resource-counts.md)方法を学習する


[api_net]: /dotnet/api/microsoft.azure.batch
[api_net_listjobs]: /dotnet/api/microsoft.azure.batch.joboperations
[api_rest]: /rest/api/batchservice/
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_ctor]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_expand]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_filter]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_select]: /dotnet/api/microsoft.azure.batch.odatadetaillevel

[net_list_certs]: /dotnet/api/microsoft.azure.batch.certificateoperations
[net_list_compute_nodes]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_job_schedules]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_jobprep_status]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_jobs]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_nodefiles]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_pools]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_schedule_jobs]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_task_files]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_list_tasks]: /dotnet/api/microsoft.azure.batch.joboperations

[rest_list_certs]: /rest/api/batchservice/certificate/list
[rest_list_compute_nodes]: /rest/api/batchservice/computenode/list
[rest_list_job_schedules]: /rest/api/batchservice/jobschedule/list
[rest_list_jobprep_status]: /rest/api/batchservice/job/listpreparationandreleasetaskstatus
[rest_list_jobs]: /rest/api/batchservice/job/list
[rest_list_nodefiles]: /rest/api/batchservice/file/listfromcomputenode
[rest_list_pools]: /rest/api/batchservice/pool/list
[rest_list_schedule_jobs]: /rest/api/batchservice/job/listfromjobschedule
[rest_list_task_files]: /rest/api/batchservice/file/listfromtask
[rest_list_tasks]: /rest/api/batchservice/task/list

[rest_get_cert]: /rest/api/batchservice/certificate/get
[rest_get_job]: /rest/api/batchservice/job/get
[rest_get_node]: /rest/api/batchservice/computenode/get
[rest_get_pool]: /rest/api/batchservice/pool/get
[rest_get_schedule]: /rest/api/batchservice/jobschedule/get
[rest_get_task]: /rest/api/batchservice/task/get

[net_cert]: /dotnet/api/microsoft.azure.batch.certificate
[net_job]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_node]: /dotnet/api/microsoft.azure.batch.computenode
[net_pool]: /dotnet/api/microsoft.azure.batch.cloudpool
[net_schedule]: /dotnet/api/microsoft.azure.batch.cloudjobschedule
[net_task]: /dotnet/api/microsoft.azure.batch.cloudtask

[rest_get_task_counts]: /rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
