---
title: Azure SQL Data Warehouse での動的 SQL の使用 | Microsoft Docs
description: ソリューション開発のための Azure SQL Data Warehouse での動的 SQL  の使用に関するヒント。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 4454b1d44d0be61dca8571e86c73e09a9527d1eb
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479656"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>SQL Data Warehouse での動的 SQL
ソリューション開発のための Azure SQL Data Warehouse での動的 SQL  の使用に関するヒント。

## <a name="dynamic-sql-example"></a>動的 SQL の例

SQL Data Warehouse のアプリケーション コードを開発する際に、柔軟性が高く汎用的なモジュール ソリューションを提供するための動的 SQL を使用する必要が生じる場合があります。 現時点では、SQL Data Warehouse は BLOB データ型をサポートしていません。 BLOB データ型には varchar(max) および nvarchar(max) が含まれるため、BLOB データ型がサポートされないと、使用する文字列のサイズが制限される場合があります。 大量の文字列を構築するために、アプリケーション コードでこれらの型を使用した場合、コードをチャンクに分割し、代わりに EXEC ステートメントを使用する必要があります。

単純な例を次に示します。

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

文字列が短い場合は、通常どおり [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) を使用できます。

> [!NOTE]
> 動的 SQL として実行されるステートメントには、すべての TSQL 検証規則が適用されます。
> 
> 

## <a name="next-steps"></a>次の手順
開発についてのその他のヒントは、[開発の概要](sql-data-warehouse-overview-develop.md)に関するページをご覧ください。

