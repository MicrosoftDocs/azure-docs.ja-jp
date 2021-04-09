---
title: Azure Monitor を使用して Synapse Analytics を監視する方法
description: Azure Monitor のメトリック、アラート、ログを使用して Synapse Analytics ワークスペースを監視する方法について説明します
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: ac5b1df5ff246623b73dbc41d18216396e6ed7ef
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549857"
---
# <a name="use-azure-monitor-with-your-azure-synapse-analytics-workspace"></a>Azure Synapse Analytics ワークスペースで Azure Monitor を使用する

クラウド アプリケーションは複雑であり、動的なパーツが多数あります。 モニターでは、アプリケーションを正常な状態で確実に稼働させ続けるためのデータを提供します。 モニターは、潜在的な問題を回避し、過去の問題をトラブルシューティングするのにも役立ちます。 監視データを使用して、アプリケーションに関する詳細な分析情報を取得できます。 この知識は、アプリケーションのパフォーマンスと保守容易性の向上に役立ちます。 また、手動操作を必要とするアクションの自動化にも役立ちます。

Azure Monitor では、ほとんどの Azure サービスに対して、基礎レベルのインフラストラクチャのメトリック、アラート、およびログを提供します。 Azure 診断ログはリソースによって出力され、そのリソースの操作に関する豊富な頻出データを提供します。 Azure Synapse Analytics を使用すると、Azure Monitor に診断ログを書き込むことができます。

詳細については、「[Azure Monitor の概要](../../azure-monitor/overview.md)」を参照してください。

## <a name="metrics"></a>メトリック

Monitor を使用すると、Azure ワークロードのパフォーマンスと正常性を可視化できます。 最も重要な種類の Monitor データは、パフォーマンス カウンターとも呼ばれるメトリックです。 メトリックは、ほとんどの Azure リソースによって出力されます。 Monitor には、監視およびトラブルシューティングのために、これらのメトリックを構成して使用する方法が複数用意されています。

これらのメトリックにアクセスするには、「[Azure Monitor データ プラットフォーム](../../azure-monitor/data-platform.md)」に記載された手順に従います。

### <a name="workspace-level-metrics"></a>ワークスペースレベルのメトリック

ワークスペースで出力されるメトリックの一部を次に示します。

| **メトリック**                           | **メトリックのカテゴリ、表示名**                  | **単位** | **集計タイプ** | **説明**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| IntegrationActivityRunsEnded         | 統合、Activity runs metric (アクティビティ実行メトリック)                     | Count    | Sum (既定値)、Count                | 1 分の枠内で発生または終了したアクティビティの実行の合計数。 </br></br> このメトリックの結果ディメンションを使用して、成功、失敗、またはキャンセルの最終ステータスでフィルター処理します。|
| IntegrationPipelineRunsEnded         | 統合、Pipeline runs metric (パイプライン実行メトリック)                     | Count    | Sum (既定値)、Count                | 1 分の枠内で発生または終了したパイプラインの実行の合計数。 </br></br> このメトリックの結果ディメンションを使用して、成功、失敗、またはキャンセルの最終ステータスでフィルター処理します。 |
| IntegrationTriggerRunsEnded          | 統合、Trigger runs metric (トリガー実行メトリック)                      | Count    | Sum (既定値)、Count                | 1 分の枠内で発生または終了したトリガーの実行の合計数。 </br></br> このメトリックの結果ディメンションを使用して、成功、失敗、またはキャンセルの最終ステータスでフィルター処理します。 |
| BuiltinSqlPoolDataProcessedBytes     | 組み込みの SQL プール、Data processed (bytes) (処理済みデータ (バイト)) | Byte | Sum (既定値) | 組み込みのサーバーレス SQL プールによって処理されたデータの量。 |
| BuiltinSqlPoolLoginAttempts          | 組み込みの SQL プール、Login attempts (ログイン試行) | Count | Sum (既定値) | 組み込みのサーバーレス SQL プールのログイン試行回数。 |
| BuiltinSqlPoolDataRequestsEnded      | 組み込みの SQL プール、Requests ended (bytes) (終了した要求 (バイト)) | Count | Sum (既定値) | 組み込みのサーバーレス SQL プールに対して終了した SQL 要求の数。 </br></br> このメトリックの結果ディメンションを使用して、最終ステータスでフィルター処理します。 |

### <a name="dedicated-sql-pool-metrics"></a>専用 SQL プールのメトリック

専用 SQL プールで出力されるメトリックの一部を次に示します。

