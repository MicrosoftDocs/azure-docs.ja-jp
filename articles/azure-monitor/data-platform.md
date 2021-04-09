---
title: Azure Monitor データ プラットフォーム | Microsoft Docs
description: Azure Monitor によって収集される監視データは、軽量でほぼリアルタイムのシナリオをサポートできるメトリックと、高度な分析に使用されるログに分けられます。
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 7356b9bb814f8bca5465fe74d48409b9dbca6d3b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731699"
---
# <a name="azure-monitor-data-platform"></a>Azure Monitor データ プラットフォーム

クラウドとオンプレミスのサービスの両方に依存して分散アプリケーションが実行される今日の複雑なコンピューティング環境で監視を有効にするには、分散システムのすべての層とすべてのコンポーネントから運用データを収集する必要があります。 このデータに対して詳細な分析を実行し、組織の数多くの利害関係者をサポートするためにさまざまな観点から見ることができる単一ウィンドウにそれを統合できる必要があります。

[Azure Monitor](overview.md) では、分析、視覚化、およびアラートを行うために使用できる共通データ プラットフォームにさまざまなソースからデータが収集されて集計されます。 これにより、複数のソースのデータに対する一貫性のあるエクスペリエンスが提供され、監視対象のすべてのリソースに加え、Azure Monitor にデータを格納する他のサービスのデータに対しても深い分析を行うことができます。


