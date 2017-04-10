---
title: "SQL Data Warehouse のテーブルのデータ型 | Microsoft Docs"
description: "Azure SQL Data Warehouse のテーブルのデータ型の概要です。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: 
ms.assetid: d4a1f0a3-ba9f-44b9-95f6-16a4f30746d6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 7757de96601c426ff07e94cfa0c12d4dec8f06f5
ms.lasthandoff: 03/22/2017


---
# <a name="data-types-for-tables-in-sql-data-warehouse"></a>SQL Data Warehouse のテーブルのデータ型
> [!div class="op_single_selector"]
> * [概要][Overview]
> * [データ型][Data Types]
> * [分散][Distribute]
> * [インデックス][Index]
> * [パーティション][Partition]
> * [統計][Statistics]
> * [一時][Temporary]
> 
> 

SQL Data Warehouse では、最もよく使用されるデータ型がサポートされています。  SQL Data Warehouse でサポートされるデータ型の一覧を以下に示します。  データ型のサポートについて詳しくは、[テーブルの作成][create table]に関するページをご覧ください。

| **サポートされているデータ型** |  |  |
| --- | --- | --- |
| [bigint][bigint] |[decimal][decimal] |[smallint][smallint] |
| [binary][binary] |[float][float] |[smallmoney][smallmoney] |
| [bit][bit] |[int][int] |[sysname][sysname] |
| [char][char] |[money][money] |[time][time] |
| [date][date] |[nchar][nchar] |[tinyint][tinyint] |
| [datetime][datetime] |[nvarchar][nvarchar] |[uniqueidentifier][uniqueidentifier] |
| [datetime2][datetime2] |[real][real] |[varbinary][varbinary] |
| [datetimeoffset][datetimeoffset] |[smalldatetime][smalldatetime] |[varchar][varchar] |

## <a name="data-type-best-practices"></a>データ型のベスト プラクティス
 列の型を定義するときに、データをサポートする最小のデータ型を使用すると、クエリのパフォーマンスが向上します。 これは、CHAR および VARCHAR 列の場合に特に重要です。 列の最長の値が 25 文字の場合は、列を VARCHAR(25) として定義します。 すべての文字列を既定の長さで定義しないようにします。 さらに、VARCHAR で済む場合は、[NVARCHAR][NVARCHAR] を使用せずに、列を VARCHAR として定義します。  可能なときは、NVARCHAR(MAX) または VARCHAR(MAX) の代わりに、NVARCHAR(4000) または VARCHAR(8000) を使用します。

## <a name="polybase-limitation"></a>Polybase の制限事項
テーブルの読み込みに Polybase を使用している場合は、データの長さが 1 MB を超えていないことを確認します。  この幅を超える可能性のある可変長データを含む行を定義し、BCP を含む行を読み込むことはできますが、Polybase を使用して、このデータを完全に読み込むことはできません。  

## <a name="unsupported-data-types"></a>サポートされていないデータ型
Azure SQL Database などの別の SQL プラットフォームからデータベースを移行する場合は、一部のデータ型が SQL Data Warehouse でサポートされていない可能性があります。  以下に、サポートされていないデータ型とサポートされていないデータ型の代わりに使用できるデータ型をいくつか示します。

| データ型 | 対処法 |
| --- | --- |
| [geometry][geometry] |[varbinary][varbinary] |
| [geography][geography] |[varbinary][varbinary] |
| [hierarchyid][hierarchyid] |[nvarchar][nvarchar](4000) |
| [image][ntext,text,image] |[varbinary][varbinary] |
| [text][ntext,text,image] |[varchar][varchar] |
| [ntext][ntext,text,image] |[nvarchar][nvarchar] |
| [sql_variant][sql_variant] |列を厳密に型指定された複数の列に分割します。 |
| [table][table] |一時テーブルに変換します。 |
| [timestamp][timestamp] |[datetime2][datetime2] と `CURRENT_TIMESTAMP` 関数を使用するようにコードを再作成します。  定数のみが既定値としてサポートされているため、current_timestamp を既定の制約として定義できません。 timestamp で型指定された列から行バージョンの値を移行する必要がある場合は、行バージョンの値 NOT NULL または NULL に [BINARY][BINARY](8) または [VARBINARY][BINARY](8) を使用します。 |
| [xml][xml] |[varchar][varchar] |
| [ユーザー定義型][user defined types] |可能な限り、ネイティブ型に変換します。 |
| 既定値 |既定値では、リテラルと定数のみがサポートされます。  `GETDATE()` や `CURRENT_TIMESTAMP` など、不明確な式または関数はサポートされていません。 |

Azure SQL Data Warehouse でサポートされていない列を識別するために、現在の SQL データベースで以下の SQL を実行できます。

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="next-steps"></a>次のステップ
[テーブルの概要][Overview]、[テーブルの分散][Distribute]、[テーブルのインデックス作成][Index]、[テーブルのパーティション分割][Partition]、[テーブル統計の更新][Statistics]、[一時テーブル][Temporary]に関する各記事で詳細を確認します。  [SQL Data Warehouse のベスト プラクティス][SQL Data Warehouse Best Practices]に関する記事でベスト プラクティスの詳細を確認します。

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->
[create table]: https://msdn.microsoft.com/library/mt203953.aspx
[bigint]: https://msdn.microsoft.com/library/ms187745.aspx
[binary]: https://msdn.microsoft.com/library/ms188362.aspx
[bit]: https://msdn.microsoft.com/library/ms177603.aspx
[char]: https://msdn.microsoft.com/library/ms176089.aspx
[date]: https://msdn.microsoft.com/library/bb630352.aspx
[datetime]: https://msdn.microsoft.com/library/ms187819.aspx
[datetime2]: https://msdn.microsoft.com/library/bb677335.aspx
[datetimeoffset]: https://msdn.microsoft.com/library/bb630289.aspx
[decimal]: https://msdn.microsoft.com/library/ms187746.aspx
[float]: https://msdn.microsoft.com/library/ms173773.aspx
[geometry]: https://msdn.microsoft.com/library/cc280487.aspx
[geography]: https://msdn.microsoft.com/library/cc280766.aspx
[hierarchyid]: https://msdn.microsoft.com/library/bb677290.aspx
[int]: https://msdn.microsoft.com/library/ms187745.aspx
[money]: https://msdn.microsoft.com/library/ms179882.aspx
[nchar]: https://msdn.microsoft.com/library/ms186939.aspx
[nvarchar]: https://msdn.microsoft.com/library/ms186939.aspx
[ntext,text,image]: https://msdn.microsoft.com/library/ms187993.aspx
[real]: https://msdn.microsoft.com/library/ms173773.aspx
[smalldatetime]: https://msdn.microsoft.com/library/ms182418.aspx
[smallint]: https://msdn.microsoft.com/library/ms187745.aspx
[smallmoney]: https://msdn.microsoft.com/library/ms179882.aspx
[sql_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[sysname]: https://msdn.microsoft.com/library/ms186939.aspx
[table]: https://msdn.microsoft.com/library/ms175010.aspx
[time]: https://msdn.microsoft.com/library/bb677243.aspx
[timestamp]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[uniqueidentifier]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[xml]: https://msdn.microsoft.com/library/ms187339.aspx
[user defined types]: https://msdn.microsoft.com/library/ms131694.aspx

