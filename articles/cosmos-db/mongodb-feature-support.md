---
title: 'Azure Cosmos DB の MongoDB (3.2 バージョン) 用 API: サポートされる機能と構文'
description: Azure Cosmos DB の MongoDB (3.2 バージョン) 用 API でサポートされる機能と構文について説明します。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 10/16/2019
author: sivethe
ms.author: sivethe
ms.openlocfilehash: ce6b01a99c29020b0ede5a860da9d3d6f37e9f06
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104605751"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-32-version-supported-features-and-syntax"></a>Azure Cosmos DB の MongoDB (3.2 バージョン) 用 API: サポートされる機能と構文
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB は、Microsoft のグローバルに分散されたマルチモデル データベース サービスです。 Azure Cosmos DB の MongoDB 用 API との通信は、オープン ソースで公開されている任意の MongoDB クライアント [ドライバー](https://docs.mongodb.org/ecosystem/drivers)を使って行うことができます。 Azure Cosmos DB の MongoDB 用 API では、MongoDB [ワイヤ プロトコル](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)に従うことにより、既存のクライアント ドライバーを利用できます。

Azure Cosmos DB の MongoDB 用 API を使用すれば、使い慣れた MongoDB API を活用できます。[グローバル配信](distribute-data-globally.md)、[自動シャーディング](partitioning-overview.md)、可用性や待ち時間の保証、すべてのフィールドの自動インデックス作成、保存時の暗号化、バックアップを始めとする Cosmos DB のエンタープライズ機能も、すべて利用できます。

> [!NOTE]
> バージョン 3.2 の MongoDB 用 Cosmos DB API には現在、EOL (End Of Life: サポート終了) のプランがありません。 将来の EOL に関する通知は最低でも 3 年となります。

## <a name="protocol-support"></a>プロトコルのサポート

Azure Cosmos DB の MongoDB 用 API のすべての新しいアカウントは、MongoDB サーバー バージョン **3.6** と互換性があります。 この記事では、MongoDB バージョン 3.2 について説明します。 以下に、サポートされている演算子およびすべての制限事項や例外の一覧を示します。 これらのプロトコルを認識するクライアント ドライバーはすべて、Azure Cosmos DB の MongoDB 用 API に接続できるはずです。 

また、Azure Cosmos DB の MongoDB 用 API は、条件を満たすアカウントにシームレスなアップグレード エクスペリエンスも提供します。 詳細については、[MongoDB バージョン アップグレード ガイド](mongodb-version-upgrade.md)を参照してください。

## <a name="query-language-support"></a>クエリ言語のサポート

Azure Cosmos DB の MongoDB 用 API では、MongoDB クエリ言語のコンストラクトが包括的にサポートされています。 以下に、現在サポートされている操作、演算子、ステージ、コマンド、およびオプションの詳細な一覧を示します。

## <a name="database-commands"></a>データベース コマンド

Azure Cosmos DB の MongoDB 用 API では、次のデータベース コマンドがサポートされています。

> [!NOTE]
> この記事では、サポートされているサーバー コマンドの一覧のみを示し、クライアント側のラッパー関数については除外しています。 `deleteMany()` や `updateMany()` などのクライアント側のラッパー関数は、内部では `delete()` や `update()` といったサーバー コマンドを利用しています。 サポートされているサーバー コマンドを利用する関数は、Azure Cosmos DB の MongoDB 用 API と互換性があります。

### <a name="query-and-write-operation-commands"></a>クエリおよび書き込み操作コマンド

- delete
- 検索
- findAndModify
- getLastError
- getMore
- insert
- update

### <a name="authentication-commands"></a>認証コマンド

- logout
- authenticate
- getnonce

### <a name="administration-commands"></a>管理コマンド

- dropDatabase
- listCollections
- drop
- create
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- connectionStatus
- reIndex

### <a name="diagnostics-commands"></a>診断コマンド

- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"></a>

## <a name="aggregation-pipelinea"></a>集計パイプライン</a>

Cosmos DB では、パブリック プレビューで MongoDB 3.2 の集計パイプラインがサポートされています。 パブリック プレビューの利用を開始する手順については、[Azure ブログ](https://azure.microsoft.com/blog/azure-cosmosdb-extends-support-for-mongodb-aggregation-pipeline-unique-indexes-and-more/)を参照してください。

### <a name="aggregation-commands"></a>集計コマンド

- 集計 (aggregate)
- count
- distinct

### <a name="aggregation-stages"></a>集計ステージ

- $project
- $match
- $limit
- $skip
- $unwind
- $group
- $sample
- $sort
- $lookup
- $out
- $count
- $addFields

### <a name="aggregation-expressions"></a>集計式

#### <a name="boolean-expressions"></a>ブール式

- $and
- $or
- $not

#### <a name="set-expressions"></a>設定式

- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>比較式

- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>算術式

- $abs
- $add
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $multiply
- $pow
- $sqrt
- $subtract
- $trunc

#### <a name="string-expressions"></a>文字列式

- $concat
- $indexOfBytes
- $indexOfCP
- $split
- $strLenBytes
- $strLenCP
- $strcasecmp
- $substr
- $substrBytes
- $substrCP
- $toLower
- $toUpper

#### <a name="array-expressions"></a>配列式

- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $in

#### <a name="date-expressions"></a>日付式

- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $second
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>条件式

- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>集計アキュムレータ

- $sum
- $avg
- $first
- $last
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>オペレーター

以下の演算子が、対応するそれらの使用例でサポートされています。 下記のクエリで使用されているこのサンプル ドキュメントを考慮に入れてください。

```json
{
  "Volcano Name": "Rainier",
  "Country": "United States",
  "Region": "US-Washington",
  "Location": {
    "type": "Point",
    "coordinates": [
      -121.758,
      46.87
    ]
  },
  "Elevation": 4392,
  "Type": "Stratovolcano",
  "Status": "Dendrochronology",
  "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

| 演算子 | 例 |
| --- | --- |
| $eq | `{ "Volcano Name": { $eq: "Rainier" } }` |
| $gt | `{ "Elevation": { $gt: 4000 } }` | 
| $gte | `{ "Elevation": { $gte: 4392 } }` | 
| $lt | `{ "Elevation": { $lt: 5000 } }` | 
| $lte | `{ "Elevation": { $lte: 5000 } }` | 
| $ne | `{ "Elevation": { $ne: 1 } }` | 
| $in | `{ "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } }` |
| $nin | `{ "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } }` |
| $or | `{ $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] }` | 
| $and | `{ $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] }` |
| $not | `{ "Elevation": { $not: { $gt: 5000 } } }`| 
| $nor | `{ $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] }` |
| $exists | `{ "Status": { $exists: true } }`|
| $type | `{ "Status": { $type: "string" } }`| 
| $mod | `{ "Elevation": { $mod: [ 4, 0 ] } }` | 
| $regex | `{ "Volcano Name": { $regex: "^Rain"} }`| 

### <a name="notes"></a>Notes

$regex クエリでは、左固定の式でインデックス検索が可能です。 ただし、'i' 修飾子 (大文字と小文字の区別なし) や 'm' 修飾子 (複数行) を使用すると、すべての式でコレクション スキャンが発生します。
'$' または '|' を含める必要がある場合、2 つ (以上) の正規表現クエリを作成することをお勧めします。
たとえば、元のクエリとして ```find({x:{$regex: /^abc$/})``` がある場合、```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})``` のように変更する必要があります。
最初の部分では、インデックスを使用して検索を ^abc で始まるドキュメントに制限し、2 番目の部分で入力そのものを照合します。
バー演算子 '|' は "or" 関数として機能します。そのためクエリ ```find({x:{$regex: /^abc|^def/})``` は、フィールド 'x' の値が "abc" または "def" で始まるドキュメントに一致します。 インデックスを利用するには、```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })``` のように、クエリを 2 つの異なるクエリに分割し、$or 演算子で結合することをお勧めします。

### <a name="update-operators"></a>更新演算子

#### <a name="field-update-operators"></a>フィールド更新演算子

- $inc
- $mul
- $rename
- $setOnInsert
- $set
- $unset
- $min
- $max
- $currentDate

#### <a name="array-update-operators"></a>配列更新演算子

- $addToSet
- $pop
- $pullAll
- $pull (注: 条件付きの $pull はサポートされていません)
- $pushAll
- $push
- $each
- $slice
- $sort
- $position

#### <a name="bitwise-update-operator"></a>ビット単位更新演算子

- $bit

### <a name="geospatial-operators"></a>地理空間演算子

演算子 | 例 | サポートされています |
--- | --- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | はい |
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | はい |
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | はい |
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | はい |
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | はい |
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | はい |
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | はい |
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | はい |
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | はい |
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | はい |
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | はい |

## <a name="sort-operations"></a>並べ替え操作

`findOneAndUpdate` 操作を使用する場合、単一フィールドに対する並べ替え操作はサポートされていますが、複数フィールドに対する並べ替え操作はサポートされていません。

## <a name="other-operators"></a>その他の演算子

演算子 | 例 | Notes
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` |
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |
$size | ```{ "Location.coordinates": { $size: 2 } }``` |
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` |
$text |  | サポートされていません。 代わりに $regex を使用してください。

## <a name="unsupported-operators"></a>サポートされていない演算子

```$where``` と ```$eval``` の演算子は、Azure Cosmos DB ではサポートされていません。

### <a name="methods"></a>メソッド

以下のメソッドがサポートされています。

#### <a name="cursor-methods"></a>カーソル メソッド

Method | 例 | Notes
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | 並べ替えキーを持たないドキュメントは返されない

## <a name="unique-indexes"></a>一意なインデックス

Cosmos DB では、既定で、データベースに書き込まれるドキュメントのすべてのフィールドにインデックスが付けられます。 一意なインデックスによって、特定のフィールドの値が、コレクション内のすべてのドキュメントにわたって重複していないことが保証されます。これは、既定の `_id` キーで一意性が保持される方法と似ています。 "unique" 制約を含めて createIndex コマンドを使用すれば、Cosmos DB でカスタム インデックスを作成できます。

Azure Cosmos DB の MongoDB 用 API を使用すると、すべての Cosmos アカウントで一意のインデックスを使用できます。

## <a name="time-to-live-ttl"></a>Time-to-live (TTL)

Cosmos DB では、ドキュメントのタイムスタンプに基づく Time-to-live (TTL) がサポートされます。 [Azure portal](https://portal.azure.com) にアクセスすると、コレクションに対して TTL を有効にできます。

## <a name="user-and-role-management"></a>ユーザーとロールの管理

Cosmos DB では、ユーザーとロールはまだサポートされていません。 ただし、Azure ロールベース アクセス制御 (Azure RBAC) と、[Azure portal](https://portal.azure.com) ([接続文字列] ページ) から取得できる読み取りと書き込みおよび読み取り専用のパスワードとキーがサポートされています。

## <a name="replication"></a>レプリケーション

Cosmos DB では、最下位のレイヤーで、自動のネイティブ レプリケーションがサポートされています。 このロジックは、低待機時間のグローバルなレプリケーションも実現するために拡張されています。 Cosmos DB Cosmos では、手動のレプリケーション コマンドはサポートされていません。

## <a name="write-concern"></a>書き込み確認

一部のアプリケーションでは、書き込み操作中に必要な応答数を指定する[書き込み確認](https://docs.mongodb.com/manual/reference/write-concern/)が利用されています。 Cosmos DB が背景でレプリケーションを処理する方法により、既定ですべての書き込みが自動的にクォーラムになります。 クライアント コードによって指定される書き込み確認はすべて無視されます。 詳細については、[整合性レベルを使用して可用性とパフォーマンスを最大化する方法](consistency-levels.md)に関するページを参照してください。

## <a name="sharding"></a>シャーディング

Azure Cosmos DB は、自動のサーバー側シャーディングをサポートしています。 シャードの作成、配置、バランシングが自動的に管理されます。 Azure Cosmos DB では、手動のシャーディング コマンドはサポートされていません。つまり、shardCollection、addShard、balancerStart、moveChunk などのコマンドを呼び出す必要はありません。必要なことは、コンテナーの作成時やデータの照会時にシャード キーを指定するだけです。

## <a name="next-steps"></a>次のステップ

- Azure Cosmos DB の MongoDB 用 API と共に [Studio 3T を使用する](mongodb-mongochef.md)方法を学習します。
- Azure Cosmos DB の MongoDB 用 API と共に [Robo 3T を使用する](mongodb-robomongo.md)方法を学びます。
- Azure Cosmos DB の MongoDB 用 API を使用した MongoDB の[サンプル](mongodb-samples.md)を調査します。
