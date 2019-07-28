---
title: Azure Database for MySQL へのオンライン移行に関する既知の問題と移行の制限事項に関する記事 | Microsoft Docs
description: Azure Database for MySQL へのオンライン移行に関する既知の問題と移行の制限事項について学習します。
services: database-migration
author: hjtoland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/23/2019
ms.openlocfilehash: 2c8a3f36e04fbedfdd127939d55fab376e3e6b30
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2019
ms.locfileid: "64691947"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-postgresql"></a>Azure DB for PostgreSQL へのオンライン移行に関する既知の問題と移行の制限事項

PostgreSQL から Azure Database for PostgreSQL へのオンライン移行に関する既知の問題点と制限事項について、後続のセクションで説明します。 

## <a name="online-migration-configuration"></a>オンライン移行の構成
- ソースの PostgreSQL サーバーは、バージョン 9.5.11、9.6.7、または 10.3 以降を実行している必要があります。 詳しくは、「[サポートされている PostgreSQL Database バージョン](../postgresql/concepts-supported-versions.md)」の記事をご覧ください。
- 同じバージョンの移行のみがサポートされています。 たとえば、PostgreSQL 9.5.11 から Azure Database for PostgreSQL 9.6.7 への移行はサポートされていません。

    > [!NOTE]
    > PostgreSQL バージョン 10 では、現在、バージョン 10.3 から Azure Database for PostgreSQL への移行をサポートしているのは DMS のみです。 近日中に PostgreSQL の新しいバージョンをサポートする予定です。

- **ソース PostgreSQL の postgresql.conf** ファイルで論理レプリケーションを有効にするには、次のパラメーターを設定します。
    - **wal_level** = logical
    - **max_replication_slots** = [移行対象のデータベースの最大数]。4 つのデータベースを移行する場合は、値を 4 に設定します
    - **max_wal_senders** = [同時に実行されるデータベース数]。推奨値は 10 です
- ソース PostgresSQL の pg_hba.conf に DMS エージェント IP を追加します
    1. DMS のインスタンスのプロビジョニングが完了したら、DMS の IP アドレスをメモしておきます。
    2. 次に示すように、pg_hba.conf ファイルに IP アドレスを追加します。

        host    all     172.16.136.18/10    md5  host    replication postgres    172.16.136.18/10    md5

- ユーザーには、ソース データベースをホストしているサーバーに対するスーパー ユーザー権限が必要です
- ソース データベース スキーマに ENUM があることに加えて、ソースとターゲットのデータベース スキーマが一致する必要があります。
- ターゲット Azure Database for PostgreSQL のスキーマに外部キーを含めることはできません。 外部キーを削除するには、次のクエリを使用します。

    ```
                                SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ', 
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema, 
        tc.constraint_name as foreignkey, 
        tc.table_name as tableName, 
        kcu.column_name, 
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name 
    FROM 
        information_schema.table_constraints AS tc 
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    
    ```

    クエリの結果内の外部キー削除 (2 列目) を実行します。

- ターゲット Azure Database for PostgreSQL のスキーマにトリガーを含めることはできません。 ターゲット データベース内のトリガーを無効にするには、次のコマンドを使用します。

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>データ型に関する制限事項

- **制限事項**:ソース PostgreSQL データベースに ENUM データ型がある場合、移行は、継続的同期中に失敗します。

    **対処法**: ENUM データ型を、Azure Database for PostgreSQL で変化する文字に変更します。

- **制限事項**:テーブルに主キーがない場合、継続的同期は失敗します。

    **対処法**: 移行を続行するには、テーブルの主キーを一時的に設定します。 データの移行が完了した後は、主キーを削除できます。

## <a name="lob-limitations"></a>LOB に関する制限事項
ラージ オブジェクト (LOB) 列は、サイズが大きくなる可能性のある列です。 PostgreSQL では、LOB データ型の例として、XML、JSON、IMAGE、TEXT などがあります。

- **制限事項**:LOB のデータ型を主キーとして使用すると、移行は失敗します。

    **対処法**: 主キーを、LOB ではない他のデータ型または列に置き換えます。

- **制限事項**:ラージ オブジェクト (LOB) 列の長さが 32 KB を超える場合、ターゲットにおいてデータが切り捨てられることがあります。 次のクエリを使用して、LOB 列の長さを確認できます。

    ```
    SELECT max(length(cast(body as text))) as body FROM customer_mail
    ```

    **対処法**: 32 KB を超える LOB 列がある場合は、[Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) でエンジニアリング チームに相談してください。

