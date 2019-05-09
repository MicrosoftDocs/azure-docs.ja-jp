---
title: Azure Stream Analytics ジョブの互換性レベルを理解する
description: Azure Stream Analytics ジョブの互換性レベルを設定する方法と、最新の互換性レベルでの大きな変更について説明します
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 6fb93152263d253de983b17d25f02f4c68a172fd
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361386"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Azure Stream Analytics ジョブの互換性レベル
 
互換性レベルとは、Azure Stream Analytics サービスのリリース固有の動作のことです。 Azure Stream Analytics は管理されたサービスであり、定期的に機能が更新され、パフォーマンスが向上しています。 通常、更新は自動的にエンド ユーザーが使用できるようになります。 ただし、一部の新機能では、大きな変更が行われている場合があります。たとえば、既存のジョブの動作の変更、これらのジョブのデータを使用する処理の変更などです。互換性レベルは、Stream Analytics に導入された大きな変更を表すために使用されます。 大きな変更は、常に新しい互換性レベルで導入されます。 

互換性レベルによって、既存のジョブをエラーなしで実行できることが確認できます。 新しい Stream Analytics ジョブを作成する場合は、最新の互換性レベルを使用して作成することをお勧めします。 
 
## <a name="set-a-compatibility-level"></a>互換性レベルの設定 

互換性レベルは、ストリーム分析ジョブの実行時の動作を制御します。 Stream Analytics ジョブの互換性レベルを設定するには、ポータルを使用するか、[create job REST API 呼び出し](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job)を使用します。 Azure Stream Analytics では、現在、"1.0" と "1.1" の 2 つの互換性レベルがサポートされています。 既定では、互換性レベルは "1.0" に設定されています。このレベルは、Azure Stream Analytics の一般公開時に導入されました。 既定値を更新するには、既存の Stream Analytics ジョブに移動し、**[構成]** セクションの **[互換性レベル]** オプションを選択して、値を変更します。 

互換性レベルを更新する前に、ジョブを停止してください。 ジョブが実行状態の場合は、互換性レベルを更新できません。 

![Azure portal の Stream Analytics の互換性レベル](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

 
互換性レベルを更新すると、T-SQL コンパイラが、選択された互換性レベルに対応する構文を使用してジョブを検証します。 

## <a name="major-changes-in-the-latest-compatibility-level-12"></a>最新の互換性レベル (1.2) での大きな変更

互換性レベル 1.2 では、以下の大きな変更が導入されました。

### <a name="geospatial-functions"></a>地理空間の関数 

**以前のバージョン:** Azure Stream Analytics では地理計算が使われていました。

**現在のバージョン:** Azure Stream Analytics では、幾何射影された地理座標を計算できます。 地理空間関数のシグネチャの変更はありません。 ただし、これまでより正確に計算できるよう、セマンティクスは若干異なります。

Azure Stream Analytics では、地理空間参照データのインデックス作成がサポートされています。 高速の結合演算のため、地理空間要素を含む参照データにインデックスを作成できます。

更新された地理空間関数は、Well Known Text (WKT) 地理空間形式の完全な表現力を備えています。 以前の GeoJson ではサポートされていなかったその他の地理空間コンポーネントを指定できます。

詳しくは、「[Azure Stream Analytics の地理空間機能を更新 – クラウドと Azure IoT Edge で使用可能](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/)」をご覧ください。

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>複数のパーティションを持つ入力ソースの並列クエリ実行 

**以前のバージョン:** Azure Stream Analytics クエリでは、入力ソースのパーティションをまたいでクエリの処理を並列化するには、PARTITION BY 句を使う必要がありました。

**現在のバージョン:** クエリ ロジックを入力ソースのパーティションをまたいで並列化できる場合は、Azure Stream Analytics で個別にクエリ インスタンスが作成されて計算が並列に実行されます。

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>CosmosDB 出力でのネイティブ Bulk API 統合

**以前のバージョン:** アップサート動作は "*挿入またはマージ*" でした。

**現在のバージョン:** CosmosDB 出力でのネイティブ Bulk API 統合により、スループットが最大になり、要求の調整が効率的に処理されます。

アップサート動作は "*挿入または置換*" です。

### <a name="datetimeoffset-when-writing-to-sql-output"></a>SQL 出力に書き込むときの DateTimeOffset

**以前のバージョン:**[DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) 型は、UTC に調整されました。

**現在のバージョン:** DateTimeOffset は調整されなくなりました。

### <a name="strict-validation-of-prefix-of-functions"></a>関数のプレフィックスの厳密な検証

**以前のバージョン:** 関数のプレフィックスの厳密な検証はありませんでした。

**現在のバージョン:** Azure Stream Analytics では、関数のプレフィックスの厳密な検証が行われます。 組み込み関数にプレフィックスを追加すると、エラーが発生します。 たとえば、`myprefix.ABS(…)` はサポートされていません。

組み込み集計にプレフィックスを追加しても、エラーになります。 たとえば、`myprefix.SUM(…)` はサポートされていません。

どのようなユーザー定義関数でも、プレフィックス "System" を使用するとエラーになります。

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Cosmos DB 出力アダプターのキー プロパティとして Array と Object が許可されない

**以前のバージョン:** Array 型と Object 型は、キー プロパティとしてサポートされていました。

**現在のバージョン:** Array 型と Object 型は、キー プロパティとしてサポートされなくなりました。


## <a name="next-steps"></a>次の手順
* [Azure Stream Analytics の入力をトラブルシューティングする](stream-analytics-troubleshoot-input.md)
* [Stream Analytics のリソース正常性](stream-analytics-resource-health.md)
