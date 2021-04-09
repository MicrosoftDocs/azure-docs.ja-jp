---
title: Azure Service Bus サブスクリプション ルールの SQL アクション構文 |Microsoft Docs
description: この記事では、SQL ルールのアクション構文のリファレンスを示します。 アクションは、メッセージに対して実行される SQL 言語ベースの構文で記述されています。
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: 75ff437bace59d7f4de07342277f0760480a5b0f
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652838"
---
# <a name="subscription-rule-sql-action-syntax"></a>サブスクリプション ルールの SQL アクション構文

"*SQL アクション*" は、メッセージがサブスクリプション ルールのフィルターによって選択された後にメッセージ メタデータを操作するために使用されます。 これは、SQL-92 標準のサブセットに基づくテキスト式です。 アクション式は、[Azure Resource Manager テンプレート](service-bus-resource-manager-namespace-topic-with-rule.md)内の Service Bus `Rule` の "action" プロパティの `sqlExpression` 要素、または Azure CLI `az servicebus topic subscription rule create` コマンドの [`--action-sql-expression`](/cli/azure/servicebus/topic/subscription/rule#az_servicebus_topic_subscription_rule_create) 引数、およびサブスクリプション ルールの管理を可能にするいくつかの SDK 関数で使用されます。
  
  
```  
<statements> ::=
    <statement> [, ...n]  
  
```  
  
```  
<statement> ::=
    <action> [;]
    Remarks
    -------
    Semicolon is optional.  
  
```  
  
```  
<action> ::=
    SET <property> = <expression>
    REMOVE <property>  
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
  
-   `<scope>` は、`<property_name>` のスコープを示す省略可能な文字列です。 有効な値は `sys` または `user`です。 `sys` 値は、`<property_name>` が [BrokeredMessage クラス](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)のパブリック プロパティ名である場合にシステム スコープを示します。 `user` は、`<property_name>` が [BrokeredMessage クラス](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)のディクショナリのキーである場合にユーザー スコープを示します。 `<scope>` が指定されていない場合、`user` スコープが既定のスコープです。  
  
### <a name="remarks"></a>解説  

存在しないシステム プロパティにアクセスしようとするとエラーになりますが、存在しないユーザー プロパティにアクセスしようとしてもエラーにはなりません。 代わりに、存在しないユーザー プロパティは不明な値として内部的に評価されます。 不明な値は演算子の評価時に特別に処理されます。  
  
## <a name="property_name"></a>property_name  
  
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
  
 `<regular_identifier>` に予約キーワードを指定することはできません。  
  
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
  
-   `%`: 0 個以上の文字から成る任意の文字列。  
  
-   `_`: 1 つの任意の文字。  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>解説
  
 `<escape_char>` は、長さ 1 の文字列として評価される式である必要があります。 これは、LIKE 演算子のエスケープ文字として使用されます。  
  
 たとえば、`property LIKE 'ABC\%' ESCAPE '\'` は、`ABC` で始まる文字列ではなく、`ABC%` と一致します。  
  
## <a name="constant"></a>定数 (constant)  
  
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
  
## <a name="boolean_constant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>解説
  
ブール型の定数は、`TRUE` または `FALSE` キーワードで表されます。 値は `System.Boolean` として格納されます。  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>解説
  
文字列定数は単一引用符で囲まれ、任意の有効な Unicode 文字が含まれます。 文字列定数に組み込む単一引用符は、2 つの単一引用符で表されます。  
  
## <a name="function"></a>関数 (function)  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>解説  

`newid()` 関数は、`System.Guid.NewGuid()` メソッドによって生成された `System.Guid` を返します。  
  
`property(name)` 関数は、`name` によって参照されるプロパティの値を返します。 `name` 値には、文字列値を返す任意の有効な式を指定できます。  

## <a name="examples"></a>例
例については、[Service Bus フィルターの例](service-bus-filter-examples.md)を参照してください。
  
## <a name="considerations"></a>考慮事項

- SET は、新しいプロパティを作成するとき、または既存のプロパティの値を更新するときに使用します。
- REMOVE は、プロパティを削除するときに使用します。
- 式の型と既存のプロパティの型が異なる場合、可能であれば、SET は暗黙的な変換を実行します。
- 存在しないシステム プロパティが参照されている場合、アクションは失敗します。
- 存在しないユーザー プロパティが参照されていても、アクションは失敗しません。
- 存在しないユーザー プロパティは内部的に "Unknown" と評価され、演算子を評価するときに [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) と同じセマンティクスに従います。

## <a name="next-steps"></a>次のステップ

- [SQLRuleAction クラス (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [SQLRuleAction クラス (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlruleaction)
- [SqlRuleAction クラス (Java)](/java/api/com.microsoft.azure.servicebus.rules.sqlruleaction)
- [SqlRuleAction (JavaScript)](/javascript/api/@azure/service-bus/sqlruleaction)
- [`az servicebus topic subscription rule`](/cli/azure/servicebus/topic/subscription/rule)
- [New-AzServiceBusRule](/powershell/module/az.servicebus/new-azservicebusrule)