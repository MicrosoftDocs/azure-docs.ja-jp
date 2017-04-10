---
title: "SQL Data Warehouse で、ラベルを使用してクエリをインストルメント化 | Microsoft Docs"
description: "ソリューション開発のための Azure SQL Data Warehouse でのラベルを使用してクエリのインストルメント化に関するヒント。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 44988de8-04c1-4fed-92be-e1935661a4e8
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5c72cd2c80d9fcee3d9340c23a629451c54c9156


---
# <a name="use-labels-to-instrument-queries-in-sql-data-warehouse"></a>SQL Data Warehouse で、ラベルを使用してクエリをインストルメント化
SQL Data Warehouse は、クエリ ラベルと呼ばれる概念をサポートします。 難解な領域に入る前に、1 つの例を説明しましょう。

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

この最後の行は、クエリに、 'My Query Label' という文字列をタグ付けします。 これは、ラベルが DMV を介してクエリできるので特に便利です。 これにより、問題のあるクエリを追跡して、ETL 実行によって進行状況を識別するためのメカニズムが提供されます。

良好な命名規則は、実際にはここで役立ちます。 たとえば、'PROJECT : PROCEDURE : STATEMENT : COMMENT' は、ソース管理内のすべてのコード間でのクエリを一意に識別する上で役立ちます。

ラベルで検索するために、動的管理ビューを使用する以下のクエリを使用することができます。

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> クエリをする際に、語句のラベルに角かっこまたは二重引用符で囲むことが必要です。 ラベルは予約語ですが、区切られていない場合、エラーが発生します。
> 
> 

## <a name="next-steps"></a>次のステップ
開発に関するその他のヒントについては、[「開発の概要」][開発の概要]をご覧ください。

<!--Image references-->

<!--Article references-->
[開発の概要]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


