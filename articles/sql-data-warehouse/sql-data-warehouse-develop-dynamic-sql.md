---
title: "SQL Data Warehouse での動的 SQL | Microsoft Docs"
description: "ソリューション開発のための Azure SQL Data Warehouse での動的 SQL  の使用に関するヒント。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: a948c2c3-3cd1-4373-90a9-79e59414b778
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6da1efc35b624e0b06693037b4f91d71f0f40eb4


---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>SQL Data Warehouse での動的 SQL
SQL Data Warehouse のアプリケーション コードを開発する際に、柔軟性が高く汎用的なモジュール ソリューションを提供するための動的 SQL を使用する必要のある場合があります。 現時点では、SQL Data Warehouse は BLOB データ型をサポートしていません。 これにより、blob 型には varchar (max) と nvarchar (max) の両方の型が含まれるので、文字列のサイズが制限される場合があります。 大量の文字列を構築するときにアプリケーション コードでこれらの型を使用した場合、コードをチャンクに分割し、代わりに EXEC ステートメントを使用する必要があります。

単純な例を次に示します。

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

文字列が短い場合は、通常どおり [sp_executesql][sp_executesql] を使用することができます。

> [!NOTE]
> 動的 SQL として実行されるステートメントには、すべての TSQL 検証規則が適用されます。
> 
> 

## <a name="next-steps"></a>次のステップ
開発に関するその他のヒントについては、[「開発の概要」][開発の概要]をご覧ください。

<!--Image references-->

<!--Article references-->
[開発の概要]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp_executesql]: https://msdn.microsoft.com/library/ms188001.aspx

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


