---
title: Azure Cosmos DB クエリ言語の RegexMatch
description: Azure Cosmos DB の RegexMatch SQL システム関数について説明します
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 7f5b2831f45e902b312636e4133557a16ee7ec95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341639"
---
# <a name="regexmatch-azure-cosmos-db"></a>REGEXMATCH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

正規表現の機能を提供します。 正規表現は、テキストのパターンを見つけるための簡潔で柔軟性の高い表記法です。 Azure Cosmos DB では、[PERL 互換正規表現 (PCRE)](http://www.pcre.org/) を使用します。 

## <a name="syntax"></a>構文
  
```sql
RegexMatch(<str_expr1>, <str_expr2>, [, <str_expr3>])  
```  
  
## <a name="arguments"></a>引数
  
*str_expr1*  
   検索対象となる文字列式です。  
  
*str_expr2*  
   正規表現です。

*str_expr3*  
   正規表現と共に使用する、選択した修飾子の文字列です。 この文字列値は省略可能です。 修飾子を指定せずに RegexMatch を実行する場合は、空の文字列を追加することも、完全に省略することも、どちらでも可能です。 

[Perl で正規表現を作成するための構文](https://perldoc.perl.org/perlre)について学習できます。 

Azure Cosmos DB では、以下の 4 つの修飾子がサポートされています。

| 修飾子 | 説明 |
| ------ | ----------- |
| `m` | 検索対象となる文字列式を複数行として扱います。 このオプションを指定しないと、"^" と "$" は文字列の先頭または末尾で一致し、個々の行それぞれには一致しません。 |
| `s` | 改行文字を含む任意の文字に一致する "." を使用できるようにします。 | 
| `i` | パターン マッチング時には、大文字と小文字は区別されません。 |
| `x` | すべての空白文字を無視します。 |

## <a name="return-types"></a>戻り値の型
  
  ブール式を返します。 検索対象の文字列式、正規表現、または選択した修飾子が無効な場合は、undefined が返されます。
  
## <a name="examples"></a>例
  
次の簡単な RegexMatch の例では、いくつか異なる修飾子を使用する場合の正規表現の一致について、文字列 "abcd" を調べます。
  
```sql
SELECT RegexMatch ("abcd", "ABC", "") AS NoModifiers, 
RegexMatch ("abcd", "ABC", "i") AS CaseInsensitive, 
RegexMatch ("abcd", "ab.", "") AS WildcardCharacter,
RegexMatch ("abcd", "ab c", "x") AS IgnoreWhiteSpace, 
RegexMatch ("abcd", "aB c", "ix") AS CaseInsensitiveAndIgnoreWhiteSpace 
```  
  
 結果セットは次のようになります。  
  
```json
[
    {
        "NoModifiers": false,
        "CaseInsensitive": true,
        "WildcardCharacter": true,
        "IgnoreWhiteSpace": true,
        "CaseInsensitiveAndIgnoreWhiteSpace": true
    }
]
```

RegexMatch では、StartsWith、EndsWith、Contains、StringEquals といったシステム関数では不可能なより複雑な文字列検索を、メタ文字を使用して行うことができます。 以下に、追加の例をいくつか示します。これらは、[Azure Cosmos DB Query Playground](https://www.documentdb.com/sql/demo) から入手できる栄養データ セットを使用して実行することができます。 

> [!NOTE] 
> 正規表現内でメタ文字を使用する必要があり、それに特別な意味を持たせたくない場合は、`\` を使用してメタ文字をエスケープする必要があります。

**"Salt" という単語が 1 回だけ含まれる説明がある項目を調べます。**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "salt{1}","")
```

**0 ～ 99 の数字が 1 つ含まれる説明がある項目を調べます。**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "[0-99]","")
```

**"S" または "s" で始まる 4 文字の単語が含まれる説明がある項目を調べます。**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, " s... ","i")
```

## <a name="remarks"></a>解説

このシステム関数では、正規表現をシステム関数 StartsWith、EndsWith、Contains、StringEquals のどれかに分割できる場合は、[範囲インデックス](index-policy.md#includeexclude-strategy)の恩恵を受けられます。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
