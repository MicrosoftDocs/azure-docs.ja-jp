---
title: Azure Monitor で使用するデータのソース | Microsoft Docs
description: Azure リソースとそれらで実行されているアプリケーションの正常性とパフォーマンスを監視するために使用できるデータについて説明します。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2018
ms.author: bwren
ms.openlocfilehash: 6d03c219025c8cd39214bd8ab6807125709f9742
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790878"
---
# <a name="sources-of-data-in-azure-monitor"></a>Azure Monitor で使用するデータのソース
この記事では、リソースとそれらで実行されているアプリケーションの正常性とパフォーマンスを監視するために Azure Monitor によって収集されるデータのソースについて説明します。 これらのリソースは、Azure、別のクラウド、またはオンプレミスに存在する可能性があります。  このデータが保存されるしくみと、データを表示する方法の詳細については、[Azure Monitor によって収集されるデータ](data-platform.md)に関する記事をご覧ください。

Azure における監視データはさまざまなソースから取得されます。データは複数の階層に分類できます。最上位の階層はアプリケーションとオペレーティング システム、下位の階層は Azure プラットフォームのコンポーネントです。 データの階層を次の図に示します。各階層については、以降のセクションで詳しく説明します。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

![監視データの階層](media/data-sources/monitoring-tiers.png)

## <a name="azure-tenant"></a>Azure テナント
Azure テナントに関連するテレメトリは、Azure Active Directory などのテナント全体のサービスから収集されます。

![Azure テナントの収集](media/data-sources/tenant-collection.png)

### <a name="azure-active-directory-audit-logs"></a>Azure Active Directory 監査ログ
[Azure Active Directory レポート](../../active-directory/reports-monitoring/overview-reports.md)には、サインイン アクティビティの履歴と、特定のテナント内で行われた変更の監査証跡が含まれています。 これらの監査ログは、他のログ データと共に分析するために、Azure Monitor ログに書き込むことができます。


## <a name="azure-platform"></a>Azure プラットフォーム
Azure 自体の正常性と操作に関連するテレメトリには、Azure サブスクリプションの操作と管理に関するデータが含まれています。 また、Azure アクティビティ ログと Azure Active Directory からの監査ログに格納されているサービスの正常性データが含まれています。

![Azure サブスクリプションの収集](media/data-sources/azure-collection.png)

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](service-notifications.md) は、アプリケーションとリソースが依存するサブスクリプション内の Azure サービスの正常性に関する情報を提供します。 アプリケーションに影響を及ぼす可能性のある現在の問題および予測される重大な問題について通知するアラートを作成できます。 Service Health レコードは、アクティビティ ログ エクスプローラーで表示したり、Azure Monitor ログにコピーしたりできるように、[Azure アクティビティ ログ](activity-logs-overview.md)に格納されます。

### <a name="azure-activity-log"></a>[Azure Activity Log (Azure アクティビティ ログ)]
[Azure アクティビティ ログ](activity-logs-overview.md)には、サービスの正常性レコードと Azure リソースに対して行われた構成の変更に関するレコードが含まれています。 アクティビティ ログは、すべての Azure リソースについて記録されます。このログはリソースの _外部_  ビューを表します。 アクティビティ ログにおける各種のレコードについては、「[Azure アクティビティ ログのイベント スキーマ](activity-log-schema.md)」をご覧ください。

特定のリソースのアクティビティ ログは Azure Portal のそのリソースのページで確認できます。また、[アクティビティ ログ エクスプローラー](activity-logs-overview.md)では、複数のリソースのログを表示できます。 これは、ログ エントリを Azure Monitor にコピーして他の監視データと結合するために特に役立ちます。 [Event Hubs](activity-logs-stream-event-hubs.md) を使用して、ログ エントリを他の場所に送信することもできます。



## <a name="azure-services"></a>Azure サービス
メトリックおよびリソース レベルの診断ログは、Azure リソースの_内部_操作に関する情報を提供します。 これらはほとんどの Azure サービスで利用できます。特定のサービスに関する追加の分析情報は管理ソリューションが提供します。

![Azure リソースの収集](media/data-sources/azure-resource-collection.png)


### <a name="metrics"></a>メトリック
ほとんどの Azure サービスでは、そのパフォーマンスと操作を反映する[プラットフォーム メトリック](data-platform-metrics.md)が生成されます。 [リソースの種類によってメトリックは異なります](metrics-supported.md)。  それらにはメトリック分析からアクセスできます。また、Log Analytics を使用した傾向分析やその他の分析のために、ログにコピーできます。


### <a name="resource-diagnostic-logs"></a>リソース診断ログ
アクティビティ ログは Azure リソースで実行される操作に関する情報を提供しますが、リソース レベルの[診断ログ](diagnostic-logs-overview.md)はリソース自体の操作に関する分析情報を提供します。   これらのログの構成の要件とコンテンツは[リソースの種類によって異なります](diagnostic-logs-schema.md)。

診断ログを Azure portal で直接表示することはできませんが、[診断ログを Azure ストレージに送信してアーカイブ](archive-diagnostic-logs.md)したり、[イベント ハブ](../../event-hubs/event-hubs-about.md)にエクスポートして他のサービスにリダイレクトしたり、[Azure Monitor](diagnostic-logs-stream-log-store.md) にコピーして分析に使用したりできます。 Azure Monitor に直接書き込むことのできるリソースと、ストレージ アカウントに書き込んでから [Log Analytics にインポートする](azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage)リソースがあります。

