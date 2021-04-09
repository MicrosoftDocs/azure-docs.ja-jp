---
title: Azure Cosmos DB を使用する場合のクエリの問題のトラブルシューティング
description: Azure Cosmos DB の SQL クエリに関する問題を特定、診断、およびトラブルシューティングする方法について説明します。
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 02/16/2021
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 6701a580cbe7790dcce2cbbcc46889f9dff00107
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100559984"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Azure Cosmos DB を使用する場合のクエリの問題のトラブルシューティング
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事では、Azure Cosmos DB のクエリのトラブルシューティングに関する一般的な推奨アプローチについて説明します。 この記事で説明されている手順により、クエリで発生する可能性がある問題を完全に防ぐことができると考えてはいけませんが、パフォーマンスに関する最も一般的なヒントを示してあります。 この記事は、Azure Cosmos DB Core (SQL) API の低速クエリまたはコストの高いクエリのトラブルシューティングのための出発点として使用してください。 また、[診断ログ](cosmosdb-monitor-resource-logs.md)を使用して、遅いクエリやスループットの消費量が多いクエリを特定することもできます。 Azure Cosmos DB の MongoDB 用 API を使用する場合、[Azure Cosmos DB の MongoDB 用 API クエリのトラブルシューティング ガイド](mongodb-troubleshoot-query.md)をご利用ください。

Azure Cosmos DB でのクエリの最適化は、次のように大きく分類されています。

- クエリの要求ユニット (RU) 使用量を削減する最適化
- クエリの待機時間を短縮するだけの最適化

クエリの RU 使用量を減らすと、一般的に待機時間も短くなります。

この記事では、[栄養データセット](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)を使用して再作成できる例を示します。

## <a name="common-sdk-issues"></a>SDK に関する一般的な問題

このガイドを読む前に、クエリ エンジンに関連しない一般的な SDK の問題について検討することをお勧めします。

- これらの [SDK パフォーマンスのヒント](performance-tips.md)に従ってください。
    - [.NET SDK トラブルシューティング ガイド](troubleshoot-dot-net-sdk.md)
    - [Java SDK トラブルシューティング ガイド](troubleshoot-java-sdk-v4-sql.md)
- SDK では、クエリの `MaxItemCount` を設定できますが、最小項目数は指定できません。
    - コードで、0 から `MaxItemCount` までのすべてのページ サイズを処理する必要があります。
- 将来のページには結果がある場合でも、クエリで空のページを受け取ることがあります。 これには、次のような理由が考えられます。
    - SDK が複数のネットワーク呼び出しを実行している可能性があります。
    - クエリでドキュメントを取得するのに時間がかかっている可能性があります。
