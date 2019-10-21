---
title: Azure Cosmos DB クエリ言語での型チェック関数
description: Azure Cosmos DB での SQL システム関数の型チェックについて説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cac8be4c39737ca209653532d00375c8c9bee48e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349071"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>型チェック関数 (Azure Cosmos DB)

型チェック関数を使用すると、SQL クエリ内の式の型をチェックできます。 型チェック関数を使用して、項目内のプロパティの型が変数または不明の場合に型をその場で判定できます。 

## <a name="functions"></a>Functions

次の表に、サポートされている組み込みの型チェック関数を示します。

次の関数は、入力値に対する型チェックをサポートし、それぞれがブール値を返します。  
  
||||  
|-|-|-|  
|[IS_ARRAY](sql-query-is-array.md)|[IS_BOOL](sql-query-is-bool.md)|[IS_DEFINED](sql-query-is-defined.md)|  
|[IS_NULL](sql-query-is-null.md)|[IS_NUMBER](sql-query-is-number.md)|[IS_OBJECT](sql-query-is-object.md)|  
|[IS_PRIMITIVE](sql-query-is-primitive.md)|[IS_STRING](sql-query-is-string.md)||  
  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
- [ユーザー定義関数](sql-query-udfs.md)
- [集計](sql-query-aggregates.md)
