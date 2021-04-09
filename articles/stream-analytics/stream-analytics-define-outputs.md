---
title: Azure Stream Analytics からの出力
description: この記事では、Azure Stream Analytics で使用できるデータ出力のオプションについて説明します。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 12/9/2020
ms.openlocfilehash: 3ce4f673657561e196520466b569d0cf83d75a8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019348"
---
# <a name="outputs-from-azure-stream-analytics"></a>Azure Stream Analytics からの出力

Azure Stream Analytics ジョブは、入力、クエリ、および出力で構成されます。 変換されたデータを送信できる出力の種類はいくつかあります。 この記事では、サポートされている Stream Analytics の出力の一覧を示します。 Stream Analytics のクエリを自分で作成するときは、[INTO 句](/stream-analytics-query/into-azure-stream-analytics)を使用して出力の名前を指定します。 ジョブごとに 1 つの出力を使用できます。または、必要に応じて、クエリ内に複数の INTO 句を追加することによって、ストリーミング ジョブごとに複数の出力を使用できます。

Stream Analytics ジョブの出力を作成、編集、テストするには、[Azure portal](stream-analytics-quick-create-portal.md#configure-job-output)、[Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job)、[.NET API](/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations)、[REST API](/rest/api/streamanalytics/)、および [Visual Studio](stream-analytics-quick-create-vs.md) を使用できます。

一部の出力の種類は[パーティション分割](#partitioning)に対応します。また、スループットを最適化するために[出力のバッチ サイズ](#output-batch-size)が異なります。 次の表は、出力の種類ごとにサポートされている機能を示しています。

| 出力の種類 | パーティション分割 | セキュリティ | 
|-------------|--------------|----------|
|[Azure Data Lake Storage Gen 1](azure-data-lake-storage-gen1-output.md)|はい|Azure Active Directory ユーザー </br> 、マネージド ID|
|[Azure SQL Database](sql-database-output.md)|はい、省略可能です。|SQL ユーザー認証、 </br> マネージド ID (プレビュー)|
|[Azure Synapse Analytics](azure-synapse-analytics-output.md)|はい|SQL ユーザー認証、 </br> マネージド ID (プレビュー)|
|[Blob Storage と Azure Data Lake Gen 2](blob-storage-azure-data-lake-gen2-output.md)|はい|アクセス キー、 </br> マネージド ID (プレビュー)|
|[Azure Event Hubs](event-hubs-output.md)|はい。出力構成でパーティション キー列を設定する必要があります。|アクセス キー、 </br> マネージド ID (プレビュー)|
|[Power BI](power-bi-output.md)|いいえ|Azure Active Directory ユーザー、 </br> マネージド ID|
|[Azure Table Storage](table-storage-output.md)|はい|アカウント キー|
|[Azure Service Bus キュー](service-bus-queues-output.md)|はい|アクセス キー|
|[Azure Service Bus トピック](service-bus-topics-output.md)|はい|アクセス キー|
|[Azure Cosmos DB](azure-cosmos-db-output.md)|はい|アクセス キー|
|[Azure Functions](azure-functions-output.md)|はい|アクセス キー|

## <a name="partitioning"></a>パーティション分割

Stream Analytics では、Power BI を除くすべての出力用のパーティションがサポートされています。 パーティション キーと出力ライターの数の詳細については、関心がある特定の出力の種類に関する記事を参照してください。 前のセクションですべての出力に関する記事がリンクされています。  

また、パーティションの高度なチューニングとして、出力ライターの数は、クエリ内の `INTO <partition count>` ([INTO](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) に関するページを参照してください) 句を使用して制御することができます。これは、目的のジョブ トポロジを実現するのに役立ちます。 出力アダプターがパーティション分割されていない場合は、ある入力パーティションにデータがないと、到着遅延時間までの遅延が発生します。 このような場合は、出力が 1 つのライターにマージされるため、ご使用のパイプラインにボトルネックが発生する可能性があります。 到着遅延ポリシーの詳細については、[Azure Stream Analytics のイベントの順序に関する考慮事項](./stream-analytics-time-handling.md)に関する記事を参照してください。

## <a name="output-batch-size"></a>出力バッチ サイズ

すべての出力でバッチ処理がサポートされていますが、バッチ サイズを明示的にサポートするのは一部に限られます。 Azure Stream Analytics では、イベントを処理して出力に書き込むために、可変サイズのバッチを使用します。 通常、Stream Analytics エンジンでは、一度に 1 つのメッセージを書き込むことはせず、効率を上げるためにバッチを使用します。 受信イベントと送信イベントのレートが両方とも高い場合は、Stream Analytics では、大きいバッチを使用します。 エグレス レートが低い場合は、小さいバッチを使用して待ち時間が短い状態を維持します。

## <a name="parquet-output-batching-window-properties"></a>Parquet 出力バッチ処理ウィンドウのプロパティ

Azure Resource Manager テンプレートのデプロイまたは REST API を使用する場合、2 つのバッチ処理ウィンドウ プロパティは次のとおりです。

1. *timeWindow*

   バッチあたりの最大待機時間。 値は Timespan の文字列とする必要があります。 たとえば、2 分だと "00:02:00" になります。 この時間が経過すると、最小行数の要件が満たされていなくても、バッチは出力に書き込まれます。 既定値は 1 分であり、最大許容値は 2 時間です。 BLOB 出力にパス パターンの頻度がある場合は、待機時間をパーティションの時間の範囲より長くすることはできません。

2. *sizeWindow*

   バッチあたりの最小行数。 Parquet の場合、すべてのバッチによって新しいファイルが作成されます。 現在の既定値は 2,000 行であり、最大許容値は 10,000 行です。

これらのバッチ処理ウィンドウ プロパティは、API バージョン **2017-04-01-preview** でサポートされているだけです。 REST API 呼び出しにおける JSON ペイロードの例を次に示します。

```json
"type": "stream",
      "serialization": {
        "type": "Parquet",
        "properties": {}
      },
      "timeWindow": "00:02:00",
      "sizeWindow": "2000",
      "datasource": {
        "type": "Microsoft.Storage/Blob",
        "properties": {
          "storageAccounts" : [
          {
            "accountName": "{accountName}",
            "accountKey": "{accountKey}",
          }
          ],
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
>
> [クイック スタート: Azure Portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
