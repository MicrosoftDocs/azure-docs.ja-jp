---
title: Azure Monitor のログ | Microsoft Docs
description: 監視データの高度な分析に使用される Azure Monitor のログについて説明します。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 9aed19c88517868c2e8cb860dc01d01b7a7c3127
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262074"
---
# <a name="logs-in-azure-monitor"></a>Azure Monitor のログ

> [!NOTE]
> Azure Monitor で収集されたすべてのデータは、2 つの基本的な型であるメトリックとログのどちらかに該当します。 この記事では、ログについて説明します。 メトリックの詳細な説明については「[Azure Monitor のメトリック](data-platform-metrics.md)」を、2 つの比較については「[Azure Monitor によって収集される監視データ](data-platform.md)」をご覧ください。

Azure Monitor のログは、さまざまなソースからのデータについて複雑な分析を実行するときに特に便利です。 この記事では、Azure Monitor でのログの構造と、データを使用してできること、およびログにデータを保存できるさまざまなデータ ソースについて説明します。

> [!NOTE]
> Azure Monitor のログと Azure 上のログ データのソースを区別することが重要です。 たとえば、Azure のサブスクリプション レベルのイベントは、Azure Monitor のメニューから表示できる[アクティビティ ログ](activity-logs-overview.md)に書き込まれます。 ほとんどのリソースでは、別の場所に転送できる[診断ログ](resource-logs-overview.md)に運用情報が書き込まれます。 Azure Monitor のログは、アクティビティ データと診断ログと共に他の監視データを収集して、リソース全体を深く分析するためのログ データ プラットフォームです。

## <a name="what-are-azure-monitor-logs"></a>Azure Monitor のログとは

Azure Monitor のログには、種類ごとに異なるプロパティ セットを持つレコードに編成されたさまざまな種類のデータが含まれます。 ログは、Azure Monitor のメトリックのような数値を含むことができますが、通常は、詳細な説明付きのテキスト データが含まれます。 さらに、それらは、構造が違うことと、多くの場合定期的に収集されないという点で、メトリック データとは異なります。 イベントやトレースなどの利用統計情報は、組み合わせて分析できるように、パフォーマンス データとともに Azure Monitor ログとして格納されます。

一般的な種類のログ エントリは、散発的に収集されるイベントです。 イベントは、アプリケーションまたはサービスによって作成され、通常は、単独で完全なコンテキストを示す十分な情報が含まれています。 たとえば、イベントは、特定のリソースが作成または変更されたこと、トラフィックの増加に応答して新しいホストが開始されたこと、またはアプリケーションでエラーが検出されたことを示すことができます。

 データの形式は多様である可能性があるため、アプリケーションは、必要な構造を使用するカスタム ログを作成できます。 メトリック データは、他の監視データと組み合わせてトレンド分析やその他のデータ分析を行うために、ログ内に格納することもできます。


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Azure Monitor のログでできること
次の表に、Azure Monitor でログを使用できるさまざまな方法を示します。


