---
title: Synapse SQL で動的 SQL を使用する
description: Synapse SQL で動的 SQL を使用するためのヒント。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 86d5028a09a805142f7632f93530f8a54965d82f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675038"
---
# <a name="dynamic-sql-in-synapse-sql"></a>Synapse SQL の動的 SQL

この記事では、Synapse SQL で動的 SQL を使用したり、ソリューションを開発したりするためのヒントについて説明します。

## <a name="dynamic-sql-example"></a>動的 SQL の例

アプリケーション コードの開発時は、柔軟性が高く汎用的なモジュール ソリューションを提供するための動的 SQL を使用する必要が生じる場合があります。

> [!NOTE]
> 現時点では、専用 SQL プールでは BLOB データ型はサポートされていません。 BLOB データ型には varchar(max) および nvarchar(max) が含まれるため、BLOB データ型がサポートされないと、使用する文字列のサイズが制限される場合があります。 大量の文字列を構築するために、アプリケーション コードでこれらの型を使用した場合、コードをチャンクに分割し、代わりに EXEC ステートメントを使用する必要があります。

単純な例を次に示します。

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

文字列が短い場合は、通常どおり [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?view=azure-sqldw-latest&preserve-view=true) を使用できます。

> [!NOTE]
> 動的 SQL として実行されるステートメントには、すべての T-SQL 検証規則が適用されます。

## <a name="next-steps"></a>次のステップ

開発についてのその他のヒントは、[開発の概要](develop-overview.md)に関するページをご覧ください。
