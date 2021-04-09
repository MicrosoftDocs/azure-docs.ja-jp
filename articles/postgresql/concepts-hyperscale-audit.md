---
title: 監査ログ - Azure Database for PostgreSQL - Hyperscale (Citus)
description: Azure Database for PostgreSQL - Hyperscale (Citus) での pgAudit 監査ログの概念。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 8a36062a2d29bcec10279d73211526a0dcba619e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101702115"
---
# <a name="audit-logging-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus) での監査ログ

Azure Database for PostgreSQL - Hyperscale (Citus) でのデータベース アクティビティの監査ログは、PostgreSQL の監査拡張機能 [pgAudit](https://www.pgaudit.org/) を使用して入手できます。 pgAudit は、セッションまたはオブジェクトの詳細な監査ログを提供します。

> [!IMPORTANT]
> pgAudit は Azure Database for PostgreSQL - Hyperscale (Citus) ではプレビュー段階にあります。

コンピューティングやストレージのスケーリングなどの操作に Azure リソースレベルのログが必要な場合は、[Azure アクティビティ ログ](../azure-monitor/essentials/platform-logs-overview.md)に関する記事を参照してください。

## <a name="usage-considerations"></a>使用に関する考慮事項
既定では、pgAudit ログ ステートメントは、Postgres の標準ログ記録機能を使用して、通常のログ ステートメントと共に出力されます。 Azure Database for PostgreSQL - Hyperscale (Citus) では、Azure Monitor ログ ストアに送信されるすべてのログを、Log Analytics で後で分析できるように構成することができます。 Azure Monitor のリソース ログを有効にすると、選択した内容に応じて、ログが (JSON 形式で) Azure Storage、Event Hubs、または Azure Monitor ログに自動的に送信されます。

## <a name="enabling-pgaudit"></a>pgAudit の有効化

pgAudit 拡張機能は、すべての Hyperscale (Citus) サーバー グループ ノードに事前にインストールされ、有効になっています。 有効にするためのアクションは必要ありません。

## <a name="pgaudit-settings"></a>pgAudit の設定

pgAudit では、セッションまたはオブジェクトの監査ログを構成できます。 [セッション監査ログ](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging)では、実行されたステートメントの詳細なログが出力されます。 [オブジェクト監査ログ](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging)は、特定の関係だけを対象にした監査です。 設定するログ記録の種類を 1 つにするか両方にするか選択できます。 

> [!NOTE]
> pgAudit 設定はグローバルに指定され、データベース レベルまたはロール レベルでは指定できません。
>
> また、pgAudit の設定は、サーバー グループ内のノードごとに指定します。 すべてのノードに変更を加えるには、各ノードに個別に適用する必要があります。

pgAudit パラメーターを、ログ記録を開始するように構成する必要があります。 [pgAudit のドキュメント](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings)には、各パラメーターの定義が記載されています。 まずパラメーターをテストし、期待どおりに動作することを確認します。

> [!NOTE]
> `pgaudit.log_client` を ON に設定すると、ログはファイルに書き込まれるのではなく、クライアント プロセス (psql など) にリダイレクトされます。 通常、この設定は無効のままにしておく必要があります。 <br> <br>
> `pgaudit.log_level` が有効になるのは、`pgaudit.log_client` がオンになっている場合のみです。

> [!NOTE]
> pgAudit のドキュメントで説明されているように、Azure Database for PostgreSQL - Hyperscale (Citus) では、`-` (マイナス) 記号のショートカットを使用して、`pgaudit.log` を設定することはできません。 必要なステートメント クラス (READ、WRITE など) はすべて、個別に指定する必要があります。

## <a name="audit-log-format"></a>監査ログの形式
各監査エントリは、ログ行の先頭付近の `AUDIT:` によって示されます。 エントリの残りの部分の形式については、[pgAudit のドキュメント](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)で詳しく説明されています。

## <a name="getting-started"></a>作業の開始
すぐに始めるには、`pgaudit.log` を `WRITE` に設定し、サーバー ログを開いて出力を確認します。 

## <a name="viewing-audit-logs"></a>監査ログの表示
ログへのアクセス方法は、選択したエンドポイントによって異なります。 Azure Storage については、[ログ ストレージ アカウント](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)に関する記事を参照してください。 Event Hubs の場合は、[Azure ログのストリーミング](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)に関する記事を参照してください。

Azure Monitor ログの場合は、選択したワークスペースにログが送信されます。 Postgres ログでは **AzureDiagnostics** コレクション モードが使用されるため、AzureDiagnostics テーブルからクエリを実行できます。 表内のフィールドについては、以下で説明します。 クエリとアラートの詳細については、[Azure Monitor のログ クエリ](../azure-monitor/logs/log-query-overview.md)の概要に関する記事を参照してください。

このクエリを使用して作業を開始できます。 クエリに基づいてアラートを構成できます。

過去 1 日の特定のサーバーに関するすべての pgAudit エントリを Postgres ログで検索する
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>次のステップ

- [Azure Database for PostgreSQL - Hyperscale (Citus) でログ記録を設定し、ログにアクセスする方法を確認する](howto-hyperscale-logging.md)