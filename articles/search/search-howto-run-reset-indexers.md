---
title: インデクサーの実行またはリセット
titleSuffix: Azure Cognitive Search
description: インデクサー、スキル、または個々のドキュメントをリセットして、インデックスまたはナレッジストアのすべてまたは一部を更新します。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: bf8a4e51e23f438265af706914a6bc73ec30f64d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667673"
---
# <a name="how-to-run-or-reset-indexers-skills-or-documents"></a>インデクサー、スキル、またはドキュメントを実行またはリセットする方法

インデクサーの実行は、[インデクサー](search-indexer-overview.md)を初めて作成するとき、オンデマンドでインデクサーを実行するとき、またはスケジュールに基づいてインデクサーを設定するときに発生する可能性があります。 最初の実行後、インデクサーは、内部の "高基準値" を使用してどの検索ドキュメントがインデックス付けされたかを追跡します。 マーカーは API で公開されることはありませんが、内部的には、インデクサーがインデックス付けが停止した場所を認識して、次の実行時に中断した場所から再開できます。

最初から再処理する場合は、インデクサーをリセットすることで高基準値をクリアできます。 リセット API は、オブジェクト階層でレベルを下げて利用できます。

+ 検索コーパス全体 ([インデクサーのリセット](#reset-indexers)を使用)
+ 特定のドキュメントまたはドキュメントのリスト ([ドキュメントのリセット(プレビュー)](#reset-docs) を使用)
+ ドキュメント内の特定のスキルまたはエンリッチメント ([スキルのリセット (プレビュー)](#reset-skills) を使用)

リセット API を使用して、キャッシュされたコンテンツを更新 ([AI エンリッチメント](cognitive-search-concept-intro.md)のシナリオで可能) するか、高基準値をクリアしてインデックスを再構築します。

リセットした後に実行すると、既存のドキュメントと新しいドキュメントを再処理できますが、以前の実行時に作成された検索インデックス内の孤立した検索ドキュメントは削除されません。 削除の詳細については、[ドキュメントの追加、更新、削除](/rest/api/searchservice/addupdate-or-delete-documents)に関するページを参照してください。

## <a name="run-indexers"></a>インデクサーの実行

[インデクサーの作成](/rest/api/searchservice/create-indexer)では、無効状態 ("disabled": true) で作成しない限り、インデクサーが作成され、実行されます。 最初の実行では、オブジェクトの作成も行われるため、少し時間がかかります。

[インデクサー実行](/rest/api/searchservice/run-indexer)では、検索インデックスをデータ ソースと同期するのに必要なものだけを検出して処理します。 Blob Storage には、変更の検出が組み込まれています。 その他のデータ ソース (Azure SQL や Cosmos DB など) は、インデクサーが新しい行と更新された行だけを読み取る前に、変更の検出用に構成する必要があります。

インデクサーは、次の方法のいずれかを使用して実行できます。

+ Azure portal のインデクサーのページで **実行** コマンドを使用する
+ [インデクサーを実行する (REST)](/rest/api/searchservice/run-indexer)
+ Azure .NET SDK の [RunIndexers メソッド](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexer)を使用する (または、別の SDK の同等の RunIndexer メソッドを使用する)

インデクサーの実行には、次の制限があります。

+ インデクサー ジョブの最大数は、レプリカあたり最大 1 個です (同時実行ジョブなし)。

  インデクサーの実行が既にフル稼働状態の場合は、次のような通知が表示されます。"インデクサー '<インデクサー名>' の実行に失敗しました。エラー:"現在、別のインデクサー呼び出しが進行中です。同時呼び出しは許可されていません。"

+ スキルセットを使用している場合の最長実行時間は 2 時間、使用していない場合は 24 時間です。 

  インデクサーをスケジュールすることで、処理を拡張できます。 Free レベルでは、実行時間の制限が低くなっています。 完全な一覧については、[インデクサーの制限](search-limits-quotas-capacity.md#indexer-limits)に関するページを参照してください。

<a name="reset-indexers"></a>

## <a name="reset-an-indexer"></a>インデクサーのリセット

インデクサーのリセットでは、包括的に処理されます。 検索インデックス内で、インデクサーによって最初に設定された検索ドキュメントすべてに、完全処理のマークが付けられます。 基になるソースが見つかった新しいドキュメントは、検索ドキュメントとしてインデックスに追加されます。 スキルセットと[キャッシュ](search-howto-incremental-index.md)を使用するようにインデクサーが構成されている場合は、スキルセットが再実行され、キャッシュが更新されます。

これらの方法のいずれかを使用してインデクサーをリセットした後、上記で説明した方法のいずれかを使用してインデクサーを実行できます。

+ Azure portal のインデクサーのページで **リセット** コマンドを使用する
+ [インデクサーをリセットする (REST)](/rest/api/searchservice/reset-indexer)
+ Azure .NET SDK の [ResetIndexers メソッド](/dotnet/api/azure.search.documents.indexes.searchindexerclient.resetindexer)を使用する (または、別の SDK の同等の ResetIndexers メソッドを使用する)

実行が完了すると、リセット フラグがクリアされます。 データ ソースに対して操作可能な定期的な変更検出ロジックは、次の実行時に再開され、データ セットの残りの部分で他の新しい値または更新された値を収集します。

> [!NOTE]
> リセット要求は、再処理されるもの (インデクサー、スキル、ドキュメント) を決定しますが、それ以外はインデクサーの実行時の動作に影響しません。 インデクサーに実行時パラメーター、フィールド マッピング、キャッシュ、バッチ オプションなどがある場合、それらの設定は、リセット後にインデクサーを実行するときにすべて有効になります。

<a name="reset-skills"></a>

## <a name="reset-skills-preview"></a>スキルのリセット (プレビュー)

> [!IMPORTANT] 
> [スキルのリセット](/rest/api/searchservice/preview-api/reset-skills)はパブリック プレビュー段階にあり、プレビュー REST API を介してのみ利用できます。 プレビュー機能は、[補足利用規約](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に基づいて、現状のまま提供されます。

スキルセットがあるインデクサーの場合は、特定のスキルをリセットして、そのスキルと、その出力に依存するダウンストリーム スキルを強制的に処理できます。 [キャッシュされたエンリッチメント](search-howto-incremental-index.md)も更新されます。 スキルをリセットすると、キャッシュされたスキルの結果が無効になります。これは、スキルの新バージョンがデプロイされたときに、インデクサーですべてのドキュメントに対してそのスキルを再実行したい場合に便利です。 

[スキルのリセット](/rest/api/searchservice/preview-api/reset-skills)は REST **`api-version=2020-06-30-Preview`** を介して実行できます。

```http
POST https://[service name].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2020-06-30-Preview
{
    "skillNames" : [
        "#1",
        "#5",
        "#6"
    ]
}
```

上記の例に示されているように、個々のスキルを指定することもできます。しかし、それらのスキルのいずれかで、一覧にないスキル (#2 から #4) からの出力が必要な場合は、キャッシュが必要な情報を提供できない限り、一覧にないスキルが実行されます。 これが可能であるには、#2 から #4 のスキルのキャッシュされたエンリッチメントが #1 に依存していない必要があります (リセットのためにリストされています)。

スキルが指定されていない場合は、スキルセット全体が実行されます。また、キャッシングが有効になっている場合は、キャッシュも更新されます。

<a name="reset-docs"></a>

## <a name="reset-docs-preview"></a>ドキュメントのリセット (プレビュー)

> [!IMPORTANT] 
> [ドキュメントのリセット](/rest/api/searchservice/preview-api/reset-documents)はパブリック プレビュー段階にあり、プレビュー REST API を介してのみ利用できます。 プレビュー機能は、[補足利用規約](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に基づいて、現状のまま提供されます。

[ドキュメントのリセット API](/rest/api/searchservice/preview-api/reset-documents) は、ドキュメント キーの一覧を受け取って、特定のドキュメントを更新できるようにします。 リセット パラメーターを指定すると、基になるデータの他の変更には関係なく、リセット パラメーターによってのみ処理対象が決定されます。 たとえば、インデクサーの最後の実行後に 20 個の BLOB が追加または更新されたが、1 つのドキュメントのみをリセットした場合、その 1 つのドキュメントだけが処理されます。

ドキュメントごとに、その検索ドキュメント内のすべてのフィールドがデータ ソースの値で更新されます。 更新するフィールドを選択することはできません。 

ドキュメントがスキルセットによってエンリッチされ、キャッシュされたデータがある場合は、指定されたドキュメントに対してのみスキルセットが呼び出され、再処理されたドキュメントに対してキャッシュが更新されます。

この API を初めてテストするときは、次の API を使用して動作を検証し、テストできます。

+ [インデクサー状態の取得](/rest/api/searchservice/get-indexer-status) (API バージョン `2020-06-30-Preview`): リセットの状態と実行の状態を確認します。 状態応答の最後でリセット要求に関する情報を確認できます。
+ [ドキュメントのリセット](/rest/api/searchservice/preview-api/reset-documents) (API バージョン`2020-06-30-Preview`): 処理するドキュメントを指定します。
+ [インデクサー実行](/rest/api/searchservice/run-indexer) (任意の API バージョン): インデクサーを実行します。
+ [ドキュメントの検索](/rest/api/searchservice/search-documents): 更新された値を確認します。値がわからない場合は、ドキュメント キーを返します。 応答に表示するフィールドを制限する場合は、`"select": "<field names>"` を使用します。

### <a name="formulate-and-send-the-reset-request"></a>リセット要求の作成と送信

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "1001",
        "4452"
    ]
}
```

要求で提供されるドキュメント キーは検索インデックスの値であり、データ ソース内の対応するフィールドとは異なる場合があります。 キーの値がわからない場合は、[クエリを送信](search-query-create.md)して値を返します。`select` を使用すると、ドキュメント キー フィールドだけを返すことができます。

複数の検索ドキュメントに解析される BLOB の場合 (たとえば、解析モードとして [jsonLines または jsonArrays](search-howto-index-json-blobs.md)、あるいは [delimitedText](search-howto-index-csv-blobs.md) を使用した場合)、ドキュメント キーがインデクサーによって生成され、ユーザーにはわからないことがあります。 その場合、ドキュメント キーのクエリが正しい値を得るのに役立ちます。

異なるキーを使用して API を複数回呼び出すと、ドキュメント キーのリセットの一覧に新しいキーが追加されます。 **`overwrite`** パラメーターを true に設定して API を呼び出すと、要求のペイロードによって、リセットされるドキュメント キーの現在のリストが上書きされます。

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "200",
        "630"
    ],
    "overwrite": true
}
```

## <a name="check-reset-status"></a>リセットの状態の確認

リセットの状態を確認し、処理のためにキューに登録されているドキュメント キーを確認するには、[インデクサー状態の取得](/rest/api/searchservice/get-indexer-status)と **`api-version=06-30-2020-Preview`** を使用します。 プレビュー API は **`currentState`** セクションを返します。これは、インデクサー状態の取得応答の最後にあります。

スキルのリセットの場合は "モード" が **`indexingAllDocs`** になります (AI エンリッチメントで設定されるフィールドについてはすべてのドキュメントが影響を受ける可能性があるため)。

ドキュメントのリセットの場合、モードは **`indexingResetDocs`** に設定されます。 ドキュメントのリセット呼び出しで指定されたすべてのドキュメント キーが処理され、操作の進行中に他のインデクサー ジョブが実行されなくなるまで、インデクサーはこの状態を保持します。 ドキュメント キー リスト内のすべてのドキュメントを見つけるには、各ドキュメントを解読してキーを特定し、一致させる必要があります。データ セットが大きい場合は、この処理に時間がかかることがあります。 BLOB コンテナーに数百の BLOB が含まれていて、リセットするドキュメントが最後にある場合、インデクサーは、他のすべてのものがチェックされるまで、一致する BLOB を検索しません。

```json
"currentState": {
    "mode": "indexingResetDocs",
    "allDocsInitialTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "allDocsFinalTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "resetDocsInitialTrackingState": null,
    "resetDocsFinalTrackingState": null,
    "resetDocumentKeys": [
        "200",
        "630"
    ]
}
```

ドキュメントを再処理した後、インデクサーは **`indexingAllDocs`** モードに戻り、次回の実行時に他の新規または更新されたドキュメントを処理します。

## <a name="next-steps"></a>次のステップ

リセット API は、次回のインデクサー実行のスコープを通知するのに使用されます。 実際の処理では、オンデマンドのインデクサー実行を呼び出すか、スケジュールされたジョブで作業を完了できるようにする必要があります。 実行が完了すると、スケジュールされた処理かオンデマンドの処理かにかかわらず、インデクサーは通常の処理に戻ります。

インデクサー ジョブをリセットして再実行した後、検索サービスで状態を監視したり、診断ログを使用して詳細情報を取得したりできます。

+ [インデクサーの操作 (REST)](/rest/api/searchservice/indexer-operations)
+ [検索インデクサーの状態を監視する](search-howto-monitor-indexers.md)
+ [ログ データを収集して分析する](search-monitor-logs.md)
+ [インデクサーのスケジュール設定](search-howto-schedule-indexers.md)