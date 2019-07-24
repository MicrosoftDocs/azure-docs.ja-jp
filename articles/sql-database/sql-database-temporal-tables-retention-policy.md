---
title: リテンション ポリシーを使用したテンポラル テーブルでの履歴データの管理 | Microsoft Docs
description: 一時的なリテンション ポリシーを使用して、履歴データを管理する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: 62e88d912c55015f87cc00f21527010ad01ee00c
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2019
ms.locfileid: "55560857"
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>リテンション ポリシーを使用したテンポラル テーブルでの履歴データの管理

特に履歴データを長期間保持する場合に、テンポラル テーブルは通常のテーブルよりもデータベースのサイズの増大に大きく影響することがあります。 したがって、履歴データのリテンション ポリシーは、あらゆるテンポラル テーブルのライフサイクルの計画と管理において重要な要素となります。 Azure SQL Database のテンポラル テーブルには、こういったタスクの実行に役立つ、使いやすい保持メカニズムが備わっています。

テンポラル履歴のリテンション期間は、個々のテーブル レベルで構成できるため、柔軟な継続ポリシーを作成できます。 テンポラル リテンション期間は簡単に適用でき、テーブルの作成時かスキーマの変更時にパラメーターを 1 つ設定するのみで適用できます。

リテンション ポリシーを定義すると、自動データ クリーンアップの対象となる履歴行があるかどうかを Azure SQL Database が定期的にチェックするようになります。 一致する行の特定と履歴テーブルからの削除は、システムによってスケジュールが設定され、実行されるバックグラウンド タスクで透過的に実行されます。 SYSTEM_TIME 期間終了を表す列に基づいて、履歴テーブルの行の有効期間の条件がチェックされます。 たとえば、リテンション期間を 6 か月間に設定すると、次の条件を満たすテーブル行がクリーンアップの対象となります。

```
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
```

上の例では、**[ValidTo]** 列が SYSTEM_TIME 期間終了と一致するという前提になっています。

## <a name="how-to-configure-retention-policy"></a>リテンション ポリシーの構成方法

テンポラル テーブルのリテンション ポリシーを構成する前に、テンポラル履歴のリテンション期間が*データベース レベルで*有効になっているかどうかを確認します。

```
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
```

データベースのフラグ **is_temporal_history_retention_enabled**は既定でオンに設定されていますが、ALTER DATABASE ステートメントを使って変更できます。 また、[ポイントインタイム リストア](sql-database-recovery-using-backups.md) 操作後は、自動的にオフに設定されます。 データベースのテンポラル履歴リテンション期間のクリーンアップを有効にするには、次のステートメントを実行します。

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

> [!IMPORTANT]
> **is_temporal_history_retention_enabled** がオフの場合であっても、テンポラル テーブルのリテンション期間を構成できますが、この場合は、期限切れの行の自動クリーンアップはトリガーされません。

リテンション ポリシーは、テーブルの作成時に HISTORY_RETENTION_PERIOD パラメーターの値を指定することによって構成します。

```sql
CREATE TABLE dbo.WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH
 (
     SYSTEM_VERSIONING = ON
     (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
     )
 );
```

Azure SQL Database では、さまざまな時間単位を使用してリテンション期間を指定できます(DAYS、WEEKS、MONTHS、YEARS)。 HISTORY_RETENTION_PERIOD を省略すると、リテンション期間が INFINITE であると判断されます。 INFINITE キーワードを明示的に使用することもできます。

一部のシナリオでは、テーブルの作成後にリテンション期間の構成が必要になったり、構成済みの値の変更が必要になることがあります。 その場合は、ALTER TABLE ステートメントを使用します。

```sql
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
```

> [!IMPORTANT]
> SYSTEM_VERSIONING をオフに設定したときには、リテンション期間の値は*保持されません*。 HISTORY_RETENTION_PERIOD を明示的に指定せずに SYSTEM_VERSIONING をオンに設定すると、リテンション期間が INFINITE に設定されます。

リテンション ポリシーの現在の状態を確認するには、データベース レベルのテンポラル リテンション期間の有効化フラグを、個々のテーブルのリテンション期間に結合する次のクエリを使用します。

```sql
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName,  SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2   
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
```


## <a name="how-sql-database-deletes-aged-rows"></a>SQL Database によって期限切れの行が削除されるしくみ

