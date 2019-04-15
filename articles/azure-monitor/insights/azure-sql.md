---
title: Azure Monitor の Azure SQL Analytics ソリューション | Microsoft Docs
description: Azure SQL Analytics ソリューションは、Azure SQL データベースの管理に役立ちます
services: log-analytics
ms.service: log-analytics
ms.custom: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: carlrab
manager: craigg
ms.date: 12/17/2018
ms.openlocfilehash: c68c278b2a7afa8287845c452e3bec5380cf05c0
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58629978"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Azure SQL Analytics (プレビュー) を使用した Azure SQL Database の監視

![Azure SQL Analytics のシンボル](./media/azure-sql/azure-sql-symbol.png)

Azure SQL Analytics は、大規模かつ複数のサブスクリプションにまたがる Azure SQL データベースやエラスティック プール、Managed Instance のパフォーマンスを、1 つの窓から監視するための高度なクラウド監視ソリューションです。 これを使用すると、パフォーマンスのトラブルシューティングのために、組み込みのインテリジェンスを使用して Azure SQL Database の重要なパフォーマンス メトリックを収集し、視覚化できます。

このソリューションを使用して収集できるメトリックを使用して、独自の監視ルールおよびアラートを作成できます。 このソリューションは、アプリケーション スタックの各層の問題を特定するのに役立ちます。 これは、Azure 診断メトリックを Azure Monitor ビューと共に使用して、マネージド インスタンス内のすべての Azure SQL データベース、エラスティック プール、およびデータベースに関するデータを 1 つの Log Analytics ワークスペースに表示します。 Azure Monitor は、構造化データや非構造化データの収集、関連付け、および視覚化に役立ちます。

Azure SQL Analytics ソリューションの使用に関する実践的な概要と、一般的な使用シナリオについては、埋め込みのビデオをご覧ください。

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>接続先ソース

Azure SQL Analytics は、Azure SQL データベース (単一、プール、およびマネージド インスタンスのデータベース) の診断テレメトリのストリーミングをサポートする唯一のクラウド監視ソリューションです。 このソリューションは Azure Monitor への接続にエージェントを使用しないため、オンプレミスまたは VM でホストされている SQL Server の監視はサポートされません。次の互換性に関する表を参照してください。

| 接続先ソース | サポートされています | 説明 |
| --- | --- | --- |
| [Azure 診断](../platform/collect-azure-metrics-logs.md) | **はい** | Azure のメトリックおよびログ データは、Azure によって直接 Azure Monitor ログに送信されます。 |
| [Azure Storage アカウント](../platform/collect-azure-metrics-logs.md) | いいえ  | Azure Monitor は、ストレージ アカウントからデータを読み取りません。 |
| [Windows エージェント](../platform/agent-windows.md) | いいえ  | このソリューションでは、直接の Windows エージェントは使用されません。 |
| [Linux エージェント](../learn/quick-collect-linux-computer.md) | いいえ  | このソリューションでは、直接の Linux エージェントは使用されません。 |
| [System Center Operations Manager 管理グループ](../platform/om-agents.md) | いいえ  | Operations Manager エージェントから Azure Monitor への直接の接続は、このソリューションでは使用されません。 |

## <a name="configuration"></a>構成
[Solutions Gallery からの Azure Monitor ソリューションの追加](../../azure-monitor/insights/solutions.md)に関するページで説明されているプロセスを使用して、Azure SQL Analytics (プレビュー) ソリューションを Log Analytics ワークスペースに追加します。

### <a name="configure-azure-sql-databases-elastic-pools-and-managed-instances-to-stream-diagnostics-telemetry"></a>診断テレメトリをストリーム配信するための Azure SQL Database、エラスティック プール、および Managed Instance の構成

ワークスペースに Azure SQL Analytics ソリューションを作成した後は、その診断テレメトリをソリューションにストリーム配信するように、監視するリソースの**それぞれを構成**する必要があります。 次のページの詳細手順に従ってください。

- Azure SQL データベースの Azure 診断を有効にして、[診断テレメトリを Azure SQL Analytics にストリーム配信](../../sql-database/sql-database-metrics-diag-logging.md)します。

上記手順ではまた､1 つの Azure SQL Analytics ワークスペースから複数の Azure サブスクリプションを 1 つの窓で監視するためのサポート機能を有効にする手順も説明しています｡ 

## <a name="using-the-solution"></a>ソリューションの使用

ソリューションをワークスペースに追加すると、Azure SQL Analytics のタイルがワークスペースに追加され、[概要] に表示されます。 タイルのコンテンツを読み込むには、[概要の表示] リンクを選択します。

