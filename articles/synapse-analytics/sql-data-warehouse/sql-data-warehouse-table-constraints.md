---
title: 主キー、外部キー、および一意キー
description: Azure Synapse Analytics の Synapse SQL プールでのテーブル制約のサポート
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 562e2cce317d8774ecf72971d53be4f66f9c3da4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85212770"
---
# <a name="primary-key-foreign-key-and-unique-key-in-synapse-sql-pool"></a>Synapse SQL プールの主キー、外部キー、および一意キー

主キー、外部キー、一意キーなど、Synapse SQL プールのテーブル制約について説明します。

## <a name="table-constraints"></a>テーブル制約

Synapse SQL プールでは、次のテーブル制約がサポートされています。 
- PRIMARY KEY は、NONCLUSTERED と NOT ENFORCED が両方とも使用されている場合にのみサポートされます。    
- UNIQUE 制約は、NOT ENFORCED が使用されている場合にのみサポートされます。

構文については、[ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) と [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse) をご覧ください。 

FOREIGN KEY 制約は、Synapse SQL プールではサポートされません。  


## <a name="remarks"></a>解説

主キーや一意キーを使用すると、Synapse SQL プール エンジンでクエリの最適な実行プランを生成できます。  主キー列または一意制約列のすべての値は、一意である必要があります。

Synapse SQL プールで主キーまたは一意制約を使用してテーブルを作成した後、ユーザーはそれらの列のすべての値が一意であることを確認する必要があります。  これに違反すると、クエリで不正確な結果が返される可能性があります。  この例では、主キーまたは一意制約の列に重複した値が含まれている場合に、クエリでどのように不正確な結果が返される可能性があるかを示します。  

```sql
 -- Create table t1
CREATE TABLE t1 (a1 INT NOT NULL, b1 INT) WITH (DISTRIBUTION = ROUND_ROBIN)

-- Insert values to table t1 with duplicate values in column a1.
INSERT INTO t1 VALUES (1, 100)
INSERT INTO t1 VALUES (1, 1000)
INSERT INTO t1 VALUES (2, 200)
INSERT INTO t1 VALUES (3, 300)
INSERT INTO t1 VALUES (4, 400)

-- Run this query.  No primary key or unique constraint.  4 rows returned. Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
1           2
2           1
3           1
4           1

(4 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 ADD CONSTRAINT unique_t1_a1 unique (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, count(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key constraint
ALTER TABLE t1 add CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Manually fix the duplicate values in a1
UPDATE t1 SET a1 = 0 WHERE b1 = 1000

-- Verify no duplicate values in column a1 
SELECT * FROM t1

/*
a1          b1
----------- -----------
2           200
3           300
4           400
0           1000
1           100

(5 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 add CONSTRAINT unique_t1_a1 UNIQUE (a1) NOT ENFORCED  

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) as total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key contraint
ALTER TABLE t1 ADD CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

```

## <a name="examples"></a>例

主キーを使用して Synapse SQL プール テーブルを作成します。 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
一意制約を使用して Synapse SQL プール テーブルを作成します。

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>次のステップ

Synapse SQL プールにテーブルを作成した後の次の手順はテーブルへのデータの読み込みです。 読み込みのチュートリアルについては、[Synapse SQL プールへのデータの読み込み](load-data-wideworldimportersdw.md)に関するページを参照してください。
