---
title: Azure Cosmos DB のインデックス作成
description: Azure Cosmos DB のインデックス作成のしくみを説明します。
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: thweiss
ms.openlocfilehash: 4d961f8635a52a09011543b793ce8a87eaa4ea9e
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914192"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Azure Cosmos DB のインデックス作成 - 概要

Azure Cosmos DB はスキーマ非依存のデータベースであり、スキーマやインデックスの管理に対応することなく、アプリケーション上で反復処理を実行することができます。 Azure Cosmos DB の既定では、[コンテナー](databases-containers-items.md#azure-cosmos-containers)内のすべての項目のすべてのプロパティに自動的にインデックスが作成されます。スキーマの定義やセカンダリ インデックスの構成は必要ありません。

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

![ツリーとして表現された前の項目](./media/index-overview/item-as-tree.png)

ツリー内で配列がどのようにエンコードされるかに注目します。配列内の各エントリは、配列内のそのエントリのインデックス (0、1 など) でラベル付けされた中間ノードを取得します。

## <a name="from-trees-to-property-paths"></a>ツリーからプロパティ パスへ

Azure Cosmos DB が項目をツリーに変換する理由は、そのようなツリー内のパスから、プロパティを参照できるようにするためです。 プロパティのパスを取得するには、ルート ノードからそのプロパティまでツリーを走査し、たどった各ノードのラベルを連結します。

上記の例の項目から各プロパティへのパスは次のとおりです。

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

項目が書き込まれると、Azure Cosmos DB は各プロパティのパスとそれに対応する値のインデックスを効果的に作成します。

## <a name="index-kinds"></a>インデックスの種類

現在、Azure Cosmos DB では 3 種類のインデックスがサポートされています。

**範囲**インデックスの種類は、以下のために使用されます。

- 等値クエリ:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

- 範囲クエリ:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (`>`、`<`、`>=`、`<=`、`!=` に適しています)

- `ORDER BY` クエリ:

   ```sql 
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN` クエリ:

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

範囲インデックスは、スカラー値 (文字列または数値) に使用できます。

**空間**インデックスの種類は、以下のために使用されます。

- 地理空間距離クエリ: 

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- クエリ内の地理空間: 

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

空間インデックスは、正しい形式の [GeoJSON](geospatial.md) オブジェクトに対して使用できます。 現在、Points、LineStrings、Polygons、MultiPolygons がサポートされています。

**複合**のインデックスの種類は、次のために使用されます。

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

## <a name="querying-with-indexes"></a>インデックスを使用してクエリを実行する

データのインデックス作成時に抽出されるパスを使用すると、クエリの処理時にインデックスを簡単に検索できます。 クエリの `WHERE` 句をインデックス付きパスの一覧と照合して、クエリの述語と一致する項目を非常にすばやく識別することができます。

たとえば、次のクエリを考えてみましょう。`SELECT location FROM location IN company.locations WHERE location.country = 'France'` クエリの述語 (どの場所にも国として "France" 含まれている項目のフィルター処理) は、以下のように赤で強調表示されているパスと一致します。

![ツリー内の特定のパスとの照合](./media/index-overview/matching-path.png)

> [!NOTE]
> 1 つのプロパティで並べ替える `ORDER BY` 句には*常に*範囲インデックスが必要であり、その句が参照するパスにこのインデックスが存在しない場合は失敗します。 同様に、複数のプロパティで並べ替える `ORDER BY` クエリには、*常に*複合インデックスが必要です。

## <a name="next-steps"></a>次の手順

以下の記事で、インデックス作成についての詳細を参照してください。

- [インデックス作成ポリシー](index-policy.md)
- [インデックス作成ポリシーを管理する方法](how-to-manage-indexing-policy.md)
