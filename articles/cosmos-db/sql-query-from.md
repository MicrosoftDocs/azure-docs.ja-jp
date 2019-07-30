---
title: Azure Cosmos DB での FROM 句
description: Azure Cosmos DB の SQL FROM 句について説明します
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: tisande
ms.openlocfilehash: 6bc93569dc9a0405ec3a8dfd719c89ede01df84d
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343249"
---
# <a name="from-clause"></a>FROM 句

ソースがクエリの後半でフィルター処理またはプロジェクションされる場合を除いて、FROM (`FROM <from_specification>`) 句はオプションです。 `SELECT * FROM Families` のようなクエリは、`Families` コンテナー全体を列挙します。 コンテナー名の代わりにコンテナーに特別な識別子 ROOT を使うこともできます。

FROM 句では、クエリごとに次の規則が適用されます。

* コンテナーは、`SELECT f.id FROM Families AS f` またはシンプルに `SELECT f.id FROM Families f` のようにエイリアス化することができます。 ここで `f` は `Families` のエイリアスです。 AS は、識別子を[エイリアス化する](sql-query-aliasing.md)ためのオプションのキーワードです。  

* エイリアス化されると、元のソース名をバインドすることはできなくなります。 たとえば、`SELECT Families.id FROM Families f` は無効な構文となります。識別子 `Families` がエイリアス化されており、それ以上解決できないためです。  

* 参照されているすべてのプロパティを完全修飾し、厳格なスキーマの準拠がない場合の曖昧なバインドを回避する必要があります。 たとえば、プロパティ `id` がバインドされていないため、`SELECT id FROM Families f` は無効な構文です。

## <a name="syntax"></a>構文
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=   
        ROOT   
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
## <a name="arguments"></a>引数
  
- `<from_source>`  
  
  別名の有無に関係なくデータソースを指定します。 別名が指定されていない場合、次の規則を使用して、`<container_expression>` から推論されます。  
  
  -  式が container_name である場合は、container_name が別名として使用されます。  
  
  -  式が `<container_expression>` である場合は、property_name が別名として使用されます。 式が container_name である場合は、container_name が別名として使用されます。  
  
- AS `input_alias`  
  
  `input_alias` が基になるコンテナー式によって返される値のセットであることを指定します。  
 
- `input_alias` IN  
  
  `input_alias` が基になるコンテナー式によって返される各配列のすべての配列要素を反復処理して取得した値のセットを表すことを指定します。 配列ではない基になるコンテナー式によって返される値はすべて無視されます。  
  
- `<container_expression>`  
  
  ドキュメントを取得するために使用するコンテナー式を指定します。  
  
- `ROOT`  
  
  既定の現在接続しているコンテナーからそのドキュメントを取得する必要があることを指定します。  
  
- `container_name`  
  
  提供されたコンテナーからそのドキュメントを取得する必要があることを指定します。 コンテナーの名前は、現在接続しているコンテナーの名前と一致する必要があります。  
  
- `input_alias`  
  
  提供された別名によって定義された他のソースからそのドキュメントを取得する必要があることを指定します。  
  
- `<container_expression> '.' property_`  
  
  指定されたコンテナー式によって取得されるすべてのドキュメントの `property_name` プロパティまたは array_index アレイ要素にアクセスすることによって、そのドキュメントを取得する必要があることを指定します。  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  指定されたコンテナー式によって取得されるすべてのドキュメントの `property_name` プロパティまたは array_index アレイ要素にアクセスすることによって、そのドキュメントを取得する必要があることを指定します。  
  
## <a name="remarks"></a>解説
  
指定されるか `<from_source>(` で推測されたすべての別名は一意である必要があります。 構文 `<container_expression>.`property_name は `<container_expression>' ['"property_name"']'` と同じです。 ただし、プロパティ名に非識別子文字が含まれている場合は、後者の構文を使用できます。  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>見つからないプロパティ、見つからない配列要素、未定義の値の処理
  
コンテナー式がプロパティまたは配列要素にアクセスし、その値が存在しない場合、値は無視され、処理は行われません。  
  
### <a name="container-expression-context-scoping"></a>コンテナー式のコンテキストのスコープ  
  
コンテナー式には、コンテナー スコープまたはドキュメント スコープがあります。  
  
-   基になるコンテナー式のソースが ROOT または `container_name` である場合、式はコンテナー スコープです。 このような式は、直接コンテナーから取得されたドキュメントのセットを表し、その他のコンテナーの式の処理に依存しません。  
  
-   基になるコンテナー式が、クエリで前に導入された `input_alias` である場合、式はドキュメント スコープです。 このような式は、別名を付けられたコンテナーに関連付けられているセットに属する各ドキュメントのスコープ内で、コンテナー式を評価することによって取得されたドキュメントのセットを表します。  結果セットは、基になるセット内の各ドキュメントのコンテナー式を評価することによって取得されたセットの和集合になります。 

## <a name="examples"></a>例

### <a name="get-subitems-by-using-the-from-clause"></a>FROM 句を使用してサブ項目を取得する

FROM 句により、ソースを小さなサブセットに限定することができます。 各項目のサブツリーだけを列挙するには、以下の例のようにサブルートをソースにすることができます。

```sql
    SELECT *
    FROM Families.children
```

結果は次のようになります。

```json
    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
       {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

前のクエリでは、配列をソースとして使用していますが、オブジェクトをソースとして使用することもできます。 クエリでは、ソース内のすべての有効な定義済みの JSON 値が結果に含まれるものと見なされます。 次の例は、`address.state` 値がない `Families` を除外しています。

```sql
    SELECT *
    FROM Families.address.state
```

結果は次のようになります。

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="next-steps"></a>次の手順

- [使用の開始](sql-query-getting-started.md)
- [SELECT 句](sql-query-select.md)
- [WHERE 句](sql-query-where.md)