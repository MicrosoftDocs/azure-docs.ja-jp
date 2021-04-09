---
title: ログ - Azure Database for PostgreSQL - フレキシブル サーバー
description: Azure Database for PostgreSQL - フレキシブルサーバーでのログの構成、保存、分析について説明します
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 852bce26e348c817b609d5ff837641741afe4461
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "90932474"
---
# <a name="logs-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - フレキシブル サーバーのログ

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

Azure Database for PostgreSQL では、Postgres の標準ログを構成してアクセスできます。 ログは、構成エラーと十分に最適化されていないパフォーマンスの特定、トラブルシューティング、修復に使用できます。 構成してアクセスできるログ情報には、エラー、クエリ情報、自動バキューム レコード、接続、チェックポイントが含まれます。 (トランザクション ログへのアクセスは利用できません)。

監査ログは、Postgres 拡張機能 `pgaudit` を通じて利用できます。 詳細については、[監査の概念](concepts-audit.md)に関する記事をご覧ください。

## <a name="configure-logging"></a>ログの構成

サーバー パラメーターのログを使用して、サーバー上で Postgres 標準ログを構成できます。 Postgres ログ パラメーターの詳細については、Postgres ドキュメントの[ログに記録するタイミング](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN)と[ログに記録する内容](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT)のセクションを参照してください。 すべてではありませんがほとんどの Postgre ログ パラメーターを Azure Database for PostgreSQL で構成できます。

Azure Database for PostgreSQL のパラメーターを構成する方法については、[portal のドキュメント](howto-configure-server-parameters-using-portal.md)または [CLI のドキュメント](howto-configure-server-parameters-using-cli.md)を参照してください。

> [!NOTE]
> ステートメントのログ記録などの大量のログを構成すると、パフォーマンスのオーバーヘッドが大幅に増加する可能性があります。 

## <a name="accessing-logs"></a>ログへのアクセス

Azure Database for PostgreSQL は、Azure Monitor の診断設定と統合されます。 診断設定を使用すると、Postgres ログを JSON 形式で分析とアラート用の Azure Monitor ログ、ストリーミング用の Event Hubs、アーカイブ用の Azure Storage に送信できます。 

### <a name="log-format"></a>ログの形式

次の表では、**PostgreSQLLogs** タイプのフィールドについて説明します。 選択した出力エンドポイントに応じて、含まれるフィールドとそれらが表示される順序が異なることがあります。 

|**フィールド** | **説明** |
|---|---|
| TenantId | テナント ID |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | ログが記録されたときのタイムスタンプ (UTC) |
| Type | ログの種類。 常に `AzureDiagnostics` |
| SubscriptionId | サーバーが属するサブスクリプションの GUID |
| ResourceGroup | サーバーが属するリソース グループの名前 |
| ResourceProvider | リソース プロバイダーの名前。 常に `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | リソース URI |
| リソース | サーバーの名前 |
| カテゴリ | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | ログ レベル、例:LOG、ERROR、NOTICE |
| Message | プライマリ ログ メッセージ | 
| Domain | サーバーのバージョン (postgres 10 など) |
| Detail | セカンダリ ログ メッセージ (該当する場合) |
| ColumnName | 列の名前 (該当する場合) |
| SchemaName | スキーマの名前 (該当する場合) |
| DatatypeName | データ型の名前 (該当する場合) |
| LogicalServerName | サーバーの名前 | 
| _ResourceId | リソース URI |
| Prefix | ログ行のプレフィックス |


## <a name="next-steps"></a>次のステップ

- [ログの構成とアクセス方法](howto-configure-and-access-logs.md)を学習する。
- [Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)を確認する。
- [監査ログ](concepts-audit.md)の詳細を確認する