![Azure Monitor の概要](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Azure Monitor でのデータの可観測性
メトリック、ログ、および分散トレースは、一般に可観測性の 3 つの柱と呼ばれています。 これらは、監視対象のシステムに十分な可観測性を提供するために監視ツールで収集して分析する必要がある異なる種類のデータです。 可観測性は、複数の柱のデータを相互に関連付けることと、監視対象のリソース全体のデータを集計することによって実現できます。 Azure Monitor には複数のソースからのデータがまとめて格納されるため、一般的なツールを使用してデータを相互に関連付けて分析できます。 また、他のサービスのデータに対するホスティングに加え、複数の Azure サブスクリプションとテナント間のデータの関連付けも行われます。

Azure リソースによって、大量の監視データが生成されます。 Azure Monitor では、このデータと他のソースからの監視データが、メトリックまたはログ プラットフォームのいずれかに統合されます。 それぞれが特定の監視シナリオ向けに最適化され、それぞれが Azure Monitor の異なる機能をサポートしています。 データ分析、視覚化、アラートなどの機能では、必要なシナリオを最も効率的かつコスト効率の高い方法で実装できるように、それらの違いを理解しておく必要があります。 [Application Insights](app/app-insights-overview.md) や [VM insights](vm/vminsights-overview.md) などの Azure Monitor での分析には、この 2 種類のデータの違いを理解しなくても特定の監視シナリオに対応できる分析ツールがあります。 


### <a name="metrics"></a>メトリック
[メトリック](essentials/data-platform-metrics.md)は、特定の時点におけるシステムの何らかの側面を表す数値です。 それらは一定の間隔で収集され、タイムスタンプ、名前、値、および 1 つまたは複数の定義ラベルで識別されます。 メトリックは、さまざまなアルゴリズムを使用して集計したり、他のメトリックと比較したり、時間の経過による傾向を分析したりできます。 

Azure Monitor のメトリックは、タイムスタンプ付きのデータを分析するために最適化された時系列データベースに格納されます。 これにより、メトリックは、特にアラートと問題の迅速な検出に適しています。 それらによって、システムがどのように実行されているかを知ることができますが、通常は、問題の根本原因を識別するためにログと組み合わせる必要があります。

メトリックは、Azure portal の [Azure メトリックス エクスプローラー](essentials/metrics-getting-started.md)で、対話式の分析を行うために使用できます。 他のデータと組み合わせた視覚化や、リアルタイムの[アラート](alerts/alerts-metric.md)で使用するために、[Azure ダッシュボード](app/tutorial-app-dashboards.md)に追加できます。

データのソースを含む Azure Monitor のメトリックの詳細については、「[Metrics in Azure Monitor (Azure Monitor のメトリック)](essentials/data-platform-metrics.md)」を参照してください。

### <a name="logs"></a>ログ
[ログ](logs/data-platform-logs.md)は、システム内で発生したイベントです。 異なる種類のデータが含まれる可能性があり、構造化されている場合も、タイムスタンプ付きの自由形式のテキストである場合もあります。 それらは、環境内でログ エントリを生成するイベントとして散発的に作成される可能性があり、通常は、大きな負荷がかかっているシステムでは大量のログが生成されます。

Azure Monitor のログは、強力な分析エンジンと[豊富なクエリ言語](/azure/kusto/query/)を備えた [Azure Data Explorer](/azure/data-explorer/) のベースになる Log Analytics ワークスペースに格納されます。 通常、ログには、識別された問題の完全なコンテキストを示す十分な情報が提供され、問題の根本原因を識別するのに役立ちます。

> [!NOTE]
> Azure Monitor のログと Azure 上のログ データのソースを区別することが重要です。 たとえば、Azure のサブスクリプション レベルのイベントは、Azure Monitor のメニューから表示できる[アクティビティ ログ](essentials/platform-logs-overview.md)に書き込まれます。 ほとんどのリソースでは、別の場所に転送できる[リソース ログ](essentials/platform-logs-overview.md)に運用情報が書き込まれます。 Azure Monitor Logs は、アクティビティ データとリソース ログと共に他の監視データを収集して、リソース全体を深く分析するためのログ データ プラットフォームです。


 Azure portal の [Log Analytics](logs/log-query-overview.md)で [ログ クエリ](logs/log-query-overview.md)を対話式で操作したり、結果を [Azure ダッシュボード](app/tutorial-app-dashboards.md)に追加して、他のデータと組み合わせて視覚化したりできます。 スケジュール クエリの結果に基づいてアラートをトリガーする[ログ アラート](alerts/alerts-log.md)を作成することもできます。

データのソースを含む Azure Monitor のログの詳細については、「[Logs in Azure Monitor (Azure Monitor のログ)](logs/data-platform-logs.md)」を参照してください。

### <a name="distributed-traces"></a>分散トレース
トレースは、分散システムを通過するユーザー要求を追いかける、関連性のある一連のイベントです。 これらを使用して、アプリケーション コードの動作と複数のトランザクションのパフォーマンスを判断できます。 ログは、多くの場合、分散システムの個々のコンポーネントによって作成されますが、トレースでは、コンポーネント セット全体の間のアプリケーションの操作とパフォーマンスが測定されます。

Azure Monitor の分散トレースは、[Application Insights SDK](app/distributed-tracing.md) で有効にすることができ、トレース データは、Application Insights によって収集された他のアプリケーション ログ データとともに格納されます。 これにより、ログ クエリ、ダッシュボード、およびアラートを含む他のログ データと同じ分析ツールを使用できます。

分散トレーシングの詳細については、「[分散トレースとは](app/distributed-tracing.md)」を参照してください。


## <a name="compare-azure-monitor-metrics-and-logs"></a>Azure Monitor のメトリックとログを比較する

次の表は、Azure Monitor のメトリックとログを比較しています。

| 属性  | メトリック | ログ |
|:---|:---|:---|
| メリット | 軽量であり、アラートなどのリアルタイムに近いシナリオが可能です。 問題の迅速な検出に最適です。 | 豊富な分析クエリ言語を使用して分析されます。 詳細な分析と根本原因の識別に最適です。 |
| Data | 数値のみ | テキストまたは数値データ |
| 構造体 | サンプリング時間、監視対象のリソース、数値を含むプロパティの標準セット。 一部のメトリックには、定義を深めるための複数のディメンションが含まれます。 | ログの種類に応じたプロパティの一意のセット。 |
| コレクション | 一定の間隔で収集されます。 | イベントによってレコードの作成がトリガされるときに、散発的に収集される可能性があります。 |
| Azure portal に表示 | メトリックス エクスプローラー | Log Analytics |
| 含まれるデータ ソース | Azure リソースから収集されたプラットフォームのメトリック。<br>Application Insights によって監視されるアプリケーション。<br>アプリケーションまたは API によってカスタム に定義される。 | アプリケーションとリソースのログ。<br>監視ソリューション。<br>エージェントと VM 拡張機能。<br>アプリケーションの要求と例外。<br>Azure Security Center。<br>Data Collector API。 |

## <a name="collect-monitoring-data"></a>Azure で監視データを収集する
さまざまな [sources of data for Azure Monitor のデータ ソース](agents/data-sources.md)から、Log Analytics ワークスペース (ログ) または Azure Monitor メトリック データベース (メトリック)、あるいはその両方に書き込みが行われます。 これらのデータ ストアに直接書き込みするデータ ソースも、Azure ストレージなどの別の場所に書き込むためにログまたはメトリックを設定する構成が必要なデータ ソースもあります。 

それぞれの種類を設定するさまざまなデータソースの一覧については、「[Metrics in Azure Monitor (Azure Monitor のメトリック)](essentials/data-platform-metrics.md) 」と「[Logs in Azure Monitor (Azure Monitor のログ)](logs/data-platform-logs.md)」を参照してください。


## <a name="stream-data-to-external-systems"></a>外部システムにデータをストリーム配信する
Azure のツールを使用した監視データの分析に加え、監視データをセキュリティ情報およびイベントの管理 (SIEM) 製品などの外部ツールに転送する要件がある場合もあります。 この転送は一般に、[Azure Event Hubs](../event-hubs/index.yml) を介して、監視対象リソースから直接行われます。 一部のソースはデータをイベント ハブに直接送信するように構成でき、Logic App などの別のプロセスを使用して必要なデータを取得できます。 詳細については、「[外部ツールで使用する Azure 監視データのイベント ハブへのストリーミング](essentials/stream-monitoring-data-event-hubs.md)」を参照してください。



## <a name="next-steps"></a>次のステップ

- [Azure Monitor のメトリック](essentials/data-platform-metrics.md)を確認します。
- [Azure Monitor のログ](logs/data-platform-logs.md)を確認します。
- Azure のさまざまなリソースで[入手できる監視データ](agents/data-sources.md)を確認します。

