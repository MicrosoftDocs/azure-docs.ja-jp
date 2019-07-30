---
title: Cosmos DB への Azure Stream Analytics の出力
description: この記事では、構造化されていない JSON データに対するデータ アーカイブと待機時間の短いクエリのために、Azure Stream Analytics を使用して、Azure Cosmos DB for JSON 出力に出力を保存する方法について説明します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/11/2019
ms.custom: seodec18
ms.openlocfilehash: de5febaeecd176a8718364720132d3fa4433c57f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443620"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Cosmos DB への Azure Stream Analytics の出力  
Stream Analytics では、 JSON 出力のターゲットを [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) にすることができるため、構造化されていない JSON データに対してデータ アーカイブと待機時間の短いクエリを有効にすることができます。 このドキュメントでは、この構成を実装するためのベスト プラクティスについて説明します。

Cosmos DB を詳しく理解していない場合は、開始する前に [Azure Cosmos DB のラーニング パス](https://azure.microsoft.com/documentation/learning-paths/documentdb/)に関するページをご覧ください。 

> [!Note]
> 現時点では、Azure Stream Analytics は、**SQL API** を使用した Azure CosmosDB への接続のみをサポートしています。
> その他の Azure Cosmos DB API は、まだサポートされていません。 Azure Stream Analytics を、その他のAPI で作成した Azure Cosmos DB アカウントへ接続する場合は、データが正しく格納されない可能性があります。 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>出力ターゲットとしての Cosmos DB の基礎
Stream Analytics で Azure Cosmos DB 出力を使用すると、ストリーム処理の結果を JSON 出力として自分の Cosmos DB コンテナーに書き込むことができます。 Stream Analytics は、データベース内にコンテナーを作成せず、代わりにユーザーが前もってコンテナーを作成するように要求します。 これにより、Cosmos DB コンテナーの課金によるコストをユーザーが制御できるようになり、[Cosmos DB API シリーズ](https://msdn.microsoft.com/library/azure/dn781481.aspx)を使用することでコンテナーのパフォーマンス、整合性、容量などを直接調整できるようになりました。

> [!Note]
> Azure Cosmos DB のファイアウォールで許可されている IP の一覧に 0.0.0.0 を追加する必要があります。

Cosmos DB コンテナーの一部のオプションの詳細を以下に示します。

## <a name="tune-consistency-availability-and-latency"></a>調整の整合性、可用性、および待機時間
Azure Cosmos DB では、アプリケーション要件を満たすために、データベースやコンテナーを微調整し、整合性、可用性、待ち時間、およびスループットの間で妥協点を見つけることができます。 読み取りおよび書き込みの待機時間に対してシナリオで求められる読み取りの一貫性レベルに応じて、データベース アカウントでの一貫性レベルを選択することができます。 スループットは、コンテナー上の要求単位 (RU) をスケールアップすることによって向上させることができます。 また Azure Cosmos DB では、コンテナーへの各 CRUD 操作に対する同期インデックス作成も、既定で有効になっています。 この機能も、Azure Cosmos DB で書き込みと読み取りのパフォーマンスを制御するための便利なオプションの 1 つです。 詳しくは、[データベースとクエリの一貫性レベルの変更](../cosmos-db/consistency-levels.md) に関する記事をご覧ください。

## <a name="upserts-from-stream-analytics"></a>Stream Analytics からのアップサート
Stream Analytics を Azure Cosmos DB と統合することで、特定のドキュメント ID 列に基づき、コンテナーでレコードを挿入または更新できるようになります。 この機能は、 *アップサート*とも呼ばれています。

Stream Analytics では、オプティミスティック アップサート手法を使用しています。この手法では、ドキュメント ID の競合が原因で挿入に失敗した場合にのみ更新が実行されます。 互換性レベル 1.0 では、この更新は PATCH として実行されるため、ドキュメントに対する部分更新が可能です。つまり、新しいプロパティの追加や既存のプロパティの置き換えは段階的に実行されます。 ただし、JSON ドキュメント内の配列プロパティの値を変更すると、配列全体が上書きされることになるので注意してください。つまり、配列はマージされません。 1\.2 では、アップサートの動作がドキュメントの挿入または置換に変更されています。 これについては、下の互換性レベル 1.2 に関するセクションで詳しく説明します。

受信した JSON ドキュメントに既存の ID フィールドがある場合、そのフィールドが Cosmos DB のドキュメント ID 列として自動的に使用され、後続の書き込みもすべてそのように処理されるため、次のいずれかの状態になります。
- 一意の ID が挿入される
- 重複した ID と 'ID' に設定された 'ドキュメント ID' によりアップサートとなる
- 最初のドキュメントの後に、重複した ID と 'ドキュメント ID' が設定されていないことでエラーになる

重複した ID を持つドキュメントを含め、<i>すべて</i>のドキュメントを保存する場合は、(AS キーワードを使用して) クエリ内の ID フィールドの名前を変更し、Cosmos DB により ID フィールドを作成するか、(AS キーワードか 'ドキュメント ID' 設定を使用して) 別の列の値と ID を置き換えます。

## <a name="data-partitioning-in-cosmos-db"></a>Cosmos DB でのデータ パーティション分割
Azure Cosmos DB ではワークロードに基づいてパーティションが自動的にスケーリングされるので、データのパーティション分割には[無制限](../cosmos-db/partition-data.md)の Azure Cosmos DB コンテナーがお勧めです。 無制限コンテナーに書き込む場合、Stream Analytics は以前のクエリ手順または入力のパーティション分割スキームと同数の並列ライターを使用します。
> [!NOTE]
> 現時点で、Azure Stream Analytics は最上位のパーティション キーを使用した無制限のコンテナーのみをサポートしています。 たとえば、`/region` がサポートされています。 入れ子になったパーティション キー (たとえば、`/region/name`) はサポートされていません。 

選択したパーティション キーによっては、次の "_警告_" が出力される場合があります。

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

パーティション キー プロパティを選ぶ際に重要なのは、多数の異なる値を持つものを選ぶことです。そうすると、それらの値全体でワークロードが均等に分散されます。 パーティションを分割すると、当然ながら、同一のパーティション キーを含む要求ではスループットが単一パーティションの最大値に制限されます。 また、同一パーティション キーに属するドキュメントのストレージ容量は最大 10GB に制限されます。 理想的なパーティション キーとは、クエリ内でフィルターとして頻繁に使用され、ソリューションのスケーラビリティを確保するために十分なカーディナリティを持つものを指します。

また、パーティション キーは DocumentDB のストアド プロシージャやトリガーでトランザクションの境界条件としても使用されます。 パーティション キーは、トランザクション内で同時に発生するドキュメントが同一のパーティション キーの値を共有できるように選択する必要があります。 [Cosmos DB でのパーティション分割](../cosmos-db/partitioning-overview.md)に関する記事に、パーティション キーの選択について詳しく説明されています。

固定の Azure Cosmos DB コンテナーの場合、コンテナーがいっぱいになっても Stream Analytics をスケールアップまたはスケールアウトすることができません。 上限は 10 GB と 10,000 RU/秒スループットです。  固定コンテナーから無制限のコンテナー (1,000 RU/秒以上のスループットとパーティション キーを備えたコンテナーなど) にデータを移行するには、[データ移行ツール](../cosmos-db/import-data.md)または[変更フィード ライブラリ](../cosmos-db/change-feed.md)を使用する必要があります。

複数の固定コンテナーに書き込む機能は非推奨であり、Stream Analytics ジョブのスケールアウトには推奨されません。

## <a name="improved-throughput-with-compatibility-level-12"></a>互換性レベル 1.2 でのスループットの向上
互換性レベル 1.2 では、Stream Analytics で Cosmos DB への一括書き込みのためのネイティブ統合がサポートされます。 これにより、スループットを最大化し、スロットル リクエストを効率的に処理して、効果的に Cosmos DB への書き込みを行うことができます。 この強化された書き込みメカニズムは、新しい互換性レベルで利用できます。これは、アップサートの動作が異なるためです。  1\.2 以前のアップサートの動作は、ドキュメントの挿入またはマージです。 1\.2 では、アップサートの動作がドキュメントの挿入または置換に変更されています。 

1\.2 以前では、Cosmos DB へのドキュメントの一括アップサートは、カスタム ストアド プロシージャを使用してパーティション キーごとに行われます。このとき、バッチはトランザクションとして書き込まれます。 1 つのレコードで一時的なエラー (スロットリング) が発生しただけでも、バッチ全体を再試行する必要があります。 このため、妥当なスロットリングのシナリオであっても、比較的低速になります。 以下の比較は、このようなジョブが 1.2 でどのように動作するかを示しています。

次の例は、同じイベント ハブ入力から読み取られるまったく同じ 2 つの Stream Analytics ジョブを示しています。 どちらの Stream Analytics ジョブもパススルー クエリにより[完全にパーティション分割](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs)されており、同じ CosmosDB コンテナーに書き込みます。 左側のメトリックは互換性レベル 1.0 で構成されたジョブからのもので、右側は 1.2 で構成されたものです。 Cosmos DB コンテナーのパーティション キーは、入力イベントからの一意の GUID です。

![Stream Analytics メトリックの比較](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

イベント ハブでのイベントの受信速度は、Cosmos DB コンテナー (20K RU) が取り込むように構成されている速度の 2 倍の速さなので、Cosmos DB でのスロットリングが必要です。 しかし、1.2 のジョブは、一環してより高いスループット (出力イベント/分) とより低い平均 SU 使用率 (%) で書き込みを行います。 この差は、お使いの環境で選択しているイベントの形式、入力イベント/メッセージのサイズ、パーティション キー、クエリなど、他の要因によっても変わってきます。

![Cosmos DB のメトリックの比較](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

1\.2 を使用することで、Stream Analytics は Cosmos DB で使用可能なスループットの 100% をより賢く利用することができ、スロットリング/速度の制限による再送信はほとんどありません。 これにより、コンテナーで同時に実行されるクエリなどの他のワークロードにも優れたエクスペリエンスが提供されます。 1k から 10k のメッセージ/秒のシンクとして Cosmos DB を使用する ASA がどのようにスケール アウトするか試してみたい場合は、この [azure サンプル プロジェクト](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb)で実行できます。
Cosmos DB の出力スループットは 1.0 と 1.1 で同じある点に注意してください。 現在 1.2 は既定ではないため、Stream Analytics ジョブの[互換性レベルを設定](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level)するには、ポータルを使用するか、[create job REST API 呼び出し](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job)を使用します。 Cosmos DB を使用する ASA では互換性レベル 1.2 を使用することを "*強くお勧め*" します。 



## <a name="cosmos-db-settings-for-json-output"></a>JSON 出力の Cosmos DB 設定

Stream Analytics で Cosmos DB を出力として作成すると、情報の入力を求めるプロンプトが以下のように表示されます。 このセクションでは、各プロパティの定義について説明します。

![documentdb stream analytics 出力画面](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|フィールド           | 説明|
|-------------   | -------------|
|出力エイリアス    | ASA クエリ内でこの出力を意味する別名。|
|サブスクリプション    | Azure サブスクリプションを選択します。|
|Account ID      | Azure Cosmos DB アカウントの名前またはエンドポイント URI。|
|アカウント キー     | Azure Cosmos DB アカウントの共有アクセス キー。|
|Database        | Azure Cosmos DB データベース名。|
|コンテナー名 | 使用するコンテナーの名前。 `MyContainer` は有効な入力の例です。`MyContainer` という 1 つのコンテナーが存在する必要があります。  |
|ドキュメント ID     | 省略可能。 挿入操作または更新操作の基にする必要がある固有キーとして使用される出力イベント内の列名。 空のままにすると、更新オプションはなく、すべてのイベントが挿入されます。|
