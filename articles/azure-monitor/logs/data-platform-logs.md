---
title: Azure Monitor ログ
description: 監視データの高度な分析に使用される Azure Monitor のログについて説明します。
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/22/2020
ms.author: bwren
ms.openlocfilehash: 5066264777c66369205489fb27a6f9206c5da521
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708001"
---
# <a name="azure-monitor-logs-overview"></a>Azure Monitor ログの概要
Azure Monitor ログは、[監視対象のリソース](../monitor-reference.md)からログとパフォーマンス データを収集して整理する Azure Monitor の機能です。 Azure サービスからの[プラットフォーム ログ](../essentials/platform-logs-overview.md)、[仮想マシン エージェント](../agents/agents-overview.md)からのログとパフォーマンス データ、[アプリケーション](../app/app-insights-overview.md)からの使用状況とパフォーマンス データなど、さまざまなソースからのデータを 1 つのワークスペースに統合し、何百万ものレコードを迅速に分析できる高度なクエリ言語を使用してまとめて分析することができます。 特定のレコードのセットを取得するだけの単純なクエリを実行することも、高度なデータ分析を実行して監視データの重要なパターンを特定することもできます。 Log Analytics を使用してログ クエリとその結果を対話形式で操作したり、アラート ルールでそれらを使用して問題が事前に通知されるようにしたり、ブックやダッシュボードでその結果を視覚化したりします。

> [!NOTE]
> Azure Monitor ログは、Azure Monitor をサポートするデータ プラットフォームの半分です。 もう一方は、時系列データベースに数値データを格納する [Azure Monitor メトリック](../essentials/data-platform-metrics.md)です。 これは、このデータを、Azure Monitor ログのデータよりも軽量化し、ほぼリアルタイムのシナリオに対応させ、問題のアラートや迅速な検出に特に役立たせることができるものです。 ただし、メトリックが特定の構造に数値データを格納することしかできないのに対し、ログは、それぞれ独自の構造を持つさまざまなデータ型を格納できます。 メトリック データの分析に使用できないログ クエリを使用して、ログ データで複雑な分析を実行することもできます。


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Azure Monitor のログでできること
次の表に、Azure Monitor でログを使用できるさまざまな方法のいくつかを示します。

