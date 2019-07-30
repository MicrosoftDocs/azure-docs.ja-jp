---
title: Azure Cosmos DB の SQL クエリでのスカラー式
description: Azure Cosmos DB のスカラー式の SQL 構文について説明します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 4464c39a45c47c680a13f3ebc34841b47ee0d7c6
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343232"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Azure Cosmos DB の SQL クエリでのスカラー式

[SELECT 句](sql-query-select.md)ではスカラー式がサポートされます。 スカラー式は、1 つの値を取得するために評価できるシンボルと演算子の組み合わせです。 スカラー式の例としては、定数、プロパティの参照、配列要素の参照、別名の参照、関数の呼び出しなどがあります。 スカラー式は、演算子を使用して複雑な式に結合できます。

## <a name="syntax"></a>構文
  
```sql  
<scalar_expression> ::=  
       <constant>
     | input_alias
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>
     | <create_array_expression>  
     | (<scalar_expression>)
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```

## <a name="arguments"></a>引数
  
- `<constant>`  
  
   定数値を表します。 詳細については、「[定数](sql-query-constants.md)」セクションを参照してください。  
  
- `input_alias`  
  
   `FROM` 句によって導入された `input_alias` で定義された値を表します。  
  この値は、**undefined** ではないことが保証されます。入力内の **undefined** 値はスキップされます。  
  
- `<scalar_expression>.property_name`  
  
   オブジェクトのプロパティの値を表します。 プロパティが存在しない場合、またはプロパティがオブジェクトではない値で参照されている場合、式は **undefined** 値として評価されます。  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   名前 `property_name` を持つプロパティの値、または配列のインデックス `array_index` を持つ配列要素を表します。 プロパティ/配列インデックスが存在しない場合、またはプロパティ/配列インデックスがオブジェクト/配列ではない値で参照されている場合、式は undefined 値として評価されます。  
  
- `unary_operator <scalar_expression>`  
  
   1 つの値に適用される演算子を表します。 詳細については、「[演算子](sql-query-operators.md)」セクションを参照してください。  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   2 つの値に適用される演算子を表します。 詳細については、「[演算子](sql-query-operators.md)」セクションを参照してください。  
  
- `<scalar_function_expression>`  
  
   関数呼び出しの結果によって定義された値を表します。  
  
- `udf_scalar_function`  
  
   ユーザー定義のスカラー関数の名前です。  
  
- `builtin_scalar_function`  
  
   組み込みのスカラー関数の名前です。  
  
- `<create_object_expression>`  
  
   指定したプロパティとその値で新しいオブジェクトを作成することによって取得される値を表します。  
  
- `<create_array_expression>`  
  
   指定した値を要素として新しい配列を作成することによって取得される値を表します。  
  
- `parameter_name`  
  
   指定したパラメーターの値を表します。 パラメーター名は、最初の文字として 1 つの \@ を使用する必要があります。  
  
## <a name="remarks"></a>解説
  
  組み込みまたはユーザー定義のスカラー関数を呼び出すときには、すべての引数を定義する必要があります。 引数のいずれかが定義されていない場合、関数は呼び出されず、結果は未定義になります。  
  
  オブジェクトを作成するときに、未定義の値が割り当てられているプロパティはすべてスキップされ、作成されたオブジェクトに含まれません。  
  
  配列を作成するときに、**undefined** 値が割り当てられている要素値はすべてスキップされ、作成されたオブジェクトに含まれません。 この場合、作成される配列にスキップされたインデックスが含まれないようにするために、次の定義済みの要素が代わりに使用されます。  

## <a name="examples"></a>例

```sql
    SELECT ((2 + 11 % 7)-2)/3
```

結果は次のようになります。

```json
    [{
      "$1": 1.33333
    }]
```

次のクエリでは、スカラー式の結果はブール値になります。

```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

結果は次のようになります。

```json
    [
      {
        "AreFromSameCityState": false
      },
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の概要](introduction.md)
- [Azure Cosmos DB .NET のサンプル](https://github.com/Azure/azure-cosmosdb-dotnet)
- [サブクエリ](sql-query-subquery.md)