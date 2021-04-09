---
title: SQL データ同期でスキーマ変更のレプリケーションを自動化する
description: Azure SQL データ同期でスキーマ変更のレプリケートを自動化する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/14/2018
ms.openlocfilehash: f9997ea737b96185b31a7f51996a396fb5fc46c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92790187"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>Azure SQL データ同期でスキーマ変更のレプリケートを自動化する
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

SQL データ同期を使用すると、Azure SQL 内のデータベースと SQL Server インスタンス間で、データを一方向または双方向に同期できます。 SQL データ同期における現在の制限事項の 1 つは、スキーマ変更のレプリケートのサポートが十分ではないことです。 テーブル スキーマを変更するたびに、ハブやすべてのメンバーを含め、すべてのエンドポイントに変更を手動で適用してから、同期スキーマを更新する必要があります。

この記事では、すべての SQL データ同期エンドポイントにスキーマの変更を自動的にレプリケートするソリューションを紹介します。

1. このソリューションでは DDL トリガーを使用して、スキーマの変更を追跡します。
1. トリガーによって、スキーマ変更コマンドが追跡テーブルに挿入されます。
1. この追跡テーブルが、データ同期サービスを使って、すべてのエンドポイントに同期されます。
1. 挿入後の DML トリガーは、他のエンドポイントにスキーマの変更を適用するために使用されます。

この記事では、スキーマ変更の例として ALTER TABLE を使用していますが、このソリューションは他の種類のスキーマ変更にも有効です。

