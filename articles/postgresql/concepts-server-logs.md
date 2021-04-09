---
title: ログ - Azure Database for PostgreSQL - Single Server
description: Azure Database for PostgreSQL - Single Server でのログの構成、保存、分析について説明します。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: 621d5a6a91a8c22c52e6febc7c2638571f5bf113
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100595802"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - Single Server 内のログ

Azure Database for PostgreSQL では、Postgres の標準ログを構成してアクセスできます。 ログは、構成エラーと十分に最適化されていないパフォーマンスの特定、トラブルシューティング、修復に使用できます。 構成してアクセスできるログ情報には、エラー、クエリ情報、自動バキューム レコード、接続、チェックポイントが含まれます。 (トランザクション ログへのアクセスは利用できません)。

監査ログは、Postgres 拡張機能 pgaudit を通じて利用できます。 詳細については、[監査の概念](concepts-audit.md)に関する記事をご覧ください。


## <a name="configure-logging"></a>ログの構成 
サーバー パラメーターのログを使用して、サーバー上で Postgres 標準ログを構成できます。 各 Azure Database for PostgreSQL サーバーでは、`log_checkpoints` と `log_connections` が既定でオンになっています。 ログのニーズに合わせて調整できる追加のパラメーターがあります。 

:::image type="content" source="./media/concepts-server-logs/log-parameters.png" alt-text="Azure Database for PostgreSQL - ログ パラメーター":::

Postgres ログ パラメーターの詳細については、Postgres ドキュメントの[ログに記録するタイミング](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN)と[ログに記録する内容](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT)のセクションを参照してください。 すべてではありませんがほとんどの Postgre ログ パラメーターを Azure Database for PostgreSQL で構成できます。

Azure Database for PostgreSQL のパラメーターを構成する方法については、[portal のドキュメント](howto-configure-server-parameters-using-portal.md)または [CLI のドキュメント](howto-configure-server-parameters-using-cli.md)を参照してください。 

> [!NOTE]
> ステートメントのログ記録などの大量のログを構成すると、パフォーマンスのオーバーヘッドが大幅に増加する可能性があります。 

## <a name="access-log-files"></a>.log ファイルへのアクセス
Azure Database for PostgreSQL での既定のログ形式は .log です。 このログのサンプル行は次のようになります。

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

Azure Database for PostgreSQL には、.log ファイル用に短期的な保存場所が用意されています。 新しいファイルは、1 時間ごとまたは 100 MB ごと (どちらか早い方) に開始されます。 Postgres から出力されるときに、現在のファイルにログが追加されます。  

この短期的なログ ストレージのリテンション期間は、`log_retention_period` パラメーターを使用して設定できます。 既定値は 3 日間です。最大値は 7 日間です。 短期的な保存場所には、最大 1 GB のログ ファイルを保持できます。 1 GB を超えると、保持期間に関係なく最も古いファイルが削除されて、新しいログ用の領域が確保されます。 

ログとログ分析をより長期にわたって保有する場合は、.log ファイルをダウンロードして、サードパーティのサービスに移動できます。 [Azure portal](howto-configure-server-logs-in-portal.md)、[Azure CLI](howto-configure-server-logs-using-cli.md) を使用してファイルをダウンロードできます。 または、(JSON 形式の) ログを長期的な場所に自動的に出力する Azure Monitor 診断設定を構成することもできます。 このオプションの詳細については、以下のセクションを参照してください。 

パラメーター `logging_collector` をオフに設定すると、.log ファイルの生成を停止できます。 Azure Monitor の診断設定を使用している場合は、.log ファイルの生成をオフにすることをお勧めします。 この構成により、追加のログ記録によるパフォーマンスへの影響が軽減されます。

## <a name="resource-logs"></a>リソース ログ

Azure Database for PostgreSQL は、Azure Monitor の診断設定と統合されます。 診断設定を使用すると、Postgres ログを JSON 形式で分析とアラート用の Azure Monitor ログ、ストリーミング用の Event Hubs、アーカイブ用の Azure Storage に送信できます。 

> [!IMPORTANT]
> サーバー ログに対するこの診断機能は、General Purpose 価格レベルとメモリ最適化[価格レベル](concepts-pricing-tiers.md)でのみ使用できます。


### <a name="configure-diagnostic-settings"></a>診断設定を構成する

Azure portal、CLI、REST API、Powershell を使用して、Postgres サーバーの診断設定を有効にすることができます。 選択するログ カテゴリは **PostgreSQLLogs** です。 ([クエリ ストア](concepts-query-store.md)を使用している場合は、他にも構成できるログがあります。)

Azure portal を使用してリソース ログを有効にするには

   1. ポータルで、Postgres サーバーのナビゲーション メニューの *[診断設定]* に移動します。
   2. *[診断設定の追加]* を選択します。
   3. この設定に名前を付けます。 
   4. 任意の優先エンドポイント (ストレージ アカウント、イベント ハブ、ログ分析) を選択します。 
   5. ログの種類 **PostgreSQLLogs** を選択します。
   7. 設定を保存します。

Powershell、CLI、または REST API を使用してリソース ログを有効にするには、[診断の設定](../azure-monitor/essentials/diagnostic-settings.md)に関する記事をご覧ください。

### <a name="access-resource-logs"></a>リソース ログへのアクセス

ログへのアクセス方法は、選択したエンドポイントによって異なります。 Azure Storage については、[ログ ストレージ アカウント](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)に関する記事を参照してください。 Event Hubs の場合は、[Azure ログのストリーミング](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)に関する記事を参照してください。

Azure Monitor ログの場合は、選択したワークスペースにログが送信されます。 Postgres ログでは **AzureDiagnostics** コレクション モードが使用されるため、AzureDiagnostics テーブルからクエリを実行できます。 表内のフィールドについては、以下で説明します。 クエリとアラートの詳細については、[Azure Monitor のログ クエリ](../azure-monitor/logs/log-query-overview.md)の概要に関する記事を参照してください。

次に、作業を開始するために試すことのできるクエリを示します。 クエリに基づいてアラートを構成できます。

過去 1 日の特定のサーバーに関するすべての Postgres ログを検索する
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where Category == "PostgreSQLLogs"
| where TimeGenerated > ago(1d) 
```

localhost 以外のすべての接続の試行を検索する
```
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```
上記のクエリを使うと、このワークスペースにログインしている任意の Postgres サーバーについて過去 6 時間の結果が表示されます。

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
- [Azure portal](howto-configure-server-logs-in-portal.md) または [Azure CLI](howto-configure-server-logs-using-cli.md) からのログへのアクセスを確認する。
- [Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)を確認する。
- [監査ログ](concepts-audit.md)の詳細を確認する