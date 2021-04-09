---
title: Azure Cosmos DB のインデックス作成
description: Azure Cosmos DB でのインデックス作成のしくみについて説明します。範囲、空間、複合インデックスなどのさまざまな種類のインデックスがサポートされています。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: tisande
ms.openlocfilehash: b7349a08b93810dcc3befd6058302d6c4573ab8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019331"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Azure Cosmos DB のインデックス作成 - 概要
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB はスキーマ非依存のデータベースであり、スキーマやインデックスの管理に対応することなく、アプリケーション上で反復処理を実行することができます。 Azure Cosmos DB の既定では、[コンテナー](account-databases-containers-items.md#azure-cosmos-containers)内のすべての項目のすべてのプロパティに自動的にインデックスが作成されます。スキーマの定義やセカンダリ インデックスの構成は必要ありません。

この記事の目的は、Azure Cosmos DB がデータのインデックスを作成する方法と、インデックスを使用してクエリのパフォーマンスを向上する方法について説明することです。 [インデックス作成ポリシー](index-policy.md)のカスタマイズ方法を検討する前に、このセクションに目を通すことをお勧めします。

## <a name="from-items-to-trees"></a>項目からツリーへ

項目がコンテナーに格納されるたびに、そのコンテンツは JSON ドキュメントとして投影され、ツリー表現に変換されます。 つまり、その項目のすべてのプロパティがツリー内のノードとして表示されます。 擬似ルート ノードは、項目のすべての第 1 レベル プロパティに対する親として作成されます。 リーフ ノードには、項目に格納されている実際のスカラー値が含まれます。

たとえば、次の項目を検討してみましょう。

```json
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 },
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

これは次のツリーで表現されます。

:::image type="content" source="./media/index-overview/item-as-tree.png" alt-text="ツリーとして表現された前の項目" border="false":::

ツリー内で配列がどのようにエンコードされるかに注目します。配列内の各エントリは、配列内のそのエントリのインデックス (0、1 など) でラベル付けされた中間ノードを取得します。

## <a name="from-trees-to-property-paths"></a>ツリーからプロパティ パスへ

Azure Cosmos DB が項目をツリーに変換する理由は、そのようなツリー内のパスから、プロパティを参照できるようにするためです。 プロパティのパスを取得するには、ルート ノードからそのプロパティまでツリーを走査し、たどった各ノードのラベルを連結します。

上記の例の項目から各プロパティへのパスは次のとおりです。

- /locations/0/country:"Germany"
- /locations/0/city:"Berlin"
- /locations/1/country:"France"
- /locations/1/city:"Paris"
- /headquarters/country:"Belgium"
- /headquarters/employees:250
- /exports/0/city:"Moscow"
- /exports/1/city:"Athens"

項目が書き込まれると、Azure Cosmos DB は各プロパティのパスとそれに対応する値のインデックスを効果的に作成します。

## <a name="types-of-indexes"></a><a id="index-types"></a>インデックスのタイプ

現在、Azure Cosmos DB では 3 種類のインデックスがサポートされています。 インデックス作成ポリシーを定義するときに、これらのインデックスのタイプを構成できます。

### <a name="range-index"></a>範囲インデックス

**範囲** インデックスは、順序付けされたツリーのような構造に基づいています。 範囲インデックス タイプは、次のために使用されます。

- 等値クエリ:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   配列要素の等値一致
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1")
    ```

- 範囲クエリ:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (`>`、`<`、`>=`、`<=`、`!=` に適しています)

- プロパティが存在するかどうかの確認:

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- 文字列システム関数:

   ```sql
   SELECT * FROM c WHERE CONTAINS(c.property, "value")
   ```

   ```sql
   SELECT * FROM c WHERE STRINGEQUALS(c.property, "value")
   ```

- `ORDER BY` クエリ:

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN` クエリ:

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

範囲インデックスは、スカラー値 (文字列または数値) に使用できます。 新しく作成したコンテナーの既定のインデックス作成ポリシーでは、文字列または数値に範囲インデックスが適用されます。 範囲インデックスを構成する方法については、[範囲インデックス作成ポリシーの例](how-to-manage-indexing-policy.md#range-index)に関する記事を参照してください。

### <a name="spatial-index"></a>空間インデックス

**空間** インデックスを使用すると、点、線、多角形、複数の多角形などの地理空間オブジェクトに対して、効率的なクエリを行うことができます。 これらのクエリでは、ST_DISTANCE、ST_WITHIN、ST_INTERSECTS の各キーワードを使用します。 空間インデックス タイプを使用するいくつかの例を次に示します。

- 地理空間距離クエリ:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- クエリ内の地理空間:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] })
   ```

- 地理空間交差クエリ:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

空間インデックスは、正しい形式の [GeoJSON](./sql-query-geospatial-intro.md) オブジェクトに対して使用できます。 現在、Points、LineStrings、Polygons、MultiPolygons がサポートされています。 このインデックス タイプを使用するには、インデックス作成ポリシーを構成するときに `"kind": "Range"` プロパティを使用して設定します。 空間インデックスを構成する方法については、[空間インデックス作成ポリシーの例](how-to-manage-indexing-policy.md#spatial-index)に関する記事を参照してください。

### <a name="composite-indexes"></a>複合インデックス

**複合** インデックスを使用すると、複数のフィールドに対して操作を実行するときの効率が向上します。 複合インデックス タイプは、次のために使用されます。

- 複数のプロパティに対する `ORDER BY` クエリ:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- フィルターと `ORDER BY` を使用するクエリ。 これらのクエリは、フィルター プロパティが `ORDER BY` 句に追加された場合に、複合インデックスを利用できます。

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- 少なくとも 1 つのプロパティが等値フィルターである 2 つ以上のプロパティに対するフィルターを使用するクエリ

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

1 つのフィルター述語でいずれかのインデックス タイプが使用されている限り、クエリ エンジンでは、残りの部分をスキャンする前に最初にそれが評価されます。 たとえば、次のような SQL クエリがあるとします: `SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* 上のクエリでは、最初にインデックスを使用して firstName = "Andrew" のエントリがフィルター処理されます。 次に、すべての firstName = "Andrew" エントリが後続のパイプラインを通過して、CONTAINS フィルター述語が評価されます。

* インデックスを使用するフィルター述語を追加することによって、インデックスを使用しない関数 (CONTAINS など) を使用するときの、クエリを高速化し、完全なコンテナー スキャンを回避できます。 フィルター句の順序は重要ではありません。 クエリ エンジンでは、どの述語がより選択的であるかが判断され、それに応じてクエリが実行されます。

複合インデックスを構成する方法については、[複合インデックス作成ポリシーの例](how-to-manage-indexing-policy.md#composite-index)に関する記事を参照してください。

## <a name="querying-with-indexes"></a>インデックスを使用してクエリを実行する

データのインデックス作成時に抽出されるパスを使用すると、クエリの処理時にインデックスを簡単に検索できます。 クエリの `WHERE` 句をインデックス付きパスの一覧と照合して、クエリの述語と一致する項目を非常にすばやく識別することができます。

たとえば、次のクエリを考えてみましょう。`SELECT location FROM location IN company.locations WHERE location.country = 'France'` クエリの述語 (どこかの場所に国やリージョンとして "France" が含まれている項目のフィルター処理) は、以下のように赤で強調表示されているパスと一致します。

:::image type="content" source="./media/index-overview/matching-path.png" alt-text="ツリー内の特定のパスとの照合" border="false":::

> [!NOTE]
> 1 つのプロパティで並べ替える `ORDER BY` 句には *常に* 範囲インデックスが必要であり、その句が参照するパスにこのインデックスが存在しない場合は失敗します。 同様に、複数のプロパティで並べ替える `ORDER BY` クエリには、*常に* 複合インデックスが必要です。

## <a name="next-steps"></a>次のステップ

以下の記事で、インデックス作成についての詳細を参照してください。

- [インデックス作成ポリシー](index-policy.md)
- [インデックス作成ポリシーを管理する方法](how-to-manage-indexing-policy.md)