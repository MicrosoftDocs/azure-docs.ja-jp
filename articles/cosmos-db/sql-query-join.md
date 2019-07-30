---
title: Azure Cosmos DB の SQL JOIN クエリ
description: Azure Cosmos DB の JOIN SQL 構文について説明します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 408ee11b318143b3128833a741e04dd68f3816ed
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343230"
---
# <a name="joins-in-azure-cosmos-db"></a>Azure Cosmos DB での結合

リレーショナル データベースでは、テーブル間の結合は、正規化されたスキーマの設計の論理的必然です。 一方、SQL API では、スキーマがない項目の正規化されていないデータ モデルが使われます。これは論理的に*自己結合*と同義です。

内部結合の結果は、結合に含まれるセットの完全なクロス積になります。 N-way 結合の結果は、N 要素タプルのセットであり、タプル内の各値が結合に含まれている別名セットに関連付けられていて、他の句でその別名を参照してアクセスできます。

## <a name="syntax"></a>構文

言語では構文 `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>` をサポートしています。 このクエリでは、`N` 個の値を持つタプルのセットが返されます。 それぞれのタプルは、対応するセットに対する、すべてのコンテナーのエイリアスの反復によって生成された値を持ちます。 

それでは、以下の FROM 句を見てみましょう。`<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 各ソースで `input_alias1, input_alias2, …, input_aliasN` を定義します。 この FROM 句は、N-タプル (N 個の値を持つタプル) のセットを返します。 それぞれのタプルは、対応するセットに対する、すべてのコンテナーのエイリアスの反復によって生成された値を持ちます。  
  
**例 1** - 2 つのソース  
  
- `<from_source1>` をコンテナー スコープにし、セット {A, B, C} を表します。  
  
- `<from_source2>` を input_alias1 を参照するドキュメント スコープにし、次のセットを表します。  
  
    `input_alias1 = A,` の {1, 2}  
  
    `input_alias1 = B,` の {3}  
  
    `input_alias1 = C,` の {4, 5}  
  
- FROM 句 `<from_source1> JOIN <from_source2>` の結果は次のタプルになります。  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**例 2** - 3 つのソース  
  
- `<from_source1>` をコンテナー スコープにし、セット {A, B, C} を表します。  
  
- `<from_source2>` を `input_alias1` を参照するドキュメント スコープにし、次のセットを表します。  
  
    `input_alias1 = A,` の {1, 2}  
  
    `input_alias1 = B,` の {3}  
  
    `input_alias1 = C,` の {4, 5}  
  
- `<from_source3>` を `input_alias2` を参照するドキュメント スコープにし、次のセットを表します。  
  
    `input_alias2 = 1,` の {100, 200}  
  
    `input_alias2 = 3,` の {300}  
  
- FROM 句 `<from_source1> JOIN <from_source2> JOIN <from_source3>` の結果は次のタプルになります。  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > 他の値 `input_alias1`、`input_alias2` のタプルは欠如しており、`<from_source3>` はこれらに対して何も値を返しませんでした。  
  
**例 3** - 3 つのソース  
  
- <from_source1> をコンテナー スコープにし、セット {A, B, C} を表します。  
  
- `<from_source1>` をコンテナー スコープにし、セット {A, B, C} を表します。  
  
- <from_source2> を input_alias1 を参照するドキュメント スコープにし、次のセットを表します。  
  
    `input_alias1 = A,` の {1, 2}  
  
    `input_alias1 = B,` の {3}  
  
    `input_alias1 = C,` の {4, 5}  
  
- `<from_source3>` のスコープを `input_alias1` にし、次のセットを表します。  
  
    `input_alias2 = A,` の {100, 200}  
  
    `input_alias2 = C,` の {300}  
  
- FROM 句 `<from_source1> JOIN <from_source2> JOIN <from_source3>` の結果は次のタプルになります。  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200),  (C, 4, 300) ,  (C, 5, 300)  
  
  > [!NOTE]
  > 両方が同じスコープ `<from_source1>` になっているので、この結果は、`<from_source2>` と `<from_source3>` のクロス積になります。  この結果は、4 (2 x 2) タプルが値 A を持ち、0 タプルが値 B (1 x 0) を持ち、2 (2 x 1) タプルが値 C を持ちます。  
  
## <a name="examples"></a>例

JOIN 句の動作を示す例をいくつか紹介します。 次の例では、ソースの各項目と空集合のクロス積が空になるため、結果は空となります。

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

結果は次のとおりです。

```json
    [{
    }]
```

次の例で、結合は、項目のルート `id` と `children` サブルートの 2 つの JSON オブジェクト間のクロス積です。 この結合では、`children` が配列であるという事実は影響していません。これは、`children` 配列である単一のルートを処理しているためです。 結果には 2 つの結果しか含まれません。これは、配列がある各項目のクロス積によって項目が正確に 1 つだけ導出されるためです。

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

結果は次のようになります。

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

より一般的な結合の例を以下に示します。

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

結果は次のようになります。

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

JOIN 句の FROM ソースは反復子です。 そのため、前の例のフローは次のようになります。  

1. 各子要素 `c` を配列に展開します。
2. 項目 `f` のルートと、最初の手順でフラット化した各子要素 `c` とのクロス積を適用します。
3. 最後に、ルート オブジェクト `f` の `id` プロパティだけをプロジェクションします。

最初の項目 `AndersenFamily` には `children` 要素が 1 つだけ含まれているため、結果セットには、オブジェクトが 1 つだけ含まれます。 2 つ目の項目 `WakefieldFamily` には 2 つの `children` が含まれているため、クロス積によって、`children` 要素ごとに 1 つずつ、2 つのオブジェクトが生成されます。 クロス積で想定されるとおり、これら両方の項目のルート フィールドは同じです。

JOIN 句の便利な点は、クロス積からタプルを生成できる点です。これ以外の形式によるプロジェクションは簡単ではありません。 以下の例では、タプル全体で満たされる条件をユーザーが選択できるように、タプルの組み合わせをフィルターしています。

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

結果は次のようになります。

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

次の前述の例の拡張は、二重結合を実行しています。 クロス積は以下の擬似コードのように捉えることができます。

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily` には 1 人の子どもがいて、子どもはペットを 1 匹飼っています。このため、クロス積によってこの家族から 1 行 (1\*1\*1) が導出されます。 `WakefieldFamily` には子どもが 2 人いて、ペットを飼っているのは 1 人だけですが、その子どもには 2 匹のペットがいます。 この家族からのクロス積は 1\*1\*2 = 2 行となります。

次の例では、`pet` に対するフィルターを追加します。これによって、ペットの名前が `Shadow` ではないタプルがすべて除外されます。 配列からタプルを構築し、タプルのすべての要素に対してフィルターを実行し、要素の任意の組み合わせをプロジェクションできます。

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

結果は次のようになります。

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a name="next-steps"></a>次の手順

- [使用の開始](sql-query-getting-started.md)
- [Azure Cosmos DB .NET のサンプル](https://github.com/Azure/azure-cosmosdb-dotnet)
- [サブクエリ](sql-query-subquery.md)