![Azure SQL Analytics の概要タイル](./media/azure-sql/azure-sql-sol-tile-01.png)

読み込みが完了すると、タイルに、ソリューションが診断テレメトリを受信する Azure SQL Database、エラスティック プール、Managed Instance、および Managed Instance 内のデータベースの数が表示されます。

![Azure SQL Analytics のタイル](./media/azure-sql/azure-sql-sol-tile-02.png)

ソリューションには、2 つの独立したビューが用意されています。1 つは Azure SQL Database とエラスティック プールの監視用で、他のビューは、Managed Instance と、Managed Instance 内のデータベースの監視用です。

Azure SQL Database とエラスティック プール用の Azure SQL Analytics 監視ダッシュボードを表示するには、タイルの上部をクリックします。 Managed Instance と、Managed Instance 内のデータベース用の Azure SQL Analytics 監視ダッシュボードを表示するには、タイルの下部をクリックします。

### <a name="viewing-azure-sql-analytics-data"></a>Azure SQL Analytics データの表示

ダッシュボードには、さまざまなパースペクティブから監視されるすべてのデータベースの概要が含まれています。 さまざまなパースペクティブが機能するには、適切なメトリックを有効にするか、または Log Analytics ワークスペースにストリーミングされるように SQL リソースにログオンする必要があります。

Azure Monitor に一部のメトリックまたはログがストリーミングされないと、ソリューション内のタイルに監視情報が表示されないことに注意してください。

### <a name="azure-sql-database-and-elastic-pool-view"></a>Azure SQL Database とエラスティック プールのビュー

データベースの Azure SQL Analytics タイルが選択されると、監視ダッシュボードが表示されます。

![Azure SQL Analytics の概要](./media/azure-sql/azure-sql-sol-overview.png)

タイルのいずれかを選択すると、特定のパースペクティブでドリルダウン レポートが開きます。 パースペクティブを選択すると、ドリル ダウン レポートが開きます。

![Azure SQL Analytics のタイムアウト](./media/azure-sql/azure-sql-sol-metrics.png)

このビューの各パースペクティブは、サブスクリプション、サーバー、エラスティック プール、およびデータベース レベルの概要を提供します。 さらに、各パースペクティブは、右側にパースペクティブ特定のレポートを示します。 一覧からサブスクリプション、サーバー、プール、またはデータベースを選択するとドリル ダウンが続行されます。

### <a name="managed-instance-and-databases-in-managed-instance-view"></a>Managed Instance と Managed Instance 内のデータベースのビュー

データベースの Azure SQL Analytics タイルが選択されると、監視ダッシュボードが表示されます。

![Azure SQL Analytics の概要](./media/azure-sql/azure-sql-sol-overview-mi.png)

タイルのいずれかを選択すると、特定のパースペクティブでドリルダウン レポートが開きます。 パースペクティブを選択すると、ドリル ダウン レポートが開きます。

Managed Instance のビューを選択すると、Managed Instance の使用率、それに含まれるデータベース、インスタンス全体で実行されたクエリに関するテレメトリの詳細が表示されます。

