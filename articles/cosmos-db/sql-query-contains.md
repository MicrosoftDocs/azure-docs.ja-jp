---
title: Azure Cosmos DB クエリ言語の CONTAINS
description: Azure Cosmos DB の CONTAINS SQL システム関数で、1 つ目の文字列式に 2 つ目の文字列式が含まれているかどうかを示すブール値を返す方法について説明します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4b6835b22e5cfa4ca703b95d70e20112b8723def
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339174"
---
# <a name="contains-azure-cosmos-db"></a>CONTAINS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

1 つ目の文字列式に 2 つ目の文字列式が含まれているかどうかを示すブール値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
CONTAINS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>引数
  
*str_expr1*  
   検索対象となる文字列式です。  
  
*str_expr2*  
   検索する文字列式です。  

*bool_expr* 大文字と小文字の区別を無視する場合のオプションの値です。 true に設定すると、CONTAINS は大文字と小文字を区別せずに検索を実行します。 指定しない場合、この値は false になります。
  
## <a name="return-types"></a>戻り値の型
  
  ブール式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、"abc" に "ab" が含まれ、"abc" に "A" が含まれるかどうかを確認します。  
  
```sql
SELECT CONTAINS("abc", "ab", false) AS c1, CONTAINS("abc", "A", false) AS c2, CONTAINS("abc", "A", true) AS c3
```  
  
 結果セットは次のようになります。  
  
```json
[
    {
        "c1": true,
        "c2": false,
        "c3": true
    }
]
```  

## <a name="remarks"></a>解説

このシステム関数は、[範囲インデックス](index-policy.md#includeexclude-strategy)の恩恵を受けます。

システム関数のプロパティのカーディナリティが増加するにつれて、CONTAINS の RU 消費量は増加します。 つまり、プロパティ値に特定の文字列が含まれているかどうかを確認する場合、クエリ RU の料金は、そのプロパティで使用可能な値の数によって決まります。

たとえば、town と country という 2 つのプロパティで考えてみましょう。 town のカーディナリティは 5,000 で、country のカーディナリティは 200 です。 2 つのクエリの例を次に示します。

```sql
    SELECT * FROM c WHERE CONTAINS(c.town, "Red", false)
```

```sql
    SELECT * FROM c WHERE CONTAINS(c.country, "States", false)
```

最初のクエリの方が 2 番目のクエリよりも多くの RU を使用すると思われます。これは、town のカーディナリティの方が country よりも多いためです。

CONTAINS のプロパティ サイズが一部のドキュメントで 1 KB より大きい場合、クエリ エンジンはこれらのドキュメントを読み込む必要があります。 この場合、クエリ エンジンは、インデックスで CONTAINS を完全には評価できなくなります。 プロパティ サイズが 1 KB より大きいドキュメントが多数ある場合、CONTAINS の RU 料金は高くなります。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
