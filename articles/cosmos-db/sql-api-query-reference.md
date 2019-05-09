---
title: Azure Cosmos DB での SQL 言語の構文
description: この記事では、Azure Cosmos DB で使用される SQL クエリ言語の構文、およびこの言語で使用できるさまざまな演算子とキーワードについて説明します。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 22b03417495625ef70650a015530d6f56b32fd4f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59283649"
---
# <a name="sql-language-reference-for-azure-cosmos-db"></a>Azure Cosmos DB 用の SQL 言語リファレンス 

Azure Cosmos DB は、明示的なスキーマまたはセカンダリ インデックスの作成を必要とせずに、階層型 JSON ドキュメントに対する文法などの使い慣れた SQL (構造化照会言語) を使用したドキュメンのクエリ実行をサポートします。 この記事では、SQL API アカウントで使用される SQL クエリ言語の構文に関するドキュメントを提供します。 SQL クエリの例のチュートリアルについては、[Cosmos DB の SQL クエリの例](how-to-sql-query.md)に関するページを参照してください。  
  
[Query Playground](https://www.documentdb.com/sql/demo) にアクセスすると、Cosmos DB を試したり、サンプル データセットに対して SQL クエリを実行したりできます。  
  
## <a name="select-query"></a>SELECT クエリ  
すべてのクエリは ANSI-SQL 標準に従って SELECT 句とオプションの FROM および WHERE 句で構成されます。 通常、各クエリでは、FROM 句のソースが列挙された後、JSON ドキュメントのサブセットを取得するためにそのソースに WHERE 句のフィルターが適用されます。 最後に SELECT 句を使用して、要求された JSON 値が特定のリストにプロジェクションされます。 例については、[SELECT クエリの例](how-to-sql-query.md#SelectClause)を参照してください。
  
**構文**  
  
```sql
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
  
### <a name="logical-processing-order-of-the-select-statement"></a>SELECT ステートメントの論理的な処理順序  
  
句は次の順序で処理されます。  

1.  [FROM 句](#bk_from_clause)  
2.  [WHERE 句](#bk_where_clause)  
3.  [ORDER BY 句](#bk_orderby_clause)  
4.  [SELECT 句](#bk_select_query)  

これは、構文で表示される順序と異なることに注意してください。 処理済みの句によって導入されるすべての新しいシンボルが表示され、後で処理される句で使用できるような順序になっています。 たとえば、FROM 句で宣言されている別名は、WHERE 句と SELECT 句でアクセス可能です。  

### <a name="whitespace-characters-and-comments"></a>空白文字とコメント  

引用符で囲まれた文字列または識別子に含まれない空白文字はすべて、言語文法の一部ではなく、解析時には無視されます。  

クエリ言語は、T-SQL スタイルのコメントをサポートしています  

-   SQL ステートメント`-- comment text [newline]`  

空白文字とコメントは文法内では何も意味がありませんが、トークンを区切る場合に使用する必要があります。 例: `-1e5` は、1 つの数値のトークンですが、`: – 1 e5` は負のトークンであり、その後に数字 1 と識別子 e5 が続いています。  

##  <a name="bk_select_query"></a>SELECT 句  
SELECT ステートメント内の句は、上の順序で並べられている必要があります。 オプションの句はどれも省略できます。 オプションの句を使用している場合、正しい順序で現れる必要があります。 例については、[SELECT クエリの例](how-to-sql-query.md#SelectClause)を参照してください。

**構文**  

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | <object_property_list>   
      | VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
 **引数**  
  
- `<select_specification>`  

  結果セットで選択されるプロパティまたは値。  
  
- `'*'`  

  変更を加えずに値を取得することを指定します。 具体的には、処理された値がオブジェクトである場合、すべてのプロパティが取得されます。  
  
- `<object_property_list>`  
  
  取得するプロパティの一覧を指定します。 返される各値は、指定されたプロパティを持つオブジェクトとなります。  
  
- `VALUE`  

  完全な JSON オブジェクトではなく JSON 値を取得することを指定します。 これは、`<property_list>` とは異なり、オブジェクト内の投影された値をラップしません。  
  
- `<scalar_expression>`  

  計算する値を表す式。 詳細については、「[スカラー式](#bk_scalar_expressions)」セクションを参照してください。  
  
**解説**  
  
`SELECT *` 構文は、FROM 句で正確に 1 つの別名が宣言された場合のみ有効です。 `SELECT *` は、プロジェクションが不要な場合に便利な、ID プロジェクションを提供します。 SELECT * は、FROM 句が指定され、1 つの入力ソースのみが導入されている場合にのみ有効です。  
  
`SELECT <select_list>` と `SELECT *` は両方とも「糖衣構文」であり、次に示すように単純な SELECT ステートメントを使用して、別の方法で表すことができます。  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   は以下に匹敵します。  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   は以下に匹敵します。  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
**関連項目**  
  
[スカラー式](#bk_scalar_expressions)  
[SELECT 句](#bk_select_query)  
  
##  <a name="bk_from_clause"></a>FROM 句  
ソースまたは結合されたソースを指定します。 FROM 句はオプションです (ソースがクエリの後半でフィルター処理またはプロジェクションされる場合を除く)。 この句の目的は、クエリが動作する対象のデータ ソースを指定することです。 通常はコンテナー全体がソースになりますが、コンテナーのサブセットを指定することもできます。 この句が指定されていない場合、FROM 句が 1 つの文書を提供した場合のように、他の句が実行されます。 例については、[FROM 句の例](how-to-sql-query.md#FromClause)を参照してください。
  
**構文**  
  
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
  
**引数**  
  
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
  
**解説**  
  
指定されるか `<from_source>(` で推測されたすべての別名は一意である必要があります。 構文 `<container_expression>.`property_name は `<container_expression>' ['"property_name"']'` と同じです。 ただし、プロパティ名に非識別子文字が含まれている場合は、後者の構文を使用できます。  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>見つからないプロパティ、見つからない配列要素、未定義の値の処理
  
コンテナー式がプロパティまたは配列要素にアクセスし、その値が存在しない場合、値は無視され、処理は行われません。  
  
### <a name="container-expression-context-scoping"></a>コンテナー式のコンテキストのスコープ  
  
コンテナー式には、コンテナー スコープまたはドキュメント スコープがあります。  
  
-   基になるコンテナー式のソースが ROOT または `container_name` である場合、式はコンテナー スコープです。 このような式は、直接コンテナーから取得されたドキュメントのセットを表し、その他のコンテナーの式の処理に依存しません。  
  
-   基になるコンテナー式が、クエリで前に導入された `input_alias` である場合、式はドキュメント スコープです。 このような式は、別名を付けられたコンテナーに関連付けられているセットに属する各ドキュメントのスコープ内で、コンテナー式を評価することによって取得されたドキュメントのセットを表します。  結果セットは、基になるセット内の各ドキュメントのコンテナー式を評価することによって取得されたセットの和集合になります。  
  
### <a name="joins"></a>結合 
  
現在のリリースでは、Cosmos DB は内部結合をサポートします。 追加の結合機能は近日提供予定です。 

内部結合の結果は、結合に含まれるセットの完全なクロス積になります。 N-way 結合の結果は、N 要素タプルのセットであり、タプル内の各値が結合に含まれている別名セットに関連付けられていて、他の句でその別名を参照してアクセスできます。 例については、[JOIN](how-to-sql-query.md#Joins) キーワードの例を参照してください。
  
結合の評価は、参加しているセットのコンテキストのスコープによって異なります。  
  
- コンテナー セット A とコンテナー スコープ セット B の間の結合の結果は、A と B のセットのすべての要素のクロス積になります。
  
- セット A とドキュメント スコープ セット B の間の結合の結果は、セット Aの各ドキュメントの スコープ セット B を評価することによって取得されたすべてのセットの和集合になります。  
  
  現在のリリースでは、最大で 1 つのコンテナー スコープ式が、クエリ プロセッサによってサポートされています。  
  
### <a name="examples-of-joins"></a>結合の例  
  
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
  
**関連項目**  
  
 [SELECT 句](#bk_select_query)  
  
##  <a name="bk_where_clause"></a>WHERE 句  
 クエリによって返されるドキュメントの検索条件を指定します。 例については、[WHERE 句の例](how-to-sql-query.md#WhereClause)を参照してください。
  
 **構文**  
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
 **引数**  
  
- `<filter_condition>`  
  
   返されるドキュメントが満たす条件を指定します。  
  
- `<scalar_expression>`  
  
   計算する値を表す式。 詳細については、「[スカラー式](#bk_scalar_expressions)」セクションを参照してください。  
  
  **解説**  
  
  ドキュメントが返されるには、フィルター条件として指定された式が true に評価される必要があります。 ブール値 true のみがその条件を満たし、他の値 (undefined、null、false、数字、配列、またはオブジェクト) は条件を満たしません。  
  
##  <a name="bk_orderby_clause"></a>ORDER BY 句  
 クエリによって返される結果の並べ替え順序を指定します。 例については、[ORDER BY 句の例](how-to-sql-query.md#OrderByClause)を参照してください。
  
 **構文**  
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= <scalar_expression> [ASC | DESC]  
  
```  
  
 **引数**  
  
- `<sort_specification>`  
  
   クエリの結果セットの並べ替えの条件にするプロパティまたは式を指定します。 並べ替え列は、名前または列の別名として指定できます。  
  
   複数の並べ替え列を指定することができます。 列名は一意である必要があります。 ORDER BY 句の並べ替え列の順序は、並べ替えられた結果セットの構成を定義します。 つまり、結果セットは最初のプロパティで並べ替えられ、その並べ替えられたリストが 2 番目のプロパティで並べ替えられ、以下同様に続きます。  
  
   ORDER BY 句で参照される列名は、選択リスト内の列にまたはあいまいさのない FROM 句で指定されたテーブルで定義されている列に対応する必要があります。  
  
- `<sort_expression>`  
  
   クエリの結果セットの並べ替えの条件にする 1 つのプロパティまたは式を指定します。  
  
- `<scalar_expression>`  
  
   詳細については、「[スカラー式](#bk_scalar_expressions)」セクションを参照してください。  
  
- `ASC | DESC`  
  
   指定された列の値を昇順または降順で並べ替える必要があることを指定します。 ASC は、最小値から最大値に並べ替えられます。 DESC は、最大値から最小値に並べ替えられます。 既定の並べ替え順は ASC です。 NULL 値は、有効な最小値として扱われます。  
  
  **解説**  
  
  クエリ文法では、複数のプロパティによる並べ替えがサポートされますが、Cosmos DB クエリ ランタイムでは、1 つのプロパティに対する並べ替えのみがサポートされ、プロパティ名のみが対象となります (計算されるプロパティは対象になりません)。 並べ替えではまた、インデックス作成ポリシーが、プロパティの範囲インデックス、指定された型、および最大有効桁数を含んでいる必要があります。 詳細については、インデックス作成ポリシーに関するドキュメントを参照してください。  
  
##  <a name="bk_scalar_expressions"></a>スカラー式  
 スカラー式は、1 つの値を取得するために評価できるシンボルと演算子の組み合わせです。 単純式には、定数、プロパティの参照、配列要素の参照、別名の参照、または関数の呼び出しを指定できます。 単純式は、演算子を使用して複雑な式に結合できます。 例については、[スカラー式の例](how-to-sql-query.md#scalar-expressions)を参照してください。
  
 スカラー式で使用できる値の詳細については、「[定数](#bk_constants)」セクションを参照してください。  
  
 **構文**  
  
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
  
 **引数**  
  
- `<constant>`  
  
   定数値を表します。 詳細については、「[定数](#bk_constants)」セクションを参照してください。  
  
- `input_alias`  
  
   `FROM` 句によって導入された `input_alias` で定義された値を表します。  
  この値は、**undefined** ではないことが保証されます。入力内の **undefined** 値はスキップされます。  
  
- `<scalar_expression>.property_name`  
  
   オブジェクトのプロパティの値を表します。 プロパティが存在しない場合、またはプロパティがオブジェクトではない値で参照されている場合、式は **undefined** 値として評価されます。  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   名前 `property_name` を持つプロパティの値、またはオブジェクト/配列のインデックス `array_index` を持つ配列要素を表します。 プロパティ/配列インデックスが存在しない場合、またはプロパティ/配列インデックスがオブジェクト/配列ではない値で参照されている場合、式は undefined 値として評価されます。  
  
- `unary_operator <scalar_expression>`  
  
   1 つの値に適用される演算子を表します。 詳細については、「[演算子](#bk_operators)」セクションを参照してください。  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   2 つの値に適用される演算子を表します。 詳細については、「[演算子](#bk_operators)」セクションを参照してください。  
  
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
  
|**Name**|**演算子**|**詳細**|  
|-|-|-|  
|**算術**|+<br /><br /> -|数値を返します。<br /><br /> ビットごとの否定。 否定された数値を返します。|  
|**ビット演算子**|~|1 の補数。 数値の補数を返します。|  
|**論理**|**NOT**|否定します。 否定されたブール値を返します。|  
  
 **二項演算子:**  
  
|**Name**|**演算子**|**詳細**|  
|-|-|-|  
|**算術**|+<br /><br /> -<br /><br /> *<br /><br /> /<br /><br /> %|加算。<br /><br /> 減算。<br /><br /> 乗算。<br /><br /> 除算。<br /><br /> 比率。|  
|**ビット演算子**|&#124;<br /><br /> &<br /><br /> ^<br /><br /> <<<br /><br /> >><br /><br /> >>>|ビット演算子 OR。<br /><br /> ビット演算子 AND。<br /><br /> ビット演算子 XOR。<br /><br /> 左シフト。<br /><br /> 右シフト。<br /><br /> 0 埋め右シフト。|  
|**論理**|**AND**<br /><br /> **OR**|論理積。 両方の引数が **true** である場合、**true** を返します。それ以外の場合は **false** を返します。<br /><br /> 論理和。 いずれかの引数が **true** である場合、**true** を返します。それ以外の場合は **false** を返します。|  
|**比較**|**=**<br /><br /> **!=, <>**<br /><br /> **>**<br /><br /> **>=**<br /><br /> **<**<br /><br /> **<=**<br /><br /> **??**|等しい。 両方の引数が等しい場合、**true** を返します。それ以外の場合は **false** を返します。<br /><br /> 等しくない。 両方の引数が等しくない場合、**true** を返します。それ以外の場合は **false** を返します。<br /><br /> より大きい。 1 番目の引数が 2 番目の引数より大きい場合、**true** を返します。それ以外の場合、**false** を返します。<br /><br /> 以上。 1 番目の引数が 2 番目の引数以上の場合、**true** を返します。それ以外の場合、**false** を返します。<br /><br /> より小さい。 1 番目の引数が 2 番目の引数より小さい場合、**true** を返します。それ以外の場合、**false** を返します。<br /><br /> 以下。 1 番目の引数が 2 番目の引数以下の場合、**true** を返します。それ以外の場合、**false** を返します。<br /><br /> 結合。 最初の引数が **undefined** 値である場合、2 番目の引数を返します。|  
|**文字列**|**&#124;&#124;**|連結。 両方の引数の連結を返します。|  
  
 **三項演算子:**  

|**Name**|**演算子**|**詳細**| 
|-|-|-|  
|三項演算子|?|最初の引数が **true** に評価された場合、2 番目の引数を返します。それ以外の場合、3 番目の引数を返します。|  

  
 **比較の値の順序付け**  
  
|**Type**|**値の順序**|  
|-|-|  
|**Undefined**|比較できません。|  
|**Null**|単一の値: **Null**|  
|**Number**|実数。<br /><br /> 負の無限大の値は、他のどの数値よりも小さくなります。<br /><br /> 正の無限大の値は、他のどの数値よりも大きくなります。**NaN** 値は比較できません。 **NaN** と比較すると、結果は **undefined** 値になります。|  
|**文字列**|辞書の順序。|  
|**Array**|順序付けがなく平等に扱われます。|  
|**Object**|順序付けがなく平等に扱われます。|  
  
 **解説**  
  
 Cosmos DB では、多くの場合、値の型はデータベースから取得されるまで不明です。 効率的なクエリの実行をサポートするため、大部分の演算子には厳密な型の要件があります。 また演算子自体は、暗黙的な変換を行いません。  
  
 つまり、SELECT * FROM ROOT r WHERE r.Age = 21 のようなクエリは、プロパティ Age が数値 21 と等しいドキュメントのみを返します。 プロパティ Age が文字列 "21" または文字列 "0021" と等しいドキュメントは一致しません。これは式 "21" = 21 が undefined と評価されるためです。 特定の値 (数値 21 など) の検索は、不特定数の一致候補 (数値 21 または文字列 "21"、"021"、"21.0" …) の検索よりも高速で実行されるので、これにより、インデックスを効率的に使用できます。 これは、JavaScript でさまざまな種類の値に対する演算子を評価する方法とは異なります。  
  
 **配列とオブジェクトの等値と比較**  
  
 範囲演算子 (>、> =、<、< =) を使用して配列またはオブジェクトの値を比較すると、オブジェクトまたは配列の値には順序が定義されていないので、結果は undefined になります。 ただし、等価/非等価演算子 (=、! =、<>) の使用はサポートされており、値が構造的に比較されます。  
  
 配列は、両方の配列が同じ数の要素を持ち、一致する位置にある要素も等しい場合に等しくなります。 要素のペアの比較の結果が未定義の場合、配列の比較の結果は未定義になります。  
  
 オブジェクトは、両方のオブジェクトに同じプロパティが定義され、一致するプロパティの値も等しい場合に等しくなります。 プロパティのペアの比較の結果が未定義の場合、オブジェクト比較の結果は未定義になります。  
  
##  <a name="bk_constants"></a>定数  
 定数、リテラルまたはスカラー値とも呼ばれますが、特定のデータ値を表すシンボルです。 定数の形式は、それが表す値のデータ型によって異なります。  
  
 **サポートされるスカラー データ型:**  
  
|**Type**|**値の順序**|  
|-|-|  
|**Undefined**|単一の値: **undefined**|  
|**Null**|単一の値: **Null**|  
|**Boolean**|値: **false**、**true**。|  
|**Number**|倍精度浮動小数点数、IEEE 754 標準。|  
|**String**|0 個以上の Unicode 文字のシーケンス。 文字列は、一重引用符または二重引用符で囲む必要があります。|  
|**Array**|0 個以上の要素のシーケンス。 各要素には、Undefined を除く、任意のスカラー データ型の値を指定できます。|  
|**Object**|0 個以上の名前/値ペアの順序なしのセット。 名前は Unicode 文字列で、値は **Undefined** 以外の任意のスカラー データ型を指定できます。|  
  
 **構文**  
  
```sql  
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
  
* `<undefined_constant>; undefined`  
  
  Undefined 型の未定義値を表します。  
  
* `<null_constant>; null`  
  
  **Null** 型の値 **null** を表します。  
  
* `<boolean_constant>`  
  
  Boolean 型の定数を表します。  
  
* `false`  
  
  Boolean 型の **false** 値を表します。  
  
* `true`  
  
  Boolean 型の **true** 値を表します。  
  
* `<number_constant>`  
  
  定数を表します。  
  
* `decimal_literal`  
  
  10 進数のリテラルは、10 進表記または指数表記を使用して表現された数値です。  
  
* `hexadecimal_literal`  
  
  16 進数のリテラルは、プレフィックス '0 x' の後に 1 つまたは複数の 16 進数字を使用して表現された数値です。  
  
* `<string_constant>`  
  
  文字列型の定数を表します。  
  
* `string _literal`  
  
  文字列リテラルは、0 個以上の Unicode 文字のシーケンスまたはエスケープ シーケンスによって表される Unicode 文字列です。 文字列リテラルは、単一引用符 (アポストロフィ: ') または二重引用符 (引用符:") で囲みます。  
  
  次のエスケープ シーケンスを使用できます。  
  
|**エスケープ シーケンス**|**説明**|**Unicode 文字**|  
|-|-|-|  
|\\'|アポストロフィ (')|U+0027|  
|\\"|引用符 (")|U+0022|  
|\\\ |逆斜線 (\\)|U+005C|  
|\\/|斜線 (/)|U+002F|  
|\b|バックスペース|U+0008|  
|\f|フォーム フィード|U+000C|  
|\n|ライン フィード|U+000A|  
|\r|キャリッジ リターン|U+000D|  
|\t|タブ|U+0009|  
|\uXXXX|4 つの 16 進数字によって定義された Unicode 文字。|U+XXXX|  
  
##  <a name="bk_query_perf_guidelines"></a> クエリのパフォーマンスに関するガイドライン  
 大規模なコンテナーに対してクエリを効率的に実行するには、1 つまたは複数のインデックスで利用できるフィルターを使用してください。  
  
 インデックスの検索では、次のフィルターを検討できます。  
  
- ドキュメント パス式および定数を含む等値演算子 (=) を使用します。  
  
- ドキュメント パス式および定数を含む範囲演算子 (<、\<=、>、>=) を使用します。  
  
- ドキュメント パス式は、参照先のデータベース コンテナーからドキュメント内の定数のパスを識別する任意の式を表します。  
  
  **ドキュメント パス式**  
  
  ドキュメント パス式は、データべース コンテナー ドキュメントから取得されるドキュメントのプロパティまたは配列インデクサー評価機能のパスの式です。 このパスは、データベース コンテナーのドキュメント内で、直接フィルターで参照されている値の場所を識別するために使用されます。  
  
  式がドキュメント パス式と見なされるには、次の条件を満たしている必要があります。  
  
1.  コンテナーのルートを直接参照する。  
  
2.  一部のドキュメント パス式の参照プロパティまたは定数配列のインデクサーを参照している。  
  
3.  一部のドキュメント パス式を表す別名を参照している。  
  
     **構文表記規則**  
  
     次の表では、後の SQL リファレンスで構文の説明に使われる規則について説明します。  
  
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
 Cosmos DB は、多くの組み込み SQL 関数を提供します。 組み込み関数のカテゴリは次のとおりです。  
  
|Function|説明|  
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
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例は、次の 3 つの異なる数値に対して ABS 関数を使用した結果を示しています。  
  
```  
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 結果セットは次のようになります。  
  
```  
[{abs1: 1, abs2: 0, abs3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 コサインが指定された数値式となる角度をラジアン単位で返します。アークコサインとも呼ばれます。  
  
 **構文**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例は、-1 の ACOS を返します。  
  
```  
SELECT ACOS(-1) AS acos 
```  
  
 結果セットは次のようになります。  
  
```  
[{"acos": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 サインが指定された数値式となる角度をラジアン単位で返します。 アークサインとも呼ばれます。  
  
 **構文**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例は、-1 の ASIN を返します。  
  
```  
SELECT ASIN(-1) AS asin  
```  
  
 結果セットは次のようになります。  
  
```  
[{"asin": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 タンジェントが指定された数値式となる角度をラジアン単位で返します。 アークタンジェントとも呼ばれます。  
  
 **構文**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、指定した値の ATAN を返します。  
  
```  
SELECT ATAN(-45.01) AS atan  
```  
  
 結果セットは次のようになります。  
  
```  
[{"atan": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 ラジアン単位で表される y/x のアーク タンジェンの主値を返します。  
  
 **構文**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例は、指定された x と y コンポーネントの ATN2 を計算します。  
  
```  
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 結果セットは次のようになります。  
  
```  
[{"atn2": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> CEILING  
 指定された数値式以上の最小の整数値を返します。  
  
 **構文**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、CEILING 関数を使用して、正の数値、負の数値、およびゼロ値を示します。  
  
```  
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 結果セットは次のようになります。  
  
```  
[{c1: 124, c2: -123, c3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 式で指定されたラジアン単位の角度の三角関数コサインを返します。  
  
 **構文**  
  
```  
COS(<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、指定された角度の COS を計算します。  
  
```  
SELECT COS(14.78) AS cos  
```  
  
 結果セットは次のようになります。  
  
```  
[{"cos": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 数値式で指定されたラジアン単位の角度の三角関数コタンジェントを返します。  
  
 **構文**  
  
```  
COT(<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、指定された角度の COT を計算します。  
  
```  
SELECT COT(124.1332) AS cot  
```  
  
 結果セットは次のようになります。  
  
```  
[{"cot": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> DEGREES  
 ラジアン単位で指定された角度に対応する度数単位の角度を返します。  
  
 **構文**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、PI/2 ラジアンの角度で度数を返します。  
  
```  
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 結果セットは次のようになります。  
  
```  
[{"degrees": 90}]  
```  
  
####  <a name="bk_floor"></a> FLOOR  
 指定された数値式未満の最大の整数を返します。  
  
 **構文**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、FLOOR 関数を使用して、正の数値、負の数値、およびゼロ値を示します。  
  
```  
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 結果セットは次のようになります。  
  
```  
[{fl1: 123, fl2: -124, fl3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 指定された数値式の指数値を返します。  
  
 **構文**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **解説**  
  
  定数 **e** (2.718281...) は、自然対数の底です。  
  
  数値の指数は、定数 **e** のべき乗された数値です。 たとえば、EXP(1.0) = e^1.0 = 2.71828182845905 および EXP(10) = e^10 = 22026.4657948067 になります。  
  
  数値の自然対数の指数は数自体です:EXP (LOG (n)) = n。 数値の指数の自然対数は数自体です:LOG (EXP (n)) = n。  
  
  **例**  
  
  次の例では、変数を宣言し、指定した変数 (10) の指数値を返します。  
  
```  
SELECT EXP(10) AS exp  
```  
  
 結果セットは次のようになります。  
  
```  
[{exp: 22026.465794806718}]  
```  
  
 次の例では、自然対数 20 の指数値、および指数 20 の自然対数を返します。 これらの関数は互いの逆関数であるため、どちらの場合も浮動小数点計算に丸めを適用した戻り値は 20 です。  
  
```  
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 結果セットは次のようになります。  
  
```  
[{exp1: 19.999999999999996, exp2: 20}]  
```  
  
####  <a name="bk_log"></a> LOG  
 指定された数値式の自然対数を返します。  
  
 **構文**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
- `base`  
  
   対数の底を設定する省略可能な数値引数。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **解説**  
  
  既定では、LOG() は自然対数を返します。 省略可能な底パラメーターを使用して、対数の底を別の値に変更できます。  
  
  自然対数は、底 **e** に対する自然対数です。ここで、**e** は、2.718281828 にほぼ等しい無理定数です。  
  
  数値の指数の自然対数は数自体です:LOG( EXP( n ) ) = n。 数値の自然対数の指数は数自体です:EXP( LOG( n ) ) = n。  
  
  **例**  
  
  次の例では、変数を宣言し、指定した変数 (10) の対数値を返します。  
  
```  
SELECT LOG(10) AS log  
```  
  
 結果セットは次のようになります。  
  
```  
[{log: 2.3025850929940459}]  
```  
  
 次の例では、数値の指数の LOG を計算します。  
  
```  
SELECT EXP(LOG(10)) AS expLog  
```  
  
 結果セットは次のようになります。  
  
```  
[{expLog: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 指定された数値式の底 10 の対数を返します。  
  
 **構文**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **解説**  
  
  LOG10 関数と POWER 関数は、互いに逆の関係になります。 たとえば、10 ^ log10 (n) = n になります。  
  
  **例**  
  
  次の例では、変数を宣言し、指定した変数 (100) の LOG10 値を返します。  
  
```  
SELECT LOG10(100) AS log10 
```  
  
 結果セットは次のようになります。  
  
```  
[{log10: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 円周率の定数値を返します。  
  
 **構文**  
  
```  
PI ()  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例は、PI の値を返します。  
  
```  
SELECT PI() AS pi 
```  
  
 結果セットは次のようになります。  
  
```  
[{"pi": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> POWER  
 指定されたべき乗の指定された式の値を返します。  
  
 **構文**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
- `y`  
  
   `numeric_expression` のべき乗です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、数値の 3 (数のキューブ) のべき乗数を示します。  
  
```  
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 結果セットは次のようになります。  
  
```  
[{pow1: 8, pow2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> RADIANS  
 数値式が度数単位で入力されると、ラジアンを返します。  
  
 **構文**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、入力としていくつかの角度を取得し、対応するラジアン値を返します。  
  
```  
SELECT RADIANS(-45.01) AS r1, RADIANS(-181.01) AS r2, RADIANS(0) AS r3, RADIANS(0.1472738) AS r4, RADIANS(197.1099392) AS r5  
```  
  
 結果セットは次のようになります。  
  
```  
[{  
       "r1": -0.7855726963226477,  
       "r2": -3.1592204790349356,  
       "r3": 0,  
       "r4": 0.0025704127119236249,  
       "r5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> ROUND  
 最も近い整数値に丸められた数値を返します。  
  
 **構文**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、最も近い整数に次の正と負の数値を丸めます。  
  
```  
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
 結果セットは次のようになります。  
  
```  
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  
  
####  <a name="bk_sign"></a> SIGN  
 指定された数値式の正 (+1)、ゼロ (0)、または負 (-1) の符号を返します。  
  
 **構文**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、-2 ～ 2 の数値の SIGN 値を返します。  
  
```  
SELECT SIGN(-2) AS s1, SIGN(-1) AS s2, SIGN(0) AS s3, SIGN(1) AS s4, SIGN(2) AS s5  
```  
  
 結果セットは次のようになります。  
  
```  
[{s1: -1, s2: -1, s3: 0, s4: 1, s5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 式で指定されたラジアン単位の角度の三角関数サインを返します。  
  
 **構文**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、指定された角度の SIN を計算します。  
  
```  
SELECT SIN(45.175643) AS sin  
```  
  
 結果セットは次のようになります。  
  
```  
[{"sin": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 指定された数値の平方根を返します。  
  
 **構文**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、数値 1 ～ 3 の平方根を返します。  
  
```  
SELECT SQRT(1) AS s1, SQRT(2.0) AS s2, SQRT(3) AS s3  
```  
  
 結果セットは次のようになります。  
  
```  
[{s1: 1, s2: 1.4142135623730952, s3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> SQUARE  
 指定された数値の 2 乗を返します。  
  
 **構文**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、数値 1 ～ 3 の 2 乗を返します。  
  
```  
SELECT SQUARE(1) AS s1, SQUARE(2.0) AS s2, SQUARE(3) AS s3  
```  
  
 結果セットは次のようになります。  
  
```  
[{s1: 1, s2: 4, s3: 9}]  
```  
  
####  <a name="bk_tan"></a> TAN  
 式で指定されたラジアン単位の角度のタンジェントを返します。  
  
 **構文**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例は、PI()/2 のタンジェントを計算します。  
  
```  
SELECT TAN(PI()/2) AS tan 
```  
  
 結果セットは次のようになります。  
  
```  
[{"tan": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> TRUNC  
 最も近い整数値に切り捨てられた数値を返します。  
  
 **構文**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、最も近い整数値に次の正と負の数値を丸めます。  
  
```  
SELECT TRUNC(2.4) AS t1, TRUNC(2.6) AS t2, TRUNC(2.5) AS t3, TRUNC(-2.4) AS t4, TRUNC(-2.6) AS t5  
```  
  
 結果セットは次のようになります。  
  
```  
[{t1: 2, t2: 2, t3: 2, t4: -2, t5: -2}]  
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
  
- `expression`  
  
   任意の有効な式です。  
  
  **戻り値の型**  
  
  ブール式を返します。  
  
  **例**  
  
  次の例では、IS_ARRAY 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null、オブジェクト、配列、および未定義の型をチェックします。  
  
```  
SELECT   
 IS_ARRAY(true) AS isArray1,   
 IS_ARRAY(1) AS isArray2,  
 IS_ARRAY("value") AS isArray3,  
 IS_ARRAY(null) AS isArray4,  
 IS_ARRAY({prop: "value"}) AS isArray5,   
 IS_ARRAY([1, 2, 3]) AS isArray6,  
 IS_ARRAY({prop: "value"}.prop2) AS isArray7  
```  
  
 結果セットは次のようになります。  
  
```  
[{"isArray1":false,"isArray2":false,"isArray3":false,"isArray4":false,"isArray5":false,"isArray6":true,"isArray7":false}]
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 指定した式の型がブール値であるかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **引数**  
  
- `expression`  
  
   任意の有効な式です。  
  
  **戻り値の型**  
  
  ブール式を返します。  
  
  **例**  
  
  次の例では、IS_BOOL 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null、オブジェクト、配列、および未定義の型をチェックします。  
  
```  
SELECT   
    IS_BOOL(true) AS isBool1,   
    IS_BOOL(1) AS isBool2,  
    IS_BOOL("value") AS isBool3,   
    IS_BOOL(null) AS isBool4,  
    IS_BOOL({prop: "value"}) AS isBool5,   
    IS_BOOL([1, 2, 3]) AS isBool6,  
    IS_BOOL({prop: "value"}.prop2) AS isBool7  
```  
  
 結果セットは次のようになります。  
  
```  
[{"isBool1":true,"isBool2":false,"isBool3":false,"isBool4":false,"isBool5":false,"isBool6":false,"isBool7":false}]
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 プロパティに値が代入されているかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **引数**  
  
- `expression`  
  
   任意の有効な式です。  
  
  **戻り値の型**  
  
  ブール式を返します。  
  
  **例**  
  
  次の例では、指定された JSON ドキュメント内のプロパティの存在を確認します。 1 つ目は、"a" が存在するので true を返しますが、2 つ目は "b" が存在しないので false を返します。  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 結果セットは次のようになります。  
  
```  
[{"isDefined1":true,"isDefined2":false}]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 指定した式の型が null であるかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
IS_NULL(<expression>)  
```  
  
 **引数**  
  
- `expression`  
  
   任意の有効な式です。  
  
  **戻り値の型**  
  
  ブール式を返します。  
  
  **例**  
  
  次の例では、IS_NULL 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null、オブジェクト、配列、および未定義の型をチェックします。  
  
```  
SELECT   
    IS_NULL(true) AS isNull1,   
    IS_NULL(1) AS isNull2,  
    IS_NULL("value") AS isNull3,   
    IS_NULL(null) AS isNull4,  
    IS_NULL({prop: "value"}) AS isNull5,   
    IS_NULL([1, 2, 3]) AS isNull6,  
    IS_NULL({prop: "value"}.prop2) AS isNull7  
```  
  
 結果セットは次のようになります。  
  
```  
[{"isNull1":false,"isNull2":false,"isNull3":false,"isNull4":true,"isNull5":false,"isNull6":false,"isNull7":false}]
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 指定した式の型が数値であるかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **引数**  
  
- `expression`  
  
   任意の有効な式です。  
  
  **戻り値の型**  
  
  ブール式を返します。  
  
  **例**  
  
  次の例では、IS_NULL 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null、オブジェクト、配列、および未定義の型をチェックします。  
  
```  
SELECT   
    IS_NUMBER(true) AS isNum1,   
    IS_NUMBER(1) AS isNum2,  
    IS_NUMBER("value") AS isNum3,   
    IS_NUMBER(null) AS isNum4,  
    IS_NUMBER({prop: "value"}) AS isNum5,   
    IS_NUMBER([1, 2, 3]) AS isNum6,  
    IS_NUMBER({prop: "value"}.prop2) AS isNum7  
```  
  
 結果セットは次のようになります。  
  
```  
[{"isNum1":false,"isNum2":true,"isNum3":false,"isNum4":false,"isNum5":false,"isNum6":false,"isNum7":false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT  
 指定した式の型が JSON オブジェクトであるかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **引数**  
  
- `expression`  
  
   任意の有効な式です。  
  
  **戻り値の型**  
  
  ブール式を返します。  
  
  **例**  
  
  次の例では、IS_OBJECT 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null、オブジェクト、配列、および未定義の型をチェックします。  
  
```  
SELECT   
    IS_OBJECT(true) AS isObj1,   
    IS_OBJECT(1) AS isObj2,  
    IS_OBJECT("value") AS isObj3,   
    IS_OBJECT(null) AS isObj4,  
    IS_OBJECT({prop: "value"}) AS isObj5,   
    IS_OBJECT([1, 2, 3]) AS isObj6,  
    IS_OBJECT({prop: "value"}.prop2) AS isObj7  
```  
  
 結果セットは次のようになります。  
  
```  
[{"isObj1":false,"isObj2":false,"isObj3":false,"isObj4":false,"isObj5":true,"isObj6":false,"isObj7":false}]
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 指定した式の型がプリミティブ (文字列、ブール値、数値、または null) であるかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **引数**  
  
- `expression`  
  
   任意の有効な式です。  
  
  **戻り値の型**  
  
  ブール式を返します。  
  
  **例**  
  
  次の例では、IS_PRIMITIVE 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null、オブジェクト、配列、および未定義の型をチェックします。  
  
```  
SELECT   
           IS_PRIMITIVE(true) AS isPrim1,   
           IS_PRIMITIVE(1) AS isPrim2,  
           IS_PRIMITIVE("value") AS isPrim3,   
           IS_PRIMITIVE(null) AS isPrim4,  
           IS_PRIMITIVE({prop: "value"}) AS isPrim5,   
           IS_PRIMITIVE([1, 2, 3]) AS isPrim6,  
           IS_PRIMITIVE({prop: "value"}.prop2) AS isPrim7  
```  
  
 結果セットは次のようになります。  
  
```  
[{"isPrim1": true, "isPrim2": true, "isPrim3": true, "isPrim4": true, "isPrim5": false, "isPrim6": false, "isPrim7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 指定した式の型が文字列であるかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
IS_STRING(<expression>)  
```  
  
 **引数**  
  
- `expression`  
  
   任意の有効な式です。  
  
  **戻り値の型**  
  
  ブール式を返します。  
  
  **例**  
  
  次の例では、IS_STRING 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null、オブジェクト、配列、および未定義の型をチェックします。  
  
```  
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 結果セットは次のようになります。  
  
```  
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  
  
###  <a name="bk_string_functions"></a>文字列関数  
 次のスカラー関数は、文字列入力値に対して演算を実行し、文字列、数値またはブール値を返します。  
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[CONTAINS](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[LEFT](#bk_left)|[LENGTH](#bk_length)|  
|[LOWER](#bk_lower)|[LTRIM](#bk_ltrim)|[REPLACE](#bk_replace)|  
|[REPLICATE](#bk_replicate)|[REVERSE](#bk_reverse)|[RIGHT](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[StringToArray](#bk_stringtoarray)|
|[StringToBoolean](#bk_stringtoboolean)|[StringToNull](#bk_stringtonull)|[StringToNumber](#bk_stringtonumber)|
|[StringToObject](#bk_stringtoobject)|[SUBSTRING](#bk_substring)|[ToString](#bk_tostring)|
|[TRIM](#bk_trim)|[UPPER](#bk_upper)||
  
####  <a name="bk_concat"></a> CONCAT  
 2 つ以上の文字列値を連結した結果である文字列を返します。  
  
 **構文**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例では、指定した値の連結された文字列を返します。  
  
```  
SELECT CONCAT("abc", "def") AS concat  
```  
  
 結果セットは次のようになります。  
  
```  
[{"concat": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> CONTAINS  
 1 つ目の文字列式に 2 つ目の文字列式が含まれているかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  ブール式を返します。  
  
  **例**  
  
  次の例では、"abc" に "ab" と "d" が含まれるかどうかを確認します。  
  
```  
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 結果セットは次のようになります。  
  
```  
[{"c1": true, "c2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 1 つ目の文字列式が 2 つ目の文字列で終了しているかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  ブール式を返します。  
  
  **例**  
  
  次の例では、"b" と "bc" で終わる "abc" を返します。  
  
```  
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 結果セットは次のようになります。  
  
```  
[{"e1": false, "e2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 1 つ目に指定された文字列式内で 2 つ目の文字列式が最初に出現する箇所の開始位置を返します。文字列が見つからない場合は -1 を返します。  
  
 **構文**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、"abc" 内のさまざまな部分文字列のインデックスを返します。  
  
```  
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 結果セットは次のようになります。  
  
```  
[{"i1": 0, "i2": 1, "i3": -1}]  
```  
  
####  <a name="bk_left"></a> LEFT  
 指定された文字数分、文字列の左側の部分を返します。  
  
 **構文**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
- `num_expr`  
  
   任意の有効な数値式です。  
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例では、さまざまな長さの値の "abc" の左側の部分を返します。  
  
```  
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 結果セットは次のようになります。  
  
```  
[{"l1": "a", "l2": "ab"}]  
```  
  
####  <a name="bk_length"></a> LENGTH  
 指定された文字列式の文字数を返します。  
  
 **構文**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例では、文字列の長さを返します。  
  
```  
SELECT LENGTH("abc") AS len 
```  
  
 結果セットは次のようになります。  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_lower"></a> LOWER  
 文字列式の大文字データを小文字に変換して返します。  
  
 **構文**  
  
```  
LOWER(<str_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例は、クエリ内での LOWER の使用方法を示しています。  
  
```  
SELECT LOWER("Abc") AS lower
```  
  
 結果セットは次のようになります。  
  
```  
[{"lower": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> LTRIM  
 文字列式の先頭の空白を削除して返します。  
  
 **構文**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例は、クエリ内での LTRIM の使用方法を示しています。  
  
```  
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 結果セットは次のようになります。  
  
```  
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> REPLACE  
 指定された文字列値のすべての出現箇所をもう 1 つの文字列値に置き換えます。  
  
 **構文**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例は、クエリ内での REPLACE の使用方法を示しています。  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 結果セットは次のようになります。  
  
```  
[{"replace": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLICATE  
 文字列値を指定された回数だけ繰り返します。  
  
 **構文**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
- `num_expr`  
  
   任意の有効な数値式です。 num_expr が負の数値である場合、または有限の数値でない場合、結果は未定義になります。

  > [!NOTE]
  > 結果の最大長は 10,000 文字、つまり (length(str_expr) * num_expr) <= 10,000 です。
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例は、クエリ内での REPLICATE の使用方法を示しています。  
  
```  
SELECT REPLICATE("a", 3) AS replicate  
```  
  
 結果セットは次のようになります。  
  
```  
[{"replicate": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> REVERSE  
 文字列値の順序を逆にして返します。  
  
 **構文**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例は、クエリ内での REVERSE の使用方法を示しています。  
  
```  
SELECT REVERSE("Abc") AS reverse  
```  
  
 結果セットは次のようになります。  
  
```  
[{"reverse": "cbA"}]  
```  
  
####  <a name="bk_right"></a> RIGHT  
 指定された文字数分、文字列の右側の部分を返します。  
  
 **構文**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
- `num_expr`  
  
   任意の有効な数値式です。  
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例では、さまざまな長さの値の "abc" の右側の部分を返します。  
  
```  
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 結果セットは次のようになります。  
  
```  
[{"r1": "c", "r2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> RTRIM  
 文字列式の末尾の空白を削除して返します。  
  
 **構文**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例は、クエリ内での RTRIM の使用方法を示しています。  
  
```  
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 結果セットは次のようになります。  
  
```  
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 1 つ目の文字列式が 2 つ目の文字列で始まっているかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  ブール式を返します。  
  
  **例**  
  
  次の例は、文字列 "abc" が "b" および "a" で始まるかどうかを確認します。  
  
```  
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 結果セットは次のようになります。  
  
```  
[{"s1": false, "s2": true}]  
```  

  ####  <a name="bk_stringtoarray"></a> StringToArray  
 配列に変換された式を返します。 式を変換できない場合は、undefined を返します。  
  
 **構文**  
  
```  
StringToArray(<expr>)  
```  
  
 **引数**  
  
- `expr`  
  
   これは、JSON 配列式として評価される有効なスカラー式です。 入れ子になった文字列値を有効にするには二重引用符で囲む必要があることにご注意ください。 JSON 形式の詳細については、「[json.org](https://json.org/)」をご覧ください。
  
  **戻り値の型**  
  
  配列式または undefined を返します。  
  
  **例**  
  
  次の例では、異なる型間で StringToArray がどのように動作するかを示します。 
  
 有効な入力を使用した例を次に示します。

```
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

 結果セットは次のようになります。

```
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

 無効な入力を使用した例を次に示します。 
   
 配列内に一重引用符を使用した場合は、有効な JSON ではありません。
クエリ内で有効であっても、有効な配列として解析されません。 配列文字列内の文字列は、"[\\"\\"]" のようにエスケープするか、'[""]' のように一重引用符で囲む必要があります。

```
SELECT
    StringToArray("['5','6','7']")
```

 結果セットは次のようになります。

```
[{}]
```

 無効な入力の例を次に示します。
   
 渡された式は JSON 配列として解析されます。次の場合は、配列型として評価されないため、undefined が返されます。
   
```
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

 結果セットは次のようになります。

```
[{}]
```

####  <a name="bk_stringtoboolean"></a> StringToBoolean  
 ブール値に変換された式を返します。 式を変換できない場合は、undefined を返します。  
  
 **構文**  
  
```  
StringToBoolean(<expr>)  
```  
  
 **引数**  
  
- `expr`  
  
   ブール式として評価される有効なスカラー式です。  
  
  **戻り値の型**  
  
  ブール式または undefined を返します。  
  
  **例**  
  
  次の例では、異なる型間で StringToBoolean がどのように動作するかを示します。 
 
 有効な入力を使用した例を次に示します。

 空白は "true" または "false" の前後のみで使用できます。

```  
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 結果セットは次のようになります。  
  
```  
[{"b1": true, "b2": false, "b3": false}]
```  

 無効な入力を使用した例を次に示します。
 
 ブール値では大文字と小文字が区別されるので、すべて小文字で記述する必要があります (つまり、"true" と "false")。

```  
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

 結果セットは次のようになります。  
  
```  
[{}]
``` 

 渡された式はブール式として解析されます。これらの入力はブール型として評価されないため、undefined が返されます。

 ```  
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

 結果セットは次のようになります。  
  
```  
[{}]
```  

####  <a name="bk_stringtonull"></a> StringToNull  
 null 値に変換された式を返します。 式を変換できない場合は、undefined を返します。  
  
 **構文**  
  
```  
StringToNull(<expr>)  
```  
  
 **引数**  
  
- `expr`  
  
   null 式として評価される有効なスカラー式です。
  
  **戻り値の型**  
  
  null 式または undefined を返します。  
  
  **例**  
  
  次の例では、異なる型間で StringToNull がどのように動作するかを示します。 

 有効な入力を使用した例を次に示します。
 
 空白は "null" の前後のみで使用できます。

```  
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 結果セットは次のようになります。  
  
```  
[{"n1": null, "n2": null, "n3": true}]
```  

 無効な入力を使用した例を次に示します。

 null 値では大文字と小文字が区別されるので、すべて小文字で記述する必要があります (つまり、"null")。

```  
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 結果セットは次のようになります。  
  
```  
[{}]
```  

 渡された式は null 式として解析されます。これらの入力は null 型として評価されないため、undefined が返されます。

```  
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 結果セットは次のようになります。  
  
```  
[{}]
```  

####  <a name="bk_stringtonumber"></a> StringToNumber  
 数値に変換された式を返します。 式を変換できない場合は、undefined を返します。  
  
 **構文**  
  
```  
StringToNumber(<expr>)  
```  
  
 **引数**  
  
- `expr`  
  
   JSON 数値式として評価される有効なスカラー式です。 JSON の数値は整数または浮動小数点にする必要があります。 JSON 形式の詳細については、「[json.org](https://json.org/)」をご覧ください。  
  
  **戻り値の型**  
  
  数値式または undefined を返します。  
  
  **例**  
  
  次の例では、異なる型間で StringToNumber がどのように動作するかを示します。 

 空白は数値の前後のみで使用できます。
 
```  
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 結果セットは次のようになります。  
  
```  
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

 JSON で有効な数値は、整数または浮動小数点数である必要があります。
 
```  
SELECT   
    StringToNumber("0xF")
```  
  
 結果セットは次のようになります。  
  
```  
{{}}
```  

 渡された式は数値式として解析されます。これらの入力は数値型として評価されないため、undefined が返されます。 

```  
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 結果セットは次のようになります。  
  
```  
{{}}
```  

####  <a name="bk_stringtoobject"></a> StringToObject  
 オブジェクトに変換された式を返します。 式を変換できない場合は、undefined を返します。  
  
 **構文**  
  
```  
StringToObject(<expr>)  
```  
  
 **引数**  
  
- `expr`  
  
   JSON オブジェクト式として評価される有効なスカラー式です。 入れ子になった文字列値を有効にするには二重引用符で囲む必要があることにご注意ください。 JSON 形式の詳細については、「[json.org](https://json.org/)」をご覧ください。  
  
  **戻り値の型**  
  
  オブジェクト式または undefined を返します。  
  
  **例**  
  
  次の例では、異なる型間で StringToObject がどのように動作するかを示します。 
  
 有効な入力を使用した例を次に示します。
 
``` 
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

 結果セットは次のようになります。

```
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```
 
 無効な入力を使用した例を次に示します。
クエリ内では有効であっても、有効なオブジェクトとして解析されません。 オブジェクト文字列内の文字列は、"{\\"a\\":\\"str\\"}" のようにエスケープするか、'{"a": "str"}' のように一重引用符で囲む必要があります。

 一重引用符で囲んだプロパティ名は有効な JSON ではありません。

``` 
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

 結果セットは次のようになります。

```  
[{}]
```  

 引用符で囲まれていないプロパティ名は有効な JSON ではありません。

``` 
SELECT 
    StringToObject("{a:[1,2,3]}")
```

 結果セットは次のようになります。

```  
[{}]
``` 

 無効な入力を使用した例を次に示します。
 
 渡された式は JSON オブジェクトとして解析されます。これらの入力はオブジェクト型として評価されないため、undefined が返されます。
 
``` 
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 結果セットは次のようになります。

```
[{}]
```

####  <a name="bk_substring"></a> SUBSTRING  
 指定された文字のゼロベースの位置で始まる文字列式の一部を返し、指定された長さまたは文字列の末尾まで続きます。  
  
 **構文**  
  
```  
SUBSTRING(<str_expr>, <num_expr>, <num_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
- `num_expr`  
  
   開始および終了文字を示す任意の有効な数値式です。    
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例では、1 から始まる 1 文字の長さの "abc" の部分文字列を返します。  
  
```  
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 結果セットは次のようになります。  
  
```  
[{"substring": "b"}]  
```  
####  <a name="bk_tostring"></a> ToString  
 スカラー式の文字列表現を返します。 
  
 **構文**  
  
```  
ToString(<expr>)
```  
  
 **引数**  
  
- `expr`  
  
   任意の有効なスカラー式です。  
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例では、異なる型間で ToString がどのように動作するかを示します。   
  
```  
SELECT 
    ToString(1.0000) AS str1, 
    ToString("Hello World") AS str2, 
    ToString(NaN) AS str3, 
    ToString(Infinity) AS str4,
    ToString(IS_STRING(ToString(undefined))) AS str5, 
    ToString(0.1234) AS str6, 
    ToString(false) AS str7, 
    ToString(undefined) AS str8
```  
  
 結果セットは次のようになります。  
  
```  
[{"str1": "1", "str2": "Hello World", "str3": "NaN", "str4": "Infinity", "str5": "false", "str6": "0.1234", "str7": "false"}]  
```  
 次のような入力があるものとします。
```  
{"Products":[{"ProductID":1,"Weight":4,"WeightUnits":"lb"},{"ProductID":2,"Weight":32,"WeightUnits":"kg"},{"ProductID":3,"Weight":400,"WeightUnits":"g"},{"ProductID":4,"Weight":8999,"WeightUnits":"mg"}]}
```    
 次の例は、CONCAT などの他の文字列関数を ToString とどのように一緒に使用できるかを示しています。   
 
```  
SELECT 
CONCAT(ToString(p.Weight), p.WeightUnits) 
FROM p in c.Products 
```  

 結果セットは次のようになります。  
  
```  
[{"$1":"4lb" },
 {"$1":"32kg"},
 {"$1":"400g" },
 {"$1":"8999mg" }]

```  
次のような入力があるものとします。
```
{"id":"08259","description":"Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX","nutrients":[{"id":"305","description":"Caffeine","units":"mg"},{"id":"306","description":"Cholesterol, HDL","nutritionValue":30,"units":"mg"},{"id":"307","description":"Sodium, NA","nutritionValue":612,"units":"mg"},{"id":"308","description":"Protein, ABP","nutritionValue":60,"units":"mg"},{"id":"309","description":"Zinc, ZN","nutritionValue":null,"units":"mg"}]}
```
 次の例は、REPLACE などの他の文字列関数を ToString とどのように一緒に使用できるかを示しています。   
```
SELECT 
    n.id AS nutrientID,
    REPLACE(ToString(n.nutritionValue), "6", "9") AS nutritionVal
FROM food 
JOIN n IN food.nutrients
```
 結果セットは次のようになります。  
 ```
[{"nutrientID":"305"},
{"nutrientID":"306","nutritionVal":"30"},
{"nutrientID":"307","nutritionVal":"912"},
{"nutrientID":"308","nutritionVal":"90"},
{"nutrientID":"309","nutritionVal":"null"}]
 ``` 
 
####  <a name="bk_trim"></a> TRIM  
 文字列式の先頭と末尾の空白を削除して返します。  
  
 **構文**  
  
```  
TRIM(<str_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例は、クエリ内での TRIM の使用方法を示しています。  
  
```  
SELECT TRIM("   abc") AS t1, TRIM("   abc   ") AS t2, TRIM("abc   ") AS t3, TRIM("abc") AS t4
```  
  
 結果セットは次のようになります。  
  
```  
[{"t1": "abc", "t2": "abc", "t3": "abc", "t4": "abc"}]  
``` 
####  <a name="bk_upper"></a> UPPER  
 文字列式の小文字データを大文字に変換して返します。  
  
 **構文**  
  
```  
UPPER(<str_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例は、クエリ内での UPPER の使用方法を示しています。  
  
```  
SELECT UPPER("Abc") AS upper  
```  
  
 結果セットは次のようになります。  
  
```  
[{"upper": "ABC"}]  
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
  
- `arr_expr`  
  
   任意の有効な配列式です。  
  
  **戻り値の型**  
  
  配列式を返します。  
  
  **例**  
  
  次の例では、2 つの配列を結合する方法を示します。  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 結果セットは次のようになります。  
  
```  
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a> ARRAY_CONTAINS  
配列に指定された値が含まれているかどうかを示すブール値を返します。 コマンド内でブール式を使用して、オブジェクトの部分一致または完全一致を確認できます。 

 **構文**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **引数**  
  
- `arr_expr`  
  
   任意の有効な配列式です。  
  
- `expr`  
  
   任意の有効な式です。  

- `bool_expr`  
  
   任意のブール式です。 'true' に設定されていて、指定された検索値がオブジェクトである場合、このコマンドで部分一致が確認されます (検索オブジェクトは、いずれかのオブジェクトのサブセットです)。 'false' に設定されている場合、このコマンドで配列内のすべてのオブジェクトの完全一致が確認されます。 指定しない場合の既定値は false です。 
  
  **戻り値の型**  
  
  ブール値を返します。  
  
  **例**  
  
  次の例では、ARRAY_CONTAINS を使用して、配列内のメンバーシップを確認する方法を示します。  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 結果セットは次のようになります。  
  
```  
[{"b1": true, "b2": false}]  
```  

 次の例では、ARRAY_CONTAINS を使用して、配列内 JSON の部分一致を確認する方法を示します。  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 結果セットは次のようになります。  
  
```  
[{
  "b1": true,
  "b2": false,
  "b3": false
}] 
```  
  
####  <a name="bk_array_length"></a> ARRAY_LENGTH  
 指定された配列式の要素の数を返します。  
  
 **構文**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **引数**  
  
- `arr_expr`  
  
   任意の有効な配列式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、ARRAY_LENGTH を使用して、配列の長さを取得する方法を示します。  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 結果セットは次のようになります。  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_array_slice"></a> ARRAY_SLICE  
 配列式の一部を返します。
  
 **構文**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **引数**  
  
- `arr_expr`  
  
   任意の有効な配列式です。  
  
- `num_expr`  
  
   配列を開始する位置を示す 0 から始まる数値インデックス。 配列内の最後の要素を基準とした開始インデックスを指定するために負の値が使用されることがあり、つまり、-1 は配列の最後の要素を参照します。  

- `num_expr`  

   結果の配列内の要素の最大数。    

  **戻り値の型**  
  
  配列式を返します。  
  
  **例**  
  
  次の例では、ARRAY_SLICE を使用して、配列の別のスライスを取得する方法を示します。  
  
```  
SELECT   
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 結果セットは次のようになります。  
  
```  
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  
 
###  <a name="bk_spatial_functions"></a>空間関数  
 次のスカラー関数は、空間オブジェクト入力値に対して演算を実行し、数値またはブール値を返します。  
  
|||||
|-|-|-|-|
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)||||
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 2 つの GeoJSON Point、Polygon、または LineString 式間の距離を返します。  
  
 **構文**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **引数**  
  
- `spatial_expr`  
  
   有効な GeoJSON ポイント、多角形、または LineString オブジェクト式です。  
  
  **戻り値の型**  
  
  距離を含む数値式を返します。 これは、既定の参照システムのメートル単位で表されます。  
  
  **例**  
  
  次の例では、指定された場所の 30 km 圏内に存在するすべての世帯ドキュメントを ST_DISTANCE 組み込み関数で取得する方法を示します。 。  
  
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
  
- `spatial_expr`  
  
   有効な GeoJSON ポイント、多角形、または LineString オブジェクト式です。  
 
- `spatial_expr`  
  
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
  
- `spatial_expr`  
  
   有効な GeoJSON ポイント、多角形、または LineString オブジェクト式です。  
 
- `spatial_expr`  
  
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
  
- `spatial_expr`  
  
   有効な GeoJSON ポイント、多角形、または LineString 式です。  
  
  **戻り値の型**  
  
  ブール式を返します。  
  
  **例**  
  
  次の例では、ST_VALID を使用してポイントが有効かどうかを確認する方法を示します。  
  
  たとえば、このポイントの緯度の値は有効な値の範囲 [-90, 90] に含まれていないので、クエリは false を返します。  
  
  GeoJSON 仕様では、最後に指定された座標ペアが、最初に指定された座標ペアとちょうど重なり、閉じた形状になっていることが、ポリゴンの条件となります。 ポリゴン内のポイントは、反時計回りに指定する必要があります。 時計回りに指定されたポリゴンは、その中の領域を逆にしたものを表します。  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 結果セットは次のようになります。  
  
```  
[{ "b": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 指定された GeoJSON Point、Polygon、または LineString 式が有効であるかどうかのブール値を含んだ JSON 値を返します。無効である場合はさらに、その理由が文字列値として返されます。  
  
 **構文**  
  
```  
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
 **引数**  
  
- `spatial_expr`  
  
   有効な GeoJSON ポイントまたはポリゴン式です。  
  
  **戻り値の型**  
  
  指定された GeoJSON ポイントまたはポリゴン式が有効であるかどうかのブール値を含んだ JSON 値を返します。無効である場合はさらに、その理由が文字列値として返されます。  
  
  **例**  
  
  次の例では、ST_ISVALIDDETAILED を使用して有効性 (詳細) を確認する方法を示します。  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b  
```  
  
 結果セットは次のようになります。  
  
```  
[{  
  "b": {   
    "valid": false,   
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  
  
## <a name="next-steps"></a>次の手順  

- [Cosmos DB の SQL 構文と SQL クエリ](how-to-sql-query.md)

- [Cosmos DB のドキュメント](https://docs.microsoft.com/azure/cosmos-db/)  
