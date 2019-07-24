---
title: Azure Service Bus の SQLFilter 構文リファレンス | Microsoft Docs
description: SQLFilter の文法について詳しく説明します。
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2018
ms.author: spelluru
ms.openlocfilehash: e490c7c24ed38e2988c1f097b09b508746f08178
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58118137"
---
# <a name="sqlfilter-syntax"></a>SQLFilter 構文

*SqlFilter* オブジェクトは [SqlFilter クラス](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)のインスタンスであり、[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) に対して評価される SQL 言語ベースのフィルター式を表します。 SqlFilter では、SQL-92 標準のサブセットをサポートします。  
  
 このトピックでは、SqlFilter の文法について詳しく説明します。  
  
```  
<predicate ::=  
      { NOT <predicate> }  
      | <predicate> AND <predicate>  
      | <predicate> OR <predicate>  
      | <expression> { = | <> | != | > | >= | < | <= } <expression>  
      | <property> IS [NOT] NULL  
      | <expression> [NOT] IN ( <expression> [, ...n] )  
      | <expression> [NOT] LIKE <pattern> [ESCAPE <escape_char>]  
      | EXISTS ( <property> )  
      | ( <predicate> )  
  
```  
  
```  
<expression> ::=  
      <constant>   
      | <function>  
      | <property>  
      | <expression> { + | - | * | / | % } <expression>  
      | { + | - } <expression>  
      | ( <expression> )  
  
```  
  
```  
<property> :=   
       [<scope> .] <property_name>  
  
```  
  
## <a name="arguments"></a>引数  
  
-   `<scope>` は、`<property_name>` のスコープを示す省略可能な文字列です。 有効な値は、`sys` または `user` です。 `sys` 値は、`<property_name>` が [BrokeredMessage クラス](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)のパブリック プロパティ名である場合にシステム スコープを示します。 `user` は、`<property_name>` が [BrokeredMessage クラス](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)のディクショナリのキーである場合にユーザー スコープを示します。 `<scope>` が指定されていない場合、`user` スコープが既定のスコープです。  
  
## <a name="remarks"></a>解説

存在しないシステム プロパティにアクセスしようとするとエラーになりますが、存在しないユーザー プロパティにアクセスしようとしてもエラーにはなりません。 代わりに、存在しないユーザー プロパティは不明な値として内部的に評価されます。 不明な値は演算子の評価時に特別に処理されます。  
  
## <a name="propertyname"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>引数  

 `<regular_identifier>` は、次の正規表現で表される文字列です。  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
これは、文字で始まり、その後に 1 つ以上のアンダースコア/文字/数字が続くことを意味します。  
  
`[:IsLetter:]` は、Unicode の文字として分類される任意の Unicode 文字を表します。 `c` が Unicode の文字の場合、`System.Char.IsLetter(c)` は `true` を返します。  
  
`[:IsDigit:]` は、10 進数として分類される任意の Unicode 文字を表します。 `c` が Unicode の数字の場合、`System.Char.IsDigit(c)` は `true` を返します。  
  
`<regular_identifier>` に予約済みのキーワードを指定することはできません。  
  
`<delimited_identifier>` は、左右の各かっこ ([]) で囲まれた任意の文字列です。 右角かっこは 2 つの右角かっこで表されます。 `<delimited_identifier>` の例を次に示します。  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>` は、二重引用符で囲まれた任意の文字列です。 識別子の二重引用符は 2 つの二重引用符で表されます。 引用符で囲まれた識別子は、文字列定数と混同されやすい可能性があるので使用しないことをお勧めします。 可能であれば、区切られた識別子を使用してください。 `<quoted_identifier>` の例を次に示します。  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>pattern  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>解説
  
`<pattern>` は、文字列として評価される式である必要があります。 これは LIKE 演算子のパターンとして使用されます。      次のワイルドカード文字を含めることができます。  
  
-   `%`:0 個以上の文字から成る任意の文字列。  
  
-   `_`:任意の 1 文字を表します。  
  
## <a name="escapechar"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>解説  

`<escape_char>` は、長さ 1 の文字列として評価される式である必要があります。 これは、LIKE 演算子のエスケープ文字として使用されます。  
  
 たとえば、`property LIKE 'ABC\%' ESCAPE '\'` は、`ABC` で始まる文字列ではなく、`ABC%` と一致します。  
  
## <a name="constant"></a>定数  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>引数  
  
