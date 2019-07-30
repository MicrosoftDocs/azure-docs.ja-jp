---
title: データ型の定義 - Azure SQL Data Warehouse | Microsoft Docs
description: Azure SQL Data Warehouse におけるテーブルのデータ型の定義に関する推奨事項。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 61bb977271186699b0a72389e1538573f978c56b
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479364"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse でのテーブルのデータ型
Azure SQL Data Warehouse におけるテーブルのデータ型の定義に関する推奨事項。 

## <a name="what-are-the-data-types"></a>データ型とは

SQL Data Warehouse では、最もよく使用されるデータ型がサポートされています。 サポートされるデータ型の一覧については、CREATE TABLE ステートメントの[データ型](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes)を参照してください。 

## <a name="minimize-row-length"></a>行の長さを最小化する
データ型のサイズを最小化すると、行の長さが短くなり、クエリのパフォーマンスの向上につながります。 データに適した最小のデータ型を使用します。 

- 既定の長さで文字列を定義しないようにしてください。 たとえば、最長値が 25 文字の場合は、列を VARCHAR(25) として定義します。 
- VARCHAR のみが必要な場合は、[NVARCHAR][NVARCHAR] を使用しないようにしてください。
- 可能であれば、NVARCHAR(MAX) または VARCHAR(MAX) の代わりに、NVARCHAR(4000) または VARCHAR(8000) を使用します。

テーブルの読み込みに PolyBase 外部テーブルを使用している場合は、テーブル行の定義された長さが 1 MB を超えることはできません。 可変長データを含む行が 1 MB を超える場合、BCP で行を読み込めますが、PolyBase では読み込めません。

## <a name="identify-unsupported-data-types"></a>サポートされていないデータ型を識別する
データベースを別の SQL データベースから移行する場合は、データ型が SQL Data Warehouse でサポートされていない可能性があります。 既存の SQL スキーマでサポートされていないデータ型を検出するには、このクエリを使用します。

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>サポートされていないデータ型の対処法

以下のリストには、SQL Data Warehouse でサポートされていないデータ型と、サポートされていないデータ型の代わりに使用できるデータ型が示されています。

| サポートされていないデータ型 | 対処法 |
| --- | --- |
| [ジオメトリ](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [地理](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [画像](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |列を厳密に型指定された複数の列に分割します。 |
| [table](/sql/t-sql/data-types/table-transact-sql) |一時テーブルに変換します。 |
| [timestamp](/sql/t-sql/data-types/date-and-time-types) |[datetime2](/sql/t-sql/data-types/datetime2-transact-sql) および [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) 関数を使用するように、コードを再作成します。 定数のみが既定値としてサポートされているため、current_timestamp を既定の制約として定義できません。 timestamp で型指定された列から行バージョンの値を移行する必要がある場合は、行バージョンの値 NOT NULL または NULL に [BINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) または [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) を使用します。 |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ユーザー定義型](/sql/relational-databases/native-client/features/using-user-defined-types) |可能な場合は、ネイティブ データ型に戻します。 |
| 既定値 | 既定値では、リテラルと定数のみがサポートされます。 |


## <a name="next-steps"></a>次の手順
テーブルの開発に関する詳細については、[テーブルの概要](sql-data-warehouse-tables-overview.md)に関するページを参照してください。