クリーンアップ プロセスは、履歴テーブルのインデックスのレイアウトに依存します。 *クラスター化されたインデックスを持つ履歴テーブル (B ツリーまたは列ストア) のみに有限のリテンション ポリシーを構成する*ことが重要です。 有限のリテンション期間を持つすべてのテンポラル テーブルに対して、期限切れのデータのクリーンアップを実行するバックグラウンド タスクが作成されます。
行ストア (B ツリー) のクラスター化されたインデックスのクリーンアップ ロジックでは、よりサイズの小さなまとまり (最大 10K) で期限切れの行が削除され、データベース ログや IO サブシステムへの負荷が軽減されます。 クリーンアップ ロジックでは必須の B ツリー インデックスが使用されますが、リテンション期間を過ぎた行の削除の順序は必ずしも保証されません。 そのため、*アプリケーションではクリーンアップの順序に依存関係を持たないようにしてください*。

クラスター化された列ストアのクリーンアップ タスクでは[行グループ](https://msdn.microsoft.com/library/gg492088.aspx)(通常は、1 グループに 100 万行が含まれます)全体が一度に削除されるため 、特に、履歴データが頻繁に生成されるような場合に効果的です。

![クラスター化列ストアのリテンション期間](./media/sql-database-temporal-tables-retention-policy/cciretention.png)

データ圧縮とリテンション クリーンアップの効率性が優れているため、ワークロードが短時間に大量の履歴データを生成するようなシナリオでは、クラスター化列ストア インデックスをお勧めします。 このようなパターンは、変更の追跡や監査、傾向分析、IoT データの取り込みに[テンポラル テーブルを使用する大量のトランザクション処理ワークロード](https://msdn.microsoft.com/library/mt631669.aspx)でよく見られます。

## <a name="index-considerations"></a>インデックスに関する考慮事項

行ストア クラスター化インデックスを持つテーブルのクリーンアップ タスクでは、SYSTEM_TIME 期間の終了に対応する列から始めるためのインデックスが必要です。 このようなインデックスが存在しない場合は、有限のリテンション期間を構成できません。

*Msg 13765, Level 16, State 1 <br></br> Setting finite retention period failed on system-versioned temporal table 'temporalstagetestdb.dbo.WebsiteUserInfo' because the history table 'temporalstagetestdb.dbo.WebsiteUserInfoHistory' does not contain required clustered index. (履歴テーブル 'temporalstagetestdb.dbo.WebsiteUserInfoHistory' に必要なクラスター化インデックスがないため、システム バージョンのテンポラル テーブル 'temporalstagetestdb.dbo.WebsiteUserInfo' で有限のリテンション期間の設定が失敗しました)Consider creating a clustered columnstore or B-tree index starting with the column that matches end of SYSTEM_TIME period, on the history table. (SYSTEM_TIME 期間終了に一致する列から始まるクラスター化列ストアまたは B ツリーを履歴テーブルに作成することを検討してください)*

Azure SQL Database で作成した既定の履歴テーブルには、既に、リテンション ポリシーに準拠しているクラスター化インデックスがあります。 有限のリテンション期間を持つテーブルのインデックスを削除しようとすると、次のエラーが表示されて操作が失敗します。

*Msg 13766, Level 16, State 1 <br></br> Cannot drop the clustered index 'WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory' because it is being used for automatic cleanup of aged data. (期限切れのデータの自動クリーンアップに使用されているため、クラスター化インデックス 'WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory' を削除できません)Consider setting HISTORY_RETENTION_PERIOD to INFINITE on the corresponding system-versioned temporal table if you need to drop this index. (このインデックスの削除が必要な場合は、対応するシステム バージョンのテンポラル テーブルで HISTORY_RETENTION_PERIOD を INFINITE に設定することを検討してください)*

履歴行を昇順で (期間終了列順)、SYSTEM_VERSIONIOING メカニズムによって排他的履歴テーブルのデータを設定する場合、大文字と小文字は常に挿入する場合、クラスター化列ストア インデックスのクリーンアップは最適に動作します。 履歴テーブルの行が期間終了列を基準にして並べられていない場合 (既存の履歴データを移行したような場合)、パフォーマンスを最適化するためには、適切に並べられた B ツリー行ストア インデックスの最上部にクラスター化列ストア インデックスを再作成する必要があります。

システム バージョンの操作によって自然に作成された行グループ内の順序が変更されることがあるため、有限のリテンション期間を持つ履歴テーブルのクラスター化列ストア インデックスは再構築しないでください。 履歴テーブルのクラスター化列ストア インデックスの再構築が必要な場合は、準拠している B ツリー インデックスの最上部に再作成してください。そうすることで、通常のデータ クリーンアップに必要な行グループの順番が保持されます。 データの順番を気にせずに、クラスター化列インデックスを持つ既存の履歴テーブルを持つテンポラル テーブルを作成する場合も、同じ方法で実行します。

```sql
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

クラスター化列ストア インデックスを持つ履歴テーブルに有限のリテンション期間を構成するときは、そのテーブルにクラスター化されていない B ツリーを追加で作成することはできません。

```sql
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
```

上記のステートメントを実行すると、次のエラーが表示されて失敗します。

*Msg 13772, Level 16, State 1 <br></br> Cannot create non-clustered index on a temporal history table 'WebsiteUserInfoHistory' since it has finite retention period and clustered columnstore index defined. (有限のリテンション期間とクラスター化列ストア インデックスが定義されているため、テンポラル履歴テーブル 'WebsiteUserInfoHistory' ではクラスター化されていないインデックスを作成できません)*

## <a name="querying-tables-with-retention-policy"></a>リテンション ポリシーを持つテーブルの照会

クリーンアップ タスクでは*任意の時間かつ任意の順序で*期限切れの行を削除できるため、予期しない、一貫性のない結果を避けるために、テンポラル テーブルのクエリではすべて、有限のリテンション ポリシーに一致する履歴列が除外されます。

次の図は、シンプルなクエリのクエリ プランを示しています。

```sql
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
```

クエリ プランには、履歴テーブル (ハイライト表示) での「Clustered Index Scan」操作の期間終了列 (ValidTo) に適用される追加のフィルターが含まれます。 この例では、WebsiteUserInfo テーブルに 1 か月のリテンション期間が設定されていると想定しています。

![リテンション期間クエリ フィルター](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

ただし、履歴テーブルにクエリを直接実行すると、指定したリテンション期間よりも古い行が表示されることがあり、クエリを繰り返し実行すると結果が異なることがあります。 次の図は、追加のフィルターを適用しない、履歴テーブルのクエリのクエリ実行プランを示します。

![リテンション期間フィルターを使用せずに履歴のクエリを実行する](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

一貫性のない結果や予期しない結果を得ることがあるため、ビジネス ロジックが保有期間を超える履歴テーブルの読み取りに依存することのないようにしてください。 テンポラル テーブルのデータの分析には、FOR SYSTEM_TIME 句を使ったテンポラル クエリを使用することをお勧めします。

## <a name="point-in-time-restore-considerations"></a>ポイントインタイム リストアの考慮事項

[既存のデータベースを特定の時点に復元](sql-database-recovery-using-backups.md)して新しいデータベースを作成するときは、データベース レベルのテンポラル リテンション期間が無効になります  (**is_temporal_history_retention_enabled** フラグがオフに設定されます)。 この機能では、復元時にすべての履歴列を確認できるため、クエリを実行する前に期限切れの行が削除されることはありません。 この機能を使用すると、*構成された保有期間を超える履歴データを検査*できます。

テンポラル テーブルに 1 か月のリテンション期間が指定されているとします。 Premium サービス階層でデータベースが作成されている場合、過去 35 日間までの状態のデータベースのコピーを作成できます。 履歴テーブルにクエリを直接実行することにより、実質的に、最大 65 日間の履歴行を分析できます。

テンポラル リテンション期間のクリーンアップを有効にする場合は、ポイントインタイム リストアの後に次の Transact-SQL ステートメントを実行します。

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

## <a name="next-steps"></a>次の手順

アプリケーションでテンポラル テーブルを使用する方法については、「[Azure SQL Database のテンポラル テーブルの概要](sql-database-temporal-tables.md)」をご覧ください。

Channel 9 にアクセスして、[テンポラル テーブル導入による実際の成功事例](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions)や[テンポラル技術のライブ デモンストレーション](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016)をご覧ください。

テンポラル テーブルの詳細については、[MSDN のドキュメント](https://msdn.microsoft.com/library/dn935015.aspx)をご覧ください。
