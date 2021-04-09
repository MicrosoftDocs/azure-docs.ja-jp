---
title: Azure Cosmos DB クエリ言語の EndsWith
description: 1 つ目の文字列式が 2 つ目で終了しているかどうかを示すブール値を返す、Azure Cosmos DB の ENDSWITH SQL システム関数について説明します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c0cc93fee8aacc711a797925cb2e2808b73cafd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93338834"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

1 つ目の文字列式が 2 つ目の文字列で終了しているかどうかを示すブール値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
ENDSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])
```  
  
## <a name="arguments"></a>引数
  
*str_expr1*  
   文字列式です。  
  
*str_expr2*  
   *str_expr1* の末尾と比較される文字列式です。

*bool_expr* 大文字と小文字の区別を無視する場合のオプションの値です。 true に設定すると、ENDSWITH は大文字と小文字を区別せずに検索を実行します。 指定しない場合、この値は false になります。
  
## <a name="return-types"></a>戻り値の型
  
  ブール式を返します。  
  
## <a name="examples"></a>例
  
次の例は、文字列 "abc" が "b" および "bC" で終わるかどうかを確認します。  
  
```sql
SELECT ENDSWITH("abc", "b", false) AS e1, ENDSWITH("abc", "bC", false) AS e2, ENDSWITH("abc", "bC", true) AS e3
```  
  
 結果セットは次のようになります。  
  
```json
[
    {
        "e1": false,
        "e2": false,
        "e3": true
    }
]
```  

## <a name="remarks"></a>解説

このシステム関数は、[範囲インデックス](index-policy.md#includeexclude-strategy)の恩恵を受けます。

システム関数のプロパティのカーディナリティが増加するにつれて、EndsWith の RU 消費量は増加します。 つまり、プロパティ値に特定の文字列で終わるかどうかを確認する場合、クエリ RU の料金は、そのプロパティで使用可能な値の数によって決まります。

たとえば、town と country という 2 つのプロパティで考えてみましょう。 town のカーディナリティは 5,000 で、country のカーディナリティは 200 です。 2 つのクエリの例を次に示します。

```sql
    SELECT * FROM c WHERE ENDSWITH(c.town, "York", false)
```

```sql
    SELECT * FROM c WHERE ENDSWITH(c.country, "States", false)
```

最初のクエリの方が 2 番目のクエリよりも多くの RU を使用すると思われます。これは、town のカーディナリティの方が country よりも多いためです。

EndsWith のプロパティ サイズが一部のドキュメントで 1 KB より大きい場合、クエリ エンジンはこれらのドキュメントを読み込む必要があります。 この場合、クエリ エンジンは、インデックスで EndsWith を完全には評価できなくなります。 プロパティ サイズが 1 KB より大きいドキュメントが多数ある場合、EndsWith の RU 料金は高くなります。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
