---
title: 既知の問題:PostgreSQL から Azure Database for PostgreSQL へのオンライン移行
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service を使用した PostgreSQL から Azure Database for PostgreSQL へのオンライン移行に関する既知の問題と移行の制限事項について説明します。
services: database-migration
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: fdefcabdda64402610f115832976ec9f7af81b80
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258831"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql"></a>PostgreSQL から Azure DB for PostgreSQL へのオンライン移行に関する既知の問題と移行の制限事項

PostgreSQL から Azure Database for PostgreSQL へのオンライン移行に関する既知の問題点と制限事項について、後続のセクションで説明します。

## <a name="online-migration-configuration"></a>オンライン移行の構成

- ソースの PostgreSQL サーバーでは、バージョン 9.4、9.5、9.6、10、11 が実行されている必要があります。 詳しくは、「[サポートされている PostgreSQL Database バージョン](../postgresql/concepts-supported-versions.md)」の記事をご覧ください。
- 同じバージョンまたはそれ以降のバージョンへの移行のみがサポートされます。 たとえば、PostgreSQL 9.5 から Azure Database for PostgreSQL 9.6 または 10 への移行はサポートされていますが、PostgreSQL 11 から PostgreSQL 9.6 への移行はサポートされていません。
- **ソース PostgreSQL の postgresql.conf** ファイルで論理レプリケーションを有効にするには、次のパラメーターを設定します。
  - **wal_level** = logical
  - **max_replication_slots** = [移行対象のデータベースの最大数以上]。4 つのデータベースを移行する場合は、値を 4 以上に設定します
  - **max_wal_senders** = [同時に実行されるデータベース数]。推奨値は 10 です
- ソース PostgreSQL の pg_hba.conf に DMS エージェント IP を追加します
  1. Azure Database Migration Service のインスタンスのプロビジョニングが完了したら、DMS の IP アドレスを書き留めます。
  2. 次に示すように、pg_hba.conf ファイルに IP アドレスを追加します。

      ```
          host    all    172.16.136.18/10    md5
          host    replication postgres    172.16.136.18/10     md5
      ```

- ユーザーは、ソース データベースをホストするサーバー上でレプリケーション ロールを持っている必要があります。
- ソースとターゲットのデータベース スキーマが一致している必要があります。
- ターゲット Azure Database for PostgreSQL-Single Server のスキーマに外部キーを含めることはできません。 外部キーを削除するには、次のクエリを使用します。

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

- ターゲット Azure Database for PostgreSQL-Single Server のスキーマにトリガーを含めることはできません。 ターゲット データベース内のトリガーを無効にするには、次のコマンドを使用します。

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="size-limitations"></a>サイズの制限
- 1 つの DMS サービスを使用して、PostgreSQL から Azure DB for PostgreSQL に最大 2 TB のデータを移行できます。
## <a name="datatype-limitations"></a>データ型に関する制限事項

  **制限事項**:テーブルに主キーがない場合は、変更がターゲット データベースと同期されない可能性があります。

  **回避策**:移行を続行するには、テーブルの主キーを一時的に設定します。 データの移行が完了した後は、主キーを削除できます。

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>AWS RDS PostgreSQL からオンラインで移行するときの制限事項

AWS RDS PostgreSQL から Azure Database for PostgreSQL へのオンライン移行を実行しようとすると、次のエラーが発生する場合があります。

- **Error**: The Default value of column '{column}' in table '{table}' in database '{database}' is different on source and target servers.\(データベース '{database}' のテーブル '{table}' にある列 '{column}' の既定値が、ソース サーバーとターゲット サーバーで異なります。\) It's '{value on source}' on source and '{value on target}' on target.\(ソースでは '{value on source}'、ターゲットでは '{value on target}' です。\)

  **制限事項**:このエラーは、列スキーマの既定値がソース データベースとターゲット データベースで異なる場合に発生します。
  **回避策**:ターゲットのスキーマがソースのスキーマと一致していることを確認してください。 スキーマの移行の詳細については、[Azure PostgreSQL のオンライン移行に関するドキュメント](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema)をご覧ください。

- **Error**: ターゲット データベース '{database}' には '{number of tables}' 個のテーブルが含まれていますが、ソース データベース '{database}' に含まれるテーブルは '{number of tables}' 個です。 ソース データベースとターゲット データベースのテーブル数は同じでなければなりません。

  **制限事項**:このエラーは、ソース データベースとターゲット データベースのテーブル数が異なる場合に発生します。

  **回避策**:ターゲットのスキーマがソースのスキーマと一致していることを確認してください。 スキーマの移行の詳細については、[Azure PostgreSQL のオンライン移行に関するドキュメント](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema)をご覧ください。

- **エラー:** ソース データベース {database} が空です。

  **制限事項**:このエラーは、ソース データベースが空の場合に発生します。 これは、間違ったデータベースをソースとして選択したことが原因と考えられます。

  **回避策**:移行用に選択したソース データベースを再確認して、再試行してください。

- **エラー:** ターゲット データベース {database} が空です。 スキーマを移行してください。

  **制限事項**:このエラーは、ターゲット データベースにスキーマがない場合に発生します。 ターゲットのスキーマがソースのスキーマと一致していることを確認してください。
  **回避策**:ターゲットのスキーマがソースのスキーマと一致していることを確認してください。 スキーマの移行の詳細については、[Azure PostgreSQL のオンライン移行に関するドキュメント](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema)をご覧ください。

## <a name="other-limitations"></a>その他の制限事項

- データベース名にセミコロン (;) を含めることはできません。
- キャプチャしたテーブルには主キーが必要です。 テーブルに主キーがない場合、DELETE および UPDATE レコード操作の結果は予測できません。
- 主キー セグメントの更新は無視されます。 このような場合、こうした更新の適用は、ターゲットによってどの行も更新しなかった更新として識別され、例外テーブルにレコードが書き込まれることになります。
- 名前が同じで大文字/小文字が異なる複数のテーブル (例: table1、TABLE1、Table1) の移行は、予期しない動作が発生する原因となるためサポートされていません。
- [CREATE | ALTER | DROP | TRUNCATE] テーブル DDL の変更処理はサポートされていません。
- Azure Database Migration Service では、1 つの移行アクティビティで最大 4 つのデータベースにのみ対応できます。
- pg_largeobject テーブルの移行はサポートされていません。 