- **制限事項**:テーブルにLOB 列があり、テーブルに主キーが設定されていない場合、このテーブルのデータが移行されない可能性があります。

    **対処法**: 移行を続行するには、テーブルの主キーを一時的に設定します。 データの移行が完了した後は、主キーを削除できます。

## <a name="postgresql10-workaround"></a>PostgreSQL10 の対処法
PostgreSQL 10.x では pg_xlog フォルダー名にさまざまな変更が加えられているので、移行が期待どおりに実行されないことがあります。 PostgreSQL 10.x から Azure Database for PostgreSQL 10.3 に移行する場合は、ソース PostgreSQL データベースに対して次のスクリプトを実行して、pg_xlog 関数の周囲にラッパー関数を作成します。

```
BEGIN;
CREATE SCHEMA IF NOT EXISTS fnRenames;
CREATE OR REPLACE FUNCTION fnRenames.pg_switch_xlog() RETURNS pg_lsn AS $$ 
   SELECT pg_switch_wal(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_pause() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_pause(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_resume() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_resume(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_is_xlog_replay_paused() RETURNS boolean AS $$ 
   SELECT pg_is_wal_replay_paused(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name(lsn pg_lsn) RETURNS TEXT AS $$ 
   SELECT pg_walfile_name(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_replay_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_replay_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_receive_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_receive_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_flush_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_flush_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_insert_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_insert_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_location_diff(lsn1 pg_lsn, lsn2 pg_lsn) RETURNS NUMERIC AS $$ 
   SELECT pg_wal_lsn_diff(lsn1, lsn2); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name_offset(lsn pg_lsn, OUT TEXT, OUT INTEGER) AS $$ 
   SELECT pg_walfile_name_offset(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_create_logical_replication_slot(slot_name name, plugin name, 
   temporary BOOLEAN DEFAULT FALSE, OUT slot_name name, OUT xlog_position pg_lsn) RETURNS RECORD AS $$ 
   SELECT slot_name::NAME, lsn::pg_lsn FROM pg_catalog.pg_create_logical_replication_slot(slot_name, plugin, 
   temporary); $$ LANGUAGE SQL;
ALTER USER PG_User SET search_path = fnRenames, pg_catalog, "$user", public;

-- DROP SCHEMA fnRenames CASCADE;
-- ALTER USER PG_User SET search_path TO DEFAULT;
COMMIT;
```

## <a name="other-limitations"></a>その他の制限事項
- データベース名にセミコロン (;) を含めることはできません。
- 開く中かっこと閉じる中かっこ {  } を含むパスワード文字列はサポートされていません。 この制限は、ソース PostgreSQL とターゲット Azure Database for PostgreSQL の両方への接続に適用されます。
- キャプチャしたテーブルには主キーが必要です。 テーブルに主キーがない場合、DELETE および UPDATE レコード操作の結果は予測できません。
- 主キー セグメントの更新は無視されます。 このような場合、こうした更新の適用は、ターゲットによってどの行も更新しなかった更新として識別され、例外テーブルにレコードが書き込まれることになります。
- 名前が同じで大文字/小文字が異なる複数のテーブル (例: table1、TABLE1、Table1) の移行は、予期しない動作が発生する原因となるためサポートされていません。
- [CREATE | ALTER | DROP] テーブル DDL の変更処理は、内部関数/プロシージャ本体のブロックまたはその他の入れ子になった構造で保持されている場合を除き、サポートされます。 たとえば、次の変更はキャプチャされません。

    ```
    CREATE OR REPLACE FUNCTION pg.create_distributors1() RETURNS void
    LANGUAGE plpgsql
    AS $$
    BEGIN
    create table pg.distributors1(did serial PRIMARY KEY,name varchar(40)
    NOT NULL);
    END;
    $$;
    ```

- TRUNCATE 操作の変更処理 (継続的同期) はサポートされていません。 パーティション テーブルの移行はサポートされていません。 パーティション テーブルが検出されると、次の処理が行われます。
    - データベースによって、親と子のテーブルの一覧がレポートされます。
    - テーブルは、選択したテーブルと同じプロパティを持つ通常のテーブルとしてターゲット上に作成されます。
    - ソース データベース内の親テーブルに、その子テーブルと同じ主キー値がある場合は、「重複するキー」エラーが生成されます。
- DMS では、1 回の移行アクティビティで移行できるデータベースは最大で 4 個です。