|  |  |
|:---|:---|
| **分析** | Azure portal で [Log Analytics](./log-analytics-tutorial.md) を使用して[ログ クエリ](./log-query-overview.md)を書き、強力な分析エンジンを使用してログ データを対話形式で分析します。 |
| **Alert** | クエリの結果が特定の結果に一致するときに、通知を送信するか[自動化されたアクション](../alerts/action-groups.md)を実行する、[ログ警告ルール](../alerts/alerts-log.md)を構成します。 |
| **視覚化** | テーブルまたはグラフとして表示されるクエリ結果を [Azure ダッシュボード](../../azure-portal/azure-portal-dashboards.md)にピン留めします。<br>[ブック](../visualize/workbooks-overview.md)を作成し、対話形式のレポートに複数のデータ セットを結合します。 <br>クエリの結果を [Power BI](../visualize/powerbi.md) にエクスポートし、さまざまな視覚化を使用して Azure の外部のユーザーと共有します。<br>クエリの結果を [Grafana](../visualize/grafana-plugin.md) にエクスポートし、そのダッシュボード機能を活用し、他のデータ ソースと結合します。|
| **分析情報** | 特定のアプリケーションやサービスを監視するためのカスタマイズされたエクスペリエンスを提供する[分析情報](../monitor-reference.md#insights-and-core-solutions)をサポートします。  |
| **取得** | [Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics) を使用して、コマンド ラインからログ クエリの結果にアクセスします。<br>[PowerShell コマンドレット](/powershell/module/az.operationalinsights)を使用して、コマンド ラインからログ クエリの結果にアクセスします。<br>[REST API](https://dev.loganalytics.io/) を使用して、カスタム アプリケーションからログ クエリの結果にアクセスします。 |
| **エクスポート** | Azure ストレージ アカウントまたは Azure Event Hubs への[ログ データの自動エクスポート](./logs-data-export.md)を構成します。<br>[Logic Apps](./logicapp-flow-connector.md) を使用し、ログ データを取得して外部の場所にコピーするワークフローを構築します。 |

![ログの概要](media/data-platform-logs/logs-overview.png)


## <a name="data-collection"></a>データ コレクション
Log Analytics のワークスペースを作成したら、データを送信するようにさまざまなソースを構成する必要があります。 データは自動的には収集されません。 この構成は、データ ソースによって異なります。 たとえば、Azure リソースからワークスペースにリソース ログを送信するには、[診断設定](../essentials/diagnostic-settings.md)を作成します。 仮想マシンからデータを収集するには、[VM 分析情報を有効にします](../vm/vminsights-enable-overview.md)。 追加のイベントとパフォーマンス データを収集するには、[ワークスペース上にデータ ソースを構成します](../agents/data-sources.md)。

- Azure Monitor ログにデータを送信するよう構成できるデータ ソースの完全なリストについては、「[Azure Monitor によって監視される内容](../monitor-reference.md)」を参照してください。


## <a name="log-analytics-workspaces"></a>Log Analytics ワークスペース
Azure Monitor ログによって収集されたデータは、1 つ以上の [Log Analytics ワークスペース](./design-logs-deployment.md)に格納されます。 ワークスペースでは、データの地理的な場所、データにアクセスできるユーザーを定義するアクセス権、価格レベルやデータ保有期間などの構成設定が定義されます。  

Azure Monitor ログを使用するには、少なくとも 1 つのワークスペースを作成する必要があります。 すべての監視データに対して 1 つのワークスペースで十分である場合もあります。また、要件に応じて複数のワークスペースを作成することもできます。 たとえば、実稼働データ用に 1 つのワークスペースを、そしてテスト用に別のものを用意することができます。 

- 新しいワークスペースを設定するには、「[Azure portal で Log Analytics ワークスペースを作成する](./quick-create-workspace.md)」を参照してください。
- 複数のワークスペースを作成する際の考慮事項については、「[Azure Monitor ログのデプロイの設計](design-logs-deployment.md)」を参照してください。

## <a name="data-structure"></a>データ構造
ログ クエリにより、Log Analytics ワークスペースからデータが取得されます。 各ワークスペースには、複数のデータ行を含む別々の列に編成された複数のテーブルが含まれています。 各テーブルは、データ ソースによって提供されるデータ行によって共有される一意の列セットによって定義されます。 

[![Azure Monitor ログの構造](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Application Insights のログ データは、Azure Monitor ログにも格納されますが、アプリケーションの構成方法によってその格納方法は異なります。 ワークスペースベースのアプリケーションの場合、アプリケーション要求、例外、ページ ビューなどのデータを保持するために、データは標準のテーブ ルセットの Log Analytics ワークスペースに格納されます。 複数のアプリケーションで同じワークスペースを使用できます。 従来のアプリケーションでは、データは Log Analytics ワークスペースには格納されません。 同じクエリ言語が使用されているため、Azure portal の同じ Log Analytics ツールを使用してクエリを作成して実行します。 ただし、従来のアプリケーションのデータは、互いに独立して格納されます。 テーブル名と列名は異なっていますが、その一般的な構造は、ワークスペースベースのアプリケーションと同じです。 ワークスペースベースおよび従来のアプリケーションのスキーマの詳細な比較については、「[ワークスペースベース リソースの変更](../app/apm-tables.md)」を参照してください。


> [!NOTE]
> Application Insights のエクスペリエンスにおける Application Insights クラシック リソース クエリ、ブック、およびログベースのアラートについては、引き続き完全な下位互換性を提供します。 [新しいワークスペース ベースのテーブル構造またはスキーマ](../app/apm-tables.md)に対してクエリ/ビューを実行するには、まず Log Analytics ワークスペースに移動する必要があります。 プレビュー中に [Application Insights] ウィンドウの **[ログ]** を選択すると、従来の Application Insights クエリ エクスペリエンスにアクセスできます。 詳しくは、「[クエリ スコープ](./scope.md)」を参照してください。


[![Application Insights の Azure Monitor ログの構造](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)


## <a name="log-queries"></a>ログ クエリ
データはログ クエリを使用して Log Analytics ワークスペースから取得されます。これは、データを処理して結果を返す読み取り専用の要求です。 ログ クエリは、Azure Data Explorer で使用されるものと同じクエリ言語である [Kusto クエリ言語 (KQL)](/azure/data-explorer/kusto/query/) で記述されます。 Log Analytics でログ クエリを記述してその結果を対話形式で操作したり、アラート ルールでそれらを使用して問題が事前に通知されるようにしたり、ブックやダッシュボードにその結果を含めたりすることができます。 分析情報には、ビューとブックをサポートするための事前に構築されたクエリが含まれます。

- ログ クエリが使用されている場所の一覧と、開始するためのチュートリアルやその他のドキュメントのリファレンスについては、「[Azure Monitor のログ クエリ](./log-query-overview.md)」を参照してください。

![Log Analytics](media/data-platform-logs/log-analytics.png)

## <a name="log-analytics"></a>Log Analytics
Azure portal のツールである Log Analytics を使用して、ログ クエリを編集して実行し、その結果を対話形式で分析します。 その後、作成したクエリを使用して、ログ クエリ アラートやブックなどの Azure Monitor の他の機能をサポートできます。 Azure Monitor メニューの **[ログ]** オプション、または Azure portal のその他のほとんどのサービスから、Log Analytics にアクセスできます。

- Log Analytics の説明については、[Azure Monitor の Log Analytics の概要](./log-analytics-overview.md)に関するページを参照してください。 
- Log Analytics 機能を使用して簡単なログクエリを作成し、その結果を分析する方法を体験するには、[Log Analytics チュートリアル](./log-analytics-tutorial.md)を参照してください。



## <a name="relationship-to-azure-data-explorer"></a>Azure Data Explorer とのリレーションシップ
Azure Monitor ログは、Azure Data Explorer に基づいています。 Log Analytics ワークスペースは Azure Data Explorer のデータベースとほぼ同じで、テーブルは同じように構造化され、どちらも同じ Kusto クエリ言語 (KQL) が使用されています。 Log Analytics を使用して Azure portal で Azure Monitor クエリを操作するエクスペリエンスは、Azure Data Explorer Web UI を使用したエクスペリエンスと似ています。 [Log Analytics ワークスペースのデータを Azure Data Explorer クエリに含める](/azure/data-explorer/query-monitor-data)こともできます。 


## <a name="next-steps"></a>次の手順

- Log Analytics ワークスペースからデータを取得して分析する[ログ クエリ](./log-query-overview.md)について説明します。
- [Azure Monitor でのメトリック](../essentials/data-platform-metrics.md)を確認します。
- Azure のさまざまなリソースで[入手できる監視データ](../agents/data-sources.md)を確認します。