---
title: Azure Cosmos DB での ORDER BY 句
description: Azure Cosmos DB の SQL の ORDER BY 句について説明します。 Azure Cosmos DB JSON クエリ言語として SQL を使用します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: tisande
ms.openlocfilehash: 887dc13eb5e351688718d2a221e69499557b23e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93338301"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Azure Cosmos DB での ORDER BY 句
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

省略可能な `ORDER BY` 句では、クエリによって返される結果の並べ替え順序を指定します。

## <a name="syntax"></a>構文
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>引数
  
- `<sort_specification>`  
  
   クエリの結果セットの並べ替えの条件にするプロパティまたは式を指定します。 並べ替え列は、名前またはプロパティの別名として指定できます。  
  
   複数のプロパティを指定することができます。 プロパティ名は一意である必要があります。 `ORDER BY` 句の並べ替えプロパティの順序により、並べ替えられた結果セットの構成が定義されます。 つまり、結果セットは最初のプロパティで並べ替えられ、その並べ替えられたリストが 2 番目のプロパティで並べ替えられ、以下同様に続きます。  
  
   `ORDER BY` 句で参照されるプロパティ名は、選択リスト内のプロパティに、またはあいまいさのない `FROM` 句で指定されたコレクションで定義されているプロパティに対応する必要があります。  
  
- `<sort_expression>`  
  
   クエリの結果セットの並べ替えの条件にする 1 つ以上のプロパティまたは式を指定します。  
  
- `<scalar_expression>`  
  
   詳細については、「[スカラー式](sql-query-scalar-expressions.md)」セクションを参照してください。  
  
- `ASC | DESC`  
  
   指定した列の値を昇順と降順のどちらで並べ替えるかを指定します。 `ASC` を指定した場合、最小値から最大値の順序で並べ替えられます。 `DESC` を指定した場合、最大値から最小値の順序で並べ替えられます。 `ASC` が既定の並べ替え順序です。 NULL 値は最小値として扱われます。  
  
## <a name="remarks"></a>解説  
  
   `ORDER BY` 句では、並べ替えるフィールドのインデックスがインデックス作成ポリシーに含まれている必要があります。 Azure Cosmos DB のクエリ ランタイムでは、計算されたプロパティに対してではなく、プロパティ名に対する並べ替えがサポートされています。 Azure Cosmos DB では、複数の `ORDER BY` プロパティがサポートされています。 複数の ORDER BY プロパティを使ったクエリを実行するには、並べ替えるフィールドに対する[複合インデックス](index-policy.md#composite-indexes)を定義する必要があります。

> [!Note]
> 並べ替えるプロパティが一部のドキュメントに定義されていない可能性があり、ORDER BY クエリでそれらを取得したい場合は、インデックスにこのパスを明示的に含める必要があります。 既定のインデックス作成ポリシーでは、並べ替えプロパティが定義されていないドキュメントを取得することができません。 [フィールドがないドキュメントに対するクエリ例を確認してください](#documents-with-missing-fields)。

## <a name="examples"></a>例

たとえば、居住都市名の昇順で家族を取得するクエリは次のようになります。

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

結果は次のようになります。

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

次のクエリは、家族 `id` をその項目の作成日の順序で取得します。 項目 `creationDate` は、*エポック時間*、つまり、1970 年 1 月 1 日からの経過時間を秒で表す数字です。

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

結果は次のようになります。

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

さらに、複数のプロパティで並べ替えることができます。 複数のプロパティで並べ替えるクエリには、[複合インデックス](index-policy.md#composite-indexes)が必要です。 次のクエリを考えてみます。

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

このクエリでは、都市名の昇順で家族の `id` が取得されます。 複数の項目に同じ都市名がある場合、クエリが `creationDate` の降順で並べ替えます。

## <a name="documents-with-missing-fields"></a>フィールドがないドキュメント

既定のインデックス作成ポリシーが設定されているコンテナーに対して実行される、`ORDER BY` を含むクエリでは、並べ替えプロパティが定義されていないドキュメントは返されません。 並べ替えプロパティが定義されていないドキュメントを含める場合は、インデックス作成ポリシーにこのプロパティを明示的に含める必要があります。

たとえば、`"/*"` 以外のパスが明示的に含まれていないインデックス作成ポリシーを設定したコンテナーを次に示します。

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

`Order By` 句に `lastName` を含むクエリを実行すると、結果には `lastName` プロパティが定義されているドキュメントのみが含まれます。 `lastName` について明示的に含めるパスが定義されていないため、クエリ結果に `lastName` のないドキュメントは表示されません。

2 つのドキュメント (そのうちの 1 つには `lastName` が定義されていない) について、`lastName` で並べ替えるクエリを次に示します。

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

結果には、`lastName` が定義されているドキュメントのみが含まれます。

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

コンテナーのインデックス作成ポリシーを更新して `lastName` のパスを明示的に含めた場合、並べ替えプロパティが定義されていないドキュメントがクエリ結果に含まれます。 このスカラー値に至る (かつ、これを超えない) パスを明示的に定義する必要があります。 インデックス作成ポリシーのパス定義に `?` 文字を使用して、プロパティ `lastName` の明示的なインデックス付けを、それを超えるその他の入れ子になったパスなしで確実に行う必要があります。 ご利用の `Order By` クエリに[複合インデックス](index-policy.md#composite-indexes)が使用されている場合は、その結果として常に、並べ替えプロパティが定義されていないドキュメントがクエリ結果に含められます。

`lastName` が定義されていないドキュメントをクエリ結果に含めることができるインデックス作成ポリシーの例を次に示します。

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/lastName/?"
        },
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

同じクエリをもう一度実行すると、`lastName` がないドキュメントがクエリ結果の最初に表示されます。

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

結果は次のようになります。

```json
[
    {
        "id": "WakefieldFamily"
    },
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    }
]
```

並べ替え順序を `DESC` に変更すると、 `lastName` がないドキュメントがクエリ結果の最後に表示されます。

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

結果は次のようになります。

```json
[
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    },
    {
        "id": "WakefieldFamily"
    }
]
```

> [!Note]
> .NET SDK バージョン 3.4.0 以降でのみ、混合型で ORDER BY がサポートされます。 そのため、未定義の値と定義済みの値の組み合わせで並べ替える場合、このバージョン (またはそれ以降) を使用してください。

さまざまな型を結果に表示する順序を制御できます。 上の例では、文字列値の前に未定義の値がどのように並べ替えられたかを示しました。 そうではなく、たとえば、未定義の値の並べ替え順序をもっと細かく制御することが望まれた場合、未定義のプロパティが確実に最初か最後になるように、"aaaaaaaaa" または "zzzzzzzz" の文字列値を割り当てることができました。

## <a name="next-steps"></a>次のステップ

- [作業の開始](sql-query-getting-started.md)
- [Azure Cosmos DB での自動インデックス作成](index-policy.md)
- [OFFSET LIMIT 句](sql-query-offset-limit.md)
