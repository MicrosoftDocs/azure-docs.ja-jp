---
title: "SQL Data Warehouse の変数の代入 | Microsoft Docs"
description: "ソリューション開発のための、Azure SQL Data Warehouse での Transact-SQL 変数の代入に関するヒント。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 81ddc7cf-a6ba-4585-91a3-b6ea50f49227
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5ec9f7ad24dad833758d3b981fd4d8735119d813


---
# <a name="assign-variables-in-sql-data-warehouse"></a>SQL Data Warehouse の変数の代入
SQL Data Warehouse の変数は、`DECLARE` ステートメントまたは `SET` ステートメントを使用して設定します。

以下に示す方法は、いずれも変数値を設定する有効な方法です。

## <a name="setting-variables-with-declare"></a>DECLARE を使用した変数の設定
DECLARE を使用した変数の初期化は、SQL Data Warehouse で変数値を設定する最も柔軟性の高い方法の 1 つです。

```sql
DECLARE @v  int = 0
;
```

DECLARE を使用して、一度に複数の変数を設定することもできます。 `SELECT` または `UPDATE` を使用して、これを行うことはできません。

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

同じ DECLARE ステートメントで変数を初期化し、使用することはできません。 要点を示すために、次の例では、同じ DECLARE ステートメントで @p1 を初期化し、使用しています。このようなステートメントは使用 "**できません**"。 これはエラーになります。

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>SET を使用した値の設定
SET は、1 つの変数を設定する際によく使用されるメソッドです。

次の例はすべて、SET を使用して変数を設定する有効な方法です。

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

SET を使用して設定できる変数は、一度に 1 つに限られます。 ただし、上記のように複合演算子を使用できます。

## <a name="limitations"></a>制限事項
SELECT および UPDATE は変数代入には使用できません。

## <a name="next-steps"></a>次のステップ
開発に関するその他のヒントについては、[「開発の概要」][開発の概要]をご覧ください。

<!--Image references-->

<!--Article references-->
[開発の概要]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


