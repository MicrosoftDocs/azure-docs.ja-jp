---
title: Azure Cosmos DB でのユーザー定義関数 (UDF)
description: Azure Cosmos DB でのユーザー定義関数について説明します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 98698264f0beb25a8b4f74861f1150ae889d7115
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546337"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Azure Cosmos DB でのユーザー定義関数 (UDF)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

SQL API ではユーザー定義関数 (UDF) のサポートを提供しています。 スカラー UDF を使用して、0 個またはいくつかの引数を渡し、1 つの引数を結果として返すことができます。 API によって、引数のそれぞれが有効な JSON 値であることがチェックされます。  

## <a name="udf-use-cases"></a>UDF ユース ケース

API によって、UDF を使用して、SQL 構文が拡張され、カスタムのアプリケーション ロジックがサポートされます。 SQL API を使用して、UDF を登録し、それらを SQL クエリで参照できます。 ストアド プロシージャやトリガーとは異なり、UDF は読み取り専用です。

UDF を使用すると、Azure Cosmos DB のクエリ言語を拡張できます。 UDF は、クエリのプロジェクションで複雑なビジネス ロジックを表すための優れた方法です。

ただし、次の場合は UDF の使用を回避することをお勧めします。

- 同等の[システム関数](sql-query-system-functions.md)が既に Azure Cosmos DB に存在する。 システム関数では、常に同等の UDF より少ない RU が使用されます。
- その UDF がクエリの `WHERE` 句にある唯一のフィルターである。 UDF ではインデックスが利用されないため、UDF を評価するにはドキュメントの読み込みが必要になります。 `WHERE` 句で、インデックスを使用する追加のフィルター述語を UDF と組み合わせて使用すると、UDF によって処理されるドキュメントの数が削減されます。

クエリで同じ UDF を複数回使用する必要がある場合は、その UDF を[サブクエリ](sql-query-subquery.md#evaluate-once-and-reference-many-times)で参照するようにしてください。それにより、JOIN 式を使用して UDF を 1 回評価した後、何回も参照できるようになります。

## <a name="examples"></a>例

次の例では、Cosmos データベースの項目コンテナーで UDF を登録します。 例では `REGEX_MATCH` という名前の UDF を作成しています。 これは 2 つの JSON 文字列値 `input` と `pattern` を受け取り、JavaScript の `string.match()` 関数を使用して、1 つ目の文字列値が、2 つ目の文字列値で指定されたパターンに一致するかどうかをチェックします。

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

ここで、この UDF をクエリ プロジェクションで使用します。 UDF はクエリ内から呼び出すときに、大文字と小文字が区別されるプレフィックス `udf.` で修飾する必要があります。

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

結果は次のようになります。

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

次の例のように、フィルター内で、`udf.` プレフィックスで修飾された UDF を使用できます。

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

結果は次のようになります。

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

基本的に UDF は、プロジェクションとフィルターの両方で使用することができる有効なスカラー式です。

UDF の機能を拡張するには、条件ロジックが使用された別の例をご覧ください。

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

次の例では、この UDF を実行しています。

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

結果は次のようになります。

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

UDF のパラメーターに参照されるプロパティを JSON 値で利用できない場合、パラメーターは未定義と見なされ、UDF 呼び出しがスキップされます。 同様に UDF の結果が未定義の場合は結果に含められません。

前述の例でわかるように、UDF は、JavaScript 言語の機能と SQL API を統合します。 UDF は、組み込みの JavaScript ランタイム機能を活用して、手続き型で条件付きの複雑なロジックを実行するためのリッチ プログラミング インターフェイスを提供します。 SQL API は、現在の WHERE 句または SELECT 句の処理の段階で、各ソース項目に、UDF への引数を提供します。 結果は、実行パイプライン全体にシームレスに組み込まれます。 まとめると、複雑なビジネス ロジックをクエリの一部として実行するには、UDF は非常に便利な手段です。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の概要](introduction.md)
- [システム関数](sql-query-system-functions.md)
- [集計](sql-query-aggregate-functions.md)