- すべてのクエリには、クエリの続行を許可する継続トークンがあります。 必ず、クエリを完全にドレインするようにしてください。 [複数にわたる結果のページの処理](sql-query-pagination.md#handling-multiple-pages-of-results)に関するページをご覧ください

## <a name="get-query-metrics"></a>クエリのメトリックを取得する

Azure Cosmos DB でクエリを最適化する場合、最初の手順は常にクエリの[クエリ メトリックを取得する](profile-sql-api-query.md)ことです。 これらのメトリックは、Azure portal からも入手できます。 Data Explorer でクエリを実行すると、 **[結果]** タブの横にクエリ メトリックが表示されます。

:::image type="content" source="./media/troubleshoot-query-performance/obtain-query-metrics.png" alt-text="クエリ メトリックの取得" lightbox="./media/troubleshoot-query-performance/obtain-query-metrics.png":::

クエリ メトリックを取得した後、クエリの **[取得したドキュメント数]** と **[出力したドキュメント数]** を比較します。 この比較により、この記事で確認する関連セクションを特定します。

**[取得したドキュメント数]** は、クエリ エンジンが読み込む必要があったドキュメントの数です。 **[出力したドキュメント数]** は、クエリの結果に必要だったドキュメントの数です。 **[取得したドキュメント数]** が **[出力したドキュメント数]** より大幅に多い場合は、クエリの少なくとも 1 つの部分でインデックスを使用できず、スキャンを実行する必要がありました。

シナリオに関連するクエリの最適化について理解するには、以下のセクションを参照してください。

### <a name="querys-ru-charge-is-too-high"></a>クエリの RU 使用量が高すぎる

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>取得したドキュメント数が出力したドキュメント数を大幅に超えている

- [インデックス作成ポリシーに必要なパスを含める。](#include-necessary-paths-in-the-indexing-policy)

- [インデックスを使用するシステム関数について理解する。](#understand-which-system-functions-use-the-index)

- [文字列システム関数の実行を改善する。](#improve-string-system-function-execution)

- [インデックスを使用する集計クエリについて理解する。](#understand-which-aggregate-queries-use-the-index)

- [フィルターと ORDER BY 句の両方を使用するクエリを最適化する。](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

- [サブクエリを使用して JOIN 式を最適化する。](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>取得したドキュメント数が出力したドキュメント数とほぼ等しい

- [クロス パーティション クエリを最小化する。](#minimize-cross-partition-queries)

- [複数のプロパティに対するフィルターがあるクエリを最適化する。](#optimize-queries-that-have-filters-on-multiple-properties)

- [フィルターと ORDER BY 句の両方を使用するクエリを最適化する。](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>クエリの RU 使用量は許容されるが、待機時間は依然として長すぎる

- [近接性を向上させる。](#improve-proximity)

- [プロビジョニングされたスループットを増やす。](#increase-provisioned-throughput)

- [MaxConcurrency を増やす。](#increase-maxconcurrency)

- [MaxBufferedItemCount を増やす。](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>取得したドキュメント数が出力したドキュメント数を超えるクエリ

 **[取得したドキュメント数]** は、クエリ エンジンが読み込む必要があったドキュメントの数です。 **[出力したドキュメント数]** は、クエリによって返されたドキュメントの数です。 **[取得したドキュメント数]** が **[出力したドキュメント数]** より大幅に多い場合は、クエリの少なくとも 1 つの部分でインデックスを使用できず、スキャンを実行する必要がありました。

インデックスによって完全には処理されなかったスキャン クエリの例を次に示します。

クエリ:

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

クエリ メトリック:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

**[取得したドキュメント数]** (60,951) は、 **[出力したドキュメント数]** (7) を大幅に上回っており、このクエリの結果としてドキュメントのスキャンが行われたことを示しています。 この場合、システム関数 [UPPER()](sql-query-upper.md) ではインデックスは使用されません。

### <a name="include-necessary-paths-in-the-indexing-policy"></a>インデックス作成ポリシーに必要なパスを含める

インデックス作成ポリシーでは、`WHERE` 句、`ORDER BY` 句、`JOIN`、およびほとんどのシステム関数に含まれるすべてのプロパティがカバーされている必要があります。 インデックス ポリシーに指定された必要なパスは、JSON ドキュメント内のプロパティと一致している必要があります。

> [!NOTE]
> Azure Cosmos DB のインデックス作成ポリシーのプロパティでは、大文字と小文字が区別されます。

[栄養](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)データセットに対して次の単純なクエリを実行する場合、`WHERE` 句のプロパティのインデックスが作成されると RU 料金がはるかに低くなります。

#### <a name="original"></a>変更元

クエリ:

```sql
SELECT *
FROM c
WHERE c.description = "Malabar spinach, cooked"
```

インデックス作成ポリシー:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/description/*"
        }
    ]
}
```

**RU 使用量:** 409.51 RU

#### <a name="optimized"></a>最適化

更新されたインデックス作成ポリシー:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

**RU 使用量:** 2.98 RU

書き込みまたは読み取りの可用性に影響を与えることなく、いつでもインデックス作成ポリシーにプロパティを追加できます。 [インデックス変換の進行状況を追跡](./how-to-manage-indexing-policy.md#dotnet-sdk)できます。

### <a name="understand-which-system-functions-use-the-index"></a>インデックスを使用するシステム関数について理解する

ほとんどのシステム関数では、インデックスが使用されます。 インデックスを使用する一般的な文字列関数の一覧を次に示します。

- StartsWith
- Contains
- RegexMatch
- Left
- Substring - ただし、最初の num_expr が 0 の場合のみ

`WHERE` 句で使用されたときに、インデックスを使用せず、各ドキュメントを読み込む必要がある一般的なシステム関数は、次のとおりです。

| **システム関数**                     | **最適化のアイデア**             |
| --------------------------------------- |------------------------------------------------------------ |
| UPPER/LOWER                         | システム関数を使用して比較のためにデータを正規化する代わりに、挿入時に大文字と小文字を正規化します。 ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` のようなクエリが、```SELECT * FROM c WHERE c.name = 'BOB'``` になります。 |
| GetCurrentDateTime/GetCurrentTimestamp/GetCurrentTicks | クエリ実行前に現在の時刻を計算し、その文字列値を `WHERE` 句で使用します。 |
| 数学関数 (非集計) | クエリで値を頻繁に計算する必要がある場合は、JSON ドキュメントのプロパティとして値を格納することを検討します。 |

これらのシステム関数では、集計を使用したクエリで使用される場合を除き、インデックスを使用できます。

| **システム関数**                     | **最適化のアイデア**             |
| --------------------------------------- |------------------------------------------------------------ |
| 空間システム関数                        | リアルタイムの具体化されたビューにクエリ結果を格納する |

`SELECT` 句で使用された場合、非効率的なシステム関数は、クエリでインデックスを使用する方法に影響しません。

### <a name="improve-string-system-function-execution"></a>文字列システム関数の実行を改善する

インデックスを使用するシステム関数の中には、クエリに `ORDER BY` 句を追加することによって、クエリの実行を改善できるものもあります。 

具体的には、プロパティのカーディナリティが増加するにつれて RU 料金が増加するすべてのシステム関数は、クエリに `ORDER BY` を含めることでメリットが得られる可能性があります。 これらのクエリではインデックス スキャンが実行されるため、クエリ結果を並べ替えることにより、クエリの効率を高めることができます。

この最適化により、次のシステム関数の実行が向上します。

- StartsWith (大文字と小文字を区別しない = true)
- StringEquals (大文字と小文字を区別しない = true)
- Contains
- RegexMatch
- EndsWith

たとえば、次のような `CONTAINS` が含まれる SQL クエリについて考えます。 `CONTAINS` ではインデックスが使用されますが、関連するインデックスを追加した後でも、次のクエリを実行すると非常に高い RU 料金が発生する場合があります。

元のクエリ:

```sql
SELECT *
FROM c
WHERE CONTAINS(c.town, "Sea")
```

`ORDER BY` を追加することで、クエリの実行を改善させることができます。

```sql
SELECT *
FROM c
WHERE CONTAINS(c.town, "Sea")
ORDER BY c.town
```

同じ最適化は、フィルターを追加したクエリにも役立ちます。 この場合、`ORDER BY` 句に等値フィルターを含むプロパティも追加することをお勧めします。

元のクエリ:

```sql
SELECT *
FROM c
WHERE c.name = "Samer" AND CONTAINS(c.town, "Sea")
```

`ORDER BY` と (c.name, c.town) の[複合インデックス](index-policy.md#composite-indexes)を追加することで、クエリの実行を改善することができます。

```sql
SELECT *
FROM c
WHERE c.name = "Samer" AND CONTAINS(c.town, "Sea")
ORDER BY c.name, c.town
```

### <a name="understand-which-aggregate-queries-use-the-index"></a>インデックスを使用する集計クエリについて理解する

ほとんどの場合、Azure Cosmos DB の集計システム関数ではインデックスが使用されます。 ただし、集計クエリのフィルターや追加の句によっては、クエリ エンジンで大量のドキュメントを読み込みことが必要になる場合があります。 通常、クエリ エンジンでは、等値および範囲フィルターが最初に適用されます。 これらのフィルターを適用した後で、クエリ エンジンは追加のフィルターを評価し、必要に応じて残りのドキュメントを読み込んで集計を計算できます。

たとえば、次の 2 つのサンプル クエリの場合、等値と `CONTAINS` システム関数フィルターの両方を含むクエリは、通常、`CONTAINS` システム関数フィルターだけを含むクエリよりも効率的です。 これは、より負荷の高い `CONTAINS` フィルターのためにドキュメントの読み込みが必要になる前に、等値フィルターが最初に適用され、インデックスが使用されるためです。

`CONTAINS` フィルターのみを含むクエリ - RU 料金が高くなる:

```sql
SELECT COUNT(1)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

等値フィルターと `CONTAINS` フィルターを含むクエリ - RU 料金が低くなる:

```sql
SELECT AVG(c._ts)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

次に、インデックスを完全には使用しない集計クエリのその他の例を示します。

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>インデックスを使用しないシステム関数を含むクエリ

インデックスを使用するかどうかを確認するには、関連する[システム関数のページ](sql-query-system-functions.md)を参照する必要があります。

```sql
SELECT MAX(c._ts)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>ユーザー定義関数 (UDF) を含む集計クエリ

```sql
SELECT AVG(c._ts)
FROM c
WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>ORDER BY を含むクエリ

`GROUP BY` 句内のプロパティのカーディナリティが増えると、`GROUP BY` を含むクエリの RU 料金が増加します。 たとえば、次のクエリでは、一意の説明の数が増えるにつれて、クエリの RU 料金が増加します。

`GROUP BY` 句を含む集計関数の RU 料金は、集計関数だけの RU 料金よりも高くなります。 この例では、クエリ エンジンは `c.foodGroup = "Sausages and Luncheon Meats"` フィルターに一致するすべてのドキュメントを読み込む必要があるため、RU 料金が高くなることが予想されます。

```sql
SELECT COUNT(1)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats"
GROUP BY c.description
```

同じ集計クエリを頻繁に実行する予定がある場合は、個々のクエリを実行するのではなく、[Azure Cosmos DB の変更フィード](change-feed.md)を使用して、リアルタイムのマテリアライズドビューを作成する方が効率的です。

### <a name="optimize-queries-that-have-both-a-filter-and-an-order-by-clause"></a>フィルターと ORDER BY 句の両方を使用するクエリを最適化する

通常、フィルターと `ORDER BY` 句を使用するクエリでは範囲インデックスが使用されますが、複合インデックスから提供できる場合は、効率が向上します。 インデックス作成ポリシーを変更するだけでなく、複合インデックス内のすべてのプロパティを `ORDER BY` 句に追加する必要があります。 このようにクエリを変更すると、複合インデックスが使用されます。  [栄養](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)データセットに対してクエリを実行することで、影響を観察できます。

#### <a name="original"></a>変更元

クエリ:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c._ts ASC
```

インデックス作成ポリシー:

```json
{

        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[]
}
```

**RU 使用量:** 44.28 RU

#### <a name="optimized"></a>最適化

更新されたクエリ (`ORDER BY` 句の両方のプロパティが含まれます):

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c.foodGroup, c._ts ASC
```

更新されたインデックス作成ポリシー:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
        },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
    }

```

**RU 使用量:** 8.86 RU

### <a name="optimize-join-expressions-by-using-a-subquery"></a>サブクエリを使用して JOIN 式を最適化する

複数値サブクエリは、`WHERE` 句内のすべてのクロス結合の後ではなく、それぞれの select-many 式の後に述語をプッシュすることによって `JOIN` 式を最適化できます。

次のクエリについて考えてみます。

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**RU 使用量:** 167.62 RU

このクエリの場合、インデックスは、`infant formula` という名前のタグを持ち、`nutritionValue` が 0 より大きく、`amount` が 1 より大きいすべてのドキュメントに一致します。 ここでの `JOIN` 式は、一致するドキュメントごとに、タグ、栄養素、1 回分の各配列の全項目の外積を、フィルターが適用される前に実行します。 `WHERE` 句はその後、`<c, t, n, s>` タプルごとにフィルター述語を適用します。

たとえば、一致するドキュメントで、3 つの配列のそれぞれに 10 個の項目があるとした場合、1 x 10 x 10 x 10 (つまり、1,000) タプルに展開されます。 ここでサブクエリを使用すると、次の式と結合する前に、結合された配列項目をフィルターで除外するために役立ちます。

このクエリは前のものと同等ですが、サブクエリを使用します。

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**RU 使用量:** 22.17 RU

tags 配列の 1 つの項目のみがフィルターに一致し、nutrients 配列と servings 配列の両方に 5 つの項目があるとします。 `JOIN` 式は、最初のクエリの 1,000 項目とは異なり、1 x 1 x 5 x 5 = 25 項目に展開されます。

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>取得したドキュメント数が出力したドキュメント数と等しいクエリ

**[取得したドキュメント数]** が **[出力したドキュメント数]** とほぼ同じ場合、クエリ エンジンは多くの不要なドキュメントをスキャンする必要がありませんでした。 `TOP` キーワードを使用するクエリのように、多くのクエリで、 **[取得したドキュメント数]** は **[出力したドキュメント数]** より 1 つ多い可能性があります。 これについて心配する必要はありません。

### <a name="minimize-cross-partition-queries"></a>クロス パーティション クエリを最小化する

Azure Cosmos DB では[パーティション分割](partitioning-overview.md)を使用して、要求ユニットとデータ ストレージのニーズの増加に応じて個々のコンテナーをスケーリングします。 各物理パーティションには、区切られて独立したインデックスがあります。 クエリにコンテナーのパーティション キーと一致する等値フィルターがある場合、確認する必要があるのは関連するパーティションのインデックスのみです。 この最適化により、クエリが必要とする RU の合計数が減少します。

プロビジョニングされた RU 使用量の数が多い (3 万以上) 場合や、大量のデータ (約 100 GB 以上) が格納されている場合は、おそらく、クエリ RU の使用量の大幅な削減を確認するのに十分な大きさなのコンテナーがあります。

たとえば、foodGroup というパーティション キーを持つコンテナーを作成する場合、次のクエリでは 1 つの物理パーティションのみを確認する必要があります。

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

パーティション キーに `IN` フィルターがあるクエリでは、関連する物理パーティションだけがチェックされ、"ファンアウト" は行われません。

```sql
SELECT *
FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

パーティション キーに対して範囲フィルターが設定されているか、パーティション キーにフィルターがないクエリは、"ファンアウト" して、すべての物理パーティションのインデックスの結果を確認する必要があります。

```sql
SELECT *
FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT *
FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>複数のプロパティに対するフィルターがあるクエリを最適化する

通常、複数のプロパティのフィルターを使用したクエリでは範囲インデックスが使用されますが、複合インデックスから処理できる場合は、効率が向上します。 少量のデータの場合、この最適化は大きな影響を与えません。 ただし、大量のデータを使用する場合には、便利な場合があります。 最適化できる非等値フィルターは、複合インデックスごとに最大 1 つです。 クエリに複数の非等値フィルターがある場合は、複合インデックスを使用するクエリを 1 つ選択します。 残りの部分では、範囲インデックスが引き続き使用されます。 非等値フィルターは、複合インデックス内で最後に定義する必要があります。 [複合インデックスについての詳細情報](index-policy.md#composite-indexes)。

複合インデックスを使用して最適化できるクエリの例をいくつか次に示します。

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

関連する複合インデックスは次のとおりです。

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
                },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
}
```

## <a name="optimizations-that-reduce-query-latency"></a>クエリの待機時間を短縮する最適化

多くの場合、クエリの待機時間がまだ長すぎるときは、RU 使用量が許容される可能性があります。 以下のセクションでは、クエリの待機時間を短縮するためのヒントの概要を説明します。 同じクエリを同じデータセットに対して複数回実行すると、一般的には毎回同じ RU 使用量が発生します。 ただし、クエリの待機時間はクエリの実行間隔によって異なる場合があります。

### <a name="improve-proximity"></a>近接性の向上

Azure Cosmos DB アカウントとは異なるリージョンから実行されるクエリでは、同じリージョン内で実行された場合よりも待機時間が長くなります。 たとえば、デスクトップ コンピューターでコードを実行していた場合、クエリが Azure Cosmos DB と同じ Azure リージョン内の仮想マシンからのものである場合よりも、待機時間は数十ミリ秒または数百ミリ秒 (またはそれ以上) 分長くなることが予想されます。 データをアプリの近くに配置できるように、[Azure Cosmos DB にデータをグローバルに分散する](distribute-data-globally.md)ことは簡単です。

### <a name="increase-provisioned-throughput"></a>プロビジョニングされたスループットの増加

Azure Cosmos DB では、プロビジョニングされたスループットは要求ユニット (RU) で測定されます。 5 RU のスループットを使用するクエリがあると仮定します。 たとえば、1,000 RU をプロビジョニングする場合、そのクエリは 1 秒あたり 200 回実行できます。 スループットが十分でないときにクエリを実行しようとすると、Azure Cosmos DB によって HTTP 429 エラーが返されます。 現在の Core (SQL) API SDK では、短時間待機した後に、このクエリを自動的に再試行します。 スロットルされた要求にはさらに時間がかかるため、プロビジョニングされたスループットを増やすとクエリの待機時間が改善します。 Azure portal の **[メトリック]** ブレードで、[調整された要求の合計数](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors)を確認できます。

### <a name="increase-maxconcurrency"></a>MaxConcurrency の増加

並列クエリは、複数のパーティションに並列にクエリを実行することによって機能します。 ただし、個々のパーティション分割されたコレクションからのデータは、クエリごとに順番に取得されます。 そのため、MaxConcurrency をパーティションの数に設定すると、その他のすべてのシステムの条件が変わらなければ、クエリのパフォーマンスを最大にできる可能性が最大になります。 パーティションの数がわからない場合は、MaxConcurrency (または古いバージョンの SDK では MaxDegreesOfParallelism) を高い値に設定できます。 システムにより、最大の並列処理の次数として最小値 (パーティションの数、ユーザー指定の入力) が選択されます。

### <a name="increase-maxbuffereditemcount"></a>MaxBufferedItemCount の増加

クエリは、結果の現在のバッチがクライアントによって処理されている間に結果をプリフェッチするように設計されています。 プリフェッチは、クエリの全体的な遅延の削減に役立ちます。 MaxBufferedItemCount を設定すると、プリフェッチされる結果の数が制限されます。 この値を、返される結果の予期される数 (またはそれ以上の数) に設定すると、クエリに対するプリフェッチの効果が最大になります。 この値を -1 に設定すると、バッファーに格納される項目の数はシステムによって自動的に決定されます。

## <a name="next-steps"></a>次のステップ
クエリあたりの RU 数を測定する方法や、実行の統計を取得してクエリの調整を行う方法などについては、次の記事を参照してください。

* [.NET SDK を使用して SQL クエリの実行メトリックを取得する](profile-sql-api-query.md)
* [Azure Cosmos DB を使用したクエリ パフォーマンスのチューニング](./sql-api-query-metrics.md)
* [.NET SDK のパフォーマンスに関するヒント](performance-tips.md)
* [Java v4 SDK のパフォーマンスに関するヒント](performance-tips-java-sdk-v4-sql.md)