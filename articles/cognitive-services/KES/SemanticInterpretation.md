---
title: セマンティック解釈 - Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Knowledge Exploration Service (KES) API でセマンティック解釈を使用する方法について説明します。
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 26f8d885f8cf85ab849ba221392df206e492aac4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860815"
---
# <a name="semantic-interpretation"></a>セマンティック解釈

セマンティック解釈は、文法を介して、意味の出力を解釈された各パスに関連付けます。  具体的には、このサービスは、解釈によって走査された `tag` 要素内のステートメントのシーケンスを評価して最終出力を計算します。  

ステートメントは、リテラルの割り当てや、別の変数への変数になる場合があります。  また、0 または複数のパラメーターが指定された関数の出力が変数に割り当てられる場合もあります。  各関数パラメーターは、リテラルまたは変数を使用して指定できます。  関数が出力を返さない場合、割り当ては省略されます。

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

変数は、英字で始まり、英字 (A から Z)、数字 (0 から 9)、およびアンダースコア (\_) のみで構成される名前識別子を使用して指定されます。  その型は、割り当てられたリテラルまたは関数の出力値から暗黙的に推定されます。 

現在サポートされているデータ型の一覧を次に示します。

|type|説明|例|
|----|----|----|
|String|0 文字以上のシーケンス|"Hello World!"<br/>""|
|Bool|ブール値|true<br/>false|
|Int32|32 ビット符号付き整数  -2.1e9 から 2.1e9|123<br/>-321|
|Int64|64 ビット符号付き整数 -9.2e18 と 9.2e18|9876543210|
|Double|倍精度浮動小数点数。 1.7e+/-308 (15 桁)|123.456789<br/>1.23456789e2|
|Guid|グローバル一意識別子|"602DD052-CC47-4B23-A16A-26B52D30C05B"|
|Query|インデックス内のデータ オブジェクトのサブセットを指定するクエリ式|All()<br/>And(*q1*, *q2*)|

## <a name="semantic-functions"></a>セマンティック関数

組み込みの一連のセマンティック関数があります。  高度なクエリを構築し、状況に応じて文法解釈を制御できます。

### <a name="and-function"></a>And 関数

`query = And(query1, query2);`

2 つの入力クエリの共通集合から構成されるクエリを返します。

### <a name="or-function"></a>Or 関数

`query = Or(query1, query2);`

2 つの入力クエリの和集合から構成されるクエリを返します。

### <a name="all-function"></a>All 関数

`query = All();`

すべてのデータ オブジェクトを含むクエリを返します。

次の例では、All() 関数を使用して、1 つまたは複数のキーワードの共通集合に基づいてクエリを繰り返し構築します。

```
<tag>query = All();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = And(query, keyword);</tag>
</item>
```

### <a name="none-function"></a>None 関数

`query = None();`

データ オブジェクトを含まないクエリを返します。

次の例では、None() 関数を使用して、1 つまたは複数のキーワードの和集合に基づいてクエリを繰り返し構築します。

```
<tag>query = None();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = Or(query, keyword);</tag>
</item>
```

### <a name="query-function"></a>Query 関数

```
query = Query(attrName, value)
query = Query(attrName, value, op)
```

指定された操作 *op* に従って属性 *attrName* が値 *value* と一致するデータ オブジェクトのみを含むクエリを返します。既定値は "eq" です。  通常、`attrref` 要素を使用して、一致する入力クエリ文字列に基づいてクエリを作成します。  値が他の方法で指定または取得されると、Query() 関数を使用してこの値と一致するクエリを作成できます。

次の例では、Query() 関数を使用して、特定の 10 年に発行された学術的刊行物を指定するためのサポートを実装しています。

```xml
written in the 90s
<tag>
  beginYear = Query("academic#Year", 1990, "ge");
  endYear = Query("academic#Year", 2000, "lt");
  year = And(beginYear, endYear);
</tag>
```

### <a name="composite-function"></a>Composite 関数

`query = Composite(innerQuery);`

共通複合属性 *attr* のサブ属性に対する一致で構成される *innerQuery* をカプセル化するクエリを返します。  カプセル化では、一致するデータ オブジェクトの複合属性 *attr* に、*innerQuery* を個別に満たす値が少なくとも 1 つ存在する必要があります。  複合属性のサブ属性に対するクエリは、他のクエリと組み合わせる前に Composite() 関数を使用してカプセル化する必要があります。

たとえば、次のクエリは、"harry shum" が "microsoft" に在籍時に著した学術的刊行物を返します。
```
Composite(And(Query("academic#Author.Name", "harry shum"), 
              Query("academic#Author.Affiliation", "microsoft")));
```

一方、次のクエリは、著者の 1 人が "harry shum" であり、所属のいずれかが "microsoft" である学術的刊行物を返します。
```
And(Composite(Query("academic#Author.Name", "harry shum"), 
    Composite(Query("academic#Author.Affiliation", "microsoft")));
```

### <a name="getvariable-function"></a>GetVariable 関数

`value = GetVariable(name, scope);`

指定された *scope* で定義された変数 *name* の値を返します。  *name* は、英字で始まり、英字 (A から Z)、数字 (0 から 9)、およびアンダースコア (_) のみで構成される識別子です。  *スコープ* は "request" または "system" に設定できます。  異なるスコープで定義された変数は、セマンティック関数の出力を介して定義されたものを含め、相互に区別されることに注意してください。

要求の scope 変数は、現在の解釈要求内のすべての解釈全体で共有されます。  文法に関する解釈の検索を制御するために使用できます。

システム変数はサービスによって事前に定義されており、システムの現在の状態に関するさまざまな統計情報を取得するために使用できます。  現在サポートされている一連のシステム変数を次に示します。

|Name|type|説明|
|----|----|----|
|IsAtEndOfQuery|Bool|現在の解釈がすべての入力クエリ テキストと一致した場合は true|
|IsBeyondEndOfQuery|Bool|現在の解釈に、入力クエリ テキスト以外の提案された入力候補がある場合は true|

### <a name="setvariable-function"></a>SetVariable 関数

`SetVariable(name, value, scope);`

指定された *scope* で変数 *name* に *value* を割り当てます。  *name* は、英字で始まり、英字 (A から Z)、数字 (0 から 9)、およびアンダースコア (_) のみで構成される識別子です。  現在、*scope* の唯一の有効な値は "request" です。  設定可能なシステム変数はありません。

要求の scope 変数は、現在の解釈要求内のすべての解釈全体で共有されます。  文法に関する解釈の検索を制御するために使用できます。

### <a name="assertequals-function"></a>AssertEquals 関数

`AssertEquals(value1, value2);`

*value1* と *value2* が等しい場合、関数は成功します。副作用はありません。  それ以外の場合、関数は失敗し、解釈を拒否します。

### <a name="assertnotequals-function"></a>AssertNotEquals 関数

`AssertNotEquals(value1, value2);`

*value1* と *value2* が等しくない場合、関数は成功します。副作用はありません。  それ以外の場合、関数は失敗し、解釈を拒否します。


