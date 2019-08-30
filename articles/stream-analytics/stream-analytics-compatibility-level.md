---
title: Azure Stream Analytics ジョブの互換性レベルを理解する
description: Azure Stream Analytics ジョブの互換性レベルを設定する方法と、最新の互換性レベルでの大きな変更について説明します
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 5/2/2019
ms.openlocfilehash: a6e5cd69a0655bf8235bf5755ab596ca2bde0387
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716171"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Azure Stream Analytics ジョブの互換性レベル

この記事では、Azure Stream Analytics の互換性レベル オプションについて説明します。 Stream Analytics は管理されたサービスであり、定期的に機能が更新され、パフォーマンスが向上しています。 サービスのほとんどのランタイム更新は、自動的にエンド ユーザーが使用できるようになります。 

ただし、サービスの一部の新機能では、既存のジョブの動作変更や、実行中ジョブによるデータの使用方法の変更など、大幅な変更が導入されている場合があります。 互換性レベルの設定を下げたままにすることで、大幅な変更を取り入れずに既存の Stream Analytics ジョブの実行を保持することができます。 最新のランタイム動作への準備ができたら、互換性レベルを上げることでオプトインできます。 

## <a name="choose-a-compatibility-level"></a>互換性レベルを選択する

互換性レベルは、ストリーム分析ジョブの実行時の動作を制御します。 

Azure Stream Analytics では現在、次の 3 つの互換性レベルがサポートされています。

* 1.0 - 以前の動作
* 1.1 - 既定の動作
* 1.2 (プレビュー) - 評価中の最新の改善による最新の動作

元の互換性レベル 1.0 は、数年前の Azure Stream Analytics の一般公開時に導入されました。

新しい Stream Analytics ジョブを作成する場合は、最新の互換性レベルを使用して作成することをお勧めします。 その後に追加される変更や複雑さを避けるために、最新の動作に基づいてジョブの設計を開始ししてください。

## <a name="set-the-compatibility-level"></a>互換性レベルの設定

Stream Analytics ジョブの互換性レベルは、Azure portal または [create job REST API 呼び出し](/rest/api/streamanalytics/stream-analytics-job)を使用して設定できます。

Azure portal でジョブの互換性レベルを更新するには:

1. [Azure portal](https://portal.azure.com) を使用して、Stream Analytics ジョブを見つけます。
2. 互換性レベルを更新する前に、ジョブを**停止**します。 ジョブが実行状態の場合は、互換性レベルを更新できません。
3. **[構成]** 見出しで、 **[互換性レベル]** を選択します。
4. 必要な互換性レベルの値を選択します。
5. ページの下部にある **[保存]** を選択します。

![Azure portal の Stream Analytics の互換性レベル](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

互換性レベルを更新すると、T-SQL コンパイラが、選択された互換性レベルに対応する構文を使用してジョブを検証します。

## <a name="compatibility-level-12-preview"></a>互換性レベル 1.2 (プレビュー)

互換性レベル 1.2 では、以下の大きな変更が導入されました。

### <a name="geospatial-functions"></a>地理空間の関数

**以前のレベル:** Azure Stream Analytics では地理計算が使われていました。

**1.2 レベル:** Azure Stream Analytics では、幾何射影された地理座標を計算できます。 地理空間関数のシグネチャの変更はありません。 ただし、これまでより正確に計算できるよう、セマンティクスは若干異なります。

Azure Stream Analytics では、地理空間参照データのインデックス作成がサポートされています。 高速の結合演算のため、地理空間要素を含む参照データにインデックスを作成できます。

更新された地理空間関数は、Well Known Text (WKT) 地理空間形式の完全な表現力を備えています。 以前の GeoJson ではサポートされていなかったその他の地理空間コンポーネントを指定できます。

詳しくは、「[Azure Stream Analytics の地理空間機能を更新 – クラウドと Azure IoT Edge で使用可能](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/)」をご覧ください。

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>複数のパーティションを持つ入力ソースの並列クエリ実行

**以前のレベル:** Azure Stream Analytics クエリでは、入力ソースのパーティションをまたいでクエリの処理を並列化するには、PARTITION BY 句を使う必要がありました。

**1.2 レベル:** クエリ ロジックを入力ソースのパーティションをまたいで並列化できる場合は、Azure Stream Analytics で個別にクエリ インスタンスが作成されて計算が並列に実行されます。

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>CosmosDB 出力でのネイティブ Bulk API 統合

**以前のレベル:** アップサート動作は "*挿入またはマージ*" でした。

**1.2 レベル:** CosmosDB 出力でのネイティブ Bulk API 統合により、スループットが最大になり、要求の調整が効率的に処理されます。 詳細については、[Azure Cosmos DB ページへの Azure Stream Analytics の出力](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12)に関する記事を参照してください。

アップサート動作は "*挿入または置換*" です。

### <a name="datetimeoffset-when-writing-to-sql-output"></a>SQL 出力に書き込むときの DateTimeOffset

**以前のレベル:** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) 型は、UTC に調整されました。

**1.2 レベル:** DateTimeOffset は調整されなくなりました。

### <a name="long-when-writing-to-sql-output"></a>SQL 出力に書き込むときの Long

**以前のレベル:** 値はターゲットの型に基づいて切り捨てられました。

**1.2 レベル:** ターゲットの型に適合しない値は、出力エラー ポリシーに従って処理されます。

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>SQL 出力への書き込み時のレコードと配列のシリアル化

**以前のレベル:** レコードは "Record" として書き込まれ、配列は "Array" として書き込まれました。

**1.2 レベル:** レコードと配列は JSON 形式でシリアル化されます。

### <a name="strict-validation-of-prefix-of-functions"></a>関数のプレフィックスの厳密な検証

**以前のレベル:** 関数のプレフィックスの厳密な検証はありませんでした。

**1.2 レベル:** Azure Stream Analytics では、関数のプレフィックスの厳密な検証が行われます。 組み込み関数にプレフィックスを追加すると、エラーが発生します。 たとえば、`myprefix.ABS(…)` はサポートされていません。

組み込み集計にプレフィックスを追加しても、エラーになります。 たとえば、`myprefix.SUM(…)` はサポートされていません。

どのようなユーザー定義関数でも、プレフィックス "System" を使用するとエラーになります。

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Cosmos DB 出力アダプターのキー プロパティとして Array と Object が許可されない

**以前のレベル:** Array 型と Object 型は、キー プロパティとしてサポートされていました。

**1.2 レベル:** Array 型と Object 型は、キー プロパティとしてサポートされなくなりました。

## <a name="compatibility-level-11"></a>互換性レベル 1.1

互換性レベル 1.1 では、以下の大きな変更が導入されました。

### <a name="service-bus-xml-format"></a>Service Bus XML 形式

**1.0 レベル:** Azure Stream Analytics が DataContractSerializer を使用していたため、メッセージのコンテンツに XML タグが含まれていました。 例:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**1.1 レベル:** メッセージのコンテンツにはストリームが直接含まれていて、追加のタグは含まれていません。 次に例を示します。`{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>フィールド名の大文字と小文字の区別の保持

**1.0 レベル:** Azure Stream Analytics エンジンによる処理で、フィールド名が小文字に変更されました。

**1.1 レベル:** Azure Stream Analytics エンジンによる処理で、フィールド名の大文字と小文字の区別が保持されます。

> [!NOTE]
> 大文字と小文字の区別の保持は、Edge 環境を使用してホストされている Stream Analytic ジョブではまだ利用できません。 その結果、ジョブが Edge にホストされている場合は、すべてのフィールド名が小文字に変換されます。

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**1.0 レベル:** CREATE TABLE コマンドが FLOAT 列型で NaN (Not-a-Number。 たとえば、Infinity、-Infinity) のイベントをフィルター処理しませんでした。これらの数値の文書化されている範囲を超えているためです。

**1.1 レベル:** CREATE TABLE で、強力なスキーマを指定することができます。 Stream Analytics エンジンによって、データがこのスキーマに準拠していることが検証されます。 このモデルでは、NaN 値を持つイベントをコマンドがフィルター処理できます。

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>JSON の日時文字列の自動アップキャストの無効化

**1.0 レベル:** JSON パーサーが、日付/時刻/ゾーン情報を持つ文字列値を DateTime 型に自動的にアップ キャストし、UTC に変換しました。 この動作により、タイムゾーン情報が失われていました。

**1.1 レベル:** 日付/時刻/ゾーン情報を持つ文字列値を DateTime 型に自動的にアップキャストすることはなくなりました。 そのため、タイムゾーン情報は保持されます。

## <a name="next-steps"></a>次の手順

* [Azure Stream Analytics の入力をトラブルシューティングする](stream-analytics-troubleshoot-input.md)
* [Stream Analytics のリソース正常性](stream-analytics-resource-health.md)