### <a name="monitoring-solutions"></a>監視ソリューション
 [監視ソリューション](../../azure-monitor/insights/solutions.md)は、データを収集して、特定のサービスやアプリケーションの操作に関する追加の分析情報を提供します。 これらは、データを Azure Monitor ログに収集します。そこで、そのデータを[クエリ言語](../../azure-monitor/log-query/log-query-overview.md)や、通常はソリューションに含まれている[ビュー](view-designer.md)を使用して分析できます。


## <a name="guest-operating-system"></a>ゲスト オペレーティング システム
Azure、他のクラウド、オンプレミスのコンピューティング リソースには、監視対象のゲスト オペレーティング システムがあります。 1 つ以上のエージェントをインストールすると、ゲストから同じ監視ツール (たとえば、Azure サービス自体) にテレメトリを収集できます。

![Azure コンピューティング リソースの収集](media/data-sources/compute-resource-collection.png)

### <a name="azure-diagnostic-extension"></a>Azure 診断の拡張機能
Azure Diagnostics の拡張機能では、Azure コンピューティング リソースのクライアント オペレーティング システムからログとパフォーマンス データを収集することで、基本的なレベルの監視を実現できます。   

### <a name="log-analytics-agent"></a>Log Analytics エージェント
Windows または Linux の仮想マシンまたは物理コンピューターを包括的に監視および管理するには、Log Analytics エージェントを使用します。 仮想マシンは、Azure、別のクラウド、またはオンプレミスで実行できます。エージェントは、直接または System Center Operations Manager 経由で Azure Monitor に接続し、ユーザーが構成済みの[データ ソース](agent-data-sources.md)や、仮想マシン上で実行されているアプリケーションに追加の分析情報を提供する[監視ソリューション](../../azure-monitor/insights/solutions.md)からデータを収集できるようにします。


### <a name="dependency-agent"></a>依存関係エージェント
[Service Map](../insights/service-map.md) と [Azure Monitor for VMs](../../azure-monitor/insights/vminsights-overview.md) には、Windows および Linux 仮想マシン上の Dependency Agent が必要です。 これは Log Analytics エージェントと統合され、仮想マシンで実行されているプロセスおよび外部プロセスの依存関係に関する検出データが収集されます。 これは、このデータを Azure Monitor に格納し、検出された相互接続コンポーネントを視覚化します。  

エージェント間の違いと監視要件に応じて何を使用するかについては、[監視エージェントの概要](agents-overview.md)に関するページを参照してください。

## <a name="applications"></a>[アプリケーション]
アプリケーションがゲスト オペレーティング システムに書き込むことのできるテレメトリに加え、アプリケーションの詳細な監視が [Application Insights](https://docs.microsoft.com/azure/application-insights/) を使用して行われます。 Application Insights では、さまざまなプラットフォームで実行されているアプリケーションからデータを収集できます。 アプリケーションは Azure、別のクラウド、またはオンプレミスで実行できます。

![アプリケーション データの収集](media/data-sources/application-collection.png)


### <a name="application-data"></a>アプリケーション データ
インストルメンテーション パッケージをインストールすることでアプリケーションに対して有効にした Application Insights は、アプリケーションのパフォーマンスおよび操作に関連するメトリックとログを収集します。 これには、ページ ビュー、アプリケーションの要求、および例外に関する詳細情報が含まれます。 Application Insights は、収集したデータを Azure Monitor に格納します。 ここには、このデータを分析するためのさまざまなツールが含まれていますが、メトリック分析やログ分析などのツールを使用して、他のソースからのデータと共に分析することもできます。

Application Insights を使用して[カスタム メトリックを作成する](../../application-insights/app-insights-api-custom-events-metrics.md)こともできます。  これにより、数値を算出して他のメトリックと共に格納するための独自のロジックを定義できます。これにはメトリック分析からアクセスでき、[自動スケーリング](autoscale-custom-metric.md)やメトリック アラートのために使用できます。


### <a name="dependencies"></a>依存関係
アプリケーションのさまざまな論理操作を監視するには、[複数のコンポーネントにわたってテレメトリを収集する](../../application-insights/app-insights-transaction-diagnostics.md)必要があります。 Application Insights では、[分散したテレメトリの関連付け](../../application-insights/application-insights-correlation.md)をサポートします。これにより、コンポーネント間の依存関係を識別し、それらをまとめて分析できます。

### <a name="availability-tests"></a>可用性テスト
Application Insights の[可用性テスト](../../application-insights/app-insights-monitor-web-app-availability.md)では、パブリック インターネット上のさまざまな場所からアプリケーションの可用性と応答性をテストできます。 シンプルな ping テストを実行してアプリケーションが動作していることを確認したり、Visual Studio を使用してユーザー シナリオをシミュレートする Web テストを作成したりできます。  可用性テストには、アプリケーションでのインストルメンテーションは必要ありません。

## <a name="custom-sources"></a>カスタム ソース
アプリケーションの標準的な階層に加え、他のデータ ソースで収集できないテレメトリがある他のリソースを監視することが必要な場合があります。 これらのリソースについては、Azure Monitor API を使用してこのデータを書き込む必要があります。

![カスタム データの収集](media/data-sources/custom-collection.png)

### <a name="data-collector-api"></a>データ コレクター API
Azure Monitor では、[Data Collector API](../../azure-monitor/platform/data-collector-api.md) を使用して任意の REST クライアントからログ データを収集できます。 これにより、カスタム監視シナリオを作成し、他のソースを通じてテレメトリが公開されないリソースも監視対象に含めることができます。

## <a name="next-steps"></a>次の手順

- [Azure Monitor によって収集される監視データの種類](data-platform.md)と、このデータを表示および分析する方法の詳細を確認します。
