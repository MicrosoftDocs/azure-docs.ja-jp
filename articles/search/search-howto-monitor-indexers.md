---
title: インデクサーの状態と結果を監視する方法 - Azure Search
description: REST API または .NET SDK を使用し、Azure portal で Azure Search インデクサーの状態、進捗状況、結果を監視します。
ms.date: 06/28/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 07b4fe2ef830c3ce09b655cf4b433d14923229a9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486183"
---
# <a name="how-to-monitor-azure-search-indexer-status-and-results"></a>Azure Search インデクサーの状態と結果を監視する方法

Azure Search では、各インデクサーの現在と過去の実行を対象に、状態と監視に関する情報を提供します。

インデクサー監視は次の場合に役立ちます。

* 実行が進行している間、インデクサーの進捗状況を追跡する。
* 進行中または過去のインデクサー実行の結果を見る。
* トップレベルのインデクサー エラーとインデックスが作成されている個々のドキュメントに関するエラーまたは警告を確認する。

## <a name="find-indexer-status-and-history-details"></a>インデクサーの状態と履歴の詳細を見つける。

インデクサー監視情報には以下を含むさまざまな方法でアクセスできます。

* [Azure Portal](#portal) で次のように実行します
* [REST API](#restapi) の使用
* [.NET SDK](#dotnetsdk) の使用

利用できるインデクサー監視情報には、以下がすべて含まれます (ただし、使用されるアクセス方法によってデータ形式が異なります)。

* インデクサー自体に関する状態情報
* その状態、開始時刻、終了時刻、詳しいエラーと警告など、インデクサーの最近の実行に関する情報。
* 過去のインデクサー実行とその状態、結果、エラー、警告の一覧。

大量のデータを処理するインデクサーの実行には時間がかかることがあります。 たとえば、数百万単位のソース ドキュメントを処理するインデクサーの場合、実行に 24 時間かかり、ほぼ直後に再起動することがあります。 ボリュームの多いインデクサーの場合、ポータルでは状態が常に **[進行中]** になることがあります。 インデクサーの実行中でも、進行中の実行と過去の実行の進捗状況に関する詳細を利用できます。

<a name="portal"></a>

## <a name="monitor-indexers-in-the-portal"></a>ポータルでインデクサーを監視する

検索サービスの [概要] ページの **[インデクサー]** 一覧では、すべてのインデクサーの現在の状況を確認できます。

   ![インデクサーの一覧](media/search-monitor-indexers/indexers-list.png "インデクサーの一覧")

インデクサーの実行時、一覧の状態に **[進行中]** と表示され、 **[成功したドキュメント]** 値にこれまでに処理されたドキュメントの数が表示されます。 インデクサーの状態値やドキュメント数がポータルで更新されるまで数分かかることがあります。

最近の実行が成功したインデクサーには **[成功]** と表示されます。 エラーの数がインデクサーの **[失敗したアイテムの最大数]** 設定より少なければ、個々のドキュメントにエラーがあってもインデクサー実行は成功となることがあります。

最近の実行がエラーで終了した場合、状態には **[失敗]** と表示されます。 **[リセット]** という状態は、インデクサーの変更追跡状態がリセットされたことを意味します。

一覧のインデクサーをクリックすると、インデクサーの現在と過去の実行に関する詳細が表示されます。

   ![インデクサーの概要と実行履歴](media/search-monitor-indexers/indexer-summary.png "インデクサーの概要と実行履歴")

**インデクサー概要**グラフには、最近の実行で処理されたドキュメントの数がグラフで表示されます。

**実行の詳細**一覧には、最近の実行結果が最大 50 件表示されます。

一覧の実行結果をクリックすると、その実行に関する詳細が表示されます。 その開始時刻、終了時刻、発生したエラーや警告などです。

   ![インデクサー実行の詳細](media/search-monitor-indexers/indexer-execution.png "インデクサー実行の詳細")

実行中にドキュメント固有の問題が発生した場合、[エラー] フィールドと [警告] フィールドにそれが一覧表示されます。

   ![エラーを含むインデクサー詳細](media/search-monitor-indexers/indexer-execution-error.png "エラーを含むインデクサー詳細")

警告は一部の種類のインデクサーで一般的であり、必ずしも問題を示すとは限りません。 たとえば、認識サービスを利用するインデクサーの場合、画像または PDF ファイルに処理するテキストが含まれないとき、警告が報告されることがあります。

インデクサーのエラーと警告を調査する方法については、「[Azure Search のインデクサーの一般的な問題のトラブルシューティング](search-indexer-troubleshooting.md)」を参照してください。

<a name="restapi"></a>

## <a name="monitor-indexers-using-the-rest-api"></a>REST API を使用してインデクサーを監視する

インデクサーの現在の状態と実行の履歴は、[インデクサー状態の取得](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)コマンドを使用して取得できます。

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

応答には、全体的なインデクサーの状態、最後の (または実行中の) インデクサー呼び出し、およびインデクサー呼び出しの最近の履歴が含まれています。

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

実行履歴には最近の実行が最大 50 件含まれます。これは逆の時系列 (一番新しいものが最初) で整理されています。

2 つの異なる状態値があることに注意してください。 トップレベルの状態はインデクサー自体の状態です。 **running** というインデクサー状態は、インデクサーが正しく設定されており、実行可能であることを意味しますが、現在実行されているという意味ではありません。

インデクサーの各実行には、実行別の状態も与えられ、その特定の実行が進行中であるか (**running**)、**success**、**transientFailure**、または **persistentFailure** 状態で既に完了しているかどうかを示します。 

インデクサーがリセットされ、その変更追跡状態が更新されると、別個の実行履歴エントリが **[リセット]** という状態で追加されます。

状態コードとインデクサー監視データに関する詳細は、「[GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)」を参照してください。

<a name="dotnetsdk"></a>

## <a name="monitor-indexers-using-the-net-sdk"></a>.NET SDK を使用してインデクサーを監視する

Azure Search .NET SDK を使用してインデクサーのスケジュールを定義できます。 そのためには、インデクサーを作成または更新するときに、**schedule** プロパティを含めます。

次の C# 例では、インデクサーの状態とその最近の (あるいは進行中の) 実行の結果に関する情報がコンソールに書き込まれます。

```csharp
static void CheckIndexerStatus(Indexer indexer, SearchServiceClient searchService)
{
    try
    {
        IndexerExecutionInfo execInfo = searchService.Indexers.GetStatus(indexer.Name);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("  Run Status: {0}", result.Status.ToString());
        Console.WriteLine("  Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("  StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("  ErrorMessage: {0}", errorMsg);
        Console.WriteLine("  Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

コンソールの出力は次のようになります。

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

2 つの異なる状態値があることに注意してください。 トップレベルの状態は、インデクサー自体の状態です。 **Running** というインデクサー状態は、インデクサーが正しく設定されており、実行可能であることを意味しますが、現在実行されているという意味ではありません。

インデクサーの各実行にもそれ自体の状態が与えられ、特定の実行が進行中 (**Running**) であるか、**Success** または **TransientError** 状態で完了済みであるかを示します。 

インデクサーがリセットされ、その変更追跡状態が更新されると、別個の履歴エントリが **[リセット]** という状態で追加されます。

状態コードとインデクサー監視情報に関する詳細は、REST API の「[GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)」を参照してください。

ドキュメント固有のエラーまたは警告に関する詳細は、`IndexerExecutionResult.Errors` 一覧と `IndexerExecutionResult.Warnings` 一覧を列挙することで取得できます。

インデクサーの監視に使用する .NET SDK クラスの詳細については、「[IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet)」と「[IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet)」を参照してください。
