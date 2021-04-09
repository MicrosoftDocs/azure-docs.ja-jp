---
title: Bulk Executor library からAzure Cosmos DB .NET V3 SDK のBulkサポートに移行する
description: ご利用のアプリケーションを、Bulk Executorライブラリの使用から、Azure Cosmos DB SDK V3 のBulkサポートに移行する方法について説明します
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/24/2020
ms.author: maquaran
ms.custom: devx-track-dotnet
ms.openlocfilehash: 24d6b475964e4bf7745495e9c41d0e89bb76f7e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341290"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>Bulk Executor library からAzure Cosmos DB .NET V3 SDK のBulkサポートに移行する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事は、[.NET Bulk Executorライブラリ](bulk-executor-dot-net.md)を使用する既存のアプリケーションのコードを、最新バージョンの .NET SDK の[一括サポート](tutorial-sql-api-dotnet-bulk-import.md)機能に移行するために必要な手順について説明します。

## <a name="enable-bulk-support"></a>Bulkサポートを有効にする

`CosmosClient`インスタンスで、[AllowBulkExecution](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution) 構成を使用し、Bulkサポートを有効にする：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>各操作のタスクを作成する

.NET SDKにおけるBulkサポートは、 [タスク並列ライブラリ、および](/dotnet/standard/parallel-programming/task-parallel-library-tpl)同時に発動するグループ化操作を活用することによって作動する。 

SDK にはドキュメントまたは操作のリストを入力パラメーターとして受け取る単一のメソッドがありませんが、一括で実行する操作ごとにタスクを作成する必要があります。その後、それらが完了するのを待ちます。

例えば最初の入力が、各項目が以下のスキーマを取るアイテムのリストであるとします：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

一括インポートを実行 (BulkExecutor.BulkImportAsync の使用と同様) する場合は、`CreateItemAsync` の同時呼び出しが必要です。 次に例を示します。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

一括 *アップデート*（[BulkExecutor.BulkImportAsync](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync)の使用と同様）を実行したい場合は、項目の値を更新してから`ReplaceItemAsync`メソッドを同時に起動する必要があります。 次に例を示します。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

一括 *削除*（[BulkExecutor.BulkDeleteAsync](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)の使用と同様）を実行したい場合は、`DeleteItemAsync`を `id`および各項目のパーテーションキーと同時に起動する必要があります。 次に例を示します。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>タスクの結果の状態をCaptureします

以前のコード例では、タスクの同時実行リストを作成し、各タスクで `CaptureOperationResponse` メソッドを呼び出しました。 このメソッドは、エラーや [リクエストユニットの使用](request-units.md)の追跡を行うことによって、*BulkExecutorとしての* 要求した応答スキーマを維持することを可能にする拡張機能です。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

`OperationResponse`が以下のように示されているところは：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>同時に操作を実行する

タスクのリスト全体のスコープを追跡するには、このヘルパー クラスを使用します。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkOperationsHelper":::

`ExecuteAsync` メソッドはすべての操作が完了するまで待ちます。ユーザーは次のようにこのメソッドを使用できます。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>統計をCaptureする

前のコードは、全ての操作が完了するまで待機し、それから必要な統計の計算を行います。 これらの統計は、一括実行プログラムの[BulkImportResponse](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse)のものと類似しています。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

`BulkOperationResponse`には以下のものが含まれます：

1. 一括サポートを通して操作の一覧を処理するのにかかった時間の合計。
1. 正常に処理された操作の数。
1. 消費された要求ユニットの合計。
1. エラーが生じた場合は、例外を含む組み合わせの一覧およびロギングと識別目的のための関連項目が表示されます。

## <a name="retry-configuration"></a>コンフィグレーションを再試行する

一括実行ライブラリには、`MaxRetryWaitTimeInSeconds`および [RetryOptions](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions)の `MaxRetryAttemptsOnThrottledRequests`を、ライブラリにコントロールを委任するために`0`に設定する[ガイダンス](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account)がありました。

.NET SDKの一括サポートに関しては、非表示の動作はありません。 再試行オプションを[CosmosClientOptions.MaxRetryAttemptsOnRateLimitedRequests](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretryattemptsonratelimitedrequests) および [CosmosClientOptions.MaxRetryWaitTimeOnRateLimitedRequests](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretrywaittimeonratelimitedrequests)を通して直接コンフィギュアする事が出来ます。

> [!NOTE]
> データの量に基づき、供給された要求ユニットが予想よりはるかに低いとされた場合には、これらの値を高い値に設定することを検討してください。 一括操作は長時間かかりますが、再試行の回数が多いため、より高い可能性で完全に成功します。

## <a name="performance-improvements"></a>パフォーマンスの向上

.NET SDKの他の操作と同様に、stream APIsを使用することでよりパフォーマンスが向上させ、不要なシリアル化を回避することにつながります。 

stream APIsの使用は、ご使用のデータの性質がbytesのストリームのものと合致する場合においてのみ可能です（例：ファイルストリーム）。 そのような場合には、`CreateItemStreamAsync`または`ReplaceItemStreamAsync`、 `DeleteItemStreamAsync`メソッドを使用し、`ResponseMessage` (`ItemResponse`ではなく)を操作することで、達成出来るスループットが向上します。

## <a name="next-steps"></a>次のステップ

* .NET SDKリリースについての詳細は、[Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)記事を参照してください。
* 完了した [移行ソースコードを](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration)GitHubから取得する。
* [GitHub上の追加の一括サンプル](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)