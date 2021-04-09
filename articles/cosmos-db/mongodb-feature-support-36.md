---
title: Azure Cosmos DB の MongoDB (3.6 バージョン) 用 API でサポートされる機能と構文
description: Azure Cosmos DB の MongoDB (3.6 バージョン) 用 API でサポートされる機能と構文について説明します。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 03/02/2021
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 0b6f85a675dc98928309870ea177629203db39e7
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557337"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-36-version-supported-features-and-syntax"></a>Azure Cosmos DB の MongoDB (3.6 バージョン) 用 API: サポートされる機能と構文
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB は、Microsoft のグローバルに分散されたマルチモデル データベース サービスです。 Azure Cosmos DB の MongoDB 用 API との通信は、オープン ソースで公開されている任意の MongoDB クライアント [ドライバー](https://docs.mongodb.org/ecosystem/drivers)を使って行うことができます。 Azure Cosmos DB の MongoDB 用 API では、MongoDB [ワイヤ プロトコル](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)に従うことにより、既存のクライアント ドライバーを利用できます。

Azure Cosmos DB の MongoDB 用 API を使用すれば、使い慣れた MongoDB API を活用できます。[グローバル配信](distribute-data-globally.md)、[自動シャーディング](partitioning-overview.md)、可用性や待ち時間の保証、保存時の暗号化、バックアップを始めとする Cosmos DB のエンタープライズ機能も、すべて利用できます。

> [!NOTE]
> バージョン 3.6 の MongoDB 用 Cosmos DB API には現在、EOL (End Of Life: サポート終了) のプランがありません。 将来の EOL に関する通知は最低でも 3 年となります。

## <a name="protocol-support"></a>プロトコルのサポート

Azure Cosmos DB の MongoDB 用 API は、新しいアカウントでは、既定で MongoDB サーバー バージョン **3.6** と互換性があります。 以下に、サポートされている演算子およびすべての制限事項や例外の一覧を示します。 これらのプロトコルを認識するクライアント ドライバーはすべて、Azure Cosmos DB の MongoDB 用 API に接続できるはずです。 Azure Cosmos DB の MongoDB 用 API アカウントを使用するときは、3.6 バージョンのアカウントのエンドポイントが `*.mongo.cosmos.azure.com` という形式であるのに対し、3.2 バージョンのアカウントのエンドポイントは `*.documents.azure.com` という形式であることに注意してください。

## <a name="query-language-support"></a>クエリ言語のサポート

Azure Cosmos DB の MongoDB 用 API では、MongoDB クエリ言語のコンストラクトが包括的にサポートされています。 次のセクションでは、Azure Cosmos DB で現在サポートされているサーバー操作、演算子、ステージ、コマンド、およびオプションの詳細な一覧を示します。

> [!NOTE]
> この記事では、サポートされているサーバー コマンドの一覧のみを示し、クライアント側のラッパー関数については除外しています。 `deleteMany()` や `updateMany()` などのクライアント側のラッパー関数は、内部では `delete()` や `update()` といったサーバー コマンドを利用しています。 サポートされているサーバー コマンドを利用する関数は、Azure Cosmos DB の MongoDB 用 API と互換性があります。

## <a name="database-commands"></a>データベース コマンド

Azure Cosmos DB の MongoDB 用 API では、次のデータベース コマンドがサポートされています。

### <a name="query-and-write-operation-commands"></a>クエリおよび書き込み操作コマンド

| command | サポートされています |
|---------|---------|
| [change streams](mongodb-change-streams.md) | はい |
| delete | はい |
| eval | いいえ |
| 検索 | はい |
| findAndModify | はい |
| getLastError | はい |
| getMore | はい |
| getPrevError | いいえ |
| insert | はい |
| parallelCollectionScan | いいえ |
| resetError | いいえ |
| update | はい |

### <a name="authentication-commands"></a>認証コマンド

| command | サポートされています |
|---------|---------|
| authenticate | はい |
| getnonce | はい |
| logout | はい |

### <a name="administration-commands"></a>管理コマンド

| command | サポートされています |
|---------|---------|
| cloneCollectionAsCapped | いいえ |
| collMod | いいえ |
| connectionStatus | いいえ |
| convertToCapped | いいえ |
| copydb | いいえ |
| create | はい |
| createIndexes | はい |
| currentOp | はい |
| drop | はい |
| dropDatabase | はい |
| dropIndexes | はい |
| filemd5 | はい |
| killCursors | はい |
| killOp | いいえ |
| listCollections | はい |
| listDatabases | はい |
| listIndexes | はい |
| reIndex | はい |
| renameCollection | いいえ |


### <a name="diagnostics-commands"></a>診断コマンド

| command | サポートされています |
|---------|---------|
| buildInfo | はい |
| collStats | はい |
| connPoolStats | いいえ |
| connectionStatus | いいえ |
| dataSize | いいえ |
| dbHash | いいえ |
| dbStats | はい |
| explain | はい |
| features | いいえ |
| hostInfo | はい |
| listDatabases | はい |
| listCommands | いいえ |
| profiler | いいえ |
| serverStatus | いいえ |
| top | いいえ |
| whatsmyuri | はい |

<a name="aggregation-pipeline"></a>

## <a name="aggregation-pipelinea"></a>集計パイプライン</a>

### <a name="aggregation-commands"></a>集計コマンド

| command | サポートされています |
|---------|---------|
| 集計 (aggregate) | はい |
| count | はい |
| distinct | はい |
| mapReduce | いいえ |

### <a name="aggregation-stages"></a>集計ステージ

| command | サポートされています |
|---------|---------|
| $addFields | はい |
| $bucket | いいえ |
| $bucketAuto | いいえ |
| $changeStream | はい |
| $collStats | いいえ |
| $count | はい |
| $currentOp | いいえ |
| $facet | はい |
| $geoNear | はい |
| $graphLookup | はい |
| $group | はい |
| $indexStats | いいえ |
| $limit | はい |
| $listLocalSessions | いいえ |
| $listSessions | いいえ |
| $lookup | 部分的 |
| $match | はい |
| $out | はい |
| $project | はい |
| $redact | はい |
| $replaceRoot | はい |
| $replaceWith | いいえ |
| $sample | はい |
| $skip | はい |
| $sort | はい |
| $sortByCount | はい |
| $unwind | はい |

> [!NOTE]
> `$lookup` では、サーバー バージョン3.6 で導入された[非相関サブクエリ](https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/#join-conditions-and-uncorrelated-sub-queries)機能をまだサポートしていません。 `let` および `pipeline` フィールドで `$lookup` 演算子を使用しようとすると、`let is not supported` という文字列を含むメッセージが表示されエラーが発生します。

### <a name="boolean-expressions"></a>ブール式

| command | サポートされています |
|---------|---------|
| $and | はい |
| $not | はい |
| $or | はい |

### <a name="set-expressions"></a>設定式

| command | サポートされています |
|---------|---------|
| $setEquals | はい |
| $setIntersection | はい |
| $setUnion | はい |
| $setDifference | はい |
| $setIsSubset | はい |
| $anyElementTrue | はい |
| $allElementsTrue | はい |

### <a name="comparison-expressions"></a>比較式

| command | サポートされています |
|---------|---------|
| $cmp | はい |
| $eq | はい | 
| $gt | はい | 
| $gte | はい | 
| $lt | はい |
| $lte | はい | 
| $ne | はい | 
| $in | はい | 
| $nin | はい | 

### <a name="arithmetic-expressions"></a>算術式

| command | サポートされています |
|---------|---------|
| $abs | はい |
| $add | はい |
| $ceil | はい |
| $divide | はい |
| $exp | はい |
| $floor | はい |
| $ln | はい |
| $log | はい |
| $log10 | はい |
| $mod | はい |
| $multiply | はい |
| $pow | はい |
| $sqrt | はい |
| $subtract | はい |
| $trunc | はい |

### <a name="string-expressions"></a>文字列式

| command | サポートされています |
|---------|---------|
| $concat | はい |
| $indexOfBytes | はい |
| $indexOfCP | はい |
| $split | はい |
| $strLenBytes | はい |
| $strLenCP | はい |
| $strcasecmp | はい |
| $substr | はい |
| $substrBytes | はい |
| $substrCP | はい |
| $toLower | はい |
| $toUpper | はい |

### <a name="text-search-operator"></a>テキスト検索演算子

| command | サポートされています |
|---------|---------|
| $meta | いいえ |

### <a name="array-expressions"></a>配列式

| command | サポートされています |
|---------|---------|
| $arrayElemAt | はい |
| $arrayToObject | はい |
| $concatArrays | はい |
| $filter | はい |
| $indexOfArray | はい |
| $isArray | はい |
| $objectToArray | はい |
| $range | はい |
| $reverseArray | はい |
| $reduce | はい |
| $size | はい |
| $slice | はい |
| $zip | はい |
| $in | はい |

### <a name="variable-operators"></a>変数演算子

| command | サポートされています |
|---------|---------|
| $map | はい |
| $let | はい |

### <a name="system-variables"></a>システム変数

| command | サポートされています |
|---------|---------|
| $$CURRENT | はい |
| $$DESCEND | はい |
| $$KEEP | はい |
| $$PRUNE | はい |
| $$REMOVE | はい |
| $$ROOT | はい |

### <a name="literal-operator"></a>リテラル演算子

| command | サポートされています |
|---------|---------|
| $literal | はい |

### <a name="date-expressions"></a>日付式

| command | サポートされています |
|---------|---------|
| $dayOfYear | はい |
| $dayOfMonth | はい |
| $dayOfWeek | はい |
| $year | はい |
| $month | はい | 
| $week | はい |
| $hour | はい |
| $minute | はい | 
| $second | はい |
| $millisecond | はい | 
| $dateToString | はい |
| $isoDayOfWeek | はい |
| $isoWeek | はい |
| $dateFromParts | いいえ | 
| $dateToParts | いいえ |
| $dateFromString | いいえ |
| $isoWeekYear | はい |

### <a name="conditional-expressions"></a>条件式

| command | サポートされています |
|---------|---------|
| $cond | はい |
| $ifNull | はい |
| $switch | はい |

### <a name="data-type-operator"></a>データ型演算子

| command | サポートされています |
|---------|---------|
| $type | はい |

### <a name="accumulator-expressions"></a>アキュムレータ式

| command | サポートされています |
|---------|---------|
| $sum | はい |
| $avg | はい |
| $first | はい |
| $last | はい |
| $max | はい |
| $min | はい |
| $push | はい |
| $addToSet | はい |
| $stdDevPop | はい |
| $stdDevSamp | はい |

### <a name="merge-operator"></a>マージ演算子

| command | サポートされています |
|---------|---------|
| $mergeObjects | はい |

## <a name="data-types"></a>データ型

| command | サポートされています |
|---------|---------|
| Double | はい |
| String | はい |
| Object | はい |
| Array | はい |
| Binary Data | はい | 
| ObjectId | はい |
| Boolean | はい |
| Date | はい |
| [Null] | はい |
| 32 ビット整数 (int) | はい |
| Timestamp | はい |
| 64 ビット整数 (long) | はい |
| MinKey | はい |
| MaxKey | はい |
| Decimal128 | はい | 
| Regular Expression | はい |
| JavaScript | はい |
| JavaScript (スコープあり)| はい |
| 未定義。 | はい |

## <a name="indexes-and-index-properties"></a>インデックスとそのプロパティ

### <a name="indexes"></a>インデックス

| command | サポートされています |
|---------|---------|
| 単一フィールド インデックス | はい |
| 複合インデックス | はい |
| 複数キー インデックス | はい |
| テキスト インデックス | いいえ |
| 2dsphere | はい |
| 2d インデックス | いいえ |
| ハッシュ インデックス | はい |

### <a name="index-properties"></a>インデックスのプロパティ

| command | サポートされています |
|---------|---------|
| TTL | はい |
| 一意 | はい |
| 部分的 | いいえ |
| 大文字と小文字の区別をしない | いいえ |
| スパース | いいえ |
| バックグラウンド | はい |

## <a name="operators"></a>オペレーター

### <a name="logical-operators"></a>論理演算子

| command | サポートされています |
|---------|---------|
| $or | はい |
| $and | はい |
| $not | はい |
| $nor | はい | 

### <a name="element-operators"></a>要素演算子

| command | サポートされています |
|---------|---------|
| $exists | はい |
| $type | はい |

### <a name="evaluation-query-operators"></a>評価クエリ演算子

| command | サポートされています |
|---------|---------|
| $expr | いいえ |
| $jsonSchema | いいえ |
| $mod | はい |
| $regex | はい |
| $text | いいえ (サポートされていません。 代わりに $regex を使用してください)。| 
| $where | いいえ | 

$regex クエリでは、左固定の式でインデックス検索が可能です。 ただし、'i' 修飾子 (大文字と小文字の区別なし) や 'm' 修飾子 (複数行) を使用すると、すべての式でコレクション スキャンが発生します。

'$' または '|' を含める必要がある場合、2 つ (以上) の正規表現クエリを作成することをお勧めします。 たとえば、元のクエリとして ```find({x:{$regex: /^abc$/})``` がある場合、次のように変更する必要があります:

```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```

最初の部分では、インデックスを使用して検索を ^abc で始まるドキュメントに制限し、2 番目の部分で入力そのものを照合します。 バー演算子 '|' は "or" 関数として機能します。そのためクエリ ```find({x:{$regex: /^abc |^def/})``` は、フィールド 'x' の値が "abc" または "def" で始まるドキュメントに一致します。 インデックスを利用するには、```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })``` のように、クエリを 2 つの異なるクエリに分割し、$or 演算子で結合することをお勧めします。

### <a name="array-operators"></a>配列演算子

| command | サポートされています | 
|---------|---------|
| $all | はい | 
| $elemMatch | はい | 
| $size | はい | 

### <a name="comment-operator"></a>コメント演算子

| command | サポートされています | 
|---------|---------|
| $comment | はい | 

### <a name="projection-operators"></a>射影演算子

| command | サポートされています |
|---------|---------|
| $elemMatch | はい |
| $meta | いいえ |
| $slice | はい |

### <a name="update-operators"></a>更新演算子

#### <a name="field-update-operators"></a>フィールド更新演算子

| command | サポートされています |
|---------|---------|
| $inc | はい |
| $mul | はい |
| $rename | はい |
| $setOnInsert | はい |
| $set | はい |
| $unset | はい |
| $min | はい |
| $max | はい |
| $currentDate | はい |

#### <a name="array-update-operators"></a>配列更新演算子

| command | サポートされています |
|---------|---------|
| $ | はい |
| $[]| はい |
| $[<identifier>]| はい |
| $addToSet | はい |
| $pop | はい |
| $pullAll | はい |
| $pull | はい |
| $push | はい |
| $pushAll | はい |


#### <a name="update-modifiers"></a>更新修飾子

| command | サポートされています |
|---------|---------|
| $each | はい |
| $slice | はい |
| $sort | はい |
| $position | はい |

#### <a name="bitwise-update-operator"></a>ビット単位更新演算子

| command | サポートされています |
|---------|---------|
| $bit | はい | 
| $bitsAllSet | いいえ |
| $bitsAnySet | いいえ |
| $bitsAllClear | いいえ |
| $bitsAnyClear | いいえ |

### <a name="geospatial-operators"></a>地理空間演算子

演算子 | サポートされています | 
--- | --- |
$geoWithin | はい |
$geoIntersects | はい | 
$near | はい |
$nearSphere | はい |
$geometry | はい |
$minDistance | はい |
$maxDistance | はい |
$center | いいえ |
$centerSphere | いいえ |
$box | いいえ |
$polygon | いいえ |

## <a name="sort-operations"></a>並べ替え操作

`findOneAndUpdate` 操作を使用する場合、単一フィールドに対する並べ替え操作はサポートされていますが、複数フィールドに対する並べ替え操作はサポートされていません。

## <a name="unique-indexes"></a>一意なインデックス

[一意なインデックス](mongodb-indexing.md#unique-indexes)によって、特定のフィールドの値が、コレクション内のすべてのドキュメントにわたって重複していないことが保証されます。これは、既定の "_id" キーで一意性が保持される方法と似ています。 Cosmos DB で一意なインデックスを作成するには、`unique` 制約パラメーターを指定して `createIndex` コマンドを実行します。

```javascript
globaldb:PRIMARY> db.coll.createIndex( { "amount" : 1 }, {unique:true} )
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

## <a name="compound-indexes"></a>複合インデックス

[複合インデックス](mongodb-indexing.md#compound-indexes-mongodb-server-version-36)では、最大 8 つのフィールドから成るフィールド グループにインデックスを付けることができます。 この種のインデックスは、ネイティブの MongoDB 複合インデックスとは異なります。 Azure Cosmos DB の複合インデックスは、複数のフィールドに適用される並べ替え操作に使用されます。 複合インデックスを作成するには、パラメーターとして複数のプロパティを指定する必要があります。

```javascript
globaldb:PRIMARY> db.coll.createIndex({"amount": 1, "other":1})
{
        "createdCollectionAutomatically" : false, 
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 2,
        "ok" : 1
}
```

## <a name="gridfs"></a>GridFS

Azure Cosmos DB では、GridFS と互換性のある MongoDB ドライバーを通じて GridFS がサポートされています。

## <a name="replication"></a>レプリケーション

Cosmos DB では、最下位のレイヤーで、自動のネイティブ レプリケーションがサポートされています。 このロジックは、低待機時間のグローバルなレプリケーションも実現するために拡張されています。 Cosmos DB Cosmos では、手動のレプリケーション コマンドはサポートされていません。





## <a name="retryable-writes"></a>再試行可能書き込み

Azure Cosmos DB では、再試行可能書き込みはまだサポートされていません。 クライアントのドライバーでは、接続文字列に `retryWrites=false` を追加する必要があります。

## <a name="sharding"></a>シャーディング

Azure Cosmos DB は、自動のサーバー側シャーディングをサポートしています。 シャードの作成、配置、バランシングが自動的に管理されます。 Azure Cosmos DB では、手動のシャーディング コマンドはサポートされていません。つまり、addShard、balancerStart、moveChunk などのコマンドを呼び出す必要はありません。必要なことは、コンテナーの作成時やデータの照会時にシャード キーを指定するだけです。

## <a name="sessions"></a>セッション

Azure Cosmos DB では、サーバー側のセッション コマンドはまだサポートされていません。

## <a name="time-to-live-ttl"></a>Time-to-live (TTL)

Azure Cosmos DB では、ドキュメントのタイムスタンプに基づく Time-to-live (TTL) がサポートされます。 コレクションに対して TTL を有効にするには、[Azure portal](https://portal.azure.com) を使用します。

## <a name="user-and-role-management"></a>ユーザーとロールの管理

Azure Cosmos DB では、ユーザーとロールはまだサポートされていません。 ただし、Azure ロールベース アクセス制御 (Azure RBAC) と、[Azure portal](https://portal.azure.com) ([接続文字列] ペイン) から取得できる読み取り/書き込みおよび読み取り専用のパスワードまたはキーがサポートされています。

## <a name="write-concern"></a>書き込み確認

一部のアプリケーションでは、書き込み操作中に必要な応答数を指定する[書き込み確認](https://docs.mongodb.com/manual/reference/write-concern/)が利用されています。 Azure Cosmos DB でレプリケーションが処理される方法のため、厳密な整合性を使用する場合、すべての書き込みが既定で自動的にマジョリティ クォーラムになります。 クライアント コードによって指定される書き込み確認はすべて無視されます。 詳細については、[整合性レベルを使用した可用性とパフォーマンスの最大化](consistency-levels.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

- 詳細については、[Mongo 3.6 バージョンの機能](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)を参照してください。
- Azure Cosmos DB の MongoDB 用 API と共に [Studio 3T を使用する](mongodb-mongochef.md)方法を学習します。
- Azure Cosmos DB の MongoDB 用 API と共に [Robo 3T を使用する](mongodb-robomongo.md)方法を学びます。
- Azure Cosmos DB の MongoDB 用 API を使用した MongoDB の[サンプル](mongodb-samples.md)を調査します。
