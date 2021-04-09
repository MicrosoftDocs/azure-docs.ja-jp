---
title: Azure Cosmos DB への Azure Stream Analytics の出力
description: この記事では、構造化されていない JSON データに対するデータ アーカイブと待機時間の短いクエリのために、Azure Stream Analytics を使用して、Azure Cosmos DB for JSON 出力に出力を保存する方法について説明します。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/2/2020
ms.custom: seodec18
ms.openlocfilehash: 2d00d489ff248ecf5599d78e0a351c93248cf8ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018091"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Cosmos DB への Azure Stream Analytics の出力  
Azure Stream Analytics では、JSON 出力のターゲットを [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) にすることができるため、構造化されていない JSON データに対してデータ アーカイブと待機時間の短いクエリを有効にすることができます。 このドキュメントでは、この構成を実装するためのベスト プラクティスについて説明します。 Azure Cosmos DB を出力として使用する場合は、ジョブを互換性レベル 1.2 に設定することをお勧めします。

Azure Cosmos DB に馴染みがない場合は、「[Azure Cosmos DB のドキュメント](../cosmos-db/index.yml)」を参照して、作業を開始してください。 

> [!Note]
> 現時点で、Stream Analytics では *SQL API* を介した Azure Cosmos DB への接続のみがサポートされています。
> その他の Azure Cosmos DB API は、まだサポートされていません。 Stream Analytics を、その他の API で作成した Azure Cosmos DB アカウントへ接続する場合は、データが正しく格納されない可能性があります。 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>出力ターゲットとしての Azure Cosmos DB の基礎
Stream Analytics で Azure Cosmos DB 出力を使用すると、ストリーム処理の結果を JSON 出力として自分の Azure Cosmos DB コンテナーに書き込むことができます。 

Stream Analytics によって、ご利用のデータベース内にコンテナーは作成されません。 代わりに、お客様はそれらを事前に作成するように求められます。 これにより、Azure Cosmos DB コンテナーの課金コストを制御できるようになります。 また、[Azure Cosmos DB API](/rest/api/cosmos-db/) を使用して、ご利用のコンテナーのパフォーマンス、整合性、および容量を直接調整することもできます。

> [!Note]
> Azure Cosmos DB のファイアウォールで許可されている IP の一覧に 0.0.0.0 を追加する必要があります。

以下のセクションでは、Azure Cosmos DB 用のコンテナー オプションのいくつかを詳しく説明します。

## <a name="tuning-consistency-availability-and-latency"></a>整合性、可用性、および待機時間の調整
Azure Cosmos DB では、アプリケーション要件を満たすために、データベースやコンテナーを微調整し、整合性、可用性、待ち時間、およびスループットの間で妥協点を見つけることができます。 

読み取りおよび書き込みの待機時間に対してシナリオで求められる読み取りの一貫性レベルに応じて、データベース アカウントでの一貫性レベルを選択することができます。 コンテナー上の要求単位 (RU) をスケールアップすれば、スループットを向上させることができます。 

また Azure Cosmos DB では、コンテナーへの各 CRUD 操作に対する同期インデックス作成も、既定で有効になっています。 これは、Azure Cosmos DB 内で書き込みと読み取りのパフォーマンスを制御するための便利なオプションの 1 つです。 

詳細については、[データベースとクエリの一貫性レベルの変更](../cosmos-db/consistency-levels.md)に関する記事をご覧ください。

## <a name="upserts-from-stream-analytics"></a>Stream Analytics からのアップサート
Stream Analytics を Azure Cosmos DB と統合することで、特定の **ドキュメント ID** 列に基づき、ご利用のコンテナーでレコードを挿入または更新できるようになります。 これは *upsert* とも呼ばれます。

Stream Analytics ではオプティミスティック upsert 手法が使用されます。 ドキュメント ID の競合により挿入が失敗した場合のみ、更新が行われます。 

互換性レベルが 1.0 の場合、Stream Analytics ではこの更新が PATCH 操作として実行されるため、ドキュメントに対する部分的な更新が可能になります。 Stream Analytics では、新しいプロパティの追加、または既存のプロパティの置き換えが段階的に行われます。 ただし、JSON ドキュメント内の配列プロパティの値を変更すると、配列全体が上書きされます。 つまり、配列はマージされません。 

1\.2 では、アップサートの動作がドキュメントの挿入または置換に変更されています。 互換性レベル 1.2 に関する以降のセクションでは、この動作について詳しく説明します。

受信した JSON ドキュメントに既存の ID フィールドがある場合、そのフィールドは自動的に Azure Cosmos DB の **ドキュメント ID** 列として使用されます。 後続のどの書き込みもそのように処理されるため、次のいずれかの状況になります。

- ID が一意の場合は挿入となる。
- ID が重複していて、**ドキュメント ID** が **ID** に設定されていると upsert となる。
- ID が重複していて、**ドキュメント ID** が設定されていないと、最初のドキュメントの後でエラーになる。

