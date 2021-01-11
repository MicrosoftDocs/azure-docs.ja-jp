---
title: メトリックとリソース ログのストリーミング エクスポートを構成する
description: メトリックとリソース ログのストリーミング エクスポートを構成する方法について説明します。これには、Azure SQL Database および Azure SQL Managed Instance から任意の宛先へ送信されるインテリジェントな診断分析が含まれ、リソース使用率とクエリ実行統計の情報が格納されています。
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: seoapril2019
ms.devlang: sqldbrb=2
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 04/06/2020
ms.openlocfilehash: efb99e23466e4615dfa1f4a429addcd8c4ac68f5
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085601"
---
# <a name="configure-streaming-export-of-azure-sql-database-and-sql-managed-instance-diagnostic-telemetry"></a>Azure SQL Database および SQL Managed Instance 診断テレメトリのストリーミング エクスポートを構成する
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

この記事では、複数の分析先のいずれかにエクスポートできる Azure SQL Database のパフォーマンス メトリックとリソース ログについて説明します。 この診断テレメトリのストリーミング エクスポートを構成する方法については、Azure portal、PowerShell、Azure CLI、REST API、および Azure Resource Manager テンプレートを参照してください。

また、この診断テレメトリのストリーミング先と、その選択肢の中から選択する方法についても説明します。 送信先のオプションは次のとおりです。

