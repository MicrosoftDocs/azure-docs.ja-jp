---
title: "Azure Cosmos DB DocumentDB API: SQL 構文 | Microsoft Docs"
description: "Azure Cosmos DB DocumentDB API SQL クエリ言語のリファレンス ドキュメント。"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/13/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: 63b2d20c74df4fd6173994ee1a727594ba8afba3
ms.contentlocale: ja-jp
ms.lasthandoff: 08/17/2017

---

# <a name="azure-cosmos-db-documentdb-api-sql-syntax-reference"></a>Azure Cosmos DB DocumentDB API: SQL 構文リファレンス

Azure Cosmos DB DocumentDB API は、明示的なスキーマまたはセカンダリ インデックスの作成を必要とせずに、階層型 JSON ドキュメントに対する文法などの使い慣れた SQL (構造化照会言語) を使用したドキュメンのクエリ実行をサポートします。 このトピックでは、DocumentDB API SQL クエリ言語のリファレンス ドキュメントを示します。

DocumentDB API SQL クエリ言語のチュートリアルについては、「[SQL queries for Azure Cosmos DB DocumentDB API](documentdb-sql-query.md)」(Azure Cosmos DB DocumentDB API 用の SQL クエリ) を参照してください。  
  
さらに、Azure Cosmos DB を試したり、データセットに対して SQL クエリ実行したりできる「[Query Playground](http://www.documentdb.com/sql/demo)」にアクセスすることもお勧めします。  
  
## <a name="select-query"></a>SELECT クエリ  
データベースから JSON ドキュメントを取得します。 式の評価、プロジェクション、フィルター処理、結合をサポートします。  SELECT ステートメントを記述するために使用される規則については、「構文表記規則」セクションの表を参照してください。  
  
**構文**  
  
```
<select_query> ::=  
SELECT <select_specification>   
    [ FROM <from_specification>]   
    [ WHERE <filter_condition> ]  
    [ ORDER BY <sort_specification> ]  
```  
  
 **解説**  
  
 各句の詳細については、以下のセクションを参照してください。  
  
-   [SELECT 句](#bk_select_query)  
  
-   [FROM 句](#bk_from_clause)  
  
-   [WHERE 句](#bk_where_clause)  
  
-   [ORDER BY 句](#bk_orderby_clause)  
  
SELECT ステートメント内の句は、上の順序で並べられている必要があります。 オプションの句はどれも省略できます。 オプションの句を使用している場合、正しい順序で現れる必要があります。  
  
**SELECT ステートメントの論理的な処理順序**  
  
句は次の順序で処理されます。  

1.  [FROM 句](#bk_from_clause)  
2.  [WHERE 句](#bk_where_clause)  
3.  [ORDER BY 句](#bk_orderby_clause)  
4.  [SELECT 句](#bk_select_query)  

これは、構文で表示される順序と異なることに注意してください。 処理済みの句によって導入されるすべての新しいシンボルが表示され、後で処理される句で使用できるような順序になっています。 たとえば、FROM 句で宣言されている別名は、WHERE 句と SELECT 句でアクセス可能です。  

**空白文字とコメント**  

引用符または区切られた識別子で囲まれた文字列の一部ではないすべての空白文字、言語の文法の一部ではなく、解析時に無視されます。  

クエリ言語は、T-SQL スタイルのコメントをサポートしています  

-   SQL ステートメント`-- comment text [newline]`  

空白文字とコメントは文法内では何も意味がありませんが、トークンを区切る場合に使用する必要があります。 例: `-1e5` は、1 つの数値のトークンですが、`: – 1 e5` は負のトークンであり、その後に数字 1 と識別子 e5 が続いています。  

##  <a name="bk_select_query"></a>SELECT 句  
SELECT ステートメント内の句は、上の順序で並べられている必要があります。 オプションの句はどれも省略できます。 オプションの句を使用している場合、正しい順序で現れる必要があります。  

**構文**  
```  
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | <object_property_list>   
      | VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
 **引数**  
  
 `<select_specification>`  
  
 結果セットで選択されるプロパティまたは値。  
  
 `'*'`  
  
変更を加えずに値を取得することを指定します。 具体的には、処理された値がオブジェクトである場合、すべてのプロパティが取得されます。  
  
 `<object_property_list>`  
  
取得するプロパティの一覧を指定します。 返される各値は、指定されたプロパティを持つオブジェクトとなります。  
  
`VALUE`  
  
完全な JSON オブジェクトではなく JSON 値を取得することを指定します。 これは、`<property_list>` とは異なり、オブジェクト内の投影された値をラップしません。  
  
`<scalar_expression>`  
  
計算する値を表す式。 詳細については、「[スカラー式](#bk_scalar_expressions)」セクションを参照してください。  
  
**解説**  
  
`SELECT *` 構文は、FROM 句で正確に 1 つの別名が宣言された場合のみ有効です。 `SELECT *` は、プロジェクションが不要な場合に便利な、ID プロジェクションを提供します。 SELECT * は、FROM 句が指定され、1 つの入力ソースのみが導入されている場合にのみ有効です。  
  
`SELECT <select_list>` と `SELECT *` は「糖衣構文」であり、次に示すように単純な SELECT ステートメントを使用して、別の方法で表すことができます。  
  
1.  `SELECT * FROM ... AS from_alias ...`  
  
     は以下に匹敵します。  
  
     `SELECT from_alias FROM ... AS from_alias ...`  
  
2.  `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
     は以下に匹敵します。  
  
     `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
**関連項目**  
  
[スカラー式](#bk_scalar_expressions)  
[SELECT 句](#bk_select_query)  
  
##  <a name="bk_from_clause"></a>FROM 句  
ソースまたは結合されたソースを指定します。 FROM 句はオプションです。 指定されていない場合、FROM 句が 1 つの文書を提供した場合のように、他の句が実行されます。  
  
**構文**  
  
```  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <collection_expression> [[AS] input_alias]  
        | input_alias IN <collection_expression>  
  
<collection_expression> ::=   
        ROOT   
     | collection_name  
     | input_alias  
     | <collection_expression> '.' property_name  
     | <collection_expression> '[' "property_name" | array_index ']'  
```  
  
**引数**  
  
`<from_source>`  
  
別名の有無に関係なくデータソースを指定します。 別名が指定されていない場合、次の規則を使用して、`<collection_expression>` から推論されます。  
  
-   式が collection_name である場合は、collection_name が別名として使用されます。  
  
-   式が `<collection_expression>` である場合は、property_name が別名として使用されます。 式が collection_name である場合は、collection_name が別名として使用されます。  
  
AS `input_alias`  
  
`input_alias` が基になるコレクション式によって返される値のセットであることを指定します。  
 
`input_alias` IN  
  
`input_alias` が基になるコレクション式によって返される各配列のすべての配列要素を反復処理して取得した値のセットを表すことを指定します。 配列ではない基になるコレクション式によって返される値はすべて無視されます。  
  
`<collection_expression>`  
  
ドキュメントを取得するために使用するコレクション式を指定します。  
  
`ROOT`  
  
既定の現在接続しているコレクションからそのドキュメントを取得する必要があることを指定します。  
  
`collection_name`  
  
提供されたコレクションからそのドキュメントを取得する必要があることを指定します。 コレクションの名前は、現在接続しているコレクションの名前と一致する必要があります。  
  
`input_alias`  
  
提供された別名によって定義された他のソースからそのドキュメントを取得する必要があることを指定します。  
  
`<collection_expression> '.' property_`  
  
指定されたコレクション式によって取得されるすべてのドキュメントの `property_name` プロパティまたは array_index アレイ要素にアクセスすることによってそのドキュメントを取得する必要があることを指定します。  
  
`<collection_expression> '[' "property_name" | array_index ']'`  
  
指定されたコレクション式によって取得されるすべてのドキュメントの `property_name` プロパティまたは array_index アレイ要素にアクセスすることによってそのドキュメントを取得する必要があることを指定します。  
  
**解説**  
  
指定されるか `<from_source>(` で推測されたすべての別名は一意である必要があります。 構文 `<collection_expression>.`property_name は `<collection_expression>' ['"property_name"']'` と同じです。 ただし、プロパティ名に非識別子文字が含まれている場合は、後者の構文を使用できます。  
  
**見つからないプロパティ、見つからない配列要素、未定義の値の処理**  
  
コレクション式がプロパティまたは配列要素にアクセスし、その値が存在しない場合、その値は無視され、処理は行われません。  
  
**コレクション式のコンテキストのスコープ**  
  
コレクション式には、コレクション スコープまたはドキュメント スコープがあります。  
  
-   基になるコレクション式のスコープが ROOT または `collection_name` である場合、式はコレクション スコープです。 このような式は、直接コレクションから取得されたドキュメントのセットを表し、その他のコレクションの式の処理に依存しません。  
  
-   基になるコレクション式が、クエリで前に導入された `input_alias` である場合、式はドキュメント スコープです。 このような式は、別名を付けられたコレクションに関連付けられているセットに属する各ドキュメントのスコープ内で、コレクション式を評価することによって取得されたドキュメントのセットを表します。  結果セットは、基になるセット内の各ドキュメントのコレクション式を評価することによって取得されたセットの和集合になります。  
  
**結合**  
  
現在のリリースでは、Azure Cosmos DB は、内部結合をサポートします。 追加の結合機能は近日提供予定です。

内部結合の結果は、結合に含まれるセットの完全なクロス積になります。 N-way 結合の結果は、N 要素タプルのセットであり、タプル内の各値が結合に含まれている別名セットに関連付けられていて、他の句でその別名を参照してアクセスできます。  
  
結合の評価は、参加しているセットのコンテキストのスコープによって異なります。  
  
-  コレクション セット A とコレクション スコープ セット B の間の結合の結果は、A と B のセットのすべての要素のクロス積になります。
  
-   セット A とドキュメント スコープ セット B の間の結合の結果は、セット Aの各ドキュメントの スコープ セット B を評価することによって取得されたすべてのセットの和集合になります。  
  
 現在のリリースでは、最大で 1 つのコレクション スコープ式が、クエリ プロセッサによってサポートされています。  
  
**結合例:**  
  
それでは、以下の FROM 句を見てみましょう。`<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 各ソースで `input_alias1, input_alias2, …, input_aliasN` を定義します。 この FROM 句は、N-タプル (N 個の値を持つタプル) のセットを返します。 それぞれのタプルは、対応するセットに対する、すべてのコレクションのエイリアスの反復によって生成された値を持ちます。  
  
*結合の例 1、2 つのソースを含む:*  
  
- `<from_source1>` をコレクション スコープにし、セット {A, B, C} を表します。  
  
- `<from_source2>` を input_alias1 を参照するドキュメント スコープにし、次のセットを表します。  
  
    `input_alias1 = A,` の {1, 2}  
  
    `input_alias1 = B,` の {3}   
  
    `input_alias1 = C,` の {4, 5}  
  
- FROM 句 `<from_source1> JOIN <from_source2>` の結果は次のタプルになります。  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
*結合の例 2、3 つのソースを含む:*  
  
- `<from_source1>` をコレクション スコープにし、セット {A, B, C} を表します。  
  
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
  
*結合の例 3、3 つのソース含む:*  
  
- <from_source1> をコレクション スコープにし、セット {A, B, C} を表します。  
  
- `<from_source1>` をコレクション スコープにし、セット {A, B, C} を表します。  
  
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
  
**関連項目**  
  
 [SELECT 句](#bk_select_query)  
  
##  <a name="bk_where_clause"></a>WHERE 句  
 クエリによって返されるドキュメントの検索条件を指定します。  
  
 **構文**  
  
```  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
 **引数**  
  
-   `<filter_condition>`  
  
     返されるドキュメントが満たす条件を指定します。  
  
-   `<scalar_expression>`  
  
     計算する値を表す式。 詳細については、「[スカラー式](#bk_scalar_expressions)」セクションを参照してください。  
  
 **解説**  
  
 ドキュメントが返されるには、フィルター条件として指定された式が true に評価される必要があります。 ブール値 true のみがその条件を満たし、他の値 (undefined、null、false、数字、配列、またはオブジェクト) は条件を満たしません。  
  
##  <a name="bk_orderby_clause"></a>ORDER BY 句  
 クエリによって返される結果の並べ替え順序を指定します。  
  
 **構文**  
  
```  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= <scalar_expression> [ASC | DESC]  
  
```  
  
 **引数**  
  
-   `<sort_specification>`  
  
     クエリの結果セットの並べ替えの条件にするプロパティまたは式を指定します。 並べ替え列は、名前または列の別名として指定できます。  
  
     複数の並べ替え列を指定することができます。 列名は一意である必要があります。 ORDER BY 句の並べ替え列の順序は、並べ替えられた結果セットの構成を定義します。 つまり、結果セットは最初のプロパティで並べ替えられ、その並べ替えられたリストが 2 番目のプロパティで並べ替えられ、以下同様に続きます。  
  
     ORDER BY 句で参照される列名は、選択リスト内の列にまたはあいまいさのない FROM 句で指定されたテーブルで定義されている列に対応する必要があります。  
  
-   `<sort_expression>`  
  
     クエリの結果セットの並べ替えの条件にする 1 つのプロパティまたは式を指定します。  
  
-   `<scalar_expression>`  
  
     詳細については、「[スカラー式](#bk_scalar_expressions)」セクションを参照してください。  
  
-   `ASC | DESC`  
  
     指定された列の値を昇順または降順で並べ替える必要があることを指定します。 ASC は、最小値から最大値に並べ替えられます。 DESC は、最大値から最小値に並べ替えられます。 既定の並べ替え順は ASC です。 NULL 値は、有効な最小値として扱われます。  
  
 **解説**  
  
 クエリ文法では、複数のプロパティにる並べ替えがサポートされますが、Azure Cosmos DB クエリ ランタイムでは、1 つのプロパティに対する並べ替えのみがサポートされ、プロパティ名のみが対象となります。つまり、計算されるプロパティは対象になりません。 並べ替えではまた、インデックス作成ポリシーが、プロパティの範囲インデックス、指定された型、および最大有効桁数を含んでいる必要があります。 詳細については、インデックス作成ポリシーに関するドキュメントを参照してください。  
  
##  <a name="bk_scalar_expressions"></a>スカラー式  
 スカラー式は、1 つの値を取得するために評価できるシンボルと演算子の組み合わせです。 単純式には、定数、プロパティの参照、配列要素の参照、別名の参照、または関数の呼び出しを指定できます。 単純式は、演算子を使用して複雑な式に結合できます。  
  
 スカラー式で使用できる値の詳細については、「[定数](#bk_constants)」セクションを参照してください。  
  
 **構文**  
  
```  
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
  
 **引数**  
  
-   `<constant>`  
  
     定数値を表します。 詳細については、「[定数](#bk_constants)」セクションを参照してください。  
  
-   `input_alias`  
  
     `FROM` 句によって導入された `input_alias` で定義された値を表します。  
    この値は、**undefined** ではないことが保証されます。入力内の **undefined** 値はスキップされます。  
  
-   `<scalar_expression>.property_name`  
  
     オブジェクトのプロパティの値を表します。 プロパティが存在しない場合、またはプロパティがオブジェクトではない値で参照されている場合、式は **undefined** 値として評価されます。  
  
-   `<scalar_expression>'['"property_name"|array_index']'`  
  
     名前 `property_name` を持つプロパティの値、またはオブジェクト/配列のインデックス `array_index` を持つ配列要素を表します。 プロパティ/配列インデックスが存在しない場合、またはプロパティ/配列インデックスがオブジェクト/配列ではない値で参照されている場合、式は undefined 値として評価されます。  
  
-   `unary_operator <scalar_expression>`  
  
     1 つの値に適用される演算子を表します。 詳細については、「[演算子](#bk_operators)」セクションを参照してください。  
  
-   `<scalar_expression> binary_operator <scalar_expression>`  
  
     2 つの値に適用される演算子を表します。 詳細については、「[演算子](#bk_operators)」セクションを参照してください。  
  
-   `<scalar_function_expression>`  
  
     関数呼び出しの結果によって定義された値を表します。  
  
-   `udf_scalar_function`  
  
     ユーザー定義のスカラー関数の名前です。  
  
-   `builtin_scalar_function`  
  
     組み込みのスカラー関数の名前です。  
  
-   `<create_object_expression>`  
  
     指定したプロパティとその値で新しいオブジェクトを作成することによって取得される値を表します。  
  
-   `<create_array_expression>`  
  
     指定した値を要素として新しい配列を作成することによって取得される値を表します。  
  
-   `parameter_name`  
  
     指定したパラメーターの値を表します。 パラメーター名は、最初の文字として 1 つの @ を使用する必要があります。  
  
 **解説**  
  
 組み込みまたはユーザー定義のスカラー関数を呼び出すときには、すべての引数を定義する必要があります。 引数のいずれかが定義されていない場合、関数は呼び出されず、結果は未定義になります。  
  
 オブジェクトを作成するときに、未定義の値が割り当てられているプロパティはすべてスキップされ、作成されたオブジェクトに含まれません。  
  
 配列を作成するときに、**undefined** 値が割り当てられている要素値はすべてスキップされ、作成されたオブジェクトに含まれません。 この場合、作成される配列にスキップされたインデックスが含まれないようにするために、次の定義済みの要素が代わりに使用されます。  
  
##  <a name="bk_operators"></a>演算子  
 このセクションでは、サポートされている演算子について説明します。 各演算子は、正確に 1 つのカテゴリに割り当てることができます。  
  
 **undefined**値の処理の詳細、入力値の型の要件、一致する方がない値の処理詳細については、下の**演算子のカテゴリ**の表を参照してください。  
  
 **演算子のカテゴリ:**  
  
|**カテゴリ**|**詳細**|  
|-|-|  
|**算術**|演算子には、数値の入力が必要です。 出力も数値です。 入力のいずれかが **undefined** か数値以外の型である場合、結果は **undefined** です。|  
|**ビット演算子**|演算子には、32 ビット符号付き整数の入力が必要です。 出力も、32 ビット符号付き整数です。<br /><br /> 任意の整数以外の値は丸められます。 正の値は切り捨てられます、負の値は切り上げられます。<br /><br /> 32 ビット整数範囲の外部にある値は、その 2 の補数表記の最後の 32 ビットを取ることによって変換されます。<br /><br /> 入力のいずれかが **undefined** か数値以外の型である場合、結果は **undefined** です。<br /><br /> **注:** 上記の動作は JavaScript のビットごとの演算子の動作と互換性があります。|  
|**論理**|演算子には、ブール値の入力が必要です。 出力もブール値です。<br />入力のいずれかが **undefined** かブール値以外の型である場合、結果は **undefined** です。|  
|**比較**|演算子には、同じ型の undefined ではない入力が必要です。 出力はブール値です。<br /><br /> 入力のいずれかが **undefined** か異なる型である場合、結果は **undefined** です。<br /><br /> 値の順序付けの詳細については、**比較の値の順序付けの表**を参照してください。|  
|**string**|演算子には、文字列の入力が必要です。 出力も文字列です。<br />入力のいずれかが **undefined** か文字列以外の型である場合、結果は **undefined** です。|  
  
 **単項演算子**  
  
|**名前**|**演算子**|**詳細**|  
|-|-|-|  
|**算術**|+<br /><br /> -|数値を返します。<br /><br /> ビットごとの否定。 否定された数値を返します。|  
|**ビット演算子**|~|1 の補数。 数値の補数を返します。|  
|**論理**|**NOT**|否定します。 否定されたブール値を返します。|  
  
 **二項演算子:**  
  
|**名前**|**演算子**|**詳細**|  
|-|-|-|  
|**算術**|+<br /><br /> -<br /><br /> *<br /><br /> /<br /><br /> %|加算。<br /><br /> 減算。<br /><br /> 乗算。<br /><br /> 除算。<br /><br /> 比率。|  
|**ビット演算子**|&#124;<br /><br /> &<br /><br /> ^<br /><br /> <<<br /><br /> >><br /><br /> >>>|ビット演算子 OR。<br /><br /> ビット演算子 AND。<br /><br /> ビット演算子 XOR。<br /><br /> 左シフト。<br /><br /> 右シフト。<br /><br /> 0 埋め右シフト。|  
|**論理**|**AND**<br /><br /> **OR**|論理積。 両方の引数が **true** である場合、**true** を返します。それ以外の場合は **false** を返します。<br /><br /> 論理積。 両方の引数が **true** である場合、**true** を返します。それ以外の場合は **false** を返します。|  
|**比較**|**=**<br /><br /> **!=, <>**<br /><br /> **>**<br /><br /> **>=**<br /><br /> **<**<br /><br /> **<=**<br /><br /> **??**|等しい。 両方の引数が等しい場合、**true** を返します。それ以外の場合は **false** を返します。<br /><br /> 等しくない。 両方の引数が等しくない場合、**true** を返します。それ以外の場合は **false** を返します。<br /><br /> より大きい。 1 番目の引数が 2 番目の引数より大きい場合、**true** を返します。それ以外の場合、**false** を返します。<br /><br /> 以上。 1 番目の引数が 2 番目の引数以上の場合、**true** を返します。それ以外の場合、**false** を返します。<br /><br /> より小さい。 1 番目の引数が 2 番目の引数より小さい場合、**true** を返します。それ以外の場合、**false** を返します。<br /><br /> 以下。 1 番目の引数が 2 番目の引数以下の場合、**true** を返します。それ以外の場合、**false** を返します。<br /><br /> 結合。 最初の引数が **undefined** 値である場合、2 番目の引数を返します。|  
|**文字列**|**&#124;&#124;**|連結。 両方の引数の連結を返します。|  
  
 **三項演算子:**  
  
|三項演算子|?|最初の引数が **true** に評価された場合、2 番目の引数を返します。それ以外の場合、3 番目の引数を返します。|  
|-|-|-|  
  
 **比較の値の順序付け**  
  
|**型**|**値の順序**|  
|-|-|  
|**Undefined**|比較できません。|  
|**Null**|単一の値: **Null**|  
|**数値**|実数。<br /><br /> 負の無限大の値は、他のどの数値よりも小さくなります。<br /><br /> 正の無限大の値は、他のどの数値よりも大きくなります。**NaN** 値は比較できません。 **NaN** と比較すると、結果は **undefined** 値になります。|  
|**String**|辞書の順序。|  
|**Array**|順序付けがなく平等に扱われます。|  
|**Object**|順序付けがなく平等に扱われます。|  
  
 **解説**  
  
 Azure Cosmos DB では、多くの場合、値の型は、実際にデータベースから取得されるまで不明です。 効率的なクエリの実行をサポートするため、大部分の演算子には厳密な型の要件があります。 また演算子自体は、暗黙的な変換を行いません。  
  
 つまり、SELECT * FROM ROOT r WHERE r.Age = 21 のようなクエリは、プロパティ Age が数値 21 と等しいドキュメントのみを返します。 プロパティ Age が文字列 "21" または文字列 "0021" と等しいドキュメントは一致しません。これは式 "21" = 21 が undefined と評価されるためです。 特定の値 (つまり、数値 21) の検索は、不特定数の一致候補 (つまり、数値 21 または文字列 "21"、"021"、"21.0" …) の検索よりも高速に実行されるので、これにより、インデックスを効率的に使用できます。 これは、JavaScript でさまざまな種類の値に対する演算子を評価する方法とは異なります。  
  
 **配列とオブジェクトの等値と比較**  
  
 範囲演算子 (>、> =、<、< =) を使用して配列またはオブジェクトの値を比較すると、オブジェクトまたは配列の値には順序が定義されていないので、結果は undefined になります。 ただし、等価/非等価演算子 (=、! =、<>) の使用はサポートされており、値が構造的に比較されます。  
  
 配列は、両方の配列が同じ数の要素を持ち、一致する位置にある要素も等しい場合に等しくなります。 要素のペアの比較の結果が未定義の場合、配列の比較の結果は未定義になります。  
  
 オブジェクトは、両方のオブジェクトに同じプロパティが定義され、一致するプロパティの値も等しい場合に等しくなります。 プロパティのペアの比較の結果が未定義の場合、オブジェクト比較の結果は未定義になります。  
  
##  <a name="bk_constants"></a>定数  
 定数、リテラルまたはスカラー値とも呼ばれますが、特定のデータ値を表すシンボルです。 定数の形式は、それが表す値のデータ型によって異なります。  
  
 **サポートされるスカラー データ型:**  
  
|**型**|**値の順序**|  
|-|-|  
|**Undefined**|単一の値: **undefined**|  
|**Null**|単一の値: **Null**|  
|**Boolean**|値: **false**、**true**。|  
|**数値**|倍精度浮動小数点数、IEEE 754 標準。|  
|**String**|0 個以上の Unicode 文字のシーケンス。 文字列は、一重引用符または二重引用符で囲む必要があります。|  
|**Array**|0 個以上の要素のシーケンス。 各要素には、Undefined を除く、任意のスカラー データ型の値を指定できます。|  
|**Object**|0 個以上の名前/値ペアの順序なしのセット。 名前は Unicode 文字列で、値は **Undefined** 以外の任意のスカラー データ型を指定できます。|  
  
 **構文**  
  
```  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
 **引数**  
  
1.  `<undefined_constant>; undefined`  
  
     Undefined 型の未定義値を表します。  
  
2.  `<null_constant>; null`  
  
     **Null** 型の値 **null** を表します。  
  
3.  `<boolean_constant>`  
  
     Boolean 型の定数を表します。  
  
4.  `false`  
  
     Boolean 型の **false** 値を表します。  
  
5.  `true`  
  
     Boolean 型の **true** 値を表します。  
  
6.  `<number_constant>`  
  
     定数を表します。  
  
7.  `decimal_literal`  
  
     10 進数のリテラルは、10 進表記または指数表記を使用して表現された数値です。  
  
8.  `hexadecimal_literal`  
  
     16 進数のリテラルは、プレフィックス '0 x' の後に 1 つまたは複数の 16 進数字を使用して表現された数値です。  
  
9. `<string_constant>`  
  
     文字列型の定数を表します。  
  
10. `string _literal`  
  
     文字列リテラルは、0 個以上の Unicode 文字のシーケンスまたはエスケープ シーケンスによって表される Unicode 文字列です。 文字列リテラルは、単一引用符 (アポストロフィ: ') または二重引用符 (引用符:") で囲みます。  
  
 次のエスケープ シーケンスを使用できます。  
  
|**エスケープ シーケンス**|**説明**|**Unicode 文字**|  
|-|-|-|  
|\\'|アポストロフィ (')|U+0027|  
|\\"|引用符 (")|U+0022|  
|\\\|逆斜線 (\\)|U+005C|  
|\\/|斜線 (/)|U+002F|  
|\b|バックスペース|U+0008|  
|\f|フォーム フィード|U+000C|  
|\n|ライン フィード|U+000A|  
|\r|キャリッジ リターン|U+000D|  
|\t|タブ|U+0009|  
|\uXXXX|4 つの 16 進数字によって定義された Unicode 文字。|U+XXXX|  
  
##  <a name="bk_query_perf_guidelines"></a> クエリのパフォーマンスに関するガイドライン  
 大規模なコレクションに対してクエリを効率的に実行するには、1 つまたは複数のインデックスで利用できるフィルターを使用してください。  
  
 インデックスの検索では、次のフィルターを検討できます。  
  
-   ドキュメント パス式および定数を含む等値演算子 (=) を使用します。  
  
-   ドキュメント パス式および定数を含む範囲演算子 (<、\<=、>、>=) を使用します。  
  
-   ドキュメント パス式は、参照先のデータベース コレクションからドキュメント内の定数のパスを識別する任意の式を表します。  
  
 **ドキュメント パス式**  
  
 ドキュメント パス式は、データべース コレクション ドキュメントから取得されるドキュメントの配列インデクサー評価機能のプロパティのパスである式です。 このパスは、データベース コレクション内のドキュメント内で直接フィルターで参照されている値の場所を識別するために使用されます。  
  
 式がドキュメント パス式と見なされるには、次の条件を満たしている必要があります。  
  
1.  コレクションのルートを直接参照する。  
  
2.  一部のドキュメント パス式の参照プロパティまたは定数配列のインデクサーを参照している。  
  
3.  一部のドキュメント パス式を表す別名を参照している。  
  
     **構文表記規則**  
  
     次の表では、DocumentDB API クエリ言語リファレンスで構文の説明に使用される規則について説明します。  
  
    |**規則**|**用途**|  
    |-|-|    
    |UPPERCASE|大文字と小文字が区別されないキーワード。|  
    |lowercase|大文字と小文字が区別されるキーワード。|  
    |\<nonterminal>|個別に定義される非終端要素。|  
    |\<nonterminal> ::=|非終端要素の構文の定義。|  
    |other_terminal|ターミナル (トークン)、単語で詳しく説明されます。|  
    |identifier|ID。 a - z、A - Z、0 - 9 の文字のみを使用できます。最初の文字を数字にすることはできません。|  
    |"string"|引用符で囲まれた文字列です。 任意の有効な文字列を使用できます。 string_literal の説明を参照してください。|  
    |'symbol'|構文の一部であるリテラル シンボル。|  
    |&#124; (垂直線)|構文項目の代替。 指定した項目のいずれかのみを使用することができます。|  
    |[ ] /(角かっこ)|角かっこは、1 つまたは複数の省略可能な項目を囲みます。|  
    |[ ,...n ]|先行する項目を n 回繰り返すことができることを示します。 出現箇所は、コンマで区切られます。|  
    |[ ...n ]|先行する項目を n 回繰り返すことができることを示します。 出現箇所は、コンマで区切られます。|  
  
##  <a name="bk_built_in_functions"></a>組み込み関数  
 Azure の Cosmos DB は、多くの組み込み SQL 関数を提供します。 組み込み関数のカテゴリは次のとおりです。  
  
|関数|Description|  
|--------------|-----------------|  
|[数学関数](#bk_mathematical_functions)|一般に、各数学関数は、引数として提供された入力値に基づいて計算を実行し、数値を返します。|  
|[型チェック関数](#bk_type_checking_functions)|型チェック関数を使用すると、SQL クエリ内の式の型をチェックできます。|  
|[文字列関数](#bk_string_functions)|文字列関数は、文字列入力値に対して演算を実行し、文字列、数値またはブール値を返します。|  
|[配列関数](#bk_array_functions)|配列関数は、配列入力値に対して演算を実行し、数値、ブール値、または配列値を返します。|  
|[空間関数](#bk_spatial_functions)|空間関数は、空間オブジェクト入力値に対して演算を実行し、数値またはブール値を返します。|  
  
###  <a name="bk_mathematical_functions"></a>数学関数  
 次の数学関数は、引数として提供された入力値に基づいて計算を実行し、数値を返します。  
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[CEILING](#bk_ceiling)|  
|[COS](#bk_cos)|[COT](#bk_cot)|[DEGREES](#bk_degrees)|  
|[EXP](#bk_exp)|[FLOOR](#bk_floor)|[LOG](#bk_log)|  
|[LOG10](#bk_log10)|[PI](#bk_pi)|[POWER](#bk_power)|  
|[RADIANS](#bk_radians)|[ROUND](#bk_round)|[SIN](#bk_sin)|  
|[SQRT](#bk_sqrt)|[SQUARE](#bk_square)|[SIGN](#bk_sign)|  
|[TAN](#bk_tan)|[TRUNC](#bk_trunc)||  
  
####  <a name="bk_abs"></a> ABS  
 指定された数値式の絶対値 (正の値) を返します。  
  
 **構文**  
  
```  
ABS (<numeric_expression>)  
```  
  
 **引数**  
  
-   `numeric_expression`  
  
     数値式です。  
  
 **戻り値の型**  
  
 数値式を返します。  
  
 **例**  
  
 次の例は、次の 3 つの異なる数値に対して ABS 関数を使用した結果を示しています。  
  
```  
SELECT ABS(-1), ABS(0), ABS(1)  
```  
  
 結果セットは次のようになります。  
  
```  
[{$1: 1, $2: 0, $3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 コサインが指定された数値式となる角度をラジアン単位で返します。アークコサインとも呼ばれます。  
  
 **構文**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **引数**  
  
-   `numeric_expression`  
  
     数値式です。  
  
 **戻り値の型**  
  
 数値式を返します。  
  
 **例**  
  
 次の例は、-1 の ACOS を返します。  
  
```  
SELECT ACOS(-1)  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 サインが指定された数値式となる角度をラジアン単位で返します。 アークサインとも呼ばれます。  
  
 **構文**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **引数**  
  
-   `numeric_expression`  
  
     数値式です。  
  
 **戻り値の型**  
  
 数値式を返します。  
  
 **例**  
  
 次の例は、-1 の ASIN を返します。  
  
```  
SELECT ASIN(-1)  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 タンジェントが指定された数値式となる角度をラジアン単位で返します。 アークタンジェントとも呼ばれます。  
  
 **構文**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **引数**  
  
-   `numeric_expression`  
  
     数値式です。  
  
 **戻り値の型**  
  
 数値式を返します。  
  
 **例**  
  
 次の例では、指定した値の ATAN を返します。  
  
```  
SELECT ATAN(-45.01)  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 ラジアン単位で表される y/x のアーク タンジェンの主値を返します。  
  
 **構文**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **引数**  
  
-   `numeric_expression`  
  
     数値式です。  
  
 **戻り値の型**  
  
 数値式を返します。  
  
 **例**  
  
 次の例は、指定された x と y コンポーネントの ATN2 を計算します。  
  
```  
SELECT ATN2(35.175643, 129.44)  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> CEILING  
 指定された数値式以上の最小の整数値を返します。  
  
 **構文**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **引数**  
  
-   `numeric_expression`  
  
     数値式です。  
  
 **戻り値の型**  
  
 数値式を返します。  
  
 **例**  
  
 次の例では、CEILING 関数を使用して、正の数値、負の数値、およびゼロ値を示します。  
  
```  
SELECT CEILING(123.45), CEILING(-123.45), CEILING(0.0)  
```  
  
 結果セットは次のようになります。  
  
```  
[{$1: 124, $2: -123, $3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 式で指定されたラジアン単位の角度の三角関数コサインを返します。  
  
 **構文**  
  
```  
COS(<numeric_expression>)  
```  
  
 **引数**  
  
-   `numeric_expression`  
  
     数値式です。  
  
 **戻り値の型**  
  
 数値式を返します。  
  
 **例**  
  
 次の例では、指定された角度の COS を計算します。  
  
```  
SELECT COS(14.78)  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 数値式で指定されたラジアン単位の角度の三角関数コタンジェントを返します。  
  
 **構文**  
  
```  
COT(<numeric_expression>)  
```  
  
 **引数**  
  
-   `numeric_expression`  
  
     数値式です。  
  
 **戻り値の型**  
  
 数値式を返します。  
  
 **例**  
  
 次の例では、指定された角度の COT を計算します。  
  
```  
SELECT COT(124.1332)  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> DEGREES  
 ラジアン単位で指定された角度に対応する度数単位の角度を返します。  
  
 **構文**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **引数**  
  
-   `numeric_expression`  
  
     数値式です。  
  
 **戻り値の型**  
  
 数値式を返します。  
  
 **例**  
  
 次の例では、PI/2 ラジアンの角度で度数を返します。  
  
```  
SELECT DEGREES(PI()/2)  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": 90}]  
```  
  
####  <a name="bk_floor"></a> FLOOR  
 指定された数値式未満の最大の整数を返します。  
  
 **構文**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **引数**  
  
-   `numeric_expression`  
  
     数値式です。  
  
 **戻り値の型**  
  
 数値式を返します。  
  
 **例**  
  
 次の例では、FLOOR 関数を使用して、正の数値、負の数値、およびゼロ値を示します。  
  
```  
SELECT FLOOR(123.45), FLOOR(-123.45), FLOOR(0.0)  
```  
  
 結果セットは次のようになります。  
  
```  
[{$1: 123, $2: -124, $3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 指定された数値式の指数値を返します。  
  
 **構文**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **引数**  
  
-   `numeric_expression`  
  
     数値式です。  
  
 **戻り値の型**  
  
 数値式を返します。  
  
 **解説**  
  
 定数 **e** (2.718281...) は、自然対数の底です。  
  
 数値の指数は、定数 **e** のべき乗された数値です。 たとえば、EXP(1.0) = e^1.0 = 2.71828182845905 および EXP(10) = e^10 = 22026.4657948067 になります。  
  
 数値の自然対数の指数は数自体です: EXP (LOG (n)) = n。 数値の指数の自然対数は数自体です: LOG (EXP (n)) = n。  
  
 **例**  
  
 次の例では、変数を宣言し、指定した変数 (10) の指数値を返します。  
  
```  
SELECT EXP(10)  
```  
  
 結果セットは次のようになります。  
  
```  
[{$1: 22026.465794806718}]  
```  
  
 次の例では、自然対数 20 の指数値、および指数 20 の自然対数を返します。 これらの関数は互いの逆関数であるため、どちらの場合も浮動小数点計算に丸めを適用した戻り値は 20 です。  
  
```  
SELECT EXP(LOG(20)), LOG(EXP(20))  
```  
  
 結果セットは次のようになります。  
  
```  
[{$1: 19.999999999999996, $2: 20}]  
```  
  
####  <a name="bk_log"></a> LOG  
 指定された数値式の自然対数を返します。  
  
 **構文**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **引数**  
  
-   `numeric_expression`  
  
     数値式です。  
  
-   `base`  
  
     対数の底を設定する省略可能な数値引数。  
  
 **戻り値の型**  
  
 数値式を返します。  
  
 **解説**  
  
 既定では、LOG() は自然対数を返します。 省略可能な底パラメーターを使用して、対数の底を別の値に変更できます。  
  
 自然対数は、底 **e** に対する自然対数です。ここで、**e** は、2.718281828 にほぼ等しい無理定数です。  
  
 数値の指数の自然対数は数自体です: LOG( EXP( n ) ) = n。 数値の自然対数の指数は数自体です: EXP( LOG( n ) ) = n。  
  
 **例**  
  
 次の例では、変数を宣言し、指定した変数 (10) の対数値を返します。  
  
```  
SELECT LOG(10)  
```  
  
 結果セットは次のようになります。  
  
```  
[{$1: 2.3025850929940459}]  
```  
  
 次の例では、数値の指数の LOG を計算します。  
  
```  
SELECT EXP(LOG(10))  
```  
  
 結果セットは次のようになります。  
  
```  
[{$1: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 指定された数値式の底 10 の対数を返します。  
  
 **構文**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **引数**  
  
-   `numeric_expression`  
  
     数値式です。  
  
 **戻り値の型**  
  
 数値式を返します。  
  
 **解説**  
  
 LOG10 関数と POWER 関数は、互いに逆の関係になります。 たとえば、10 ^ log10 (n) = n になります。  
  
 **例**  
  
 次の例では、変数を宣言し、指定した変数 (100) の LOG10 値を返します。  
  
```  
SELECT LOG10(100)  
```  
  
 結果セットは次のようになります。  
  
```  
[{$1: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 円周率の定数値を返します。  
  
 **構文**  
  
```  
PI ()  
```  
  
 **引数**  
  
-   `numeric_expression`  
  
     数値式です。  
  
 **戻り値の型**  
  
 数値式を返します。  
  
 **例**  
  
 次の例は、PI の値を返します。  
  
```  
SELECT PI()  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> POWER  
 指定されたべき乗の指定された式の値を返します。  
  
 **構文**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **引数**  
  
-   `numeric_expression`  
  
     数値式です。  
  
-   `y`  
  
     `numeric_expression` のべき乗です。  
  
 **戻り値の型**  
  
 数値式を返します。  
  
 **例**  
  
 次の例では、数値の 3 (数のキューブ) のべき乗数を示します。  
  
```  
SELECT POWER(2, 3), POWER(2.5, 3)  
```  
  
 結果セットは次のようになります。  
  
```  
[{$1: 8, $2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> RADIANS  
 数値式が度数単位で入力されると、ラジアンを返します。  
  
 **構文**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **引数**  
  
-   `numeric_expression`  
  
     数値式です。  
  
 **戻り値の型**  
  
 数値式を返します。  
  
 **例**  
  
 次の例では、入力としていくつかの角度を取得し、対応するラジアン値を返します。  
  
```  
SELECT RADIANS(-45.01), RADIANS(-181.01), RADIANS(0), RADIANS(0.1472738), RADIANS(197.1099392)  
```  
  
 結果セットは次のようになります。  
  
```  
[{  
       "$1": -0.7855726963226477,  
       "$2": -3.1592204790349356,  
       "$3": 0,  
       "$4": 0.0025704127119236249,  
       "$5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> ROUND  
 最も近い整数値に丸められた数値を返します。  
  
 **構文**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **引数**  
  
-   `numeric_expression`  
  
     数値式です。  
  
 **戻り値の型**  
  
 数値式を返します。  
  
 **例**  
  
 次の例では、最も近い整数に次の正と負の数値を丸めます。  
  
```  
SELECT ROUND(2.4), ROUND(2.6), ROUND(2.5), ROUND(-2.4), ROUND(-2.6)  
```  
  
 結果セットは次のようになります。  
  
```  
[{$1: 2, $2: 3, $3: 3, $4: -2, $5: -3}]  
```  
  
####  <a name="bk_sign"></a> SIGN  
 指定された数値式の正 (+1)、ゼロ (0)、または負 (-1) の符号を返します。  
  
 **構文**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **引数**  
  
-   `numeric_expression`  
  
     数値式です。  
  
 **戻り値の型**  
  
 数値式を返します。  
  
 **例**  
  
 次の例では、-2 ～ 2 の数値の SIGN 値を返します。  
  
```  
SELECT SIGN(-2), SIGN(-1), SIGN(0), SIGN(1), SIGN(2)  
```  
  
 結果セットは次のようになります。  
  
```  
[{$1: -1, $2: -1, $3: 0, $4: 1, $5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 式で指定されたラジアン単位の角度の三角関数サインを返します。  
  
 **構文**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **引数**  
  
-   `numeric_expression`  
  
     数値式です。  
  
 **戻り値の型**  
  
 数値式を返します。  
  
 **例**  
  
 次の例では、指定された角度の SIN を計算します。  
  
```  
SELECT SIN(45.175643)  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 指定された数値の平方根を返します。  
  
 **構文**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **引数**  
  
-   `numeric_expression`  
  
     数値式です。  
  
 **戻り値の型**  
  
 数値式を返します。  
  
 **例**  
  
 次の例では、数値 1 ～ 3 の平方根を返します。  
  
```  
SELECT SQRT(1), SQRT(2.0), SQRT(3)  
```  
  
 結果セットは次のようになります。  
  
```  
[{$1: 1, $2: 1.4142135623730952, $3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> SQUARE  
 指定された数値の 2 乗を返します。  
  
 **構文**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **引数**  
  
-   `numeric_expression`  
  
     数値式です。  
  
 **戻り値の型**  
  
 数値式を返します。  
  
 **例**  
  
 次の例では、数値 1 ～ 3 の 2 乗を返します。  
  
```  
SELECT SQUARE(1), SQUARE(2.0), SQUARE(3)  
```  
  
 結果セットは次のようになります。  
  
```  
[{$1: 1, $2: 4, $3: 9}]  
```  
  
####  <a name="bk_tan"></a> TAN  
 式で指定されたラジアン単位の角度のタンジェントを返します。  
  
 **構文**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **引数**  
  
-   `numeric_expression`  
  
     数値式です。  
  
 **戻り値の型**  
  
 数値式を返します。  
  
 **例**  
  
 次の例は、PI()/2 のタンジェントを計算します。  
  
```  
SELECT TAN(PI()/2);  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> TRUNC  
 最も近い整数値に切り捨てられた数値を返します。  
  
 **構文**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **引数**  
  
-   `numeric_expression`  
  
     数値式です。  
  
 **戻り値の型**  
  
 数値式を返します。  
  
 **例**  
  
 次の例では、最も近い整数値に次の正と負の数値を丸めます。  
  
```  
SELECT TRUNC(2.4), TRUNC(2.6), TRUNC(2.5), TRUNC(-2.4), TRUNC(-2.6)  
```  
  
 結果セットは次のようになります。  
  
```  
[{$1: 2, $2: 2, $3: 2, $4: -2, $5: -2}]  
```  
  
###  <a name="bk_type_checking_functions"></a>型チェック関数  
 次の関数は、入力値に対する型チェックをサポートし、それぞれがブール値を返します。  
  
||||  
|-|-|-|  
|[IS_ARRAY](#bk_is_array)|[IS_BOOL](#bk_is_bool)|[IS_DEFINED](#bk_is_defined)|  
|[IS_NULL](#bk_is_null)|[IS_NUMBER](#bk_is_number)|[IS_OBJECT](#bk_is_object)|  
|[IS_PRIMITIVE](#bk_is_primitive)|[IS_STRING](#bk_is_string)||  
  
####  <a name="bk_is_array"></a> IS_ARRAY  
 指定した式の型が配列であるかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
IS_ARRAY(<expression>)  
```  
  
 **引数**  
  
-   `expression`  
  
     任意の有効な式です。  
  
 **戻り値の型**  
  
 ブール式を返します。  
  
 **例**  
  
 次の例では、IS_ARRAY 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null、オブジェクト、配列、および未定義の型をチェックします。  
  
```  
SELECT   
 IS_ARRAY(true),   
 IS_ARRAY(1),  
 IS_ARRAY("value"),  
 IS_ARRAY(null),  
 IS_ARRAY({prop: "value"}),   
 IS_ARRAY([1, 2, 3]),  
 IS_ARRAY({prop: "value"}.prop2)  
```  
  
 結果セットは次のようになります。  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: false, $6: true}]  
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 指定した式の型がブール値であるかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **引数**  
  
-   `expression`  
  
     任意の有効な式です。  
  
 **戻り値の型**  
  
 ブール式を返します。  
  
 **例**  
  
 次の例では、IS_BOOL 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null、オブジェクト、配列、および未定義の型をチェックします。  
  
```  
SELECT   
    IS_BOOL(true),   
    IS_BOOL(1),  
    IS_BOOL("value"),   
    IS_BOOL(null),  
    IS_BOOL({prop: "value"}),   
    IS_BOOL([1, 2, 3]),  
    IS_BOOL({prop: "value"}.prop2)  
```  
  
 結果セットは次のようになります。  
  
```  
[{$1: true, $2: false, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 プロパティに値が代入されているかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **引数**  
  
-   `expression`  
  
     任意の有効な式です。  
  
 **戻り値の型**  
  
 ブール式を返します。  
  
 **例**  
  
 次の例では、指定された JSON ドキュメント内のプロパティの存在を確認します。 1 つ目は、"a" が存在するので true を返しますが、2 つ目は "b" が存在しないので false を返します。  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a), IS_DEFINED({ "a" : 5 }.b)  
```  
  
 結果セットは次のようになります。  
  
```  
[{  
       "$1": true,    
       "$2": false   
   }]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 指定した式の型が null であるかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
IS_NULL(<expression>)  
```  
  
 **引数**  
  
-   `expression`  
  
     任意の有効な式です。  
  
 **戻り値の型**  
  
 ブール式を返します。  
  
 **例**  
  
 次の例では、IS_NULL 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null、オブジェクト、配列、および未定義の型をチェックします。  
  
```  
SELECT   
    IS_NULL(true),   
    IS_NULL(1),  
    IS_NULL("value"),   
    IS_NULL(null),  
    IS_NULL({prop: "value"}),   
    IS_NULL([1, 2, 3]),  
    IS_NULL({prop: "value"}.prop2)  
```  
  
 結果セットは次のようになります。  
  
```  
[{$1: false, $2: false, $3: false, $4: true, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 指定した式の型が数値であるかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **引数**  
  
-   `expression`  
  
     任意の有効な式です。  
  
 **戻り値の型**  
  
 ブール式を返します。  
  
 **例**  
  
 次の例では、IS_NULL 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null、オブジェクト、配列、および未定義の型をチェックします。  
  
```  
SELECT   
    IS_NUMBER(true),   
    IS_NUMBER(1),  
    IS_NUMBER("value"),   
    IS_NUMBER(null),  
    IS_NUMBER({prop: "value"}),   
    IS_NUMBER([1, 2, 3]),  
    IS_NUMBER({prop: "value"}.prop2)  
```  
  
 結果セットは次のようになります。  
  
```  
[{$1: false, $2: true, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT  
 指定した式の型が JSON オブジェクトであるかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **引数**  
  
-   `expression`  
  
     任意の有効な式です。  
  
 **戻り値の型**  
  
 ブール式を返します。  
  
 **例**  
  
 次の例では、IS_OBJECT 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null、オブジェクト、配列、および未定義の型をチェックします。  
  
```  
SELECT   
    IS_OBJECT(true),   
    IS_OBJECT(1),  
    IS_OBJECT("value"),   
    IS_OBJECT(null),  
    IS_OBJECT({prop: "value"}),   
    IS_OBJECT([1, 2, 3]),  
    IS_OBJECT({prop: "value"}.prop2)  
```  
  
 結果セットは次のようになります。  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: true, $6: false}]  
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 指定した式の型がプリミティブ (文字列、ブール値、数値、または null) であるかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **引数**  
  
-   `expression`  
  
     任意の有効な式です。  
  
 **戻り値の型**  
  
 ブール式を返します。  
  
 **例**  
  
 次の例では、IS_PRIMITIVE 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null、オブジェクト、配列、および未定義の型をチェックします。  
  
```  
SELECT   
           IS_PRIMITIVE(true),   
           IS_PRIMITIVE(1),  
           IS_PRIMITIVE("value"),   
           IS_PRIMITIVE(null),  
           IS_PRIMITIVE({prop: "value"}),   
           IS_PRIMITIVE([1, 2, 3]),  
           IS_PRIMITIVE({prop: "value"}.prop2)  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": true, "$2": true, "$3": true, "$4": true, "$5": false, "$6": false, "$7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 指定した式の型が文字列であるかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
IS_STRING(<expression>)  
```  
  
 **引数**  
  
-   `expression`  
  
     任意の有効な式です。  
  
 **戻り値の型**  
  
 ブール式を返します。  
  
 **例**  
  
 次の例では、IS_STRING 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null、オブジェクト、配列、および未定義の型をチェックします。  
  
```  
SELECT   
       IS_STRING(true),   
       IS_STRING(1),  
       IS_STRING("value"),   
       IS_STRING(null),  
       IS_STRING({prop: "value"}),   
       IS_STRING([1, 2, 3]),  
       IS_STRING({prop: "value"}.prop2)  
```  
  
 結果セットは次のようになります。  
  
```  
[{$1: false, $2: false, $3: true, $4: false, $5: false, $6: false}]  
```  
  
###  <a name="bk_string_functions"></a>文字列関数  
 次のスカラー関数は、文字列入力値に対して演算を実行し、文字列、数値またはブール値を返します。  
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[CONTAINS](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[LEFT](#bk_left)|[LENGTH](#bk_length)|  
|[LOWER](#bk_lower)|[LTRIM](#bk_ltrim)|[REPLACE](#bk_replace)|  
|[REPLICATE](#bk_replicate)|[REVERSE](#bk_reverse)|[RIGHT](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[SUBSTRING](#bk_substring)|  
|[UPPER](#bk_upper)|||  
  
####  <a name="bk_concat"></a> CONCAT  
 2 つ以上の文字列値を連結した結果である文字列を返します。  
  
 **構文**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **引数**  
  
-   `str_expr`  
  
     任意の有効な文字列式です。  
  
 **戻り値の型**  
  
 文字列式を返します。  
  
 **例**  
  
 次の例では、指定した値の連結された文字列を返します。  
  
```  
SELECT CONCAT("abc", "def")  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> CONTAINS  
 1 つ目の文字列式に 2 つ目の文字列式が含まれているかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **引数**  
  
-   `str_expr`  
  
     任意の有効な文字列式です。  
  
 **戻り値の型**  
  
 ブール式を返します。  
  
 **例**  
  
 次の例では、"abc" に "ab" と "d" が含まれるかどうかを確認します。  
  
```  
SELECT CONTAINS("abc", "ab"), CONTAINS("abc", "d")  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": true, "$2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 1 つ目の文字列式が 2 つ目の文字列で終了しているかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **引数**  
  
-   `str_expr`  
  
     任意の有効な文字列式です。  
  
 **戻り値の型**  
  
 ブール式を返します。  
  
 **例**  
  
 次の例では、"b" と "bc" で終わる "abc" を返します。  
  
```  
SELECT ENDSWITH("abc", "b"), ENDSWITH("abc", "bc")  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 1 つ目に指定された文字列式内で 2 つ目の文字列式が最初に出現する箇所の開始位置を返します。文字列が見つからない場合は -1 を返します。  
  
 **構文**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **引数**  
  
-   `str_expr`  
  
     任意の有効な文字列式です。  
  
 **戻り値の型**  
  
 数値式を返します。  
  
 **例**  
  
 次の例では、"abc" 内のさまざまな部分文字列のインデックスを返します。  
  
```  
SELECT INDEX_OF("abc", "ab"), INDEX_OF("abc", "b"), INDEX_OF("abc", "c")  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": 0, "$2": 1, "$3": -1}]  
```  
  
####  <a name="bk_left"></a> LEFT  
 指定された文字数分、文字列の左側の部分を返します。  
  
 **構文**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **引数**  
  
-   `str_expr`  
  
     任意の有効な文字列式です。  
  
-   `num_expr`  
  
     任意の有効な数値式です。  
  
 **戻り値の型**  
  
 文字列式を返します。  
  
 **例**  
  
 次の例では、さまざまな長さの値の "abc" の左側の部分を返します。  
  
```  
SELECT LEFT("abc", 1), LEFT("abc", 2)  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": "a", "$2": "ab"}]  
```  
  
####  <a name="bk_length"></a> LENGTH  
 指定された文字列式の文字数を返します。  
  
 **構文**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **引数**  
  
-   `str_expr`  
  
     任意の有効な文字列式です。  
  
 **戻り値の型**  
  
 文字列式を返します。  
  
 **例**  
  
 次の例では、文字列の長さを返します。  
  
```  
SELECT LENGTH("abc")  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_lower"></a> LOWER  
 文字列式の大文字データを小文字に変換して返します。  
  
 **構文**  
  
```  
LOWER(<str_expr>)  
```  
  
 **引数**  
  
-   `str_expr`  
  
     任意の有効な文字列式です。  
  
 **戻り値の型**  
  
 文字列式を返します。  
  
 **例**  
  
 次の例は、クエリ内での LOWER の使用方法を示しています。  
  
```  
SELECT LOWER("Abc")  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> LTRIM  
 文字列式の先頭の空白を削除して返します。  
  
 **構文**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **引数**  
  
-   `str_expr`  
  
     任意の有効な文字列式です。  
  
 **戻り値の型**  
  
 文字列式を返します。  
  
 **例**  
  
 次の例は、クエリ内での LTRIM の使用方法を示しています。  
  
```  
SELECT LTRIM("  abc"), LTRIM("abc"), LTRIM("abc   ")  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": "abc", "$2": "abc", "$3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> REPLACE  
 指定された文字列値のすべての出現箇所をもう 1 つの文字列値に置き換えます。  
  
 **構文**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **引数**  
  
-   `str_expr`  
  
     任意の有効な文字列式です。  
  
 **戻り値の型**  
  
 文字列式を返します。  
  
 **例**  
  
 次の例は、クエリ内での REPLACE の使用方法を示しています。  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk")  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLICATE  
 文字列値を指定された回数だけ繰り返します。  
  
 **構文**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **引数**  
  
-   `str_expr`  
  
     任意の有効な文字列式です。  
  
-   `num_expr`  
  
     任意の有効な数値式です。  
  
 **戻り値の型**  
  
 文字列式を返します。  
  
 **例**  
  
 次の例は、クエリ内での REPLICATE の使用方法を示しています。  
  
```  
SELECT REPLICATE("a", 3)  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> REVERSE  
 文字列値の順序を逆にして返します。  
  
 **構文**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **引数**  
  
-   `str_expr`  
  
     任意の有効な文字列式です。  
  
 **戻り値の型**  
  
 文字列式を返します。  
  
 **例**  
  
 次の例は、クエリ内での REVERSE の使用方法を示しています。  
  
```  
SELECT REVERSE("Abc")  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": "cbA"}]  
```  
  
####  <a name="bk_right"></a> RIGHT  
 指定された文字数分、文字列の右側の部分を返します。  
  
 **構文**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **引数**  
  
-   `str_expr`  
  
     任意の有効な文字列式です。  
  
-   `num_expr`  
  
     任意の有効な数値式です。  
  
 **戻り値の型**  
  
 文字列式を返します。  
  
 **例**  
  
 次の例では、さまざまな長さの値の "abc" の右側の部分を返します。  
  
```  
SELECT RIGHT("abc", 1), RIGHT("abc", 2)  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": "c", "$2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> RTRIM  
 文字列式の末尾の空白を削除して返します。  
  
 **構文**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **引数**  
  
-   `str_expr`  
  
     任意の有効な文字列式です。  
  
 **戻り値の型**  
  
 文字列式を返します。  
  
 **例**  
  
 次の例は、クエリ内での RTRIM の使用方法を示しています。  
  
```  
SELECT RTRIM("  abc"), RTRIM("abc"), RTRIM("abc   ")  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": "   abc", "$2": "abc", "$3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 1 つ目の文字列式が 2 つ目の文字列で始まっているかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **引数**  
  
-   `str_expr`  
  
     任意の有効な文字列式です。  
  
 **戻り値の型**  
  
 ブール式を返します。  
  
 **例**  
  
 次の例は、文字列 "abc" が "b" および "a" で始まるかどうかを確認します。  
  
```  
SELECT STARTSWITH("abc", "b"), STARTSWITH("abc", "a")  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_substring"></a> SUBSTRING  
 指定された文字のゼロベースの位置で始まる文字列式の一部を返し、指定された長さまたは文字列の末尾まで続きます。  
  
 **構文**  
  
```  
SUBSTRING(<str_expr>, <num_expr> [, <num_expr>])  
```  
  
 **引数**  
  
-   `str_expr`  
  
     任意の有効な文字列式です。  
  
-   `num_expr`  
  
     任意の有効な数値式です。  
  
 **戻り値の型**  
  
 文字列式を返します。  
  
 **例**  
  
 次の例では、1 から始まる 1 文字の長さの "abc" の部分文字列を返します。  
  
```  
SELECT SUBSTRING("abc", 1, 1)  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": "b"}]  
```  
  
####  <a name="bk_upper"></a> UPPER  
 文字列式の小文字データを大文字に変換して返します。  
  
 **構文**  
  
```  
UPPER(<str_expr>)  
```  
  
 **引数**  
  
-   `str_expr`  
  
     任意の有効な文字列式です。  
  
 **戻り値の型**  
  
 文字列式を返します。  
  
 **例**  
  
 次の例は、クエリ内での UPPER の使用方法を示しています。  
  
```  
SELECT UPPER("Abc")  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": "ABC"}]  
```  
  
###  <a name="bk_array_functions"></a>配列関数  
 次のスカラー関数は、配列入力値に対して演算を実行し、数値、ブール値、または配列値を返します。  
  
||||  
|-|-|-|  
|[ARRAY_CONCAT](#bk_array_concat)|[ARRAY_CONTAINS](#bk_array_contains)|[ARRAY_LENGTH](#bk_array_length)|  
|[ARRAY_SLICE](#bk_array_slice)|||  
  
####  <a name="bk_array_concat"></a> ARRAY_CONCAT  
 2 つ以上の配列値を連結した結果である配列を返します。  
  
 **構文**  
  
```  
ARRAY_CONCAT (<arr_expr>, <arr_expr> [, <arr_expr>])  
```  
  
 **引数**  
  
-   `arr_expr`  
  
     任意の有効な配列式です。  
  
 **戻り値の型**  
  
 配列式を返します。  
  
 **例**  
  
 次の例では、2 つの配列を結合する方法を示します。  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"])  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a> ARRAY_CONTAINS  
 配列に指定された値が含まれているかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr>)  
```  
  
 **引数**  
  
-   `arr_expr`  
  
     任意の有効な配列式です。  
  
-   `expr`  
  
     任意の有効な式です。  
  
 **戻り値の型**  
  
 ブール値を返します。  
  
 **例**  
  
 次の例では、ARRAY_CONTAINS を使用して、配列内のメンバーシップを確認する方法を示します。  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples"),  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes")  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": true, "$2": false}]  
```  
  
####  <a name="bk_array_length"></a> ARRAY_LENGTH  
 指定された配列式の要素の数を返します。  
  
 **構文**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **引数**  
  
-   `arr_expr`  
  
     任意の有効な配列式です。  
  
 **戻り値の型**  
  
 数値式を返します。  
  
 **例**  
  
 次の例では、ARRAY_LENGTH を使用して、配列の長さを取得する方法を示します。  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"])  
```  
  
 結果セットは次のようになります。  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_array_slice"></a> ARRAY_SLICE  
 配列式の一部を返します。
  
 **構文**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **引数**  
  
-   `arr_expr`  
  
     任意の有効な配列式です。  
  
-   `num_expr`  
  
     任意の有効な数値式です。  
  
 **戻り値の型**  
  
 ブール値を返します。  
  
 **例**  
  
 次の例では、ARRAY_SLICE を使用して、配列の一部を取得する方法を示します。  
  
```  
SELECT   
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1),  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1)  
```  
  
 結果セットは次のようになります。  
  
```  
[{  
           "$1": ["strawberries", "bananas"],   
           "$2": ["strawberries"]  
       }]  
```  
  
###  <a name="bk_spatial_functions"></a>空間関数  
 次のスカラー関数は、空間オブジェクト入力値に対して演算を実行し、数値またはブール値を返します。  
  
||||  
|-|-|-|  
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|  
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)|||  
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 2 つの GeoJSON Point、Polygon、または LineString 式間の距離を返します。  
  
 **構文**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **引数**  
  
-   `spatial_expr`  
  
     有効な GeoJSON ポイント、多角形、または LineString オブジェクト式です。  
  
 **戻り値の型**  
  
 距離を含む数値式を返します。 これは、既定の参照システムのメートル単位で表されます。  
  
 **例**  
  
 次の例では、指定された場所の 30 km 圏内に存在するすべての世帯ドキュメントを ST_DISTANCE 組み込み関数で取得する方法を示します。 に関するページを参照してください。  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 結果セットは次のようになります。  
  
```  
[{  
  "id": "WakefieldFamily"  
}]  
```  
  
####  <a name="bk_st_within"></a> ST_WITHIN  
 最初の引数で指定された GeoJSON オブジェクト (Point、Polygon、または LineString) が 2 つ目の引数の GeoJSON (Point、Polygon、または LineString) 内に存在するかどうかを示すブール式を返します。  
  
 **構文**  
  
```  
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
 **引数**  
  
-   `spatial_expr`  
  
     有効な GeoJSON ポイント、多角形、または LineString オブジェクト式です。  
 
-   `spatial_expr`  
  
     有効な GeoJSON ポイント、多角形、または LineString オブジェクト式です。  
  
 **戻り値の型**  
  
 ブール値を返します。  
  
 **例**  
  
 次の例では、ST_WITHIN を使用して多角形内のすべての世帯ドキュメントを検索する方法を示します。  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 結果セットは次のようになります。  
  
```  
[{ "id": "WakefieldFamily" }]  
```  

####  <a name="bk_st_intersects"></a> ST_INTERSECTS  
 最初の引数で指定された GeoJSON オブジェクト (Point、Polygon、または LineString) が 2 つ目の引数の GeoJSON (Point、Polygon、または LineString) と交差するかどうかを示すブール式を返します。  
  
 **構文**  
  
```  
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
 **引数**  
  
-   `spatial_expr`  
  
     有効な GeoJSON ポイント、多角形、または LineString オブジェクト式です。  
 
-   `spatial_expr`  
  
     有効な GeoJSON ポイント、多角形、または LineString オブジェクト式です。  
  
 **戻り値の型**  
  
 ブール値を返します。  
  
 **例**  
  
 次の例では、特定の多角形と交差するすべての領域を見つける方法を示します。  
  
```  
SELECT a.id   
FROM Areas a   
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 結果セットは次のようになります。  
  
```  
[{ "id": "IntersectingPolygon" }]  
```  
  
####  <a name="bk_st_isvalid"></a> ST_ISVALID  
 指定された GeoJSON Point、Polygon、または LineString 式が有効かどうかを示すブール値を返します。  
  
 **構文**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **引数**  
  
-   `spatial_expr`  
  
     有効な GeoJSON ポイント、多角形、または LineString 式です。  
  
 **戻り値の型**  
  
 ブール式を返します。  
  
 **例**  
  
 次の例では、ST_VALID を使用してポイントが有効かどうかを確認する方法を示します。  
  
 たとえば、このポイントの緯度の値は有効な値の範囲 [-90, 90] に含まれていないので、クエリは false を返します。  
  
 GeoJSON 仕様では、最後に指定された座標ペアが、最初に指定された座標ペアとちょうど重なり、閉じた形状になっていることが、ポリゴンの条件となります。 ポリゴン内のポイントは、反時計回りに指定する必要があります。 時計回りに指定されたポリゴンは、その中の領域を逆にしたものを表します。  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })  
```  
  
 結果セットは次のようになります。  
  
```  
[{ "$1": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 指定された GeoJSON Point、Polygon、または LineString 式が有効であるかどうかのブール値を含んだ JSON 値を返します。無効である場合はさらに、その理由が文字列値として返されます。  
  
 **構文**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **引数**  
  
-   `spatial_expr`  
  
     有効な GeoJSON ポイントまたはポリゴン式です。  
  
 **戻り値の型**  
  
 指定された GeoJSON ポイントまたはポリゴン式が有効であるかどうかのブール値を含んだ JSON 値を返します。無効である場合はさらに、その理由が文字列値として返されます。  
  
 **例**  
  
 次の例では、ST_ISVALIDDETAILED を使用して有効性 (詳細) を確認する方法を示します。  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
})  
```  
  
 結果セットは次のようになります。  
  
```  
[{  
  "$1": {   
    "valid": false,   
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  
  
## <a name="next-steps"></a>次のステップ  
 [Azure Cosmos DB の SQL 構文と SQL クエリ](documentdb-sql-query.md)   
 [Azure Cosmos DB のドキュメント](https://docs.microsoft.com/en-us/azure/cosmos-db/)  
  
  