-   `<integer_constant>` は、引用符で囲まれておらず、小数点が含まれていない数値の文字列です。 値は内部的に `System.Int64` として格納され、同じ範囲に従います。  
  
     長い定数の例を次に示します。  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` は、引用符で囲まれておらず、小数点が含まれた数値の文字列です。 値は内部的に `System.Double` として格納され、同じ範囲/有効桁数に従います。  
  
     今後のバージョンでは、この数値は正確な数値セマンティクスをサポートする別のデータ型で格納される可能性があります。そのため、`<decimal_constant>` の基になるデータ型が `System.Double` であることに依存しないでください。  
  
     10 進定数の例を次に示します。  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` は、科学的表記法で記述された数値です。 値は内部的に `System.Double` として格納され、同じ範囲/有効桁数に従います。 概数定数の例を次に示します。  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="booleanconstant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>解説  

ブール型の定数は、**TRUE** または **FALSE** キーワードで表されます。 値は `System.Boolean` として格納されます。  
  
## <a name="stringconstant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>解説  

文字列定数は単一引用符で囲まれ、任意の有効な Unicode 文字が含まれます。 文字列定数に組み込む単一引用符は、2 つの単一引用符で表されます。  
  
## <a name="function"></a>function  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>解説
  
`newid()` 関数は、`System.Guid.NewGuid()` メソッドによって生成された **System.Guid** を返します。  
  
`property(name)` 関数は、`name` によって参照されるプロパティの値を返します。 `name` 値には、文字列値を返す任意の有効な式を指定できます。  
  
## <a name="considerations"></a>考慮事項
  
次の [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) セマンティクスを考慮してください。  
  
-   プロパティ名では大文字と小文字が区別されます。  
  
-   可能であれば、演算子は C# の暗黙的な変換セマンティクスに従います。  
  
-   システム プロパティは、[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) インスタンスで公開されるパブリック プロパティです。  
  
    次の `IS [NOT] NULL` セマンティクスを考慮してください。  
  
    -   プロパティが存在しないか、プロパティの値が `null` の場合、`property IS NULL` は `true` として評価されます。  
  
### <a name="property-evaluation-semantics"></a>プロパティ評価セマンティクス  
  
- 存在しないシステム プロパティを評価しようとすると、[FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) 例外がスローされます。  
  
- 存在しないプロパティは、内部的に **unknown** として評価されます。  
  
  算術演算子での unknown 評価は次のとおりです。  
  
- 2 項演算子では、左側または右側あるいは左右のオペランドが **unknown** と評価された場合、結果は **unknown** になります。  
  
- 単項演算子では、オペランドが **unknown** と評価された場合、結果は **unknown** になります。  
  
  2 項比較演算子での unknown 評価は次のとおりです。  
  
- 左側または右側あるいは左右のオペランドが **unknown** と評価された場合、結果は **unknown** になります。  
  
  `[NOT] LIKE` での unknown 評価は次のとおりです。  
  
- いずれかのオペランドが **unknown** と評価された場合、結果は **unknown** になります。  
  
  `[NOT] IN` での unknown 評価は次のとおりです。  
  
- 左側のオペランドが **unknown** と評価された場合、結果は **unknown** になります。  
  
  **AND** 演算子での unknown 評価は次のとおりです。  
  
```  
+---+---+---+---+  
|AND| T | F | U |  
+---+---+---+---+  
| T | T | F | U |  
+---+---+---+---+  
| F | F | F | F |  
+---+---+---+---+  
| U | U | F | U |  
+---+---+---+---+  
```  
  
 **OR** 演算子での unknown 評価は次のとおりです。  
  
```  
+---+---+---+---+  
|OR | T | F | U |  
+---+---+---+---+  
| T | T | T | T |  
+---+---+---+---+  
| F | T | F | U |  
+---+---+---+---+  
| U | T | U | U |  
+---+---+---+---+  
```  
  
### <a name="operator-binding-semantics"></a>演算子結合セマンティクス
  
-   比較演算子 (`>`、`>=`、`<`、`<=`、`!=`、`=` など) は、データ型の上位変換および暗黙的な変換で C# 演算子の結合と同じセマンティクスに従います。  
  
-   算術演算子 (`+`、`-`、`*`、`/`、`%` など) は、データ型の上位変換および暗黙的な変換で C# 演算子の結合と同じセマンティクスに従います。

## <a name="next-steps"></a>次の手順

- [SQLFilter クラス (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [SQLFilter クラス (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [SQLRuleAction クラス](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