|  |  |
|:---|:---|
| 分析 | Azure portal で [Log Analytics](../log-query/get-started-portal.md) を使用して[ログ クエリ](../log-query/log-query-overview.md)を書き、強力なデータ エクスプローラー分析エンジンを使用してログ データを対話形式で分析します。<br>Azure portal で [Application Insights 分析コンソール](../app/analytics.md)を使用してログ クエリを記述し、Application Insights からログ データを対話形式で分析します。 |
| 視覚化 | テーブルまたはグラフとして表示されるクエリ結果を [Azure ダッシュボード](../../azure-portal/azure-portal-dashboards.md)にピン留めします。<br>[ブック](../app/usage-workbooks.md)を作成し、対話形式のレポートに複数のデータ セットを結合します。 <br>クエリの結果を [Power BI](powerbi.md) にエクスポートし、さまざまな視覚化を使用して Azure の外部のユーザーと共有します。<br>クエリの結果を [Grafana](grafana-plugin.md) にエクスポートし、そのダッシュボード機能を活用し、他のデータ ソースと結合します。|
| アラート: | クエリの結果が特定の結果に一致するときに、通知を送信するか[自動化されたアクション](action-groups.md)を実行する、[ログ警告ルール](alerts-log.md)を構成します。<br>メトリックとして抽出された特定のログ データ ログに対して[メトリック警告ルール](alerts-metric-logs.md)を構成します。 |
| 取得 | [Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics) を使用して、コマンド ラインからログ クエリの結果にアクセスします。<br>[PowerShell コマンドレット](https://docs.microsoft.com/powershell/module/az.operationalinsights)を使用して、コマンド ラインからログ クエリの結果にアクセスします。<br>[REST API](https://dev.loganalytics.io/) を使用して、カスタム アプリケーションからログ クエリの結果にアクセスします。 |
| エクスポート | [Logic Apps](~/articles/logic-apps/index.yml) を使用し、ログ データを取得して外部の場所にコピーするワークフローを構築します。 |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Azure Monitor ログのデータの構造
Azure Monitor ログによって収集されたデータは、[Log Analytics ワークスペース](../platform/design-logs-deployment.md)に格納されます。 各ワークスペースには、それぞれに特定のソースからのデータが格納される複数のテーブルが含まれます。 すべてのテーブルで共有される[いくつかの一般的なプロパティ](log-standard-properties.md)がありますが、格納されるデータの種類に応じたそれぞれに固有のプロパティ セットもあります。 新しいワークスペースには標準のテーブル セットが作成され、ワークスペースに書き込むさまざまな監視ソリューションと他のサービスによってテーブルが追加されます。

Application Insights からのログ データではワークスペースと同じ Log Analytics エンジンが使用されますが、監視対象のアプリケーションごとに個別に格納されます。 各アプリケーションには、アプリケーションの要求、例外、ページ ビューなど、データを保持するための標準のテーブル セットがあります。

ログ クエリでは、Log Analytics ワークスペースまたは Application Insights アプリケーションからのデータが使用されます。 [リソース間のクエリ](../log-query/cross-workspace-query.md)を使用して、他のログ データと共にアプリケーション データを分析したり、複数のワークスペースまたはアプリケーションを含むクエリを作成したりすることができます。

![Workspaces](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>ログ クエリ
Azure Monitor ログのデータは、[Kusto クエリ言語](../log-query/get-started-queries.md)で書かれた[ログ クエリ](../log-query/log-query-overview.md)を使用して取得できます。これにより、収集されたデータを迅速に取得、統合、および分析できます。 [Log Analytics](../log-query/portals.md) を使用して、Azure portal でログ クエリを記述してテストします。 結果は、対話形式で操作したり、ダッシュボードにピン留めして他の視覚化と一緒に表示したりできます。

![Log Analytics](media/data-platform-logs/log-analytics.png)

Application Insights のデータを分析するには、[Application Insights から Log Analytics](../app/analytics.md) を開きます。

![Application Insights Analytics](media/data-platform-logs/app-insights-analytics.png)

[Log Analytics API](https://dev.loganalytics.io/documentation/overview) および [Application Insights REST API](https://dev.applicationinsights.io/documentation/overview) を使用して、ログ データを取得することもできます。


## <a name="sources-of-azure-monitor-logs"></a>Azure Monitor ログのソース
Azure Monitor は、Azure 内とオンプレミス リソースからの両方の、さまざまなソースからログ データを収集できます。 次の表では、Azure Monitor ログにデータを書き込むさまざまなリソースから使用できるさまざまなデータ ソースを示します。 それぞれに、必要な構成の詳細へのリンクがあります。

### <a name="azure-tenant-and-subscription"></a>Azure テナントとサブスクリプション

| Data | 説明 |
|:---|:---|
| Azure Active Directory 監査ログ | 各ディレクトリの診断設定によって構成されます。 「[Azure AD ログを Azure Monitor ログと統合する](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)」をご覧ください。  |
| アクティビティ ログ | 既定では個別に格納され、ほぼリアルタイムのアラートに使用できます。 Log Analytics ワークスペースに書き込むには、Activity Log Analytics ソリューションをインストールします。 「[Log Analytics での Azure アクティビティ ログの収集と分析](activity-log-collect.md)」をご覧ください。 |

### <a name="azure-resources"></a>Azure リソース

| Data | 説明 |
|:---|:---|
| リソース診断 | Log Analytics ワークスペースへのメトリックなど、診断データを書き込むための診断設定を構成します。 「[Azure 診断ログの Log Analytics へのストリーミング](resource-logs-collect-storage.md)」をご覧ください。 |
| 監視ソリューション | 監視ソリューションにより収集されたデータが Log Analytics ワークスペースに書き込まれます。 ソリューションの一覧については、「[Azure での管理ソリューションのデータ収集の詳細](../insights/solutions-inventory.md)」をご覧ください。 ソリューションのインストールと使用について詳しくは、「[Azure Monitor での監視ソリューション](../insights/solutions.md)」をご覧ください。 |
| メトリック | ログ データを長期間保持し、[Kusto クエリ言語](/azure/kusto/query/)を使用して他のデータの種類と共に複雑な分析を実行するために、Azure Monitor リソース用のプラットフォーム メトリックを Log Analytics ワークスペースに送信します。 「[Azure 診断ログの Log Analytics へのストリーミング](resource-logs-collect-storage.md)」をご覧ください。 |
| Azure テーブル ストレージ | 一部の Azure リソースによて監視データが書き込まれる Azure Storage からデータを収集します。 「[Log Analytics で IIS 用 Azure Blob Storage とイベント用 Azure Table Storage を使用する](azure-storage-iis-table.md)」をご覧ください。 |

### <a name="virtual-machines"></a>Virtual Machines

| Data | 説明 |
|:---|:---|
|  エージェントのデータ ソース | [Windows](agent-windows.md) と [Linux](../learn/quick-collect-linux-computer.md) のエージェントから収集されるデータ ソースには、イベント、パフォーマンス データ、カスタム ログが含まれます。 データ ソースの一覧と構成の詳細については、「[Azure Monitor のエージェント データ ソース](data-sources.md)」をご覧ください。 |
| 監視ソリューション | 監視ソリューションによりエージェントから収集されたデータが Log Analytics ワークスペースに書き込まれます。 ソリューションの一覧については、「[Azure での管理ソリューションのデータ収集の詳細](../insights/solutions-inventory.md)」をご覧ください。 ソリューションのインストールと使用について詳しくは、「[Azure Monitor での監視ソリューション](../insights/solutions.md)」をご覧ください。 |
| System Center Operations Manager | Operations Manager の管理グループを Azure Monitor に接続し、オンプレミスのエージェントからログにイベントおよびパフォーマンス データを収集します。 この構成について詳しくは、「[Operations Manager を Log Analytics に接続する](om-agents.md)」をご覧ください。 |


### <a name="applications"></a>[アプリケーション]

| Data | 説明 |
|:---|:---|
| 要求と例外 | アプリケーションの要求と例外に関する詳細なデータは、_requests_、_pageViews_、_exceptions_ の各テーブルにあります。 [外部コンポーネント](../app/asp-net-dependencies.md)の呼び出しは、_dependencies_ テーブルにあります。 |
| 使用状況とパフォーマンス | アプリケーションのパフォーマンスは、_requests_、_browserTimings_、_performanceCounters_ の各テーブルで入手できます。 [カスタム メトリック](../app/api-custom-events-metrics.md#trackevent)のデータは、_customMetrics_ テーブルにあります。|
| トレース データ | [分散トレース](../app/distributed-tracing.md)からの結果は、_traces_ テーブルに格納されます。 |
| 可用性テスト | [可用性テスト](../app/monitor-web-app-availability.md)の概要データは、_availabilityResults_ テーブルに格納されます。 これらのテストからの詳細データは別のストレージにあり、Azure portal の Application Insights でアクセスします。 |

### <a name="insights"></a>洞察

| Data | 説明 |
|:---|:---|
| コンテナーに対する Azure Monitor | [Azure Monitor for containers](../insights/container-insights-overview.md) によって収集されたインベントリとパフォーマンス データです。 テーブルの一覧については、「[コンテナーのデータ収集の詳細](../insights/container-insights-log-search.md#container-records)」をご覧ください。 |
| VM に対する Azure Monitor | [Azure Monitor for VMs](../insights/vminsights-overview.md) によって収集されたマップとパフォーマンス データです。 このデータのクエリについて詳しくは、「[VM 用 Azure Monitor からログを照会する方法](../insights/vminsights-log-search.md)」をご覧ください。 |

### <a name="custom"></a>カスタム 

| Data | 説明 |
|:---|:---|
| REST API | 任意の REST クライアントから Log Analytics ワークスペースにデータを書き込みます。 詳しくは、「[HTTP データ コレクター API を使用した Azure Monitor へのログ データの送信](data-collector-api.md)」をご覧ください。
| ロジック アプリ | **Azure Log Analytics データ コレクター** アクションでロジック アプリ ワークフローから Log Analytics ワークスペースに任意のデータを書き込みます。 |

### <a name="security"></a>セキュリティ

| Data | 説明 |
|:---|:---|
| Azure Security Center | [Azure Security Center](/azure/security-center/) には、Log Analytics ワークスペースで収集されたデータが格納され、他のログ データと共に分析できます。 ワークスペースの構成について詳しくは、「[Azure Security Center でのデータ収集](../../security-center/security-center-enable-data-collection.md)」をご覧ください。 |
| Azure Sentinel | [Azure Sentinel](/azure/sentinel/) には、データ ソースから Log Analytics ワークスペースへのデータが格納されます。 「[データ ソースの接続](/azure/sentinel/connect-data-sources)」のページを参照してください。  |


## <a name="next-steps"></a>次の手順

- [Azure Monitor データ プラットフォーム](data-platform.md)の詳細を確認します。
- [Azure Monitor でのメトリック](data-platform-metrics.md)を確認します。
- Azure のさまざまなリソースで[入手できる監視データ](data-sources.md)を確認します。