| **メトリック**                           | **表示名**                  | **単位** | **集計タイプ** | **説明**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| DWULimit                            | DWU 上限                       | Count   | Max (既定値)、Min、Avg | SQL プールの構成されたサイズ |
| DWUUsed                             | 使用済み DWU                        | Count   | Max (既定値)、Min、Avg | SQL プール全体の使用状況の概要表示を表します。 DWU の制限 * DWU の割合で測定されます |
| DWUUsedPercent                      | DWU used percentage (使用された DWU の割合)             | Percent | Max (既定値)、Min、Avg | SQL プール全体の使用状況の概要表示を表します。 CPU の割合とデータ IO の割合の最大値を取得することにより測定します |
| ConnectionsBlockedByFirewall        | ファイアウォールによってブロックされる接続数 | Count   | Sum (既定値)   | ファイアウォール規則によってブロックされる接続の数。 SQL プールのアクセス制御ポリシーを再確認し、数が多い場合はこれらの接続を監視します |
| AdaptiveCacheHitPercent             | Adaptive cache hit percentage (アダプティブ キャッシュ ヒット率)   | Percent | Max (既定値)、Min、Avg | ワークロードによるアダプティブ キャッシュの使用状況を測定します。 このメトリックをキャッシュ ヒット率メトリックと一緒に使用して、追加容量に合わせてスケーリングするか、キャッシュをハイドレートするためにワークロードを再実行するかを決定します |
| AdaptiveCacheUsedPercent            | Adaptive cache used percentage (アダプティブ キャッシュ使用率)  | Percent | Max (既定値)、Min、Avg | ワークロードによるアダプティブ キャッシュの使用状況を測定します。 このメトリックをキャッシュ使用率メトリックと一緒に使用して、追加容量に合わせてスケーリングするか、キャッシュをハイドレートするためにワークロードを再実行するかを決定します |
| LocalTempDBUsedPercent              | Local tempdb used percentage (ローカル tempdb 使用率)    | Percent | Max (既定値)、Min、Avg | すべてのコンピューティング ノードでの、ローカル tempdb の使用率 - 値は 5 分ごとに出力されます |
| MemoryUsedPercent                   | Memory used percentage (メモリ使用率)          | Percent | Max (既定値)、Min、Avg | SQL プール内のすべてのノードでのメモリ使用率 |
| CPUPercent                          | DWU used percentage (使用された CPU の割合)             | Percent | Max (既定値)、Min、Avg | SQL プール内のすべてのノードでの CPU 使用率 |
| 接続                         | 接続                     | Count   | Sum (既定値)  | SQL プールに対するログインの合計数 |
| ActiveQueries                      | アクティブなクエリ                 | Count   | Sum (既定値)   | アクティブなクエリ。 フィルター処理も分割も適用しないでこのメトリックを使用すると、システム上で実行されているすべてのアクティブなクエリが表示されます |
| QueuedQueries                      | キューに置かれたクエリ                  | Count   | Sum (既定値)   | 同時実行数の上限に達した後にキューに登録された要求の累積数 |
| WLGActiveQueries                   | Workload group active queries (ワークロード グループのアクティブなクエリ)   | Count   | Sum (既定値)   | ワークロード グループ内のアクティブなクエリ数。 フィルター処理も分割も適用しないでこのメトリックを使用すると、システム上で実行されているすべてのアクティブなクエリが表示されます |
| WLGActiveQueriesTimeouts           | Workload group query timeouts (ワークロード グループのクエリのタイムアウト)   | Count   | Sum (既定値)   | タイムアウトしたワークロード グループのクエリ。このメトリックでクエリのタイムアウトがレポートされるのは、クエリの実行が開始された場合だけです (ロックまたはリソースの待機による待ち時間は含まれません) |
| Effective min resource percent                   | Workload group queued queries (ワークロード グループのキューに登録されたクエリ)   | Count   | Sum (既定値)   | 同時実行数の上限に達した後にキューに登録された要求の累積数 |
| WLGAllocationBySystemPercent        | Workload group allocation by system percent (システム割合別のワークロード グループの割り当て) | Percent | Max (既定値)、Min、Avg、Sum | システム全体に対するリソース割り当て比率 |
| WLGAllocationByEffectiveCapResourcePercent   | Workload group allocation by max resource percent (最大リソース割合別のワークロード グループの割り当て) | Percent | Max (既定値)、Min、Avg | ワークロード グループあたりの "Effective cap resource percent (有効な上限リソース割合)" に対するリソースの割り当て比率を表示します。 このメトリックでは、ワークロード グループの有効使用率が示されます |
| WLGEffectiveCapResourcePercent      | Effective cap resource percent (有効な上限リソース割合)  | Percent | Max (既定値)、Min、Avg | ワークロードグループの有効な上限リソースの割合。 min_percentage_resource > 0 のワークロード グループが他にある場合、effective_cap_percentage_resource はそれに比例して低くなります |
| WLGEffectiveMinResourcePercent      | Effective min resource percent (有効な最小リソース割合)  | Percent | Max (既定値)、Min、Avg、Sum | 有効な最小リソース割合の設定では、サービス レベルとワークロード グループの設定が考慮されます。 有効な min_percentage_resource は、低いサービス レベルで高い値に調整できます |