![Azure SQL Analytics のタイムアウト](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="perspectives"></a>パースペクティブ

次の表に、2 つのバージョンのダッシュボードでサポートされるパースペクティブの概要を示します。1 つは Azure SQL Database とエラスティック プール用で、もう 1 つが Managed Instance 用です。

| パースペクティブ | 説明 | SQL Database とエラスティック プールのサポート | Managed Instance のサポート |
| --- | ------- | ----- | ----- |
| 種類別のリソース | 監視対象のすべてのリソースをカウントするパースペクティブです。 | はい | はい |
| 洞察 | パフォーマンスに対する Intelligent Insights の階層型のドリルダウンを提供します。 | はい | はい |
| Errors | データベースで発生した SQL エラーの階層型のドリルダウンを提供します | はい | はい |
| Timeouts | データベースで発生した SQL タイムアウトの階層型のドリルダウンを提供します | はい | いいえ  |
| ブロッキング | データベースで発生した SQL ブロッキングの階層型のドリルダウンを提供します | はい | いいえ  |
| データベース待機 | データベース レベルで発生した SQL 待機統計の階層型のドリルダウンを提供します 合計待機時間と待機の種類ごとの待機時間の概要が含まれます。 |はい | はい |
| クエリ実行時間 | クエリの実行時間、CPU 使用率、データ IO 使用率、ログ IO 使用率などのクエリ実行の統計の階層型のドリルダウンを提供します。 | はい | はい |
| クエリ待機 | 待機カテゴリ別に、クエリ待機統計の階層型のドリルダウンを提供します | はい | はい |

### <a name="intelligent-insights-report"></a>Intelligent Insights レポート

Azure SQL Database [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) では、すべての Azure SQL データベースのパフォーマンスに何が起きているかを把握できます。 収集されたすべてのインテリジェントな洞察を Insights パースペクティブを使用して視覚化およびアクセスできます。

![Azure SQL Analytics Insights](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>エラスティック プールとデータベースのレポート

エラスティック プールと SQL Database の両方に、指定された期間内にリソースについて収集されたすべてのデータを表示する、独自の具体的なレポートがあります。

![Azure SQL Analytics データベース](./media/azure-sql/azure-sql-sol-database.png)

![Azure SQL エラスティック プール](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>クエリのレポート

クエリの実行時間とクエリの待機のパースペクティブでは、クエリ レポートを介してクエリのパフォーマンスを関連付けることができます。 このレポートは、異なるデータベース間で、クエリのパフォーマンスを比較し、選択したクエリの処理速度が速いデータベースと遅いデータベースを簡単に特定できるようになります。

![Azure SQL Analytics のクエリ](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>アクセス許可

Azure SQL Analytics を使用するには､少なくとも Azure で閲覧者ロールの権限が付与されている必要があります｡ ただし､このロールはクエリのテキストを表示したり､自動チューニング アクションを実行したりすることを許可するものではありません｡ Azure には､このソリューションを最大限活用するためのもっと制限の緩やかなロールとして､所有者、共同作成者、SQL DB 共同作成者、または SQL DB 共同作成者､SQL Server 共同作成者があります｡ ポータルから､Azure SQL Analytics を使用するために必要な許可だけ与えて､他のリソースへの管理アクセス権を付与しないカスタム ロールを作成することもできます｡

### <a name="creating-a-custom-role-in-portal"></a>ポータルからのカスタム ロールの作成

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

組織によっては､Azure で厳格な許可制御を実施しています｡Azure portal には､｢SQL Analytics Monitoring Operator｣という次の PowerShell スクリプトがあります｡この､スクリプトを利用して､Azure SQL Analytics を最大限活用するために最低限必要な読み取りおよび書き込み許可を持つカスタム ロールを作成することできます｡

次のスクリプトの “{SubscriptionId}" の部分をご自分の Azure サブスクリプション ID に置き換え､スクリプトを実行して､所有者または共同作成者として Azure にログインしてください｡

   ```powershell
    Connect-AzAccount
    Select-AzSubscription {SubscriptionId}
    $role = Get-AzRoleDefinition -Name Reader
    $role.Name = "SQL Analytics Monitoring Operator"
    $role.Description = "Lets you monitor database performance with Azure SQL Analytics as a reader. Does not allow change of resources."
    $role.IsCustom = $true
    $role.Actions.Add("Microsoft.SQL/servers/databases/read");
    $role.Actions.Add("Microsoft.SQL/servers/databases/topQueries/queryText/*");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/*");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzRoleDefinition $role
   ```

新しいロールが作成されると､Azure SQL Analytics を使用する特別な許可を付与する必要があるユーザーそれぞれにそのロールを割り当てることができます｡

## <a name="analyze-data-and-create-alerts"></a>データの分析とアラートの作成

Azure SQL Analytics のデータ分析のカスタム クエリやカスタム レポート機能は [Log Analytics 言語](../log-query/get-started-queries.md)に依拠しています｡ [利用可能なメトリックおよびログ](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available)でのカスタム クエリでは､データベース リソースから収集され､利用可能になっているデータの説明を検索してください｡

ソリューションの自動アラート機能は､条件が満たされたときにアラートをトリガーする Log Analytics クエリに依拠しています｡ ソリューションでアラートを設定できる Log Analytics クエリに関する､次のいくつかの例を検索してください｡

### <a name="creating-alerts-for-azure-sql-database"></a>Azure SQL Database のアラートの作成

Azure SQL Database リソースから送られるデータを使用して簡単に[アラートを作成](../platform/alerts-metric.md)できます。 ログ アラートで使用できる実用的な[ログ クエリ](../log-query/log-query-overview.md)をいくつか示します。

#### <a name="high-cpu-on-azure-sql-database"></a>高 CPU (Azure SQL Database 上)

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - このアラートを設定するための前提条件は、監視されるデータベースが診断メトリック ([すべてのメトリック] オプション) をソリューションにストリーム配信することです。
> - 高い DTU 結果を得るために、MetricName 値の cpu_percent を dtu_consumption_percent に置き換えます。

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>高 CPU (Azure SQL Database エラスティック プール上)

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - このアラートを設定するための前提条件は、監視されるデータベースが診断メトリック ([すべてのメトリック] オプション) をソリューションにストリーム配信することです。
> - 高い DTU 結果を得るために、MetricName 値の cpu_percent を dtu_consumption_percent に置き換えます。

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>過去 1 時間の平均が 95% を超える Azure SQL Database ストレージ

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
> - このアラートを設定するための前提条件は、監視されるデータベースが診断メトリック ([すべてのメトリック] オプション) をソリューションにストリーム配信することです。
> - このクエリでは、クエリの結果が存在する (結果 > 0 である) とき、つまり条件がいくつかのデータベース上に存在するときにアラートが発生するようにアラート ルールを設定する必要があります。 出力は、定義された time_range 内で storage_threshold より上のデータベース リソースの一覧です。
> - 出力は、定義された time_range 内で storage_threshold より上のデータベース リソースの一覧です。

#### <a name="alert-on-intelligent-insights"></a>Intelligent Insights に対するアラート

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
> - このアラートを設定するための前提条件は、監視されるデータベースが SQLInsights 診断ログをソリューションにストリーム配信することです。
> - このクエリでは、結果の重複を回避するために、alert_run_interval と同じ頻度で実行するようにアラート ルールを設定する必要があります。 ルールは、クエリの結果が存在する (結果が > 0 である) ときにアラートが発生するように設定してください。
> - alert_run_interval をカスタマイズして、SQLInsights ログをソリューションにストリーミングするように構成したデータベースで条件が発生したかどうかをチェックする時間範囲を指定します。
> - insights_string をカスタマイズして、Insights の根本原因分析テキストの出力をキャプチャします。 これは、既存の分析情報から使用できる、ソリューションの UI に表示されるのと同じテキストです。 または、次のクエリを使用して、サブスクリプションに対して生成されるすべての分析情報のテキストを見ることもできます。 Insights に対するアラートを設定するために、クエリの出力を使用して特別な文字列を収集します。

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instance"></a>Managed Instance のアラートの作成

#### <a name="managed-instance-storage-is-above-90"></a>Managed Instance ストレージは 90% より上です

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
> - このアラートを設定する際の事前要件は、監視対象の Managed Instance で、ソリューションに対する ResourceUsageStats ログのストリーム配信が有効になっていることです。
> - このクエリでは、クエリの結果が存在する (結果が 0 より多い) ときにアラートが発生するようアラート ルールを設定する必要があります。それにより、条件が Managed Instance 上に存在することが示されます。 出力は、Managed Instance でのストレージの消費の割合です。

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Managed Instance CPU の過去 1 時間の平均使用量は 95% より上です

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
> - このアラートを設定する際の事前要件は、監視対象の Managed Instance で、ソリューションに対する ResourceUsageStats ログのストリーム配信が有効になっていることです。
> - このクエリでは、クエリの結果が存在する (結果が 0 より多い) ときにアラートが発生するようアラート ルールを設定する必要があります。それにより、条件が Managed Instance 上に存在することが示されます。 出力は、Managed Instance で定義された期間の平均 CPU 使用率の割合です。

### <a name="pricing"></a>価格

ソリューションは無料で使用できますが､毎月割り当てられる無料のデータ取り込み単位数を超えた診断テレメトリの使用量が適用されます｡[Log Analytics の価格](https://azure.microsoft.com/en-us/pricing/details/monitor)を参照してください｡ 提供される無料のデータ インジェスト単位数により、毎月いくつかのデータベースの無料の監視が可能になります。 アイドル状態のデータベースに比べて、ワークロードが重いほど、またアクティブなデータベースが多いほど、取り込まれるデータは多くなることに注意してください。 データ取り込み使用量は､Azure SQL Analytics のナビゲーション メニューで OMS Workspace を選択し､Usage and Estimated Costs を選択することで簡単に監視することができます｡

## <a name="next-steps"></a>次の手順

- Azure Monitor で[ログ クエリ](../log-query/log-query-overview.md)を使用して、詳細な Azure SQL データを表示します。
- Azure SQL データを表示する[独自のダッシュ ボードを作成](../learn/tutorial-logs-dashboards.md)します。
- Azure SQL の特定のイベントが発生した場合の[アラートを作成](../platform/alerts-overview.md)します。