> [!IMPORTANT]
> お使いの同期環境にスキーマ変更のレプリケートの自動化を実装し始める前に、特に「[トラブルシューティング](#troubleshoot)」および「[その他の考慮事項](#other)」のセクションを中心に、この記事を慎重に読むことをお勧めします。 また、「[SQL データ同期を使用して複数のクラウドおよびオンプレミス データベース間でデータを同期する](sql-data-sync-data-sql-server-sql-database.md)」にも目を通すことをお勧めします。一部のデータベース操作によって、この記事で説明されているソリューションが損われる可能性があります。 このような問題をトラブルシューティングするために、SQL Server および Transact-SQL の詳細なドメインの知識が必要になる場合があります。

![スキーマ変更のレプリケートを自動化する](./media/sql-data-sync-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>自動化されたスキーマ変更のレプリケートを設定する

### <a name="create-a-table-to-track-schema-changes"></a>スキーマ変更を追跡するテーブルを作成する

同期グループ内のすべてのデータベースのスキーマ変更を追跡するためのテーブルを作成します。

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

このテーブルには、スキーマ変更の順序を追跡するための ID 列が含まれます。 必要に応じて、フィールドをさらに追加して、より多くの情報を記録できます。

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>スキーマ変更の履歴を追跡するテーブルを作成する

すべてのエンドポイント上で、直近で適用されたスキーマ変更コマンドの ID を追跡するテーブルを作成します。

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>スキーマ変更が行われたデータベースで ALTER TABLE DDL トリガーを作成する

ALTER TABLE 操作のための DDL トリガーを作成します。 スキーマ変更が行われたデータベースに、このトリガーを作成するだけでかまいません。 競合を避けるために、同期グループごとに 1 つのデータベースでしか、スキーマ変更が許可されていません。

```sql
CREATE TRIGGER AlterTableDDLTrigger
ON DATABASE
FOR ALTER_TABLE
AS

-- You can add your own logic to filter ALTER TABLE commands instead of replicating all of them.

IF NOT (EVENTDATA().value('(/EVENT_INSTANCE/SchemaName)[1]', 'nvarchar(512)') like 'DataSync')

INSERT INTO SchemaChanges (SqlStmt, Description)
    VALUES (EVENTDATA().value('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]', 'nvarchar(max)'), 'From DDL trigger')
```

トリガーでは、ALTER TABLE コマンドごとに、スキーマ変更追跡テーブルにレコードを挿入します。 スキーマ **DataSync** 下で行われたスキーマ変更は、データ同期サービスによって行われたことが十分予想できるため、この例では、これらの変更のレプリケートを防止するためにフィルターを追加しています。 特定の種類のスキーマ変更だけをレプリケートしたい場合は、さらにフィルターを追加します。

また、他の種類のスキーマ変更をレプリケートするために、さらにトリガーを追加することも可能です。 たとえば、CREATE_PROCEDURE、ALTER_PROCEDURE、および DROP_PROCEDURE トリガーを作成して、ストアド プロシージャに変更をレプリケートします。

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>他のエンドポイント上にトリガーを作成して、挿入中にスキーマ変更を適用する

このトリガーは、他のエンドポイントに同期されるときに、スキーマ変更のコマンドを実行します。 スキーマ変更が行われた (つまり、前の手順で DDL トリガー `AlterTableDDLTrigger` が作成されたデータベース内の) エンドポイントを除くすべてのエンドポイント上に、このトリガーを作成する必要があります。

```sql
CREATE TRIGGER SchemaChangesTrigger
ON SchemaChanges
AFTER INSERT
AS
DECLARE \@lastAppliedId bigint
DECLARE \@id bigint
DECLARE \@sqlStmt nvarchar(max)
SELECT TOP 1 \@lastAppliedId=LastAppliedId FROM SchemaChangeHistory
SELECT TOP 1 \@id = id, \@SqlStmt = SqlStmt FROM SchemaChanges WHERE id \> \@lastAppliedId ORDER BY id
IF (\@id = \@lastAppliedId + 1)
BEGIN
    EXEC sp_executesql \@SqlStmt
        UPDATE SchemaChangeHistory SET LastAppliedId = \@id
    WHILE (1 = 1)
    BEGIN
        SET \@id = \@id + 1
        IF exists (SELECT id FROM SchemaChanges WHERE ID = \@id)
            BEGIN
                SELECT \@sqlStmt = SqlStmt FROM SchemaChanges WHERE ID = \@id
                EXEC sp_executesql \@SqlStmt
                UPDATE SchemaChangeHistory SET LastAppliedId = \@id
            END
        ELSE
            BREAK;
    END
END
```

このトリガーは、挿入後に実行され、現在のコマンドを次に実行する必要があるかどうかをチェックします。 コード ロジックでは、スキーマ変更のどのステートメントもスキップされていないこと、また、挿入が順不同な場合にもすべての変更が適用されることを保証しています。

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>スキーマ変更追跡テーブルをすべてのエンドポイントに同期する

既存の同期グループまたは新しい同期グループを使用して、すべてのエンドポイントにスキーマ変更追跡テーブルを同期できます。 特に一方向の同期を使用している場合、追跡テーブルの変更が必ずすべてのエンドポイントに同期されるようにします。

スキーマ変更履歴テーブルは、さまざまなエンドポイント上で異なるステータスを保持するため、このテーブルを同期しないでください。

### <a name="apply-the-schema-changes-in-a-sync-group"></a>同期グループでスキーマ変更を適用する

DDL トリガーが作成されたデータベースで行われたスキーマ変更のみが、レプリケートされます。 他のデータベースで行われたスキーマ変更は、レプリケートされません。

また、スキーマ変更がすべてのエンドポイントにレプリケートされた後は、同期スキーマを更新する追加の手順を実行して、新しい列の同期を開始または停止する必要があります。

#### <a name="add-new-columns"></a>新しい列を追加する

1.  スキーマへの変更を行います。

1.  トリガーを作成する手順が完了するまで、新しい列が関連するすべてのデータ変更を防止します。

1.  スキーマの変更がすべてのエンドポイントに適用されるまで待機します。

1.  データベース スキーマを更新して、新しい列を同期スキーマに追加します。

1.  次の同期操作の中で、新しい列のデータが同期されます。

#### <a name="remove-columns"></a>列の削除

1.  同期スキーマから列を削除します。 データ同期は、これらの列のデータの同期を停止します。

1.  スキーマへの変更を行います。

1.  データベース スキーマを更新します。

#### <a name="update-data-types"></a>データ型の更新

1.  スキーマへの変更を行います。

1.  スキーマの変更がすべてのエンドポイントに適用されるまで待機します。

1.  データベース スキーマを更新します。

1.  新規と従来のデータ型に、完全な互換性はありません。たとえば、`int` を `bigint` に変更した場合、トリガーを作成する手順が完了する前に、同期がエラーになる可能性があります。 同期は、再試行後に成功します。

#### <a name="rename-columns-or-tables"></a>列またはテーブル名の変更

列またはテーブルの名前を変更すると、データ同期は動作を停止します。 名前を変更する代わりに、新しいテーブルまたは列を作成し、データのバックフィルを行ってから、古いテーブルまたは列を削除します。

#### <a name="other-types-of-schema-changes"></a>その他の種類のスキーマ変更

ストアド プロシージャの作成やインデックスの削除など、その他の種類のスキーマ変更では、同期スキーマの更新は必要ありません。

## <a name="troubleshoot-automated-schema-change-replication"></a><a name="troubleshoot"></a>スキーマ変更のレプリケートの自動化に関するトラブルシューティング

この記事で説明したレプリケート ロジックは、Azure SQL Database でサポートされていないオンプレミス データベースでスキーマ変更を行った場合など、一定の状況下で動作を停止します。 停止した場合、スキーマ変更追跡テーブルの同期はエラーになります。 この問題は、次の手順を実行して、手動で修正する必要があります。

1.  DDL トリガーを無効にして、問題が解決されるまで、スキーマ変更がそれ以上行われないようにします。

1.  問題が発生しているエンドポイント データベースで、スキーマ変更を実行できないエンドポイントでの AFTER INSERT トリガーを無効にします。 このアクションによって、スキーマ変更コマンドを同期できるようになります。

1.  同期をトリガーして、スキーマ変更追跡テーブルを同期します。

1.  問題が発生しているエンドポイント データベースで、スキーマ変更履歴テーブルにクエリを実行し、直近で適用されたスキーマ変更コマンドの ID を取得します。

1.  スキーマ変更追跡テーブルにクエリを実行して、前の手順で取得した ID 値よりも大きい ID 値のすべてのコマンドを一覧表示します。

    a.  エンドポイント データベースで実行できないコマンドを無視します。 スキーマの不一致に対応する必要があります。 不一致がアプリケーションに影響を及ぼす場合は、元のスキーマ変更を元に戻します。

    b.  適用する必要があるコマンドを手動で適用します。

1.  スキーマ変更履歴テーブルを更新して、直近で適用された ID に正しい値を設定します。

1.  スキーマが最新かどうかを再確認します。

1.  手順 2 で無効にした AFTER INSERT トリガーをもう一度有効にします。

1.  手順 1 で無効にした DDL トリガーをもう一度有効にします。

スキーマ変更追跡テーブル内のレコードをクリーンアップする場合は、TRUNCATE ではなく DELETE を使用します。 絶対に DBCC CHECKIDENT を使用してスキーマ変更追跡テーブル内の ID 列を再シードしてはいけません。 再シードが必要な場合は、新しいスキーマ変更追跡テーブルを作成して、DDL トリガーの中でテーブル名を更新できます。

## <a name="other-considerations"></a><a name="other"></a>その他の考慮事項

-   ハブおよびメンバー データベースを構成するデータベース ユーザーは、スキーマ変更コマンドを実行するための十分な権限を保持している必要があります。

-   選択したテーブルまたは操作の中でスキーマの変更をレプリケートするためだけに、DDL トリガー内でさらにフィルターを追加できます。

-   DDL トリガーが作成されたデータベースでスキーマ変更を行うことしかできません。

-   SQL Server データベースで変更を行っている場合、Azure SQL Database でスキーマの変更がサポートされていることを確認してください。

-   DDL トリガーが作成されたデータベース以外のデータベースでスキーマ変更が行われた場合、変更はレプリケートされません。 この問題を回避するために、他のエンドポイント上での変更をブロックする DLL トリガーを作成できます。

-   スキーマ変更追跡テーブルのスキーマを変更する必要がある場合は、変更を加える前に DDL トリガーを無効にしてから、手動ですべてのエンドポイントに変更を適用します。 このテーブルに AFTER INSERT トリガーでスキーマを更新しても、機能しません。

-   DBCC CHECKIDENT を使用して ID 列を再シードしないでください。

-   TRUNCATE を使用して、スキーマ変更追跡テーブルのデータをクリーンアップしないでください。

## <a name="next-steps"></a>次のステップ

SQL データ同期の詳細については、以下を参照してください。

-   概要 - [Azure SQL データ同期を使用して複数のクラウドおよびオンプレミス データベース間でデータを同期する](sql-data-sync-data-sql-server-sql-database.md)
-   データ同期の設定
    - ポータル - [チュートリアル:Azure SQL Database と SQL Server の間でデータを同期するように SQL データ同期を設定する](sql-data-sync-sql-server-configure.md)
    - PowerShell の場合
        -  [PowerShell を使用して Azure SQL Database の複数データベース間で同期する](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [PowerShell を使用して Azure SQL Database と SQL Server インスタンスのデータベース間で同期する](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   データ同期エージェント - [Azure SQL データ同期のデータ同期エージェント](sql-data-sync-agent-overview.md)
-   ベスト プラクティス - [Azure SQL データ同期のベスト プラクティス](sql-data-sync-best-practices.md)
-   監視 - [Azure Monitor ログによる SQL データ同期の監視](./monitor-tune-overview.md)
-   トラブルシューティング - [Azure SQL データ同期に関する問題のトラブルシューティング]()
-   同期スキーマの更新
    -   PowerShell の場合 - [PowerShell を使用して、既存の同期グループの同期スキーマを更新する](scripts/update-sync-schema-in-sync-group.md)