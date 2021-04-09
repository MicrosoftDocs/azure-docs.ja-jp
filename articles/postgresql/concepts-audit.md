---
title: 監査ログ - Azure Database for PostgreSQL - Single Server
description: Azure Database for PostgreSQL - Single Server での pgAudit 監査ログの概念。
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 33fa6420f52cae9c869cc75a04ea82de0ec48262
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100596289"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - Single Server の監査ログ

Azure Database for PostgreSQL - Single Server でのデータベース アクティビティの監査ログは、PostgreSQL の監査拡張機能 [pgAudit](https://www.pgaudit.org/) を使用して入手できます。 pgAudit は、セッションまたはオブジェクトの詳細な監査ログを提供します。

> [!NOTE]
> pgAudit は Azure Database for PostgreSQL ではプレビュー段階にあります。
> この拡張機能は、汎用サーバーとメモリ最適化サーバー上でのみ有効にすることができます。

コンピューティングやストレージのスケーリングなどの操作に Azure リソースレベルのログが必要な場合は、[Azure アクティビティ ログ](../azure-monitor/essentials/platform-logs-overview.md)に関する記事を参照してください。

## <a name="usage-considerations"></a>使用に関する考慮事項
既定では、pgAudit ログ ステートメントは、Postgres の標準ログ記録機能を使用して、通常のログ ステートメントと共に出力されます。 Azure Database for PostgreSQL では、これらの .log ファイルを Azure portal または CLI を介してダウンロードできます。 ファイルのコレクション用の最大ストレージ容量は 1 GB で、各ファイルは最大 7 日間 (既定では 3 日間) 使用できます。 このサービスは、短期的なストレージ オプションです。

または、Azure Monitor ログ ストアに送信されるすべてのログを、Log Analytics で後で分析できるように構成することもできます。 Azure Monitor のリソース ログを有効にすると、選択した内容に応じて、ログが (JSON 形式で) Azure Storage、Event Hubs、または Azure Monitor ログに自動的に送信されます。

pgAudit を有効にすると、サーバー上に大量のログ記録が生成され、パフォーマンスとログ ストレージに影響を与えます。 長期的なストレージ オプションのほか、分析やアラートの機能も提供する、Azure Monitor ログを使用することをお勧めします。 追加のログ記録によるパフォーマンスへの影響を軽減するために、標準のログ記録を無効にすることをお勧めします。

   1. パラメーター `logging_collector` を OFF に設定します。 
   2. この変更を適用するには、サーバーを再起動します。

Azure Storage、Event Hubs、または Azure Monitor ログへのログ記録を設定する方法については、[サーバー ログに関する記事](concepts-server-logs.md)のリソース ログのセクションを参照してください。

## <a name="installing-pgaudit"></a>pgAudit のインストール

pgAudit をインストールするには、それをサーバーの共有プリロード ライブラリに含める必要があります。 Postgres の `shared_preload_libraries` パラメーターへの変更を有効にするには、サーバーの再起動が必要です。 パラメーターを変更するには、[Azure portal](howto-configure-server-parameters-using-portal.md)、[Azure CLI](howto-configure-server-parameters-using-cli.md)、または [REST API](/rest/api/postgresql/configurations/createorupdate) を使用できます。

[Azure portal](https://portal.azure.com) を使用して以下を実行します。

   1. Azure Database for PostgreSQL サーバーを選択します。
   2. サイドバーから、 **[サーバー パラメーター]** を選択します。
   3. `shared_preload_libraries` パラメーターを検索します。
   4. **[pgaudit]** を選択します。
   5. サーバーを再起動して変更を適用します。

   6. クライアント (psql など) を使用してサーバーに接続し、pgAudit 拡張機能を有効にします。
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> エラーが表示される場合は、`shared_preload_libraries` を保存した後にサーバーを再起動したことを確認してください。

## <a name="pgaudit-settings"></a>pgAudit の設定

pgAudit では、セッションまたはオブジェクトの監査ログを構成できます。 [セッション監査ログ](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging)では、実行されたステートメントの詳細なログが出力されます。 [オブジェクト監査ログ](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging)は、特定の関係だけを対象にした監査です。 設定するログ記録の種類を 1 つにするか両方にするか選択できます。 

> [!NOTE]
> pgAudit 設定はグローバルに指定され、データベース レベルまたはロール レベルでは指定できません。

[pgAudit のインストール](#installing-pgaudit)が完了したら、ログ記録を開始するようそのパラメーターを構成できます。 [pgAudit のドキュメント](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings)には、各パラメーターの定義が記載されています。 まずパラメーターをテストし、期待どおりに動作することを確認します。

> [!NOTE]
> `pgaudit.log_client` を ON に設定すると、ログはファイルに書き込まれるのではなく、クライアント プロセス (psql など) にリダイレクトされます。 通常、この設定は無効のままにしておく必要があります。 <br> <br>
> `pgaudit.log_level` が有効になるのは、`pgaudit.log_client` がオンになっている場合のみです。

> [!NOTE]
> Azure Database for PostgreSQL では、pgAudit のドキュメントで説明されているように、`pgaudit.log` を `-` (マイナス) 記号のショートカットを使用して設定することはできません。 必要なステートメント クラス (READ、WRITE など) はすべて、個別に指定する必要があります。

### <a name="audit-log-format"></a>監査ログの形式
各監査エントリは、ログ行の先頭付近の `AUDIT:` によって示されます。 エントリの残りの部分の形式については、[pgAudit のドキュメント](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)で詳しく説明されています。

実際の監査要件を満たすためにその他のフィールドが必要な場合は、Postgres パラメーター `log_line_prefix` を使用します。 `log_line_prefix` は、各 Postgres ログ行の先頭に出力される文字列です。 たとえば、次の `log_line_prefix` 設定では、タイムスタンプ、ユーザー名、データベース名、およびプロセス ID が提供されます。

```
t=%m u=%u db=%d pid=[%p]:
```

`log_line_prefix` の詳細を確認するには、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX)にアクセスしてください。

### <a name="getting-started"></a>はじめに
すぐに作業を開始するには、`pgaudit.log` を `WRITE` に設定し、ログを開いて出力を確認します。 

## <a name="viewing-audit-logs"></a>監査ログの表示
.log ファイルを使用している場合、監査ログは、PostgreSQL エラーログと同じファイルに含まれます。 ログ ファイルは、Azure [portal](howto-configure-server-logs-in-portal.md) または [CLI](howto-configure-server-logs-using-cli.md) からダウンロードできます。 

Azure リソース ログを使用している場合、ログへのアクセス方法は、選択したエンドポイントによって異なります。 Azure Storage については、[ログ ストレージ アカウント](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)に関する記事を参照してください。 Event Hubs の場合は、[Azure ログのストリーミング](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)に関する記事を参照してください。

Azure Monitor ログの場合は、選択したワークスペースにログが送信されます。 Postgres ログでは **AzureDiagnostics** コレクション モードが使用されるため、AzureDiagnostics テーブルからクエリを実行できます。 表内のフィールドについては、以下で説明します。 クエリとアラートの詳細については、[Azure Monitor のログ クエリ](../azure-monitor/logs/log-query-overview.md)の概要に関する記事を参照してください。

このクエリを使用して作業を開始できます。 クエリに基づいてアラートを構成できます。

過去 1 日の特定のサーバーに関するすべての Postgres ログを検索する
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>次のステップ
- [Azure Database for PostgreSQL でのログ記録について学習する](concepts-server-logs.md)
- [Azure portal](howto-configure-server-parameters-using-portal.md)、[Azure CLI](howto-configure-server-parameters-using-cli.md)、または [REST API](/rest/api/postgresql/configurations/createorupdate) を使用してパラメーターを設定する方法を学習する