### <a name="apache-spark-pool-metrics"></a>Apache Spark プール メトリック

Apache Spark プールで出力されるメトリックの一部を次に示します。

| **メトリック**                           | **メトリックのカテゴリ、表示名**                  | **単位** | **集計タイプ** | **説明**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| BigDataPoolApplicationsEnded  | Ended Apache Spark applications (終了した Apache Spark アプリケーション)  | Count | Sum (既定値) | 終了した Apache Spark プール アプリケーションの数 |
| BigDataPoolAllocatedCores     | Number of vCores allocated to the Apache Spark pool (Apache Spark プールに割り当てられた仮想コアの数)                 | Count | Max (既定値)、Min、Avg | Apache Spark プールに割り当てられた仮想コア |
| BigDataPoolAllocatedMemory    | Amount of memory (GB) allocated to the Apache Spark pool (Apache Spark プールに割り当てられたメモリの量 (GB))            | Count | Max (既定値)、Min、Avg | Apache Spark プールに割り当てられたメモリ (GB) |
| BigDataPoolApplicationsActive | Active Apache Spark applications (アクティブな Apache Spark アプリケーション) | Count | Max (既定値)、Min、Avg | アクティブな Apache Spark プール アプリケーションの数 |

## <a name="alerts"></a>警告

Azure portal にサインインし、 **[モニター]**  >  **[アラート]** の順に選択してアラートを作成します。

### <a name="create-alerts"></a>アラートを作成する

1. **[+ 新しいアラート ルール]** を選択して新しいアラートを作成します。

1. アラート発生のタイミングを指定する **[アラートの条件]** を定義します。

    > [!NOTE]
    > **[リソースの種類でフィルター]** ドロップダウン リストでは **[すべて]** を選択するようにしてください。

1. アラートの構成をさらに指定するには、 **[アラートの詳細]** を定義します。

1. **[アクション グループ]** を定義して、アラートを受信するユーザー (およびその方法) を決定します。

## <a name="logs"></a>ログ

### <a name="workspace-level-logs"></a>ワークスペースレベルのログ

Azure Synapse Analytics ワークスペースによって出力されるログを次に示します。

| Log Analytics テーブル名 | ログのカテゴリ名                 | 説明 |
|-------------------------------|-------------------------------------------------|-------------|
| SynapseGatewayApiRequests     | GatewayApiRequests             | Azure Synapse gateway API 要求。 |
| SynapseRbacOperations         | SynapseRbacOperations          | Azure Synapse ロールベースのアクセス制御 (SRBAC) 操作。 |

### <a name="dedicated-sql-pool-logs"></a>専用 SQL プールのログ

専用 SQL プールによって出力されるログを次に示します。

| Log Analytics テーブル名        | ログのカテゴリ名             | 説明 |
|----------------------|--------------------------------------|-------------|
| SynapseSqlPoolExecRequests  | ExecRequests | Azure Synapse 専用 SQL プール内の SQL 要求またはクエリに関する情報。
| SynapseSqlPoolDmsWorkers    | DmsWorkers   | Azure Synapse 専用 SQL プール内の DMS ステップを完了するワーカーに関する情報。
| SynapseSqlPoolRequestSteps  | RequestSteps | Azure Synapse 専用 SQL プール内の特定の SQL 要求またはクエリを構成する要求ステップに関する情報。
| SynapseSqlPoolSqlRequests   | SqlRequests  | Azure Synapse 専用 SQL プール内の SQL 要求またはクエリのステップのクエリ ディストリビューションに関する情報。
| SynapseSqlPoolWaits         | 待機        | Azure Synapse 専用 SQL プール内の SQL 要求またはクエリの実行中に発生した待ち状態に関する情報。これには、ロックや転送キューの待機が含まれます。

これらのログの詳細については、次の情報を参照してください。
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)

### <a name="apache-spark-pool-log"></a>Apache Spark プールのログ

Apache Spark プールによって出力されるログを次に示します。

| Log Analytics テーブル名               | ログのカテゴリ名              | 説明                 |
|-----------------------------|---------------------------------------|-----------------------------|
| SynapseBigDataPoolApplicationsEnded | BigDataPoolAppsEnded | 終了した Apache Spark アプリケーションに関する情報 |