- [Log Analytics と SQL Analytics](#stream-into-sql-analytics)
- [Event Hubs](#stream-into-event-hubs)
- [Azure ストレージ](#stream-into-azure-storage)

## <a name="diagnostic-telemetry-for-export"></a>エクスポートの診断テレメトリ

エクスポートできる診断テレメトリの中で最も重要なのは、Intelligent Insights (SQLInsights) ログです。 [Intelligent Insights](intelligent-insights-overview.md) は、組み込まれているインテリジェンスを使って、人工知能によりデータベースの使用状況を継続的に監視し、パフォーマンス低下の原因となる破壊的なイベントを検出します。 検出されると、詳細な分析が実行され、問題のインテリジェントな評価を含む Intelligent Insights ログが生成されます。 このアセスメントは、データベース パフォーマンスの問題の根本原因分析と、可能な場合にはパフォーマンス向上に関する推奨事項で構成されます。 このログの内容を表示するには、このログのストリーミング エクスポートを構成する必要があります。

Intelligent Insights ログのエクスポートをストリーミングするだけでなく、さまざまなパフォーマンス メトリックとその他のデータベース ログをエクスポートすることもできます。 次の表で、複数の送信先のいずれかにストリーミング エクスポートするように構成できるパフォーマンス メトリックとリソース ログについて説明します。 この診断テレメトリは、単一データベース、エラスティック プールとプールされたデータベース、およびマネージド インスタンスとインスタンス データベースに対して構成できます。

| データベースの診断テレメトリ | Azure SQL Database のサポート | Azure SQL Managed Instance のサポート |
| :------------------- | ----- | ----- |
| [基本メトリック](#basic-metrics):DTU/CPU の割合、DTU/CPU の上限、物理データ読み取りの割合、ログ書き込みの割合、ファイアウォール接続による成功/失敗/ブロック、セッションの割合、ワーカーの割合、ストレージ、ストレージの割合、XTP ストレージの割合が含まれます。 | はい | いいえ |
| [インスタンスとアプリの詳細](#advanced-metrics):tempdb システム データベース データとログ ファイルのサイズ、tempdb のログ ファイル使用率が含まれています。 | はい | いいえ |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics):CPU 使用率、クエリ実行時間統計など、クエリのランタイム統計に関する情報が含まれます。 | はい | はい |
| [QueryStoreWaitStatistics](#query-store-wait-statistics):CPU、LOG、LOCKING など、クエリ待機統計 (クエリが待機していたもの) に関する情報が含まれます。 | はい | はい |
| [エラー](#errors-dataset):データベースには SQL エラーに関する情報が含まれます。 | はい | はい |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset):データベースが各種の待機に費やした時間に関する情報が含まれます。 | はい | いいえ |
| [Timeouts](#time-outs-dataset):データベースにはタイムアウトに関する情報が含まれます。 | はい | いいえ |
| [Blocks](#blockings-dataset):データベースにはブロック イベントに関する情報が含まれます。 | はい | いいえ |
| [Deadlocks](#deadlocks-dataset): データベースにはデッドロック イベントに関する情報が含まれます。 | はい | いいえ |
| [AutomaticTuning](#automatic-tuning-dataset): データベースの自動チューニングの推奨事項に関する情報が含まれます。 | はい | いいえ |
| [SQLInsights](#intelligent-insights-dataset):データベースのパフォーマンスに対する Intelligent Insights が含まれます。 詳細については、[Intelligent Insights](intelligent-insights-overview.md) に関するページを参照してください。 | はい | はい |

> [!NOTE]
> master、msdb、model、resource、および tempdb データベースなどの**システム データベース**に対して診断設定を構成することはできません。

## <a name="streaming-export-destinations"></a>ストリーミング エクスポートの宛先

この診断テレメトリは、次のいずれかの Azure リソースにストリーミングして分析できます。

- **[Log Analytics ワークスペース](#stream-into-sql-analytics)** :

  [Log Analytics ワークスペース](../../azure-monitor/platform/resource-logs-collect-workspace.md) にストリーミングされたデータは、[SQL Analytics](../../azure-monitor/insights/azure-sql.md) で使用できます。 SQL Analytics は、パフォーマンス レポート、アラート、軽減策の推奨事項を含むデータベースのインテリジェントな監視機能を提供する、クラウドのみの監視ソリューションです。 Log Analytics ワークスペースにストリーミングされたデータは、収集された他の監視データと組み合わせて分析できます。また、アラートや視覚化などの他の Azure Monitor 機能を利用することもできます。
- **[Azure Event Hubs](#stream-into-event-hubs)** :

  [Azure Event Hub](../../azure-monitor/platform/resource-logs-stream-event-hubs.md) にストリーミングされるデータは、次の機能を提供します。

  - **サード パーティ製のロギングおよびテレメトリ ストリームにログをストリーミングする**:すべてのメトリックとリソース ログを 1 つのイベント ハブにストリーミングして、ログ データをサード パーティの SIEM またはログ分析ツールにパイプします。
  - **カスタムのテレメトリおよびログ プラットフォームを構築する**:高い拡張性の公開サブスクライブを特長とするイベント ハブを使用することで、メトリックとリソース ログをカスタム テレメトリ プラットフォームに柔軟に取り込むことができます。 詳細については、「[Azure Event Hubs でのグローバル スケール テレメトリ プラットフォームの設計とサイズ変更](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)」を参照してください。
  - **データを Power BI にストリーミングしてサービスの正常性を表示する**:Event Hubs、Stream Analytics、Power BI を使用して、診断データを Azure サービスの近リアルタイム洞察に転換します。 このソリューションの詳細については、「[Stream Analytics と Power BI:ストリーミング データのリアルタイム分析ダッシュボード](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard)」を参照してください。
- **[Azure Storage](#stream-into-azure-storage)** :

  [Azure Storage](../../azure-monitor/platform/resource-logs-collect-storage.md) にストリーミングされたデータを使用すると、前述の 2 つのストリーミング オプションの何分の 1 かのわずかなコストで、膨大な量の診断テレメトリをアーカイブできます。

これらの送信先のいずれかにストリーミングされたこの診断テレメトリを使用することで、リソース使用率とクエリ実行統計を測定し、パフォーマンスの監視を容易にできます。

![アーキテクチャ](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/architecture.png)

## <a name="enable-and-configure-the-streaming-export-of-diagnostic-telemetry"></a>診断テレメトリのストリーミング エクスポートを有効にして構成する

次のいずれかの方法を使用してメトリックと診断テレメトリのロギングを有効にして管理できます。

- Azure portal
- PowerShell
- Azure CLI
- Azure Monitor REST API
- Azure Resource Manager テンプレート

> [!NOTE]
> キュリティ テレメトリの監査ログのストリーム配信を有効にするには、[データベースに対する監査の設定](../../sql-database/sql-database-auditing.md#setup-auditing)と [Azure Monitor ログと Azure Event Hubs の監査ログ](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242)に関するページを参照してください。

## <a name="configure-the-streaming-export-of-diagnostic-telemetry"></a>診断テレメトリのストリーミング エクスポートを構成する

診断テレメトリのストリーム配信は、Azure portal の **[診断設定]** メニューを使用して有効にし、構成することができます。 さらに、PowerShell、Azure CLI、[REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)、[Resource Manager テンプレート](../../azure-monitor/platform/diagnostic-settings-template.md)を使用して、診断テレメトリのストリーム配信を構成することもできます。 診断テレメトリのストリーム先に次の宛先を設定できます。Azure Storage、Azure Event Hubs、Azure Monitor ログです。

> [!IMPORTANT]
> 診断テレメトリのストリーミング エクスポートは既定では有効になっていません。

Azure portal で診断テレメトリのストリーミング エクスポートを構成するための手順、および PowerShell と Azure CLI でも同じことを実現するためのスクリプトについては、次のいずれかのタブを選択してください。

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

### <a name="elastic-pools-in-azure-sql-database"></a>Azure SQL Database 内のエラスティック プール

次の診断テレメトリを収集するように、エラスティック プール リソースを設定できます。

| リソース | 監視テレメトリ |
| :------------------- | ------------------- |
| **エラスティック プール** | [基本メトリック](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#basic-metrics)には、eDTU/CPU の割合、eDTU/CPU の制限、物理データ読み取りの割合、ログ書き込みの割合、セッションの割合、ワーカーの割合、ストレージ、ストレージの割合、ストレージの制限、XTP ストレージの割合が含まれています。 |

エラスティック プールおよびプールされているデータベースを対象に診断テレメトリのストリーム配信を構成するには、それぞれを個別に構成する必要があります。

- エラスティック プールの診断テレメトリのストリーム配信を有効にします
- エラスティック プール内の各データベースの診断テレメトリのストリーミングを有効にします

エラスティック プール コンテナーには、個々のプールされたデータベースのテレメトリとは別に独自のテレメトリが存在します。

エラスティック プール リソースの診断テレメトリのストリーミングを有効にするには、次の手順に従います。

1. Azure portal で**エラスティック プール** リソースに移動します。
2. **[診断設定]** を選択します。
3. 以前の設定が存在しない場合は **[診断を有効にする]** を選択します。または、以前の設定を編集するには **[設定の編集]** を選択します。

   ![エラスティック プールに対して診断を有効にする](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-elasticpool-enable.png)

4. 独自の参照の設定名を入力します。
5. 診断データのストリーミング先のリソースを **[ストレージ アカウントへのアーカイブ]** 、 **[イベント ハブへのストリーム]** 、または **[Log Analytics への送信]** から選択します。
6. Log Analytics の場合は、 **[構成]** を選択し、 **[+ 新しいワークスペースの作成]** を選択することによって新しいワークスペースを作成するか、または既存のワークスペースを選択します。
7. エラスティック プール診断テレメトリの基本メトリックのチェック ボックスをオンにします。**基本**メトリック。
   ![エラスティック プールに対して診断を構成する](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-elasticpool-selection.png)

8. **[保存]** を選択します。
9. さらに、次のセクションで説明されている手順に従って、監視対象のエラスティック プール内の各データベースに対して診断テレメトリのストリーミングを構成します。

> [!IMPORTANT]
> エラスティック プールの診断テレメトリの構成に加え、エラスティック プール内の各データベースの診断テレメトリも構成する必要があります。

### <a name="databases-in-azure-sql-database"></a>Azure SQL Database 内のデータベース

次の診断テレメトリを収集するように、データベース リソースを設定できます。

| リソース | 監視テレメトリ |
| :------------------- | ------------------- |
| **単一データベースまたはプールされたデータベース** | [基本メトリック](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#basic-metrics)には、DTU の割合、使用中の DTU、DTU の上限、CPU の割合、物理データ読み取りの割合、ログ書き込みの割合、ファイアウォール接続による成功/失敗/ブロック、セッションの割合、ワーカーの割合、ストレージ、ストレージの割合、XTP ストレージの割合、デッドロックが含まれます。 |

単一データベースまたはプールされたデータベースに対して診断テレメトリのストリーム配信を有効にするには、次の手順のようにします。

1. Azure **SQL Database** リソースに移動します。
2. **[診断設定]** を選択します。
3. 以前の設定が存在しない場合は **[診断を有効にする]** を選択します。または、以前の設定を編集するには **[設定の編集]** を選択します。 診断テレメトリをストリーミングするため、並列接続を最大 3 つ作成できます。
4. 複数のリソースへの診断データの並列ストリーミングを構成するには、 **[Add diagnostic setting]\(診断設定の追加\)** を選択します。

   ![単一データベースとプールされたデータベースの診断を有効にする](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-sql-enable.png)

5. 独自の参照の設定名を入力します。
6. 診断データのストリーミング先のリソースを **[ストレージ アカウントへのアーカイブ]** 、 **[イベント ハブへのストリーム]** 、または **[Log Analytics への送信]** から選択します。
7. 標準的なイベント ベースの監視エクスペリエンスを得るには、データベース診断ログ テレメトリ **[SQLInsights]** 、 **[AutomaticTuning]** 、 **[QueryStoreRuntimeStatistics]** 、 **[QueryStoreWaitStatistics]** 、 **[Errors]** 、 **[DatabaseWaitStatistics]** 、 **[Timeouts]** 、 **[Blocks]** 、および **[Deadlocks]** のチェックボックスをオンにします。
8. 分ごとの高度な監視エクスペリエンスを得るには、 **[基本]** メトリックのチェックボックスをオンにします。

   ![Azure SQL Database の診断を構成する](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-sql-selection.png)
9. **[保存]** を選択します。
10. 監視するデータベースごとにこれらの手順を繰り返します。

> [!TIP]
> 監視する単一データベースとプールされたデータベースのそれぞれについて、データベースごとにこれらの手順を繰り返します。

### <a name="instances-in-azure-sql-managed-instance"></a>Azure SQL Managed Instance のインスタンス

次の診断テレメトリを収集するように、マネージド インスタンス リソースを設定できます。

| リソース | 監視テレメトリ |
| :------------------- | ------------------- |
| **マネージド インスタンス** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) には、仮想コアの個数、平均の CPU の割合、IO 要求、読み取り/書き込みバイト数、予約済みストレージ領域、および使用済みストレージ領域が含まれています。 |

マネージド インスタンスとインスタンス データベースに対する診断テレメトリのストリーム配信を構成するには、それぞれを個別に構成する必要があります。

- マネージド インスタンスの診断テレメトリのストリーム配信を有効にする
- 各インスタンス データベースの診断テレメトリのストリーミングを有効にする

マネージド インスタンス コンテナーには、それぞれのインスタンス データベースのテレメトリとは別に独自のテレメトリが存在します。

マネージド インスタンス リソースに対して診断テレメトリのストリーミングを有効にするには、次の手順に従います。

1. Azure portal で**マネージド インスタンス** リソースに移動します。
2. **[診断設定]** を選択します。
3. 以前の設定が存在しない場合は **[診断を有効にする]** を選択します。または、以前の設定を編集するには **[設定の編集]** を選択します。

   ![マネージド インスタンスに対して診断を有効にする](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-mi-enable.png)

4. 独自の参照の設定名を入力します。
5. 診断データのストリーミング先のリソースを **[ストレージ アカウントへのアーカイブ]** 、 **[イベント ハブへのストリーム]** 、または **[Log Analytics への送信]** から選択します。
6. Log Analytics の場合は、 **[構成]** を選択し、 **[+ 新しいワークスペースの作成]** を選択することによって新しいワークスペースを作成するか、または既存のワークスペースを使用します。
7. インスタンスの診断テレメトリの **[ResourceUsageStats]** のチェックボックスをオンにします。

   ![マネージド インスタンスに対して診断を構成する](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-mi-selection.png)

8. **[保存]** を選択します。
9. さらに、次のセクションで説明されている手順に従って、監視対象のマネージド インスタンス内の各インスタンス データベースに対して診断テレメトリのストリーミングを構成します。

> [!IMPORTANT]
> マネージド インスタンスの診断テレメトリの構成に加えて、各マネージド インスタンスの診断テレメトリも構成する必要があります。

### <a name="databases-in-azure-sql-managed-instance"></a>Azure SQL Managed Instance のデータベース

次の診断テレメトリを収集するように、インスタンス データベース リソースを設定できます。

| リソース | 監視テレメトリ |
| :------------------- | ------------------- |
| **インスタンス データベース** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) には、仮想コアの個数、平均の CPU の割合、IO 要求、読み取り/書き込みバイト数、予約済みストレージ領域、および使用済みストレージ領域が含まれています。 |

インスタンス データベースに対して診断テレメトリのストリーム配信を有効にするには、次の手順に従います。

1. マネージド インスタンス内の**インスタンス データベース** リソースに移動します。
2. **[診断設定]** を選択します。
3. 以前の設定が存在しない場合は **[診断を有効にする]** を選択します。または、以前の設定を編集するには **[設定の編集]** を選択します。
   - 診断テレメトリをストリーミングするため並列接続を最大 3 つ作成できます。
   - 複数のリソースへの診断データの並列ストリーミングを構成するには、 **[+Add diagnostic setting] (+ 診断設定の追加)** を選択します。

   ![インスタンス データベースに対して診断を有効にする](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-mi-enable.png)

4. 独自の参照の設定名を入力します。
5. 診断データのストリーミング先のリソースを **[ストレージ アカウントへのアーカイブ]** 、 **[イベント ハブへのストリーム]** 、または **[Log Analytics への送信]** から選択します。
6. データベース診断テレメトリの **[SQLInsights]** 、 **[QueryStoreRuntimeStatistics]** 、 **[QueryStoreWaitStatistics]** 、および **[Errors]** のチェック ボックスをオンにします。
   ![インスタンス データベースに対して診断を構成する](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-mi-selection.png)
7. **[保存]** を選択します。
8. 監視するインスタンス データベースごとにこれらの手順を繰り返します。

> [!TIP]
> 監視するインスタンス データベースごとにこれらの手順を繰り返します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

PowerShell を使用してメトリックと診断のロギングを有効にできます。

- ストレージ アカウントへのメトリックとリソース ログの保存を有効にするには、次のコマンドを使います。

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
  ```

  ストレージ アカウント ID は、送信先となるストレージ アカウントのリソース ID です。

- イベント ハブへのメトリックとリソース ログのストリーミングを有効にするには、次のコマンドを使います。

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
  ```

  Azure Service Bus ルール ID は、次の形式の文字列です。

  ```powershell
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Log Analytics ワークスペースにメトリックとリソース ログを送信できるようにするには、次のコマンドを使用します。

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
  ```

- 次のコマンドを使用して、Log Analytics ワークスペースのリソース ID を取得できます。

  ```powershell
  (Get-AzOperationalInsightsWorkspace).ResourceId
  ```

このパラメーターを組み合わせて、複数の出力オプションを有効にできます。

**複数の Azure リソースを構成する**

複数のサブスクリプションをサポートするためには、「[Enable Azure resource metrics logging using PowerShell (PowerShell を使用して Azure リソース メトリックのログ記録を有効にする)](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/)」の PowerShell スクリプトを使用します。

スクリプト `Enable-AzureRMDiagnostics.ps1` の実行時にパラメーターとしてワークスペース リソース ID \<$WSID\> を入力すると、複数のリソースからの診断データをワークスペースに送信できます。

- 診断データの送信先のワークスペース ID \<$WSID\> を取得するには、次のスクリプトを使用します。

  ```powershell
  $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
  .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
  ```

  \<subID\> をサブスクリプション ID に、\<RG_NAME\> をリソース グループ名に、\<WS_NAME\> をワークスペース名に置き換えます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してメトリックと診断のロギングを有効にできます。

> [!IMPORTANT]
> Azure CLI v1.0 では、診断ログを有効にするスクリプトがサポートされています。 現時点では、Azure CLI v2.0 はサポートされていません。

- ストレージ アカウントへのメトリックとリソース ログの保存を有効にするには、次のコマンドを使います。

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
  ```

  ストレージ アカウント ID は、送信先となるストレージ アカウントのリソース ID です。

- イベント ハブへのメトリックとリソース ログのストリーミングを有効にするには、次のコマンドを使います。

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
  ```

  Service Bus ルール ID は、この形式の文字列です。

  ```azurecli-interactive
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Log Analytics ワークスペースにメトリックとリソース ログを送信できるようにするには、次のコマンドを使用します。

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
  ```

このパラメーターを組み合わせて、複数の出力オプションを有効にできます。

---

## <a name="stream-into-sql-analytics"></a>SQL Analytics へのストリーム

Log Analytics ワークスペースにストリーミングされる Azure SQL Database および Azure SQL Managed Instance のメトリックとリソース ログは Azure SQL Analytics で使用できます。 Azure SQL Analytics は、単一データベース、エラスティック プール、プールされたデータベース、マネージド インスタンス、インスタンス データベースのパフォーマンスを、複数のサブスクリプションにわたって大規模に監視するクラウド ソリューションです。 これは、パフォーマンス メトリックの収集および視覚化に役立ち、パフォーマンスのトラブルシューティング用インテリジェンスが組み込まれています。

![Azure SQL Analytics の概要](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/azure-sql-sol-overview.png)

### <a name="installation-overview"></a>インストールの概要

Azure SQL Analytics を使用して一連のデータベースとデータベース コレクションを監視するには、次の手順を実行します。

1. Azure Marketplace から Azure SQL Analytics ソリューションを作成します。
2. ソリューションで Log Analytics ワークスペースを作成します。
3. ワークスペースに診断テレメトリをストリーミングするようにデータベースを構成します。

この診断テレメトリのストリーミング エクスポートを構成するには、Azure portal の [診断設定] タブに組み込まれている **[Log Analytics への送信]** オプションを使用します。 [PowerShell コマンドレット](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-powershell#configure-the-streaming-export-of-diagnostic-telemetry)、[Azure CLI](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-cli#configure-the-streaming-export-of-diagnostic-telemetry)、[Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)、または [Resource Manager テンプレート](../../azure-monitor/platform/diagnostic-settings-template.md)から診断設定を使用して、Log Analytics ワークスペースへのストリーミングを有効にすることもできます。

### <a name="create-an-azure-sql-analytics-resource"></a>Azure SQL Analytics リソースを作成する

1. Azure Marketplace で Azure SQL Analytics を探し、それを選択します。

   ![ポータルで Azure SQL Analytics を検索する](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/sql-analytics-in-marketplace.png)

2. ソリューションの概要画面で **[作成]** を選択します。

3. Azure SQL Analytics フォームに必要な追加情報 (ワークスペースの名前、サブスクリプション、リソース グループ、場所、価格レベル) を入力します。

   ![ポータルで Azure SQL Analytics を構成する](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/sql-analytics-configuration-blade.png)

4. **[OK]** を選択して確認し、 **[作成]** を選択します。

### <a name="configure-the-resource-to-record-metrics-and-resource-logs"></a>メトリックとリソース ログを記録するようにリソースを構成する

単一データベースおよびプールされたデータベース、エラスティック プール、マネージド インスタンス、およびインスタンス データベースに対して、診断テレメトリのストリーミングを個別に構成する必要があります。 リソースでメトリックを記録する場所を構成する最も簡単な方法は、Azure portal を使用することです。 詳細な手順については、「[診断テレメトリのストリーミング エクスポートを構成する](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-portal#configure-the-streaming-export-of-diagnostic-telemetry)」を参照してください。

### <a name="use-azure-sql-analytics-for-monitoring-and-alerting"></a>監視とアラートに Azure SQL Analytics を使用する

階層型のダッシュボードとして SQL Analytics を使用して、データベース リソースを表示できます。

- Azure SQL Analytics の使用方法については、[SQL Analytics を使用した監視](../../azure-monitor/insights/azure-sql.md)に関するページをご覧ください。
- SQL Analytics でアラートを設定する方法については、[データベース、エラスティック プール、マネージド インスタンスのアラートの作成](../../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)に関するページを参照してください。

## <a name="stream-into-event-hubs"></a>Event Hubs へのストリーム

Azure SQL Database および Azure SQL Managed Instance のメトリックとリソース ログは、Microsoft Azure portal に組み込まれている **[イベント ハブへのストリーム]** オプションを使用してイベント ハブにストリーミングできます。 Service Bus ルール ID は、PowerShell コマンドレット、Azure CLI、または Azure Monitor REST API を使用して診断設定をすることでも有効にできます。 イベント ハブがお使いのデータベースおよびサーバーと同じリージョンにあることを確認します。

### <a name="what-to-do-with-metrics-and-resource-logs-in-event-hubs"></a>Event Hubs におけるメトリックとリソース ログの活用方法

選択したデータが Event Hubs にストリーミングされると、高度な監視シナリオを有効にできます。 Event Hubs は、イベント パイプラインの玄関口として機能します。 イベント ハブに収集されたデータは、リアルタイム分析プロバイダーやストレージ アダプターを使用して、変換および保存できます。 Event Hubs は、イベントのストリームの運用と、イベントの消費を分離します。 これにより、イベントのコンシューマーは独自のスケジュールでイベントにアクセスできます。 Event Hubs の詳細については、以下を参照してください。

- [Event Hubs とは](../../event-hubs/event-hubs-about.md)
- [Event Hubs の使用](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

Azure Event Hubs でストリーミングされたメトリックは、次の目的に使用できます。

- **ホット パス データを Power BI にストリーミングしてサービスの正常性を表示する**

  Event Hubs、Stream Analytics および Power BI を使用することで、メトリクスと診断データを Azure サービスの近リアルタイム洞察に簡単に転換できます。 Event Hubs の設定、Stream Analytics を使用したデータ処理、および PowerBI を出力として使用する方法の概要については、「[Stream Analytics と Power BI](../../stream-analytics/stream-analytics-power-bi-dashboard.md)」をご覧ください。

- **サード パーティ製のロギングおよびテレメトリ ストリームにログをストリーミングする**

  Event Hubs ストリーミングを使用することで、さまざまなサード パーティのモニタリングおよびログ解析ソリューションにメトリックとリソース ログを送信できます。

- **カスタムのテレメトリおよびログ プラットフォームを構築する**

  既にカスタマイズされたテレメトリ プラットフォームがありますか、それとも構築を検討していますか? 高い拡張性の公開サブスクライブを特長とする Event Hubs を使用することでメトリックとリソース ログを柔軟に取り込むことができます。 [グローバル規模のテレメトリ プラットフォームで Event Hubs を使用する方法に関する Dan Rosanova によるガイド](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)をご覧ください。

## <a name="stream-into-azure-storage"></a>Azure Storage にストリーミングする

メトリックとリソース ログは、Azure portal に組み込まれている **[ストレージ アカウントへのアーカイブ]** オプションを使用して Azure Storage に保存できます。 Storage は、PowerShell コマンドレット、Azure CLI、または Azure Monitor REST API の診断設定からも有効にできます。

### <a name="schema-of-metrics-and-resource-logs-in-the-storage-account"></a>ストレージ アカウントのメトリックとリソース ログのスキーマ

メトリックおよびリソース ログの収集を設定した後、データの先頭行が取得されたときに、選択したストレージ アカウントにストレージ コンテナーが作成されます。 BLOB の構造は次のとおりです。

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

または、次のようによりシンプルな構造になります。

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

たとえば、基本メトリックの BLOB 名は次のようになります。

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

エラスティック プールからのデータを格納する BLOB 名は次のようになります。

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>データ リテンション期間ポリシーと価格

Event Hubs または Storage アカウントを選択した場合は、保持ポリシーを指定できます。 このポリシーは、選択した期間よりも古いデータを削除します。 Log Analytics を指定した場合、リテンション期間ポリシーは選択した価格レベルに依存します。 この場合、提供される無料のデータ インジェスト単位数により、毎月いくつかのデータベースの無料の監視が可能になります。 診断テレメトリの消費量が無料の単位数を超えると、有料になる可能性があります。

> [!IMPORTANT]
> アクティブなデータベースが取り込むデータは、ワークロードが重くなるほど、アイドル状態のデータベースよりも多くなります。 詳細については、「[Log Analytics の価格](https://azure.microsoft.com/pricing/details/monitor/)」を参照してください。

Azure SQL Analytics を使用している場合は、Azure SQL Analytics のナビゲーション メニューで **[OMS ワークスペース]** を選択してから **[使用量**と**推定コスト]** を選択することによって、データ インジェストの消費を監視できます。

## <a name="metrics-and-logs-available"></a>利用可能なメトリックとログ

このセクションでは、単一データベース、プールされたデータベース、エラスティック プール、マネージド インスタンス、インスタンス データベースで使用できるテレメトリの監視について説明しています。 SQL Analytics 内の収集された監視テレメトリは、[Azure Monitor ログ クエリ](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) 言語を使用した独自のカスタム分析とアプリケーション開発に利用できます。

### <a name="basic-metrics"></a>基本メトリック

リソースごとの基本メトリックの詳細については、次の表を参照してください。

> [!NOTE]
> 基本メトリック オプションは、以前は「すべてのメトリック」という名称でした。 単に名称が変更されただけで、監視対象メトリックには変更がありません。 このように変更された理由は、将来、他のメトリック カテゴリを導入する予定があるためです。

#### <a name="basic-metrics-for-elastic-pools"></a>エラスティック プールの基本メトリック

|**リソース**|**Metrics**|
|---|---|
|エラスティック プール|eDTU の割合、使用中の eDTU、eDTU の上限、CPU の割合、物理データ読み取りの割合、ログ書き込みの割合、セッションの割合、ワーカーの割合、ストレージ、ストレージの割合、ストレージの上限、XTP ストレージの割合 |

#### <a name="basic-metrics-for-single-and-pooled-databases"></a>単一データベースとプールされたデータベースの基本メトリック

|**リソース**|**Metrics**|
|---|---|
|単一データベースとプールされたデータベース|DTU の割合、使用中の DTU、DTU の上限、CPU の割合、物理データ読み取りの割合、ログ書き込みの割合、ファイアウォール接続による成功/失敗/ブロック、セッションの割合、ワーカーの割合、ストレージ、ストレージの割合、XTP ストレージの割合、デッドロック |

### <a name="advanced-metrics"></a>高度なメトリック

高度なメトリックの詳細については、次の表を参照してください。

|**メトリック**|**メトリックの表示名**|**説明**|
|---|---|---|
|sqlserver_process_core_percent<sup>1</sup>|SQL プロセス コアの割合|オペレーティング システムによって測定された SQL プロセスの CPU 使用率 (%)。|
|sqlserver_process_memory_percent<sup>1</sup> |SQL プロセス メモリの割合|オペレーティング システムによって測定された SQL プロセスのメモリ使用率 (%)。|
|tempdb_data_size<sup>2</sup>| Tempdb データ ファイル サイズ (KB) |Tempdb データ ファイル サイズ (KB)。|
|tempdb_log_size<sup>2</sup>| Tempdb ログ ファイル サイズ (KB) |Tempdb ログ ファイル サイズ (KB)。|
|tempdb_log_used_percent<sup>2</sup>| Tempdb ログ使用率 |Tempdb ログ使用率。|

<sup>1</sup> このメトリックはDTUベースの購入モデルに対して、仮想コア購入モデル（２仮想コア以上）または200DUT以上を使用しているデータベースで使用可能です。

<sup>1</sup> このメトリックはDTUベースの購入モデルに対して、仮想コア購入モデル（2仮想コア以上）または200DUT以上を使用しているデータベースで使用可能です。 ハイパースケールデータベースおよびデータウェアハウスでは、このメトリックは現在使用できません。

### <a name="basic-logs"></a>基本ログ

すべてのログで利用可能なテレメトリの詳細は、以下の表に記載されています。 詳細については、[サポートされている診断テレメトリ](#diagnostic-telemetry-for-export)に関するページをご覧ください。

#### <a name="resource-usage-stats-for-managed-instances"></a>マネージド インスタンスのリソース使用状況の統計

|プロパティ|説明|
|---|---|
|TenantId|テナント ID |
|SourceSystem|常時: Azure|
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ |
|Type|常時: AzureDiagnostics |
|ResourceProvider|リソース プロバイダーの名前。 常時: MICROSOFT.SQL |
|カテゴリ|カテゴリの名前。 常時: ResourceUsageStats |
|リソース|リソースの名前 |
|ResourceType|リソースの種類の名前。 常時: MANAGEDINSTANCES |
|SubscriptionId|データベースのサブスクリプション GUID |
|ResourceGroup|データベースのリソース グループの名前 |
|LogicalServerName_s|マネージド インスタンスの名前 |
|ResourceId|リソース URI |
|SKU_s|SQL Managed Instance の製品 SKU |
|virtual_core_count_s|使用可能な仮想コアの数 |
|avg_cpu_percent_s|平均 CPU の割合 |
|reserved_storage_mb_s|マネージド インスタンス上の予約済みストレージ容量 |
|storage_space_used_mb_s|マネージド インスタンス上の使用済みストレージ |
|io_requests_s|IOPS 数 |
|io_bytes_read_s|読み取り IOPS バイト |
|io_bytes_written_s|書き込み IOPS バイト |

#### <a name="query-store-runtime-statistics"></a>クエリ ストアのランタイム統計

|プロパティ|説明|
|---|---|
|TenantId|テナント ID |
|SourceSystem|常時: Azure |
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ |
|Type|常時: AzureDiagnostics |
|ResourceProvider|リソース プロバイダーの名前。 常時: MICROSOFT.SQL |
|カテゴリ|カテゴリの名前。 常時: QueryStoreRuntimeStatistics |
|OperationName|操作の名前。 常時: QueryStoreRuntimeStatisticsEvent |
|リソース|リソースの名前 |
|ResourceType|リソースの種類の名前。 常時: SERVERS/DATABASES |
|SubscriptionId|データベースのサブスクリプション GUID |
|ResourceGroup|データベースのリソース グループの名前 |
|LogicalServerName_s|データベースのサーバーの名前 |
|ElasticPoolName_s|データベースのエラスティック プールの名前 (存在する場合) |
|DatabaseName_s|データベースの名前 |
|ResourceId|リソース URI |
|query_hash_s|クエリ ハッシュ |
|query_plan_hash_s|クエリ プラン ハッシュ |
|statement_sql_handle_s|ステートメント sql ハンドル |
|interval_start_time_d|1900-1-1 からのティック数での間隔の開始 datetimeoffset |
|interval_end_time_d|1900-1-1 からのティック数での間隔の終了 datetimeoffset |
|logical_io_writes_d|論理 IO 書き込みの合計数 |
|max_logical_io_writes_d|実行ごとの論理 IO 書き込みの最大数 |
|physical_io_reads_d|物理 IO 読み取りの合計数 |
|max_physical_io_reads_d|実行ごとの論理 IO 読み取りの最大数 |
|logical_io_reads_d|論理 IO 読み取りの合計数 |
|max_logical_io_reads_d|実行ごとの論理 IO 読み取りの最大数 |
|execution_type_d|実行の種類 |
|count_executions_d|クエリの実行回数 |
|cpu_time_d|クエリで使用された合計 CPU 時間 (マイクロ秒単位) |
|max_cpu_time_d|1 回の実行による最大 CPU 時間コンシューマー (マイクロ秒単位) |
|dop_d|並列処理の次数の合計 |
|max_dop_d|1 回の実行で使用された並列処理の最大次数 |
|rowcount_d|返された行数の合計 |
|max_rowcount_d|1 回の実行で返された行の最大数 |
|query_max_used_memory_d|サポート技術情報で使用されたメモリの合計量 (KB 単位) |
|max_query_max_used_memory_d|1 回の実行で使用されたメモリの最大量 (KB 単位) |
|duration_d|合計実行時間 (マイクロ秒単位) |
|max_duration_d|1 回の実行の最大実行時間 |
|num_physical_io_reads_d|物理読み取りの合計数 |
|max_num_physical_io_reads_d|実行ごとの物理読み取りの最大数 |
|log_bytes_used_d|使用されたログの合計量 (バイト数) |
|max_log_bytes_used_d|実行ごとに使用されたログの最大量 (バイト数) |
|query_id_d|クエリ データ ストアでのクエリの ID |
|plan_id_d|クエリ データ ストアでのプランの ID |

[クエリ ストアのランタイム統計データ](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql)の詳細。

#### <a name="query-store-wait-statistics"></a>クエリ ストアの待機統計

|プロパティ|説明|
|---|---|
|TenantId|テナント ID |
|SourceSystem|常時: Azure |
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ |
|Type|常時: AzureDiagnostics |
|ResourceProvider|リソース プロバイダーの名前。 常時: MICROSOFT.SQL |
|カテゴリ|カテゴリの名前。 常時: QueryStoreWaitStatistics |
|OperationName|操作の名前。 常時: QueryStoreWaitStatisticsEvent |
|リソース|リソースの名前 |
|ResourceType|リソースの種類の名前。 常時: SERVERS/DATABASES |
|SubscriptionId|データベースのサブスクリプション GUID |
|ResourceGroup|データベースのリソース グループの名前 |
|LogicalServerName_s|データベースのサーバーの名前 |
|ElasticPoolName_s|データベースのエラスティック プールの名前 (存在する場合) |
|DatabaseName_s|データベースの名前 |
|ResourceId|リソース URI |
|wait_category_s|待機のカテゴリ |
|is_parameterizable_s|クエリがパラメーター化可能かどうか |
|statement_type_s|ステートメントの種類 |
|statement_key_hash_s|ステートメント キー ハッシュ |
|exec_type_d|実行の種類 |
|total_query_wait_time_ms_d|特定の待機カテゴリでのクエリの合計待機時間 |
|max_query_wait_time_ms_d|特定の待機のカテゴリの個々の実行でのクエリの最大待機時間 |
|query_param_type_d|0 |
|query_hash_s|クエリ データ ストア内のクエリ ハッシュ |
|query_plan_hash_s|クエリ ストア内のクエリ プラン ハッシュ |
|statement_sql_handle_s|クエリ ストア内のステートメント ハンドル |
|interval_start_time_d|1900-1-1 からのティック数での間隔の開始 datetimeoffset |
|interval_end_time_d|1900-1-1 からのティック数での間隔の終了 datetimeoffset |
|count_executions_d|クエリの実行回数 |
|query_id_d|クエリ データ ストアでのクエリの ID |
|plan_id_d|クエリ データ ストアでのプランの ID |

[クエリ ストアの待機統計データ](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql)の詳細。

#### <a name="errors-dataset"></a>エラー データセット

|プロパティ|説明|
|---|---|
|TenantId|テナント ID |
|SourceSystem|常時: Azure |
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ |
|Type|常時: AzureDiagnostics |
|ResourceProvider|リソース プロバイダーの名前。 常時: MICROSOFT.SQL |
|カテゴリ|カテゴリの名前。 常時: エラー |
|OperationName|操作の名前。 常時: ErrorEvent |
|リソース|リソースの名前 |
|ResourceType|リソースの種類の名前。 常時: SERVERS/DATABASES |
|SubscriptionId|データベースのサブスクリプション GUID |
|ResourceGroup|データベースのリソース グループの名前 |
|LogicalServerName_s|データベースのサーバーの名前 |
|ElasticPoolName_s|データベースのエラスティック プールの名前 (存在する場合) |
|DatabaseName_s|データベースの名前 |
|ResourceId|リソース URI |
|Message|プレーンテキストでのエラー メッセージ |
|user_defined_b|エラーがユーザー定義ビットかどうか |
|error_number_d|エラー コード |
|重大度|エラーの重大度 |
|state_d|エラーの状態 |
|query_hash_s|使用可能な場合は、失敗したクエリのクエリ ハッシュ |
|query_plan_hash_s|使用可能な場合は、失敗したクエリのクエリ プラン ハッシュ |

詳しくは、[SQL エラー メッセージ](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors?view=sql-server-ver15)に関するページをご覧ください。

#### <a name="database-wait-statistics-dataset"></a>データベースの待機統計データセット

|プロパティ|説明|
|---|---|
|TenantId|テナント ID |
|SourceSystem|常時: Azure |
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ |
|Type|常時: AzureDiagnostics |
|ResourceProvider|リソース プロバイダーの名前。 常時: MICROSOFT.SQL |
|カテゴリ|カテゴリの名前。 常時: DatabaseWaitStatistics |
|OperationName|操作の名前。 常時: DatabaseWaitStatisticsEvent |
|リソース|リソースの名前 |
|ResourceType|リソースの種類の名前。 常時: SERVERS/DATABASES |
|SubscriptionId|データベースのサブスクリプション GUID |
|ResourceGroup|データベースのリソース グループの名前 |
|LogicalServerName_s|データベースのサーバーの名前 |
|ElasticPoolName_s|データベースのエラスティック プールの名前 (存在する場合) |
|DatabaseName_s|データベースの名前 |
|ResourceId|リソース URI |
|wait_type_s|待機の種類の名前 |
|start_utc_date_t [UTC]|測定期間の開始時刻 |
|end_utc_date_t [UTC]|測定期間の終了時刻 |
|delta_max_wait_time_ms_d|実行ごとの最大待機時間 |
|delta_signal_wait_time_ms_d|シグナルの合計待機時間 |
|delta_wait_time_ms_d|期間内の合計待機時間 |
|delta_waiting_tasks_count_d|待機中のタスク数 |

[データベース待機統計](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)の詳細。

#### <a name="time-outs-dataset"></a>タイムアウトのデータセット

|プロパティ|説明|
|---|---|
|TenantId|テナント ID |
|SourceSystem|常時: Azure |
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ |
|Type|常時: AzureDiagnostics |
|ResourceProvider|リソース プロバイダーの名前。 常時: MICROSOFT.SQL |
|カテゴリ|カテゴリの名前。 常時: Timeouts |
|OperationName|操作の名前。 常時: TimeoutEvent |
|リソース|リソースの名前 |
|ResourceType|リソースの種類の名前。 常時: SERVERS/DATABASES |
|SubscriptionId|データベースのサブスクリプション GUID |
|ResourceGroup|データベースのリソース グループの名前 |
|LogicalServerName_s|データベースのサーバーの名前 |
|ElasticPoolName_s|データベースのエラスティック プールの名前 (存在する場合) |
|DatabaseName_s|データベースの名前 |
|ResourceId|リソース URI |
|error_state_d|エラー状態コード |
|query_hash_s|クエリ ハッシュ (使用可能な場合) |
|query_plan_hash_s|クエリ プラン ハッシュ (使用可能な場合) |

#### <a name="blockings-dataset"></a>ブロックしているデータセット

|プロパティ|説明|
|---|---|
|TenantId|テナント ID |
|SourceSystem|常時: Azure |
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ |
|Type|常時: AzureDiagnostics |
|ResourceProvider|リソース プロバイダーの名前。 常時: MICROSOFT.SQL |
|カテゴリ|カテゴリの名前。 常時: Blocks |
|OperationName|操作の名前。 常時: BlockEvent |
|リソース|リソースの名前 |
|ResourceType|リソースの種類の名前。 常時: SERVERS/DATABASES |
|SubscriptionId|データベースのサブスクリプション GUID |
|ResourceGroup|データベースのリソース グループの名前 |
|LogicalServerName_s|データベースのサーバーの名前 |
|ElasticPoolName_s|データベースのエラスティック プールの名前 (存在する場合) |
|DatabaseName_s|データベースの名前 |
|ResourceId|リソース URI |
|lock_mode_s|クエリで使用されるロック モード |
|resource_owner_type_s|ロックの所有者 |
|blocked_process_filtered_s|ブロックされているプロセスのレポート XML |
|duration_d|ロック期間 (ミリ秒) |

#### <a name="deadlocks-dataset"></a>デッドロック データセット

|プロパティ|説明|
|---|---|
|TenantId|テナント ID |
|SourceSystem|常時: Azure |
|TimeGenerated [UTC] |ログが記録されたときのタイムスタンプ |
|Type|常時: AzureDiagnostics |
|ResourceProvider|リソース プロバイダーの名前。 常時: MICROSOFT.SQL |
|カテゴリ|カテゴリの名前。 常時: デッドロック |
|OperationName|操作の名前。 常時: DeadlockEvent |
|リソース|リソースの名前 |
|ResourceType|リソースの種類の名前。 常時: SERVERS/DATABASES |
|SubscriptionId|データベースのサブスクリプション GUID |
|ResourceGroup|データベースのリソース グループの名前 |
|LogicalServerName_s|データベースのサーバーの名前 |
|ElasticPoolName_s|データベースのエラスティック プールの名前 (存在する場合) |
|DatabaseName_s|データベースの名前 |
|ResourceId|リソース URI |
|deadlock_xml_s|デッドロック レポート XML |

#### <a name="automatic-tuning-dataset"></a>自動チューニング データセット

|プロパティ|説明|
|---|---|
|TenantId|テナント ID |
|SourceSystem|常時: Azure |
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ |
|Type|常時: AzureDiagnostics |
|ResourceProvider|リソース プロバイダーの名前。 常時: MICROSOFT.SQL |
|カテゴリ|カテゴリの名前。 常時: AutomaticTuning |
|リソース|リソースの名前 |
|ResourceType|リソースの種類の名前。 常時: SERVERS/DATABASES |
|SubscriptionId|データベースのサブスクリプション GUID |
|ResourceGroup|データベースのリソース グループの名前 |
|LogicalServerName_s|データベースのサーバーの名前 |
|LogicalDatabaseName_s|データベースの名前 |
|ElasticPoolName_s|データベースのエラスティック プールの名前 (存在する場合) |
|DatabaseName_s|データベースの名前 |
|ResourceId|リソース URI |
|RecommendationHash_s|自動チューニング推奨設定の一意のハッシュ |
|OptionName_s|自動チューニング オプション |
|Schema_s|データベース スキーマ |
|Table_s|影響を受けるテーブル |
|IndexName_s|インデックス名 |
|IndexColumns_s|列名 |
|IncludedColumns_s|含まれる列 |
|EstimatedImpact_s|自動チューニング推奨設定 JSON の推定される影響 |
|Event_s|自動チューニング イベントの種類 |
|Timestamp_t|最終更新時のタイムスタンプ |

#### <a name="intelligent-insights-dataset"></a>Intelligent Insights データセット

[Intelligent Insights ログ形式](intelligent-insights-use-diagnostics-log.md)の詳細。

## <a name="next-steps"></a>次のステップ

ログ記録を有効にする方法や、各種の Azure サービスでサポートされているメトリックとログのカテゴリについては、次の資料を参照してください。

- [Microsoft Azure のメトリックの概要](../../azure-monitor/platform/data-platform.md)
- [Azure プラットフォーム ログの概要](../../azure-monitor/platform/platform-logs-overview.md)

Event Hubs の詳細については、次の資料を参照してください。

- [Azure Event Hubs とは](../../event-hubs/event-hubs-about.md)
- [Event Hubs の使用](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

Log Analytics からのテレメトリに基づいてアラートを設定する方法については、次の資料を参照してください。

- [Azure SQL Database と Azure SQL Managed Instance のアラートの作成](../../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)
