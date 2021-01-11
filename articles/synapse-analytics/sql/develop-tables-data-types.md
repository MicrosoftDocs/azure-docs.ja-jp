---
title: Synapse SQL でのテーブルのデータ型
description: Synapse SQL におけるテーブルのデータ型の定義に関するレコメンデーション。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: a92d73941aac045534b6708c4b92d741c39ff781
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494969"
---
# <a name="table-data-types-in-synapse-sql"></a>Synapse SQL でのテーブルのデータ型

Synapse SQL におけるテーブルのデータ型の定義に関するレコメンデーション。 

## <a name="data-types"></a>データ型

Synapse SQL では、最も一般的に使用されるデータ型をサポートしています。 サポートされるデータ型の一覧については、CREATE TABLE ステートメントの[データ型](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes)を参照してください。 

## <a name="minimize-row-length"></a>行の長さを最小化する

データ型のサイズを最小化すると、行の長さが短くなり、クエリのパフォーマンスの向上につながります。 データに適した最小のデータ型を使用します。

- 既定の長さで文字列を定義しないようにしてください。 たとえば、最長値が 25 文字の場合は、列を VARCHAR(25) として定義します。
- VARCHAR のみが必要な場合は、[NVARCHAR][NVARCHAR] を使用しないようにしてください。
- 可能であれば、NVARCHAR(MAX) または VARCHAR(MAX) の代わりに、NVARCHAR(4000) または VARCHAR(8000) を使用します。

> [!NOTE]
> SQL プール テーブルの読み込みに PolyBase 外部テーブルを使用している場合は、テーブル行の定義された長さが 1 MB を超えることはできません。 可変長データを含む行が 1 MB を超える場合、BCP で行を読み込めますが、PolyBase では読み込めません。

## <a name="identify-unsupported-data-types"></a>サポートされていないデータ型を識別する

データベースを別の SQL データベースから移行する場合は、データ型が Synapse SQL でサポートされていない可能性があります。 既存の SQL スキーマでサポートされていないデータ型を検出するには、このクエリを使用します。

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>サポートされていないデータ型の対処法

以下のリストには、Synapse SQL でサポートされていないデータ型と、サポートされていないデータ型の代わりに使用できるデータ型が示されています。

| サポートされていないデータ型 | 回避策 |
| --- | --- |
| [geometry](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [geography](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(4000) |
| [image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |列を厳密に型指定された複数の列に分割します。 |
| [テーブル](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |SQL プールを使用している場合は、一時テーブルに変換できます。 SQL (プレビュー) を使用している場合は、[CETAS](../sql/develop-tables-cetas.md) を使用して、ストレージにデータを格納することを検討してください。 |
| [timestamp](/sql/t-sql/data-types/date-and-time-types) |[datetime2](/sql/t-sql/data-types/datetime2-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) および [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 関数を使用するように、コードを再作成します。 定数のみが既定値としてサポートされているため、current_timestamp を既定の制約として定義できません。 timestamp で型指定された列から行バージョンの値を移行する必要がある場合は、行バージョンの値 NOT NULL または NULL に [BINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(8) または [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(8) を使用します。 |
| [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [ユーザー定義型](/sql/relational-databases/native-client/features/using-user-defined-types) |可能な場合は、ネイティブ データ型に戻します。 |
| 既定値 | 既定値では、リテラルと定数のみがサポートされます。 |

## <a name="next-steps"></a>次のステップ

テーブルの開発に関する詳細については、[テーブルの概要](develop-overview.md)に関するページを参照してください。