### <a name="diagnostic-settings"></a>診断設定

診断設定を使用して、非コンピューティング リソースの診断ログを構成します。 リソース コントロールの設定には、次の機能があります。

* 診断ログを送信する場所を指定します。 たとえば、Azure ストレージ アカウント、Azure イベント ハブ、Monitor ログなどがあります。
* 送信するログ カテゴリを指定します。
* ログの各カテゴリをストレージ アカウントに保持する期間を指定します。
* リテンション期間が 0 日の場合、ログは永続的に保持されます。 または、1 日から 2,147,483,647 日の間の任意の日数を値として指定できます。
* 保持ポリシーが設定されていても、ストレージ アカウントへのログの保存が無効になっている場合、保持ポリシーは無効になります。 たとえば、この状態は、Event Hubs または Monitor ログのオプションのみが選択されている場合に生じる可能性があります。
* 保持ポリシーは日単位で適用されます。 日と日の間の境界は、協定世界時 (UTC) の午前 0 時になります。 1 日の終わりに、保持ポリシーの期間を超えた日のログが削除されます。 たとえば、保持ポリシーが 1 日の場合、今日が始まった時点で、昨日より前のログは削除されます。

Azure Monitor 診断設定を使用すると、診断ログを複数の異なるターゲットにルーティングして分析できます。

* **ストレージ アカウント**: 監査や手動での検査のために、診断ログをストレージ アカウントに保存します。 診断設定を使用して、リテンション期間 (日数) を指定できます。
* **イベント ハブ**: ログを Azure Event Hubs にストリーミングします。 ログは、パートナー サービス/カスタム分析ソリューション (Power BI など) への入力となります。
* **Log Analytics ワークスペース**:ログを Log Analytics で分析します。 Azure Synapse と Log Analytics の統合は、次のシナリオで役立ちます。
  * Azure Synapse から Log Analytics に発行された充実したメトリックのセットに対して、複雑なクエリを記述する必要がある場合。 Azure Monitor を通じて、これらのクエリに対するカスタム アラートを作成することができます。
  * ワークスペース全体を監視する必要がある場合。 複数のワークスペースから 1 つの Log Analytics ワークスペースにデータをルーティングすることができます。

ログを出力するリソースのサブスクリプションにないストレージ アカウントまたはイベント ハブ名前空間を使用することもできます。 設定を構成するユーザーは、両方のサブスクリプションに対して適切な Azure ロールベースのアクセス制御 (Azure RBAC) アクセス権限を持っている必要があります。

#### <a name="configure-diagnostic-settings"></a>診断設定を構成する

ワークスペース、専用 SQL プール、または Apache Spark プールの診断設定を作成または追加します。

1. ポータルで Monitor に移動します。 **[設定]**  >  **[診断設定]** の順に選択します。

1. 診断設定を作成する Synapse ワークスペース、専用 SQL プール、または Apache Spark プールを選択します。

1. 選択したワークスペースの診断設定が存在しない場合は、設定を作成するように求められます。 **[診断を有効にする]** を選択します。

   ワークスペースに既存の診断設定が存在する場合は、このリソースで構成済みの設定の一覧が表示されます。 **[診断設定の追加]** を選択します。

1. 設定に名前を付け、 **[Log Analytics への送信]** を選択して、 **[Log Analytics ワークスペース]** からワークスペースを選択します。

    > [!NOTE]
    > Azure ログ テーブルには 500 個を超える列を含めることができないため、"_リソース固有モード_" を選択することを **強くお勧めします**。 詳細については、[Azure Diagnostics のログ リファレンス](/azure-monitor/reference/tables/azurediagnostics#additionalfields-column)に関するページを参照してください。

1. **[保存]** を選択します。

しばらくすると、ワークスペース、専用 SQL プール、または Apache Spark プールに対応する設定の一覧に新しい設定が表示されます。 新しいイベント データが生成されるとすぐに、診断ログがそのワークスペースにストリーミングされます。 イベントが生成されてから、Log Analytics に表示されるまでに最大 15 分かかかる場合があります。

## <a name="next-steps"></a>次のステップ

パイプラインの実行の監視に関する詳細については、[Synapse Studio でのパイプラインの実行の監視](how-to-monitor-pipeline-runs.md)に関する記事をご覧ください。 

Apache Spark アプリケーションの監視に関する詳細については、[Synapse Studio での Apache Spark アプリケーションの監視](apache-spark-applications.md)に関する記事を参照してください。

SQL 要求の監視に関する詳細については、[Synapse Studio での SQL 要求の監視](how-to-monitor-sql-requests.md)に関する記事を参照してください。