重複した ID を持つものも含め、"*すべての*" ドキュメントを保存する場合は、(**AS** キーワードを使用して) クエリ内の ID フィールドの名前を変更します。 Azure Cosmos DB により ID フィールドを作成するか、または ID を別の列の値に置き換えます (**AS** キーワードを使用するか、または **ドキュメント ID** 設定を使用)。

## <a name="data-partitioning-in-azure-cosmos-db"></a>Azure Cosmos DB でのデータ パーティション分割
Azure Cosmos DB では、パーティションがご利用のワークロードに基づいて自動的にスケーリングされます。 そのため、ご自分のデータをパーティション分割するための方法として[無制限](../cosmos-db/partitioning-overview.md)コンテナーをお勧めします。 Stream Analytics では、無制限コンテナーに書き込む場合、以前のクエリ手順または入力のパーティション分割スキームと同数の並列ライターが使用されます。

> [!NOTE]
> Azure Stream Analytics では最上位のパーティション キーを使用した無制限コンテナーのみがサポートされています。 たとえば、`/region` がサポートされています。 入れ子になったパーティション キー (たとえば、`/region/name`) はサポートされていません。 

選択したパーティション キーによっては、次の "_警告_" が出力される場合があります。

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

複数の異なる値を持つパーティション キー プロパティを選択して、これらの値に、ご利用のワークロードを均等に分散させることが重要です。 パーティションを分割すると、当然ながら、同一のパーティション キーを必要とする要求は、単一パーティションの最大スループットによって制限されます。 

