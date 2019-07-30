---
title: Azure Cosmos DB でのユーザー定義関数 (UDF)
description: Azure Cosmos DB でのユーザー定義関数について説明します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: e168e450230720f4ad78516e6edcdc3aa08ba3e1
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343219"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Azure Cosmos DB でのユーザー定義関数 (UDF)

SQL API ではユーザー定義関数 (UDF) のサポートを提供しています。 スカラー UDF を使用して、0 個またはいくつかの引数を渡し、1 つの引数を結果として返すことができます。 API によって、引数のそれぞれが有効な JSON 値であることがチェックされます。  

API によって、UDF を使用して、SQL 構文が拡張され、カスタムのアプリケーション ロジックがサポートされます。 SQL API を使用して、UDF を登録し、それらを SQL クエリで参照できます。 実際 UDF は、クエリから呼び出せるよう精巧に設計されています。 当然の帰結として、UDF は、ストアド プロシージャやトリガーなどのその他の JavaScript の型のようなコンテキスト オブジェクトにアクセスできません。 クエリは読み取り専用で、プライマリ レプリカでもセカンダリ レプリカでも実行することができます。 UDF は、その他の JavaScript の型とは異なり、セカンダリ レプリカで実行されるように設計されています。

次の例では、Cosmos DB データベースの項目コンテナーに UDF を登録しています。 例では `REGEX_MATCH` という名前の UDF を作成しています。 これは 2 つの JSON 文字列値 `input` と `pattern` を受け取り、JavaScript の `string.match()` 関数を使用して、1 つ目の文字列値が、2 つ目の文字列値で指定されたパターンに一致するかどうかをチェックします。

## <a name="examples"></a>例

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

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の概要](introduction.md)
- [システム関数](sql-query-system-functions.md)
- [集計](sql-query-aggregates.md)