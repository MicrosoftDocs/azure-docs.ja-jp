---
title: データ保有期間ポリシーを有効または無効にする - Azure SQL Edge
description: Azure SQL Edge でデータ保有期間ポリシーを有効または無効にする方法について調べる
keywords: SQL Edge, データ保有期間
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: 5b8dd911952a63ba8775f27a6128ff61e849e823
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861427"
---
# <a name="enable-and-disable-data-retention-policies"></a>データ保有期間ポリシーを有効または無効にする

このトピックでは、データベースおよびテーブルのデータ保有期間ポリシーを有効または無効にする方法について説明します。 

## <a name="enable-data-retention-for-a-database"></a>データベースのデータ保有期間を有効にする

次の例では、[Alter Database](/sql/t-sql/statements/alter-database-transact-sql-set-options) を使用してデータ保有期間を有効にする方法を示します。

```sql
ALTER DATABASE [<DatabaseName>] SET DATA_RETENTION  ON;
```

## <a name="check-if-data-retention-is-enabled-for-a-database"></a>データベースのデータ保有期間が有効になっているかどうかを確認する

次のコマンドを使用して、データベースのデータ保有期間が有効になっているかどうかを確認できます
```sql
SELECT is_data_retention_enabled, name
FROM sys.databases;
```

## <a name="enable-data-retention-for-a-table"></a>テーブルのデータ保有期間を有効にする

データ保有期間は、データを自動的に消去する各テーブルについて有効にする必要があります。 データベースとテーブルでデータ保持が有効になっている場合は、バックグラウンド システム タスクによりテーブルが定期的にスキャンされ、古くなった (期限切れの) 行が特定されて削除されます。 データ保有期間は、[Create Table](/sql/t-sql/statements/create-table-transact-sql) を使用したテーブル作成中に、または [Alter Table](/sql/t-sql/statements/alter-table-transact-sql) を使用することで有効にすることができます。

次の例では、[Create Table](/sql/t-sql/statements/create-table-transact-sql) を使用して、テーブルのデータ保有期間を有効にする方法を示します。 

```sql
CREATE TABLE [dbo].[data_retention_table] 
(
[dbdatetime2] datetime2(7), 
[product_code] int, 
[value] char(10),  
CONSTRAINT [pk_current_data_retention_table] PRIMARY KEY CLUSTERED ([product_code])
) WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )
```

create table コマンドの `WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )` 部分により、テーブルにデータ保有期間が設定されます。 このコマンドでは、次の必須パラメーターを使用します。 

- DATA_DELETION - データ保有期間がオンであるか、オフであるかを示します。
- FILTER_COLUMN - テーブル内の列の名前です。これを使用して、行が古くなっているかどうかを確認します。 フィルター列となるのは、次のデータ型の列のみです。 
    - Date
    - SmallDateTime
    - DateTime
    - DateTime2
    - DateTimeOffset
- RETENTION_PERIOD - 単位記述子が後に続く整数値。 使用できる単位は、DAY、DAYS、WEEK、WEEKS、MONTH、MONTHS、YEAR、YEARS です。

次の例では、[Alter Table](/sql/t-sql/statements/alter-table-transact-sql) を使用して、テーブルのデータ保有期間を有効にする方法を示します。  

```sql
Alter Table [dbo].[data_retention_table]
SET (DATA_DELETION = On (FILTER_COLUMN = [timestamp], RETENTION_PERIOD = 1 day))
```

## <a name="check-if-data-retention-is-enabled-for-a-table"></a>テーブルのデータ保有期間が有効になっているかどうかを確認する

次のコマンドを使用して、データ保有期間が有効になっているテーブルを確認できます。

```sql
select name, data_retention_period, data_retention_period_unit from sys.tables
```

値が data_retention_period =-1 で、data_retention_period_unit が INFINITE である場合は、テーブルにデータ保持期間が設定されていないことを示します。

次のクエリを使用して、データ保有期間の filter_column として使用される列を特定できます。 

```sql
Select name from sys.columns
where is_data_deletion_filter_column =1 
and object_id = object_id(N'dbo.data_retention_table', N'U')
```

## <a name="correlating-db-and-table-data-retention-settings"></a>データベースとテーブルのデータ保有期間の関連付け

データベースとテーブルのデータ保有期間の設定を組み合わせて使用して、期限切れの行の自動クリーンアップをテーブルで実行するかどうかを決定します。 

|データベース オプション | テーブル オプション | 動作 |
|----------------|--------------|----------|
| OFF | OFF | データ保有期間ポリシーが無効になっていて、期限切れのレコードの自動クリーンアップと手動クリーンアップの両方が無効になっています。|
| OFF | ON  | データ保有期間ポリシーがテーブルで有効になっています。 古いレコードの自動クリーンアップは無効になっていますが、手動クリーンアップ方法を使用して、古いレコードをクリーンアップできます。 |
| ON | OFF | データ保有期間ポリシーがデータベース レベルで有効になっています。 ただし、このオプションがテーブル レベルでは無効になっているため、期限切れの行の、保有期間に基づくクリーンアップは行われません。|
| ON | ON | データベースとテーブルの両方でデータ保有期間ポリシーが有効になっています。 古いレコードの自動クリーンアップが有効になっています。 |

## <a name="disable-data-retention-on-a-table"></a>テーブルでデータ保有期間を無効にする 

データ保有期間は、[Alter Table](/sql/t-sql/statements/alter-table-transact-sql) を使用してテーブルで無効にすることができます。 次のコマンドを使用して、データ保有期間をテーブルで無効にすることができます。

```sql
Alter Table [dbo].[data_retention_table]
Set (DATA_DELETION = OFF)
```

## <a name="disable-data-retention-on-a-database"></a>データベースでデータ保有期間を無効にする

データ保有期間は、[Alter Database](/sql/t-sql/statements/alter-database-transact-sql-set-options) を使用してテーブルで無効にすることができます。 次のコマンドを使用して、データ保有期間をデータベースで無効にすることができます。

```sql
ALTER DATABASE <DatabaseName> SET DATA_RETENTION  OFF;
```

## <a name="next-steps"></a>次の手順
- [データ保有期間と自動データ削除](data-retention-overview.md)
- [アイテム保持ポリシーを使用して履歴データを管理する](data-retention-cleanup.md)
