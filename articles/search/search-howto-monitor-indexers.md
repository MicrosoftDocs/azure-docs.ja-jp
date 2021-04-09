---
title: インデクサーの状態と結果を監視する
titleSuffix: Azure Cognitive Search
description: REST API または .NET SDK を使用し、Azure portal で Azure Cognitive Search インデクサーの状態、進捗状況、結果を監視します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: a94720e6b84821d53a3bfdcbdce249390078940f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99063267"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>Azure Cognitive Search インデクサーの状態と結果を監視する方法

インデクサーの処理は、Azure portal で、または REST 呼び出しや Azure SDK を使用してプログラムで監視できます。 インデクサー自体の状態に加えて、開始および終了時刻や、特定の実行の詳細なエラーおよび警告を確認できます。

## <a name="monitor-using-azure-portal"></a>Azure portal を使用して監視する

検索サービスの [概要] ページでは、すべてのインデクサーの現在の状態を確認できます。 ポータル ページは数分ごとに更新されるため、新しいインデクサーが実行された痕跡はすぐには表示されません。

   ![インデクサーの一覧](media/search-monitor-indexers/indexers-list.png "インデクサーの一覧")

| Status | 説明 |
|--------|-------------|
| **進行中** | アクティブな実行を示します。 ポータルでは部分的な情報が報告されます。 インデックスの作成が進むと、それに応じて **[成功したドキュメント]** の値が増加するのを確認できます。 大量のデータを処理するインデクサーの実行には時間がかかることがあります。 たとえば、数百万単位のソース ドキュメントを処理するインデクサーの場合、実行に 24 時間かかり、ほぼ直後に再起動することがあります。 ボリュームの多いインデクサーの場合、ポータルでは状態が常に **[進行中]** になることがあります。 インデクサーの実行中でも、進行中の実行と過去の実行の進捗状況に関する詳細を利用できます。 |
| **Success** | 実行が成功したことを示します。 エラーの数がインデクサーの **[失敗したアイテムの最大数]** 設定より少なければ、個々のドキュメントにエラーがあってもインデクサー実行は成功となることがあります。 |
| **Failed** | エラーの数が **[失敗したアイテムの最大数]** を超え、インデックスの作成が停止しました。 |
| **リセット** | インデクサーの内部の変更追跡状態がリセットされました。 インデクサーは完全に実行され、新しいタイムスタンプを持つものだけでなく、すべてのドキュメントが更新されます。 |

一覧のインデクサーをクリックすると、インデクサーの現在と最近の実行に関する詳細が表示されます。

   ![インデクサー概要と実行履歴](media/search-monitor-indexers/indexer-summary.png "インデクサー概要と実行履歴")

**インデクサー概要** グラフには、最近の実行で処理されたドキュメントの数がグラフで表示されます。

**実行の詳細** 一覧には、最近の実行結果が最大 50 件表示されます。 一覧の実行結果をクリックすると、その実行に関する詳細が表示されます。 その開始時刻、終了時刻、発生したエラーや警告などです。

   ![インデクサー実行の詳細](media/search-monitor-indexers/indexer-execution.png "インデクサー実行の詳細")

実行中にドキュメント固有の問題が発生した場合、[エラー] フィールドと [警告] フィールドにそれが一覧表示されます。

   ![エラーを含むインデクサーの詳細](media/search-monitor-indexers/indexer-execution-error.png "エラーを含むインデクサーの詳細")

警告は一部の種類のインデクサーで一般的であり、必ずしも問題を示すとは限りません。 たとえば、認識サービスを利用するインデクサーの場合、画像または PDF ファイルに処理するテキストが含まれないとき、警告が報告されることがあります。 

インデクサーのエラーと警告を調査する方法については、[Azure Cognitive Search のインデクサーの一般的な問題のトラブルシューティング](search-indexer-troubleshooting.md)に関する記事を参照してください。

## <a name="monitor-using-get-indexer-status-rest-api"></a>インデクサー状態の取得 (REST API) を使用して監視する

インデクサーの現在の状態と実行の履歴は、[インデクサー状態の取得](/rest/api/searchservice/get-indexer-status)コマンドを使用して取得できます。

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

応答には、全体的なインデクサーの状態、最後の (または実行中の) インデクサー呼び出し、およびインデクサー呼び出しの最近の履歴が含まれています。

```output
{
    "status":"running",
    "lastResult": {
        "status":"success",
        "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
     },
    "executionHistory":[ {
        "status":"success",
         "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
    }]
}
```

実行履歴には最近の実行が最大 50 件含まれます。これは逆の時系列 (一番新しいものが最初) で整理されています。

2 つの異なる状態値があることに注意してください。 トップレベルの状態はインデクサー自体の状態です。 **running** というインデクサー状態は、インデクサーが正しく設定されており、実行可能であることを意味しますが、現在実行されているという意味ではありません。

インデクサーの各実行には、実行別の状態も与えられ、その特定の実行が進行中であるか (**running**)、**success**、**transientFailure**、または **persistentFailure** 状態で既に完了しているかどうかを示します。 

インデクサーがリセットされ、その変更追跡状態が更新されると、別個の実行履歴エントリが **[リセット]** という状態で追加されます。

状態コードとインデクサー監視データに関する詳細は、[インデクサー状態の取得](/rest/api/searchservice/get-indexer-status)に関する記事を参照してください。

## <a name="monitor-using-net"></a>.NET を使用して監視する

次の C# の例では、Azure Cognitive Search .NET SDK を使用して、インデクサーの状態とその最近の (または進行中の) 実行の結果に関する情報がコンソールに書き込まれます。

```csharp
static void CheckIndexerStatus(SearchIndexerClient indexerClient, SearchIndexer indexer)
{
    try
    {
        string indexerName = "hotels-sql-idxr";
        SearchIndexerStatus execInfo = indexerClient.GetIndexerStatus(indexerName);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("Run Status: {0}", result.Status.ToString());
        Console.WriteLine("Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("ErrorMessage: {0}", errorMsg);
        Console.WriteLine(" Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

コンソールの出力は次のようになります。

```output
Indexer has run 18 times.
Indexer Status: Running
Latest run
  Run Status: Success
  Total Documents: 7, Failed: 0
  StartTime: 11:29:31 PM, EndTime: 11:29:31 PM, Elapsed: 00:00:00.2560000
  ErrorMessage: none
  Document Errors: 0, Warnings: 0
```

2 つの異なる状態値があることに注意してください。 トップレベルの状態は、インデクサー自体の状態です。 **Running** というインデクサー状態は、インデクサーが正しく設定されており、実行可能であることを意味しますが、現在実行されているという意味ではありません。

インデクサーの各実行にもそれ自体の状態が与えられ、特定の実行が進行中 (**Running**) であるか、**Success** または **TransientError** 状態で完了済みであるかを示します。 

インデクサーがリセットされ、その変更追跡状態が更新されると、別個の履歴エントリが **[リセット]** という状態で追加されます。

## <a name="next-steps"></a>次のステップ

状態コードとインデクサー監視情報に関する詳細は、次の API リファレンスを参照してください。

* [GetIndexerStatus (REST API)](/rest/api/searchservice/get-indexer-status)
* [IndexerStatus](/dotnet/api/azure.search.documents.indexes.models.indexerstatus)
* [IndexerExecutionStatus](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionstatus)
* [IndexerExecutionResult](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionresult)