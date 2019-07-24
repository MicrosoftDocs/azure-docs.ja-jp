---
title: Azure Cosmos DB の SQL クエリ
description: Azure Cosmos DB の SQL 構文、データベースの概念、および SQL クエリについて説明します。 Azure Cosmos DB JSON クエリ言語として SQL を使用します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mjbrown
ms.openlocfilehash: 04a88558e3aea33c6d99bd0e4f1354c4316f5529
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59579220"
---
# <a name="sql-query-examples-for-azure-cosmos-db"></a>Azure Cosmos DB の SQL クエリの例

Azure Cosmos DB SQL API アカウントでは、JSON クエリ言語として SQL (構造化照会言語) を使用する項目のクエリがサポートされています。 Azure Cosmos DB クエリ言語の設計目標は次のとおりです。

* 新しいクエリ言語を開発する代わりに、最もよく知られていて人気のあるクエリ言語の 1 つである SQL をサポートしています。 SQL は、JSON 項目に対するリッチ クエリ用の正式なプログラミング モデルを提供します。  

* クエリ言語の基盤として JavaScript のプログラミング モデルを使用します。 JavaScript の型システム、式評価、関数呼び出しは、SQL API の基盤です。 これらの基盤によって、リレーショナル プロジェクション、JSON 項目全体の階層型ナビゲーション、自己結合、空間クエリ、完全に JavaScript で記述されたユーザー定義関数 (UDF) の呼び出しなどの機能に対して、自然なプログラミング モデルが提供されます。

この記事では、簡単な JSON 項目への SQL クエリの例をいくつか説明します。 Azure Cosmos DB SQL 言語の構文については、[SQL 構文リファレンス](sql-api-query-reference.md)をご覧ください。

## <a id="GettingStarted"></a>SQL クエリの概要

SQL API Cosmos DB アカウントで、`Families` というコンテナーを作成します。 コンテナーに、2 つの簡単な JSON 項目を作成し、それらに対して、いくつかの簡単なクエリを実行します。

### <a name="create-json-items"></a>JSON 項目を作成する

次のコードは、家族に関する 2 つの簡単な JSON 項目を作成します。 Andersen 一家と Wakefield 一家に関する簡単な JSON 項目に、両親、子どもと子どものペット、住所、登録に関する情報が記載されています。 最初の項目には、文字列、数値、ブール値、配列、入れ子になったプロパティがあります。


```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

2 つ目の項目は、`firstName` と `lastName` の代わりに `givenName` と `familyName` を使用します。

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female", 
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>JSON 項目のクエリを実行する

Azure Cosmos DB の SQL クエリ言語の重要な側面について理解するため、JSON データに対していくつかのクエリを実行してみます。

以下のクエリを実行すると、`id` フィールドが `AndersenFamily` に一致する項目が返されます。 `SELECT *` クエリであるため、クエリの出力は完全な JSON 項目になります。 SELECT 構文の詳細については、[SELECT ステートメント](sql-api-query-reference.md#select-query)に関するページを参照してください。 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

クエリ結果: 

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

次のクエリは、JSON 出力を異なる形式に変更します。 このクエリは、住所の都市が州と同じ場合に、2 つの選択したフィールド (`Name` と `City`) を持つ JSON `Family` オブジェクトをプロジェクションします。 "NY, NY" はこのケースに一致します。

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

クエリ結果:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

次のクエリでは、`id` が `WakefieldFamily` と一致する家族の子どもの名がすべて学年順に返されます。

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.grade ASC
```

結果は次のようになります。

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

上記の例は、Cosmos DB クエリ言語のいくつかの側面を示しています。  

* SQL API は JSON 値に対して機能するので、行と列ではなくツリー形式のエンティティを処理します。 `Node1.Node2.Node3…..Nodem` のように任意の深さのツリーのノードを参照でき、ANSI SQL での `<table>.<column>` の 2 項目参照に似ています。

* クエリ言語はスキーマレス データを操作するため、型システムを動的にバインドする必要があります。 同じ式でも、項目が異なれば異なる型が導出される場合があります。 クエリ結果は有効な JSON 値ですが、固定スキーマの場合でも有効とは限りません。  

* Azure Cosmos DB は厳密な JSON 項目だけをサポートします。 型システムおよび式は、JSON 型のみを扱うように制限されます。 詳細については、[JSON の仕様](https://www.json.org/)に関する記事を参照してください。  

* Cosmos DB コンテナーは、スキーマがない JSON 項目のコレクションです。 コンテナー項目内および項目全体の関係は、含有関係によって暗黙的にキャプチャされ、主キーと外部キーの関係ではキャプチャされません。 この機能は、この記事で後述する項目間結合に重要です。

## <a id="SelectClause"></a>SELECT 句

すべてのクエリは ANSI-SQL 標準に従って SELECT 句とオプションの FROM および WHERE 句で構成されます。 通常、FROM 句のソースが列挙され、JSON 項目のサブセットを取得するためにそのソースに WHERE 句のフィルターが適用されます。 SELECT 句は、要求された JSON 値を選択リストにプロジェクションします。 構文の詳細については、[SELECT ステートメント](sql-api-query-reference.md#select-query)に関するページを参照してください。

次の SELECT クエリの例は、`id` が `AndersenFamily` と一致する `Families` から `address` を返します。

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

結果は次のようになります。

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

## <a id="EscapingReservedKeywords"></a>引用符で囲まれたプロパティのアクセサー
プロパティは、引用符で囲まれたプロパティの演算子 [] を使用してアクセスすることができます。 たとえば、 `SELECT c.grade` and `SELECT c["grade"]` は同等です。 この構文はスペース、特殊文字を含むプロパティや、SQL キーワードや予約語と同じ名前を持つプロパティをエスケープする場合に役立ちます。

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

## <a name="nested-properties"></a>入れ子になったプロパティ

以下の例では、2 つの入れ子になったプロパティ `f.address.state` と `f.address.city` をプロジェクションしています。

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

結果は次のようになります。

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```

## <a name="json-expressions"></a>JSON 式

プロジェクションは、以下の例のように JSON 式もサポートします。

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

結果は次のようになります。

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

前の例では、SELECT 句で JSON オブジェクトを作成する必要があり、サンプルはキーを提供していないため、句で暗黙的な引数変数名 `$1` を使用しています。 次のクエリは `$1` と `$2` の 2 つの暗黙的な引数の変数を返します。

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

結果は次のようになります。

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="ValueKeyword"></a>VALUE キーワード

VALUE キーワードは、JSON 値だけを返す方法を提供します。 たとえば以下のクエリでは、スカラー式 `"Hello World"` が返され、`{$1: "Hello World"}` とはなりません。

```sql
    SELECT VALUE "Hello World"
```

以下のクエリでは、`address` ラベルのない JSON 値が返されます。

```sql
    SELECT VALUE f.address
    FROM Families f
```

結果は次のようになります。

```json
    [
      {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan",
        "city": "NY"
      }
    ]
```

次の例は、JSON のプリミティブ値 (JSON ツリーのリーフ レベル) を返す方法を示しています。


```sql
    SELECT VALUE f.address.state
    FROM Families f
```

結果は次のようになります。

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="aliasing"></a>エイリアス化

クエリ内の値を明示的にエイリアス化できます。 クエリに同じ名前を持つ 2 つのプロパティがある場合、エイリアス化を使ってプロパティのいずれかまたは両方の名前を変更することで、プロジェクションの結果でこれらを区別できます。

2 つ目の値を `NameInfo` としてプロジェクションしている場合の次の例に示すように、エイリアス化に使用する AS キーワードはオプションです。

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

結果は次のようになります。

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="FromClause"></a>FROM 句

ソースがクエリの後半でフィルター処理またはプロジェクションされる場合を除いて、FROM (`FROM <from_specification>`) 句はオプションです。 構文の詳細については、[FROM 構文](sql-api-query-reference.md#bk_from_clause)に関するページをご覧ください。 `SELECT * FROM Families` のようなクエリは、`Families` コンテナー全体を列挙します。 コンテナー名の代わりにコンテナーに特別な識別子 ROOT を使うこともできます。

FROM 句では、クエリごとに次の規則が適用されます。

* コンテナーは、`SELECT f.id FROM Families AS f` またはシンプルに `SELECT f.id FROM Families f` のようにエイリアス化することができます。 ここで `f` は `Families` のエイリアスです。 AS は識別子をエイリアス化するためのオプションのキーワードです。  

* エイリアス化されると、元のソース名をバインドすることはできなくなります。 たとえば、`SELECT Families.id FROM Families f` は無効な構文となります。識別子 `Families` がエイリアス化されており、それ以上解決できないためです。  

* 参照されているすべてのプロパティを完全修飾し、厳格なスキーマの準拠がない場合の曖昧なバインドを回避する必要があります。 たとえば、プロパティ `id` がバインドされていないため、`SELECT id FROM Families f` は無効な構文です。

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

## <a id="WhereClause"></a>WHERE 句

オプションの WHERE 句 (`WHERE <filter_condition>`) は、ソース JSON 項目が、クエリでそれらを結果に含めるために、満たす必要がある条件を指定します。 結果の対象となるには、指定された条件を JSON 項目が `true` と評価する必要があります。 インデックス レイヤーは、WHERE 句を使用して、結果に含めることが可能なソース項目の最小のサブセットを判断します。 構文の詳細については、[WHERE 構文](sql-api-query-reference.md#bk_where_clause) に関するページを参照してください。

以下のクエリでは、`id` プロパティを含み、その値が `AndersenFamily` であるような項目を要求します。 `id` プロパティを備えていない、またはその値が `AndersenFamily` に一致しない項目はすべて除外されます。

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

結果は次のようになります。

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>WHERE 句内のスカラー式

上記の例では単純な等値クエリを紹介しました。 SQL API はさまざまな[スカラー式](#scalar-expressions)もサポートしています。 最も多く使用されるのはバイナリ式と単項式です。 ソース JSON オブジェクトからのプロパティ参照も有効な式です。

次のサポートされているバイナリ演算子を使用できます。  

|**演算子の種類**  | **値** |
|---------|---------|
|算術 | +、-、*、/、% |
|ビット    | \|、&、^、<<、>>、>>> (0 埋め右シフト) |
|論理    | AND、OR、NOT      |
|比較 | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|String     |  \|\| (連結) |

次のクエリでは、2 項演算子を使用しています。

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

次の例のように、クエリでは、単項演算子 +、-、~、および NOT も使用できます。

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

クエリでプロパティ参照を使用することもできます。 たとえば、`SELECT * FROM Families f WHERE f.isRegistered` は、値が `true` 値と等しい `isRegistered` プロパティを含む JSON 項目を返します。 `false`、`null`、`Undefined`、`<number>`、`<string>`、`<object>`、または `<array>` などの他の値は、項目を結果から除外します。 

### <a name="equality-and-comparison-operators"></a>等値演算子と比較演算子

以下の表は、SQL API の 2 つの JSON 型で等値比較を実行した結果を示しています。

| **演算子** | **Undefined** | **Null** | **Boolean** | **Number** | **文字列** | **Object** | **Array** |
|---|---|---|---|---|---|---|---|
| **Undefined** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Null** | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Boolean** | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined |
| **Number** | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined |
| **文字列** | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined |
| **Object** | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined |
| **Array** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** |

`>`、`>=`、`!=`、`<`、および `<=` などの比較演算子では、種類全体または 2 つのオブジェクトや配列間の比較で、`Undefined` が生成されます。  

スカラー式の結果が `Undefined` である場合、`Undefined` は `true` に等しくないため、項目が結果に含まれません。

### <a name="logical-and-or-and-not-operators"></a>論理 (AND、OR、および NOT) 演算子

論理演算子は Boolean 値に対して使用されます。 次の表に、これらの演算子の真理値表を示します。

**OR 演算子**

| または | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Undefined |
| Undefined |True |Undefined |Undefined |

**AND 演算子**

| AND | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |False |Undefined |
| False |False |False |False |
| Undefined |Undefined |False |Undefined |

**NOT 演算子**

| NOT |  |
| --- | --- |
| True |False |
| False |True |
| Undefined |Undefined |

## <a name="between-keyword"></a>BETWEEN キーワード

ANSI SQL の場合と同様に、BETWEEN キーワードを使用して、文字列値や数値の範囲に対してクエリを表現することができます。 たとえば、次のクエリでは、最初の子の学年が 1 以上 5 以下であるすべての項目が返されます。

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

ANSI SQL の場合と異なり、次の例のように、FROM 句内に BETWEEN 句を使用することもできます。

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

SQL API では、ANSI SQL と異なり、混合型のプロパティに対して範囲クエリを表すことができます。 たとえば、一部の項目の `grade` が `5` などの数値で、他の項目が `grade4` などの文字列である場合があります。 このような場合、JavaScript の場合と同様に、2 つの異なる種類を比較した結果は `Undefined` になるため、項目がスキップされます。

> [!TIP]
> クエリの実行速度を速めるには、BETWEEN 句でフィルター処理される数値プロパティやパスに対して範囲のインデックス型を使用するインデックス作成ポリシーを作成します。

## <a name="in-keyword"></a>IN キーワード

IN キーワードは、指定した値がリスト内のいずれかの値と一致するかどうかをチェックするために使用します。 たとえば以下のクエリでは、`id` が `WakefieldFamily` または `AndersenFamily` であるすべての家族の項目が返されます。

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

次の例では、状態が指定された値のいずれかであるすべての項目が返されます。

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

## <a name="-operator"></a>* 演算子

特別な演算子 * によって、項目全体が現状のままプロジェクションされます。 使用する場合は、この演算子が唯一のプロジェクションされるフィールドである必要があります。 `SELECT * FROM Families f` のようなクエリは有効ですが、`SELECT VALUE * FROM Families f` および `SELECT *, f.id FROM Families f` は無効です。 [この記事の最初のクエリ](#query-the-json-items)で * 演算子を使用しました。 

## <a name="-and--operators"></a>? および ?? 演算子

3 項 (?) 演算子と合体 (??) 演算子は、C# や JavaScript などのプログラミング言語の場合と同様に、条件式の構築に使用することができます。 

? 演算子を 使用して、実行中に新しい JSON プロパティを構築できます。 たとえば、次のクエリは、学年を `elementary` または `other` に分類します。

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

また、? 演算子の呼び出しを 次のクエリのように入れ子にすることもできます。 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

他のクエリ演算子と同様に、? 演算子は 参照先のプロパティが見つからないか、比較対象の型が異なる場合、項目を除外します。

?? 演算子を 使用すると、半構造化されたデータや混合型のデータに対してクエリを実行するときに、項目内のプロパティが効率的にチェックされます。 たとえば、次のクエリは、`lastName` が存在する場合にそれを返し、`lastName` が存在しない場合は `surname` を返します。

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a id="TopKeyword"></a>TOP 演算子

TOP キーワードは、任意の順序で最初の `N` 個のクエリ結果を返します。 ベスト プラクティスとして、ORDER BY 句で TOP を使用して、最初の `N` 個の順序付けされた値に結果を制限します。 これらの 2 つの句を組み合わせることが、TOP の影響を受ける行を予想どおりに指定する唯一の方法です。 

TOP は、次の例のように定数で、またはパラメーター化されたクエリを使用した変数値で使用できます。 詳細については、「[パラメーター化されたクエリ](#parameterized-queries)」を参照してください。

```sql
    SELECT TOP 1 *
    FROM Families f
```

結果は次のようになります。

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a id="OrderByClause"></a>ORDER BY 句

ANSI SQL の場合と同様に、クエリにオプションの ORDER BY 句を含めることができます。 オプションの ASC または DESC 引数は、結果を昇順または降順のどちらで取得するかを指定します。 既定値は ASC です。

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
## <a name="scalar-expressions"></a>スカラー式

SELECT 句は、定数、算術式、論理式などのスカラー式をサポートします。 次のクエリでは、スカラー式を使用しています。


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

## <a name="object-and-array-creation"></a>オブジェクトと配列の作成

SQL API の重要な機能は、配列とオブジェクトの作成です。 前の例では、新しい JSON オブジェクト `AreFromSameCityState` を作成しました。 次の例のように配列を構築することもできます。


```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

結果は次のようになります。

```json
    [
      {
        "CityState": [
          "Seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```


## <a id="Iteration"></a>反復

SQL API では、FROM ソースの IN キーワードで追加される新しいコンストラクトによって、JSON 配列に対する反復がサポートされています。 次の例では

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
          "pets": [{ "givenName": "Fluffy"}]
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

次のクエリは、`Families`コンテナー内の `children` への反復を実行しています。 出力配列は、前のクエリと異なります。 この例では、 `children` を分割し、結果を 1 つの配列にフラット化しています。  

```sql
    SELECT *
    FROM c IN Families.children
```

結果は次のようになります。

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
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
```

以下の例のように、配列の個々のエントリをさらにフィルターすることができます。

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

結果は次のようになります。

```json
    [{
      "givenName": "Lisa"
    }]
```

配列の反復処理の結果に対して集計することもできます。 たとえば、次のクエリは、すべての家族の子どもの数を合計します。

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

結果は次のようになります。

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a id="Joins"></a>結合

リレーショナル データベースでは、テーブル間の結合は、正規化されたスキーマの設計の論理的必然です。 一方、SQL API では、スキーマがない項目の正規化されていないデータ モデルが使われます。これは論理的に*自己結合*と同義です。

言語では構文 `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>` をサポートしています。 このクエリでは、`N` 個の値を持つタプルのセットが返されます。 それぞれのタプルは、対応するセットに対する、すべてのコンテナーのエイリアスの反復によって生成された値を持ちます。 つまりこのクエリでは、結合に含まれるセットの完全なクロス積が行われます。

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

## <a id="UserDefinedFunctions"></a>ユーザー定義関数 (UDF)

SQL API ではユーザー定義関数 (UDF) のサポートを提供しています。 スカラー UDF を使用して、0 個またはいくつかの引数を渡し、1 つの引数を結果として返すことができます。 API によって、引数のそれぞれが有効な JSON 値であることがチェックされます。  

API によって、UDF を使用して、SQL 構文が拡張され、カスタムのアプリケーション ロジックがサポートされます。 SQL API を使用して、UDF を登録し、それらを SQL クエリで参照できます。 実際 UDF は、クエリから呼び出せるよう精巧に設計されています。 当然の帰結として、UDF は、ストアド プロシージャやトリガーなどのその他の JavaScript の型のようなコンテキスト オブジェクトにアクセスできません。 クエリは読み取り専用で、プライマリ レプリカでもセカンダリ レプリカでも実行することができます。 UDF は、その他の JavaScript の型とは異なり、セカンダリ レプリカで実行されるように設計されています。

次の例では、Cosmos DB データベースの項目コンテナーに UDF を登録しています。 例では `REGEX_MATCH` という名前の UDF を作成しています。 これは 2 つの JSON 文字列値 `input` と `pattern` を受け取り、JavaScript の `string.match()` 関数を使用して、1 つ目の文字列値が、2 つ目の文字列値で指定されたパターンに一致するかどうかをチェックします。

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

## <a id="Aggregates"></a>集計関数

集計関数は、SELECT 句内の一連の値を計算して 1 つの値を返します。 たとえば、次のクエリでは、`Families` コンテナー内にある項目の数が返されます。

```sql
    SELECT COUNT(1)
    FROM Families f
```

結果は次のようになります。

```json
    [{
        "$1": 2
    }]
```

VALUE キーワードを使用して、集計のスカラー値のみを返すこともできます。 たとえば、次のクエリは、値の数を 1 つの数値として返します。

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

結果は次のようになります。

```json
    [ 2 ]
```

フィルターによって集計を組み合わせることもできます。 たとえば、次のクエリでは、住所の州が `WA` である項目の数が返されます。

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

結果は次のようになります。

```json
    [ 1 ]
```

SQL API は、次の集計関数をサポートしています。 SUM と AVG は数値に対して機能し、COUNT、MIN、および MAX は、数値、文字列、ブール値、および null 値に対して機能します。

| Function | 説明 |
|-------|-------------|
| COUNT | 式の項目の数を返します。 |
| SUM   | 式のすべての値の合計を返します。 |
| MIN   | 式の最小値を返します。 |
| MAX   | 式の最大値を返します。 |
| AVG   | 式の値の平均を返します。 |

配列の反復処理の結果に対して集計することもできます。 詳細については、「[反復](#Iteration)」を参照してください。

> [!NOTE]
> Azure portal のデータ エクスプローラーでは、集計クエリが、1 ページのみのクエリ ページに対する部分的な結果を集計することがあります。 SDK は、すべてのページにわたって累計した単一の値を生成します。 コードを使用して集計クエリを実行するには、.NET SDK 1.12.0、.NET Core SDK 1.1.0、または Java SDK 1.9.5 以降が必要です。
>

## <a id="BuiltinFunctions"></a>組み込み関数

Cosmos DB では、ユーザー定義関数 (UDF) のようにクエリ内で使用できる、一般的な演算向けのいくつかの組み込み関数をサポートしています。

| 関数グループ | Operations |
|---------|----------|
| 数学関数 | ABS、CEILING、EXP、FLOOR、LOG、LOG10、POWER、ROUND、SIGN、SQRT、SQUARE、TRUNC、ACOS、ASIN、ATAN、ATN2、COS、COT、DEGREES、PI、RADIANS、SIN、TAN |
| 型チェック関数 | IS_ARRAY、IS_BOOL、IS_NULL、IS_NUMBER、IS_OBJECT、IS_STRING、IS_DEFINED、IS_PRIMITIVE |
| 文字列関数 | CONCAT、CONTAINS、ENDSWITH、INDEX_OF、LEFT、LENGTH、LOWER、LTRIM、REPLACE、REPLICATE、REVERSE、RIGHT、RTRIM、STARTSWITH、SUBSTRING、UPPER |
| 配列関数 | ARRAY_CONCAT、ARRAY_CONTAINS、ARRAY_LENGTH、ARRAY_SLICE |
| 空間関数 | ST_DISTANCE、ST_WITHIN、ST_INTERSECTS、ST_ISVALID、ST_ISVALIDDETAILED |

現在ユーザー定義関数 (UDF) を使用している処理に対して、組み込み関数を利用できるようになった場合は、対応する組み込み関数の方が、高速かつ効率的に実行します。

Cosmos DB 関数と ANSI SQL 関数の主な違いとして、Cosmos DB 関数はスキーマレス データやスキーマが混在するデータとうまく機能するように設計されています。 たとえば、プロパティがない場合や `unknown` のような数値以外の値を持つ場合、エラーを返す代わりに、項目がスキップされます。

### <a name="mathematical-functions"></a>数学関数

各数学関数は、引数として提供された入力値に基づいて計算を実行し、数値を返します。 次の表に、サポートされている組み込みの数学関数を示します。

| 使用法 | 説明 |
|----------|--------|
| ABS (num_expr) | 指定された数値式の絶対値 (正の値) を返します。 |
| CEILING (num_expr) | 指定された数値式以上の最小の整数値を返します。 |
| FLOOR (num_expr) | 指定された数値式未満の最大の整数を返します。 |
| EXP (num_expr) | 指定された数値式の指数を返します。 |
| LOG (num_expr, base) | 指定された数値式の自然対数、または指定された基数を使用して自然対数を返します。 |
| LOG10 (num_expr) | 指定された数値式の底 10 の対数値を返します。 |
| ROUND (num_expr) | 最も近い整数値に丸められた数値を返します。 |
| TRUNC (num_expr) | 最も近い整数値に切り捨てられた数値を返します。 |
| SQRT (num_expr) | 指定された数値式の平方根を返します。 |
| SQUARE (num_expr) | 指定された数値式の 2 乗を返します。 |
| POWER (num_expr, num_expr) | 指定された値の指定された数値式のべき乗を返します。 |
| SIGN (num_expr) | 指定された数値式の符号値 (-1、0、1) を返します。 |
| ACOS (num_expr) | コサインが指定された数値式となる角度をラジアン単位で返します。アークコサインとも呼ばれます。 |
| ASIN (num_expr) | サインが指定された数値式となる角度をラジアン単位で返します。 この関数はアークサインとも呼ばれます。 |
| ATAN (num_expr) | タンジェントが指定された数値式となる角度をラジアン単位で返します。 この関数はアークタンジェントとも呼ばれます。 |
| ATN2 (num_expr) | 正の x 軸と、原点から点 (y, x) までの斜線との間の角度をラジアン単位で返します。ここで x と y は、2 つの指定された float 型の式の値です。 |
| COS (num_expr) | 式で指定されたラジアン単位の角度の三角関数コサインを返します。 |
| COT (num_expr) | 数値式で指定されたラジアン単位の角度の三角関数コタンジェントを返します。 |
| DEGREES (num_expr) | ラジアン単位で指定された角度に対応する度数単位の角度を返します。 |
| PI () | 円周率の定数値を返します。 |
| RADIANS (num_expr) | 数値式が度数単位で入力されると、ラジアンを返します。 |
| SIN (num_expr) | 式で指定されたラジアン単位の角度の三角関数サインを返します。 |
| TAN (num_expr) | 指定された式で入力された式のタンジェントを返します。 |

次の例のようなクエリを実行できます。

```sql
    SELECT VALUE ABS(-4)
```

結果は次のとおりです。

```json
    [4]
```

### <a name="type-checking-functions"></a>型チェック関数

型チェック関数を使用すると、SQL クエリ内の式の型をチェックできます。 型チェック関数を使用して、項目内のプロパティの型が変数または不明の場合に型をその場で判定できます。 次の表に、サポートされている組み込みの型チェック関数を示します。

| **使用方法** | **説明** |
|-----------|------------|
| [IS_ARRAY (expr)](sql-api-query-reference.md#bk_is_array) | 値の型が配列であるかどうかを示すブール値を返します。 |
| [IS_BOOL (expr)](sql-api-query-reference.md#bk_is_bool) | 値の型がブール値であるかどうかを示すブール値を返します。 |
| [IS_NULL (expr)](sql-api-query-reference.md#bk_is_null) | 値の型が null であるかどうかを示すブール値を返します。 |
| [IS_NUMBER (expr)](sql-api-query-reference.md#bk_is_number) | 値の型が数値であるかどうかを示すブール値を返します。 |
| [IS_OBJECT (expr)](sql-api-query-reference.md#bk_is_object) | 値の型が JSON オブジェクトであるかどうかを示すブール値を返します。 |
| [IS_STRING (expr)](sql-api-query-reference.md#bk_is_string) | 値の型が文字列であるかどうかを示すブール値を返します。 |
| [IS_DEFINED (expr)](sql-api-query-reference.md#bk_is_defined) | プロパティに値が代入されているかどうかを示すブール値を返します。 |
| [IS_PRIMITIVE (expr)](sql-api-query-reference.md#bk_is_primitive) | 値の型が文字列、数値、ブール値、null のいずれであるかどうかを示すブール値を返します。 |

これらの関数を使用して、次の例のようなクエリを実行できます。

```sql
    SELECT VALUE IS_NUMBER(-4)
```

結果は次のとおりです。

```json
    [true]
```

### <a name="string-functions"></a>文字列関数

次のスカラー関数は、文字列入力値に対して演算を実行し、文字列、数値またはブール値を返します。 組み込みの文字列関数を次の表に示します。

| 使用法 | 説明 |
| --- | --- |
| [LENGTH (str_expr)](sql-api-query-reference.md#bk_length) | 指定された文字列式の文字数を返します。 |
| [CONCAT (str_expr, str_expr [, str_expr])](sql-api-query-reference.md#bk_concat) | 2 つ以上の文字列値を連結した結果である文字列を返します。 |
| [SUBSTRING (str_expr, num_expr, num_expr)](sql-api-query-reference.md#bk_substring) | 文字列式の一部を返します。 |
| [STARTSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_startswith) | 1 つ目の文字列式が 2 つ目の文字列で始まっているかどうかを示すブール値を返します。 |
| [ENDSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_endswith) | 1 つ目の文字列式が 2 つ目の文字列で終了しているかどうかを示すブール値を返します。 |
| [CONTAINS (str_expr, str_expr)](sql-api-query-reference.md#bk_contains) | 1 つ目の文字列式に 2 つ目の文字列式が含まれているかどうかを示すブール値を返します。 |
| [INDEX_OF (str_expr, str_expr)](sql-api-query-reference.md#bk_index_of) | 1 つ目に指定された文字列式内で、2 つ目の文字列式が最初に出現する箇所の開始位置を返します。文字列が見つからない場合は -1 を返します。 |
| [LEFT (str_expr, num_expr)](sql-api-query-reference.md#bk_left) | 指定された文字数分、文字列の左側の部分を返します。 |
| [RIGHT (str_expr, num_expr)](sql-api-query-reference.md#bk_right) | 指定された文字数分、文字列の右側の部分を返します。 |
| [LTRIM (str_expr)](sql-api-query-reference.md#bk_ltrim) | 文字列式の先頭の空白を削除して返します。 |
| [RTRIM (str_expr)](sql-api-query-reference.md#bk_rtrim) | 文字列式のすべての後続の空白を切り捨てて返します。 |
| [LOWER (str_expr)](sql-api-query-reference.md#bk_lower) | 文字列式の大文字データを小文字に変換して返します。 |
| [UPPER (str_expr)](sql-api-query-reference.md#bk_upper) | 文字列式の小文字データを大文字に変換して返します。 |
| [REPLACE (str_expr, str_expr, str_expr)](sql-api-query-reference.md#bk_replace) | 指定された文字列値のすべての出現箇所をもう 1 つの文字列値に置き換えます。 |
| [REPLICATE (str_expr, num_expr)](sql-api-query-reference.md#bk_replicate) | 文字列値を指定された回数だけ繰り返します。 |
| [REVERSE (str_expr)](sql-api-query-reference.md#bk_reverse) | 文字列値の順序を逆にして返します。 |

これらの関数を使用して、次のようなクエリを実行できます。これは、大文字で家族 `id` を返します。

```sql
    SELECT VALUE UPPER(Families.id)
    FROM Families
```

結果は次のようになります。

```json
    [
        "WAKEFIELDFAMILY",
        "ANDERSENFAMILY"
    ]
```

次の例のように文字列を連結することもできます。

```sql
    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families
```

結果は次のようになります。

```json
    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "Seattle,WA"
    }]
```

WHERE 句で文字列関数を使用して、次の例のように結果をフィルター処理することもできます。

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")
```

結果は次のようになります。

```json
    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]
```

### <a name="array-functions"></a>配列関数

次のスカラー関数では、配列入力値に対して演算が実行され、数値、ブール値、または配列値が返されます。 組み込みの配列関数を次の表に示します。

| 使用法 | 説明 |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](sql-api-query-reference.md#bk_array_length) |指定された配列式の要素の数を返します。 |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](sql-api-query-reference.md#bk_array_concat) |2 つ以上の配列値を連結した結果である配列を返します。 |
| [ARRAY_CONTAINS (arr_expr, expr [, bool_expr])](sql-api-query-reference.md#bk_array_contains) |配列に指定された値が含まれているかどうかを示すブール値を返します。 一致が完全か部分的かを指定できます。 |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](sql-api-query-reference.md#bk_array_slice) |配列式の一部を返します。 |

配列関数を使用して、JSON に含まれる配列を操作します。 例として、次のクエリでは、`parents` の 1 人が `Robin Wakefield` であるすべての項目 `id` が返されます。 

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })
```

結果は次のとおりです。

```json
    [{
      "id": "WakefieldFamily"
    }]
```

配列内の要素を照合する部分的なフラグメントを指定できます。 次のクエリでは、`givenName` が `Robin` の `parents` を持つすべての項目 `id` が検索されます。

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)
```

結果は次のとおりです。

```json
    [{
      "id": "WakefieldFamily"
    }]
```

ARRAY_LENGTH を使用して、家族あたりの `children` の数を取得するもう 1 つの例を次に示します。

```sql
    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 
```

結果は次のようになります。

```json
    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]
```

### <a name="spatial-functions"></a>空間関数

Cosmos DB は、以下の Open Geospatial Consortium (OGC) 組み込み関数を使った地理空間検索をサポートしています。 

| 使用法 | 説明 |
| --- | --- |
| ST_DISTANCE (point_expr, point_expr) | 2 つの GeoJSON `Point`、`Polygon`、または `LineString` 式間の距離を返します。 |
| T_WITHIN (point_expr, polygon_expr) | 1 つ目の GeoJSON オブジェクト (`Point`、`Polygon`、`LineString`) が 2 つ目の GeoJSON オブジェクト (`Point`、`Polygon`、または `LineString`) 内に存在するかどうかを示すブール式を返します。 |
| ST_INTERSECTS (spatial_expr, spatial_expr) | 指定された 2 つの GeoJSON オブジェクト (`Point`、`Polygon`、または `LineString`) が重なるかどうかを示すブール式を返します。 |
| ST_ISVALID | 指定された GeoJSON `Point`、`Polygon`、または `LineString` 式が有効かどうかを示すブール値を返します。 |
| ST_ISVALIDDETAILED | 指定された GeoJSON `Point`、`Polygon`、または `LineString` 式が有効であるかどうかのブール値を含んだ JSON 値を返します。無効である場合は、その理由が文字列値として返されます。 |

空間関数を使用して、空間データに対して近接検索クエリを実行することができます。 例として、ST_DISTANCE 組み込み関数の使用によって、指定された場所の 30 km 圏内に存在するすべての家族の項目が返されるクエリを以下に示します。

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

結果は次のとおりです。

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Cosmos DB での地理空間のサポートの詳細については、[Azure Cosmos DB での地理空間データの操作](geospatial.md)に関するページを参照してください。 

## <a name="parameterized-queries"></a>パラメーター化されたクエリ

Cosmos DB では、使い慣れた @ 表記で表されたパラメーターを使用するクエリがサポートされます。 パラメーター化された SQL により、ユーザーの入力を堅牢に処理し、流用して、SQL インジェクションによってデータが誤って開示されるリスクを回避することができます。

たとえば、パラメーターとして `lastName` と `address.state` を使用するクエリを記述し、ユーザーの入力に基づいて、`lastName` と `address.state` にさまざまな値を指定して実行できます。

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

次に、この要求は、次のように、パラメーター化された JSON クエリとして Cosmos DB に送信できます。

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

次の例では、パラメーター化されたクエリで TOP 引数を設定しています。 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

パラメーターの値には、有効な任意の JSON (文字列、数値、ブール値、null、配列や入れ子になった JSON も含む) を指定できます。 Cosmos DB はスキーマレスであるため、パラメーターはどの型に対しても検証されません。

## <a id="JavaScriptIntegration"></a>JavaScript 統合

Azure Cosmos DB が提供するプログラミング モデルでは、ストアド プロシージャとトリガーを使用して、JavaScript ベースのアプリケーション ロジックをコンテナーで直接実行することができます。 このモデルは次をサポートします。

* データベース エンジン内部での JavaScript ランタイムの緊密な統合による、コンテナー内の項目に対する高パフォーマンスなトランザクション CRUD 操作とクエリ。
* 制御フロー、変数のスコープ設定、例外処理プリミティブとデータベース トランザクションの割り当てと統合の自然なモデリング。 

Azure Cosmos DB JavaScript 統合の詳細については、「[JavaScript のサーバー側 API](#JavaScriptServerSideApi)」を参照してください。

### <a name="operator-evaluation"></a>演算子の評価

JSON データベースという特性を持つ Cosmos DB は、JavaScript 演算子と評価セマンティクスに似た関係を備えています。 Cosmos DB は JSON サポートという点で JavaScript のセマンティクスを保持しようとしますが、演算子の評価は場合によって異なります。

従来の SQL とは異なり、SQL API では多くの場合に、API によって値がデータベースから取得されるまで、値の型は不明になります。 クエリの実行を効率化するため、大部分の演算子には厳密な型の要件があります。

JavaScript とは異なり、SQL API は暗黙的な変換を実行しません。 たとえば、`SELECT * FROM Person p WHERE p.Age = 21` のようなクエリは、値が `21` である `Age` プロパティが含まれている項目に一致します。 `Age` プロパティが `twenty-one`、`021`、または `21.0` のような潜在的に無限のバリエーションに一致するその他の項目には一致しません。 これは、`==` などの演算子に基づいて、文字列の値が暗黙的に数値にキャストされる JavaScript とは異なります。 この SQL API の動作は、インデックスを効率的に照合するために避けられないものとなっています。

## <a id="ExecutingSqlQueries"></a>SQL クエリの実行

HTTP/HTTPS 要求機能を持つ任意の言語で Cosmos DB REST API を呼び出すことができます。 さらに、.NET、Node.js、JavaScript、Python プログラミング言語用のプログラミング ライブラリも Cosmos DB に用意されています。 REST API とライブラリはすべて SQL 経由のクエリをサポートしており、NET SDK は [LINQ クエリ](#Linq)もサポートしています。

以下の例では、クエリを作成して Cosmos DB データベース アカウントに送信する方法について説明します。

### <a id="RestAPI"></a>REST API

Cosmos DB は、HTTP を介したオープンな RESTful プログラミング モデルを提供します。 リソース モデルは、Azure サブスクリプションがプロビジョニングする、データベース アカウントに従属する一連のリソースから構成されます。 データベース アカウントは一連の*データベース*で構成され、各データベースには複数の*コンテナー*が含まれています。さらにそのそれぞれに、*項目*、UDF、その他のリソースの種類が含まれます。 各 Cosmos DB リソース モデルは、不変の論理 URI を使用してアドレス指定できます。 一連のリソースは、*フィード*と呼ばれます。 

これらのリソースとの基本的な対話モデルでは、HTTP の動詞である `GET`、`PUT`、`POST`、および `DELETE` が標準の解釈で使用されます。 `POST` を使用して、新しいリソースの作成、ストアド プロシージャの実行、または Cosmos DB クエリの発行を行います。 クエリは常に読み取り専用の操作で、副作用はありません。

以下の例は、サンプル項目に対する SQL API クエリの `POST` を示しています。 このクエリには、JSON の `name` プロパティに対するシンプルなフィルターがあります。 `x-ms-documentdb-isquery` と Content-Type: `application/query+json` ヘッダーは、クエリによる操作であることを示しています。 `mysqlapicosmosdb.documents.azure.com:443` を Cosmos DB アカウントの URI に置き換えます。

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

結果は次のようになります。

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

次のもっと複雑なクエリは、結合から複数の結果を返します。

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": [] 
    }
```

結果は次のようになります。 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

クエリ結果を 1 つのページに収めることができない場合、REST API は `x-ms-continuation-token` 応答ヘッダーを使って継続トークンを返します。 クライアントは、このヘッダーを後続の結果に含めることで、結果を改ページすることができます。 ページあたりの結果の数も、`x-ms-max-item-count` 数値ヘッダーによって制御できます。 

クエリに COUNT などの集計関数が含まれる場合、クエリ ページは、結果の 1 ページのみに対する部分的な集計値を返すことがあります。 クライアントが、最終的な結果を得るためには、これらの結果に対して二次的な集計を実行する必要があります。 たとえば、個々のページで返された数を集計して合計数を返します。

クエリのデータ一貫性ポリシーを管理するには、すべての REST API 要求と同様に `x-ms-consistency-level` ヘッダーを使用します。 セッションの一貫性には、クエリ要求で最新の `x-ms-session-token` Cookie ヘッダーもエコーする必要があります。 クエリされたコンテナーのインデックス作成ポリシーは、クエリ結果の一貫性にも影響を与えます。 コンテナーの既定のインデックス作成ポリシーの設定では、インデックスは項目の内容に関して常に最新の状態になり、クエリ結果はデータ用に選択された一貫性と一致します。 詳細については、[Azure Cosmos DB の一貫性レベル][consistency-levels]に関する記事をご覧ください。

コンテナーで構成されたインデックス作成ポリシーが指定されたクエリをサポートできない場合、Azure Cosmos DB サーバーによって 400 "Bad Request" が返されます。 このエラー メッセージは、インデックス作成から明示的に除外されたパスが含まれているクエリに対して返されます。 `x-ms-documentdb-query-enable-scan` ヘッダーを指定して、インデックスを利用できない場合のクエリによるスキャン実行を許可することができます。

`x-ms-documentdb-populatequerymetrics` ヘッダーを `true` に設定することによって、クエリ実行についての詳細なメトリックを取得できます。 詳細については、[Azure Cosmos DB の SQL クエリ メトリック](sql-api-query-metrics.md)に関するページをご覧ください。

### <a id="DotNetSdk"></a>C# (.NET SDK)

.NET SDK は LINQ クエリと SQL クエリの両方をサポートしています。 次の例は、.NET で前述したフィルター クエリを実行する方法を示しています。

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

次の例では、2 つのプロパティの等値比較を各項目内で実行し、匿名プロジェクションを使用しています。

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

次の例は、LINQ `SelectMany` によって表された結合を示しています。

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

前の例に示したとおり、.NET クライアントは `foreach` ブロックのクエリ結果のすべてのページを自動で反復処理します。 [REST API](#RestAPI) のセクションで説明したクエリ オプションは、.NET SDK でも利用可能です。これには、`FeedOptions` および `FeedResponse` クラスを `CreateDocumentQuery` メソッドで使用します。 ページの数は `MaxItemCount` 設定を使用して制御できます。

開発者は、`IDocumentQueryable` を作成することでページ設定を明示的に制御できます。これには `IQueryable` オブジェクトを使用し、次に `ResponseContinuationToken` の値を読み取り、これらを `RequestContinuationToken` として `FeedOptions` 内で渡します。 `EnableScanInQuery` を設定して、構成されたインデックス作成ポリシーでクエリがサポートされない場合に、スキャンを有効にすることができます。 パーティション分割コンテナーの場合は、`PartitionKey` を使用すると 1 つのパーティションに対してクエリを実行できますが、Azure Cosmos DB ではクエリ テキストからこれを自動的に抽出できます。 `EnableCrossPartitionQuery` を使用して、複数のパーティションに対してクエリを実行できます。

クエリを含む他の .NET サンプルについては、GitHub の [Azure Cosmos DB .NET サンプル](https://github.com/Azure/azure-cosmosdb-dotnet)を参照してください。

### <a id="JavaScriptServerSideApi"></a>JavaScript のサーバー側 API

Cosmos DB が提供するプログラミング モデルでは、ストアド プロシージャとトリガーを使用して、JavaScript ベースのアプリケーション ロジックをコンテナーで直接実行することができます。 コンテナー レベルで登録された JavaScript ロジックは、アンビエント ACID トランザクションでラップされた特定のコンテナーの項目に対してデータベース操作を発行できます。

以下の例は、JavaScript サーバー API で `queryDocuments` を使用して、ストアド プロシージャとトリガー内からクエリを実行する方法を示しています。

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a id="Linq"></a>LINQ to SQL API

LINQ は、計算処理をオブジェクトのストリームに対するクエリとして表現する .NET プログラミング モデルです。 Cosmos DB は LINQ と連携するためのクライアント側ライブラリを提供しています。ここでは、JSON オブジェクトと .NET オブジェクト間の変換と、LINQ クエリのサブセットから Cosmos DB クエリへのマッピングを実施します。

以下の図に、Cosmos DB を使用した LINQ クエリのサポートのアーキテクチャを示します。 Cosmos DB クライアントを使用して、Cosmos DB クエリ プロバイダーを直接クエリし、LINQ クエリを Cosmos DB クエリに変換する `IQueryable` オブジェクトを作成できます。 次にクエリを Cosmos DB サーバーに渡します。これは、一連の結果を JSON 形式で取得します。 JSON デシリアライザーは、クライアント側で .NET オブジェクトのストリームに結果を変換します。

![SQL API を使用する LINQ クエリをサポートするアーキテクチャ - SQL 構文、JSON クエリ言語、データベースの概念と SQL クエリ][1]

### <a name="net-and-json-mapping"></a>.NET と JSON のマッピング

.NET オブジェクトと JSON 項目間のマッピングは自然です。 データ メンバーの各フィールドは JSON オブジェクトにマッピングされます。ここで、フィールド名はオブジェクトの*キー*部分にマッピングされ、値は再帰的にオブジェクトの*値*部分にマッピングされます。 次のコードは、`Family` クラスを JSON 項目にマッピングし、次に `Family` オブジェクトを作成します。

```csharp
    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };

    public struct Parent
    {
        public string familyName;
        public string givenName;
    };

    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };

    public class Pet
    {
        public string givenName;
    };

    public class Address
    {
        public string state;
        public string county;
        public string city;
    };

    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };
```

前の例では、次の JSON 項目を作成しています。

```json
    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                "givenName": "Jesse",
                "gender": "female",
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller",
              "givenName": "Lisa",
              "gender": "female",
              "grade": 8
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };
```

### <a name="linq-to-sql-translation"></a>LINQ から SQL への変換

Cosmos DB クエリ プロバイダーは、LINQ クエリから Cosmos DB SQL クエリへのマッピングをベスト エフォートで実行します。 以下の説明では、LINQ の基本的知識を前提としています。

クエリ プロバイダーの型システムでは、JSON プリミティブ型 (数値型、ブール値、文字列、null) のみがサポートされます。 

クエリ プロバイダーでは、以下のスカラー式がサポートされます。

- 定数値。クエリ評価時のプリミティブ データ型の定数値を含みます。
  
- プロパティ/配列インデックス式。オブジェクトまたは配列要素のプロパティを参照します。 例: 
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- 算術式。数値およびブール値に対する共通の算術式を含みます。 完全な一覧については、[Azure Cosmos DB SQL の仕様](https://go.microsoft.com/fwlink/p/?LinkID=510612)に関するページを参照してください。
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- 文字列比較式。これは、文字列値と定数文字列値との比較を含みます。  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- オブジェクト/配列作成式。複合値の型または匿名型のオブジェクト、またはこうしたオブジェクトの配列を返します。 これらの値は入れ子にすることができます。
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

### <a id="SupportedLinqOperators"></a>サポートされている LINQ 演算子

SQL .NET SDK に含まれる LINQ プロバイダーでは、次の演算子がサポートされています。

- **Select**:オブジェクトの構築など、プロジェクションによって SQL SELECT に変換します。
- **Where**:フィルターによって SQL WHERE に変換します。また、`&&`、`||`、および `!` から SQL 演算子への変換をサポートしています
- **SelectMany**:SQL JOIN 句に対して配列をアンワインドできます。 配列要素に関してフィルターする式を連結または入れ子にするために使用します。
- **OrderBy** と **OrderByDescending**:ASC または DESC で ORDER BY に変換します。
- 集計のための **Count**、**Sum**、**Min**、**Max**、**Average** 演算子と非同期でそれに相当する **CountAsync**、**SumAsync**、**MinAsync**、**MaxAsync**、**AverageAsync** 演算子。
- **CompareTo**:範囲比較に変換します。 .NET では文字列を比較できないので、一般的に文字列に使用されます。
- **Take**:クエリからの結果を制限するために SQL TOP に変換します。
- **数学関数**:.NET `Abs`、`Acos`、`Asin`、`Atan`、`Ceiling`、`Cos`、`Exp`、`Floor`、`Log`、`Log10`、`Pow`、`Round`、`Sign`、`Sin`、`Sqrt`、`Tan`、および `Truncate` から同等の SQL 組み込み関数への変換をサポートします。
- **文字列関数**:.NET `Concat`、`Contains`、`Count`、`EndsWith`、`IndexOf`、`Replace`、`Reverse`、`StartsWith`、`SubString`、`ToLower`、`ToUpper`、`TrimEnd`、および `TrimStart` から同等の SQL 組み込み関数への変換をサポートします。
- **配列関数**:.NET `Concat`、`Contains`、および `Count` から同等の SQL 組み込み関数への変換をサポートします。
- **地理空間の拡張関数**:スタブ メソッド `Distance`、`IsValid`、`IsValidDetailed`、および `Within` から同等の SQL 組み込み関数への変換をサポートします。
- **ユーザー定義関数の拡張関数**:スタブ メソッドの `UserDefinedFunctionProvider.Invoke` から、対応するユーザー定義関数への変換をサポートします。
- **その他**:`Coalesce` 演算子と条件演算子の変換をサポートします。 コンテキストに応じて、`Contains` から、文字列 CONTAINS、ARRAY_CONTAINS、または SQL IN に変換できます。

### <a name="sql-query-operators"></a>SQL クエリ演算子

一部の標準 LINQ クエリ演算子が Cosmos DB クエリにどのように変換されるかを以下の例で示します。

#### <a name="select-operator"></a>Select 演算子

構文は `input.Select(x => f(x))` です。`f` はスカラー式です。

**Select 演算子、例 1:**

- **LINQ ラムダ式**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Select 演算子、例 2:** 

- **LINQ ラムダ式**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Select 演算子、例 3:**

- **LINQ ラムダ式**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

#### <a name="selectmany-operator"></a>SelectMany 演算子

構文は `input.SelectMany(x => f(x))` です。`f` はコンテナー型を返すスカラー式です。

- **LINQ ラムダ式**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

#### <a name="where-operator"></a>Where 演算子

構文は `input.Where(x => f(x))` です。`f` は Boolean 値を返すスカラー式です。

**Where 演算子、例 1:**

- **LINQ ラムダ式**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Where 演算子、例 2:**

- **LINQ ラムダ式**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

### <a name="composite-sql-queries"></a>複合 SQL クエリ

上記の演算子を組み合わせて、より強力なクエリを作成できます。 Cosmos DB では入れ子になったコンテナーがサポートされるため、複合を連結または入れ子にできます。

#### <a name="concatenation"></a>連結

構文は `input(.|.SelectMany())(.Select()|.Where())*`です。 連結クエリは、オプションの `SelectMany` クエリで開始し、複数の `Select` または `Where` 演算子を追加できます。

**連結、例 1:**

- **LINQ ラムダ式**
  
  ```csharp
      input.Select(family=>family.parents[0])
          .Where(familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**連結、例 2:**

- **LINQ ラムダ式**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**連結、例 3:**

- **LINQ ラムダ式**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**連結、例 4:**

- **LINQ ラムダ式**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

#### <a name="nesting"></a>入れ子

構文は `input.SelectMany(x=>x.Q())` です。`Q` は `Select`、`SelectMany`、または `Where` 演算子です。

入れ子になったクエリは、内側のクエリを外側のコンテナーの各要素に適用します。 1 つの重要な機能として、内側のクエリは外側のコンテナーの要素のフィールドを自己結合のように参照できます。

**入れ子、例 1:**

- **LINQ ラムダ式**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**入れ子、例 2:**

- **LINQ ラムダ式**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**入れ子、例 3:**

- **LINQ ラムダ式**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```

## <a id="References"></a>参考資料

- [Azure Cosmos DB SQL の仕様](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [Javascript 仕様](https://www.ecma-international.org/publications/standards/Ecma-262.htm) 
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 
- Graefe, Goetz 「[Query evaluation techniques for large databases](https://dl.acm.org/citation.cfm?id=152611)」 *ACM Computing Surveys* 25、no. 2 (1993)
- Graefe, G.「The Cascades framework for query optimization」 *IEEE Data Eng.Bull.* 18, no. 3 (1995)
- Lu、Ooi、Tan 「Query Processing in Parallel Relational Database Systems」 *IEEE Computer Society Press* (1994)
- Olston、Christopher、Benjamin Reed、Utkarsh Srivastava、Ravi Kumar、Andrew Tomkins 「Pig Latin:A Not-So-Foreign Language for Data Processing」 *SIGMOD* (2008)

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の概要][introduction]
- [Azure Cosmos DB .NET のサンプル](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Azure Cosmos DB の一貫性レベル][consistency-levels]

[1]: ./media/how-to-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