同一パーティション キー値に属するドキュメントのストレージ サイズは 20 GB に制限されます ([物理的なパーティション サイズの制限](../cosmos-db/partitioning-overview.md)は 50 GB です)。 [理想的なパーティション キー](../cosmos-db/partitioning-overview.md#choose-partitionkey)とは、クエリ内でフィルターとして頻繁に使用され、ソリューションのスケーラビリティを確保するために十分なカーディナリティを持つものを指します。

Stream Analytics クエリと Cosmos DB に使用されるパーティション キーは、同一である必要はありません。 完全並列トポロジでは、Stream Analytics クエリのパーティション キーとして *入力パーティション キー* `PartitionId` を使用することが推奨されますが、Cosmos DB コンテナーのパーティション キーとしては推奨される選択肢ではないことがあります。

また、パーティション キーは Azure Cosmos DB 用のストアド プロシージャやトリガーでのトランザクションの境界でもあります。 パーティション キーは、トランザクション内で同時に発生するドキュメントが同一のパーティション キーの値を共有できるように選択する必要があります。 パーティション キーの選択については、記事「[Azure Cosmos DB でのパーティション分割](../cosmos-db/partitioning-overview.md)」に詳しく説明されています。

固定の Azure Cosmos DB コンテナーの場合、それらがいっぱいになっても、Stream Analytics ではスケールアップまたはスケールアウトすることができません。 それらの上限は 10 GB と 10,000 RU/秒のスループットです。 固定コンテナーから無制限コンテナー (1,000 RU/秒以上のスループットとパーティション キーを備えたコンテナーなど) にデータを移行するには、[データ移行ツール](../cosmos-db/import-data.md)または[変更フィード ライブラリ](../cosmos-db/change-feed.md)を使用します。

複数の固定コンテナーに書き込む機能は非推奨とされています。 ご利用の Stream Analytics ジョブのスケール アウトには、お勧めしません。

## <a name="improved-throughput-with-compatibility-level-12"></a>互換性レベル 1.2 でのスループットの向上
互換性レベル 1.2 の場合、Stream Analytics では Azure Cosmos DB への一括書き込みのためのネイティブ統合がサポートされます。 この統合により、スループットを最大化し、スロットル リクエストを効率的に処理しながら、Azure Cosmos DB に効率的に書き込むことができます。 

この強化された書き込みメカニズムは、upsert の動作が異なるため、新しい互換性レベルで利用できます 1\.2 より前のレベルでは、upsert の動作として、ドキュメントの挿入またはマージが実行されます。 1\.2 では、アップサートの動作がドキュメントの挿入または置換に変更されています。

1\.2 より前のレベルの場合、Stream Analytics ではカスタム ストアド プロシージャを使用して、パーティション キーごとにドキュメントが Azure Cosmos DB に一括 upsert されます。 ここでは、バッチはトランザクションとして書き込まれます。 1 つのレコードで一時的なエラー (スロットリング) が発生しただけでも、バッチ全体を再試行する必要があります。 このため、妥当なスロットリングのシナリオであっても、比較的低速になります。

次の例は、同じ Azure Event Hubs 入力から読み取られるまったく同じ 2 つの Stream Analytics ジョブを示しています。 どちらの Stream Analytics ジョブもパススルー クエリにより[完全にパーティション分割](./stream-analytics-parallelization.md#embarrassingly-parallel-jobs)されており、同じ Azure Cosmos DB コンテナーに書き込みます。 左側のメトリックは、互換性レベル 1.0 で構成されたジョブからのものです。 右側のメトリックは、1.2 で構成されています。 Azure Cosmos DB コンテナーのパーティション キーは、入力イベントから取得される一意の GUID です。

![Stream Analytics メトリックの比較](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Event Hubs でのイベントの受信速度は、取り込むように構成されている Azure Cosmos DB コンテナー (20,000 RU) より 2 倍速いので、Azure Cosmos DB でのスロットリングが必要です。 しかし、1.2 のジョブは、一貫してより高いスループット (1 分あたりの出力イベント) とより低い平均 SU 使用率 (%) で書き込みを行います。 ご利用の環境では、この違いはさらにいくつかの要因に依存します。 これらの要因には、選択しているイベント形式、入力イベントまたはメッセージのサイズ、パーティション キー、クエリが含まれます。

![Azure Cosmos DB メトリックの比較](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

1\.2 を使用することで、Stream Analytics は Azure Cosmos DB で使用可能なスループットの 100 パーセントをより賢く利用することができ、スロットリングまたは速度の制限による再送信はほとんどありません。 これにより、コンテナーで同時に実行されるクエリなどの他のワークロードにも優れたエクスペリエンスが提供されます。 1 秒あたり 1,000 から 10,000 のメッセージに対応するシンクとして Azure Cosmos DB を使用する場合、Stream Analytics でどのようにスケール アウトが行われるのかを確認したい場合は、[この Azure サンプル プロジェクト](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-streamanalytics-cosmosdb)を試してください。

Azure Cosmos DB 出力のスループットは、1.0 および 1.1 と同じです。 Azure Cosmos DB を使用する Stream Analytics では、互換性レベル 1.2 を使用することを "*強くお勧めします*"。

## <a name="azure-cosmos-db-settings-for-json-output"></a>JSON 出力の Azure Cosmos DB 設定

Stream Analytics で Azure Cosmos DB を出力として作成すると、情報の入力を求める以下のプロンプトが表示されます。

![Azure Cosmos DB 出力ストリームの情報フィールド](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|フィールド           | 説明|
|-------------   | -------------|
|出力エイリアス    | ご利用の Stream Analytics クエリ内でこの出力を参照するエイリアス。|
|サブスクリプション    | Azure サブスクリプション。|
|Account ID      | Azure Cosmos DB アカウントの名前またはエンドポイント URI。|
|アカウント キー     | Azure Cosmos DB アカウントの共有アクセス キー。|
|データベース        | Azure Cosmos DB データベース名。|
|コンテナー名 | `MyContainer` などのコンテナー名。 `MyContainer` という名前のコンテナーが 1 つ存在する必要があります。  |
|ドキュメント ID     | 省略可能。 挿入操作または更新操作の基にする必要がある固有キーとして使用される出力イベント内の列名。 空のままにすると、更新オプションはなく、すべてのイベントが挿入されます。|

Azure Cosmos DB 出力を構成したら、それをクエリ内で [INTO ステートメント](/stream-analytics-query/into-azure-stream-analytics)のターゲットとして使用できます。 Azure Cosmos DB 出力をそのように使用する場合は、[パーティション キーを明示的に設定する必要があります](./stream-analytics-parallelization.md#partitions-in-inputs-and-outputs)。 

出力レコードには、Azure Cosmos DB のパーティション キーの後に名前が付けられた大文字と小文字が区別される列が含まれている必要があります。 より多くの並列処理を実現するには、同じ列を使用する [PARTITION BY 句](./stream-analytics-parallelization.md#embarrassingly-parallel-jobs)がステートメントで必要になることがあります。

クエリの例を次に示します。

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>エラー処理と再試行

Azure Cosmos DB へのイベントの送信中に一時的な障害、サービスの利用不可、またはスロットリングが発生した場合、Stream Analytics では、操作を正常に終了するために無期限に再試行が行われます。 ただし、以下の障害の場合は、再試行されません。

- Unauthorized (HTTP エラー コード 401)
- NotFound (HTTP エラー コード 404)
- Forbidden (HTTP エラー コード 403)
- BadRequest (HTTP エラー コード 400)

## <a name="common-issues"></a>一般的な問題

1. 一意なインデックスの制約がコレクションに追加され、Stream Analytics からの出力データがこの制約に違反しています。 Stream Analytics の出力データが一意制約に違反しないようにするか、または制約を削除してください。 詳細については、「[Azure Cosmos DB における一意キー制約](../cosmos-db/unique-keys.md)」を参照してください。

2. 列 `PartitionKey` は存在しません。

3. 列 `Id` は存在しません。

## <a name="next-steps"></a>次のステップ

* [Azure Stream Analytics からの出力を理解する](stream-analytics-define-outputs.md) 
* [Azure SQL Database への Azure Stream Analytics の出力](stream-analytics-sql-output-perf.md)
* [Azure Stream Analytics でのカスタム BLOB 出力のパーティション分割](stream-analytics-custom-path-patterns-blob-storage-output.md)