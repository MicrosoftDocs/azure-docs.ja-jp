---
title: Azure Monitor で Azure リソースを管理する | Microsoft Docs
description: Azure Monitor を使用し、Azure のリソースから監視データを収集して分析する方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: af2803a945821a23ff59f196967d792a6593009f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033386"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Azure Monitor を使用した Azure リソースの監視
Azure リソースに依存するクリティカルなアプリケーションとビジネス プロセスがある場合は、それらのリソースの可用性、パフォーマンス、操作を監視する必要があります。 この記事では、Azure リソースによって生成される監視データと、Azure Monitor の機能を使用してこのデータについての分析とアラートを行う方法について説明します。

> [!IMPORTANT]
> この記事は、Azure Monitor を使用する Azure のすべてのサービスに適用されます。 VM や App Service などのコンピューティング リソースでは、ここで説明するのと同じ監視データが生成されますが、ログとメトリックを生成する場合があるゲスト オペレーティング システムもあります。 このデータを収集して分析する方法の詳細については、これらのサービスの監視に関するドキュメントを参照してください。

## <a name="what-is-azure-monitor"></a>Azure Monitor とは
Azure Monitor は Azure のフルスタックの監視サービスであり、他のクラウドやオンプレミスのリソースに加えて、Azure リソースを監視するための完全な機能セットが提供されます。 [Azure Monitor データ プラットフォーム](../data-platform.md)では[ログ](../logs/data-platform-logs.md)と[メトリック](../essentials/data-platform-metrics.md)にデータが収集され、完備された監視ツール セットを使用して、それらをまとめて分析できます。 Azure Monitor によって監視できるアプリケーションとサービスの完全な一覧については、「[Azure Monitor によって監視される内容](../monitor-reference.md)」を参照してください。

Azure リソースを作成するとすぐに、Azure Monitor が有効にされ、[Azure portal で表示して分析する](#monitoring-in-the-azure-portal)ことができるメトリックとアクティビティ ログの収集が開始されます。 一部の構成では、追加の監視データを収集し、追加の機能を有効にすることができます。 構成要件の詳細については、後の「[データの監視](#monitoring-data)」を参照してください。


## <a name="costs-associated-with-monitoring"></a>監視に関連するコスト
既定で収集される監視データの分析にはコストはかかりません。 これには、次の内容が含まれます。

- メトリックス エクスプローラーによるプラットフォーム メトリックの収集と分析。
- Azure portal でのアクティビティ ログの収集と分析。
- アクティビティ ログ警告ルールの作成。

ログとメトリックの収集とエクスポートに対する Azure Monitor のコストはありませんが、それを使う先では関連するコストが発生する可能性があります。

- Log Analytics ワークスペースでログとメトリックを収集するときの、データ インジェストと保持に関連するコスト。 [Log Analytics に関する Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)に関する記事を参照してください。
- Azure ストレージ アカウントにログとメトリックを収集するときの、データ ストレージに関連するコスト。 [Blob Storage に関する Azure Storage の価格](https://azure.microsoft.com/pricing/details/storage/blobs/)に関する記事を参照してください。
- ログとメトリックを Azure Event Hubs に転送するときの、イベント ハブ ストリーミングに関連するコスト。 「[Event Hubs の価格](https://azure.microsoft.com/pricing/details/event-hubs/)」を参照してください。

以下に関連して Azure Monitor のコストが発生する場合があります。 「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」を参照してください。

- ログ クエリの実行。
- メトリックまたはログ クエリの警告ルールの作成。
- 警告ルールからの通知の送信。
- API によるメトリックへのアクセス。

## <a name="monitoring-data"></a>データの監視
Azure のリソースによって、次の図に示すような[ログ](../logs/data-platform-logs.md)と[メトリック](../essentials/data-platform-metrics.md)が生成されます。 各 Azure サービスによって生成される特定のデータおよび提供されるその他のソリューションや分析情報については、各サービスのドキュメントを参照してください。

![概要](media/monitor-azure-resource/logs-metrics.png)



- [プラットフォーム メトリック](../essentials/data-platform-metrics.md) - 一定の間隔で自動的に収集される、特定の時刻におけるリソースのいくつかの側面が記述されている数値。 
- [リソース ログ](./platform-logs-overview.md) - Azure リソース (データ プレーン) 内で実行された操作に関する分析情報が提供されます。たとえば、キー コンテナーからのシークレットの取得や、データベースに対する要求などです。 リソース ログの内容と構造は、Azure サービスとリソースの種類によって異なります。
- [アクティビティ ログ](./platform-logs-overview.md) - サブスクリプションの各 Azure リソースに対する外部 (管理プレーン) からの操作についての分析情報が提供されます。新しいリソースの作成や、仮想マシンの起動などです。 これは、サブスクリプションのリソースに対して行われたすべての書き込み操作 (PUT、POST、DELETE) についての、"何"、"誰"、"いつ" に関する情報です。


## <a name="configuration-requirements"></a>構成要件

### <a name="configure-monitoring"></a>監視の構成
一部の監視データは自動的に収集されますが、要件に応じた何らかの構成の実行が必要になる場合があります。 監視データの各種類についての具体的な情報は、以下を参照してください。

- [プラットフォーム メトリック](../essentials/data-platform-metrics.md) - プラットフォーム メトリックは、[ Azure Monitor のメトリック](../essentials/data-platform-metrics.md)に自動的に収集され、構成する必要はありません。 エントリを Azure Monitor ログに送信したり、Azure の外部に転送したりするための、診断設定を作成します。
- [リソース ログ](./platform-logs-overview.md) - リソース ログは、Azure リソースによって自動的に生成されますが、診断設定がないと収集されません。  エントリを Azure Monitor ログに送信したり、Azure の外部に転送したりするための、診断設定を作成します。
- [アクティビティ ログ](./platform-logs-overview.md) - アクティビティ ログは、構成を必要とせずに自動的に収集され、Azure portal で表示できます。 Azure Monitor ログにコピーしたり、Azure の外部に転送したりするための、診断設定を作成します。

### <a name="log-analytics-workspace"></a>Log Analytics ワークスペース
Azure Monitor ログにデータを収集するには、Log Analytics ワークスペースが必要です。 新しいワークスペースを作成してサービスの監視をすぐに始められますが、他のサービスからデータを収集しているワークスペースを使用するとよい場合があります。 ワークスペースの作成の詳細については、「[Azure portal で Log Analytics ワークスペースを作成する](../logs/quick-create-workspace.md)」を参照してください。要件に最適なワークスペースの設計の決定については、「[Azure Monitor ログのデプロイの設計](../logs/design-logs-deployment.md)」を参照してください。 組織内の既存のワークスペースを使用する場合は、「[Azure Monitor でログ データとワークスペースへのアクセスを管理する](../logs/manage-access.md)」で説明されているように、適切なアクセス許可が必要です。 





## <a name="diagnostic-settings"></a>診断設定
診断設定では、特定のリソースのリソース ログとメトリックを送信する場所を定義します。 使用できる送信先は次のとおりです。

- [Log Analytics ワークスペース](./resource-logs.md#send-to-log-analytics-workspace) - 強力なログ クエリを使用して、Azure Monitor で収集された他の監視データと組み合わせてデータを分析でき、ログ アラートや視覚化などの Azure Monitor の他の機能を利用することもできます。 
- [イベント ハブ](./resource-logs.md#send-to-azure-event-hubs) - サードパーティの SIEM や他のログ分析ソリューションなどの外部システムにデータをストリーミングできます。 
- [Azure ストレージ アカウント](./resource-logs.md#send-to-azure-storage)- 監査、スタティック分析、またはバックアップに役立ちます。

「[Azure でプラットフォーム ログとメトリックを収集するための診断設定を作成する](../essentials/diagnostic-settings.md)」の手順に従って、Azure portal で診断設定を作成および管理します。 テンプレートで診断設定を定義し、作成時にリソースの完全な監視を有効にするには、「[Azure で Resource Manager テンプレートを使用して診断設定を作成する](./resource-manager-diagnostic-settings.md)」を参照してください。


## <a name="monitoring-in-the-azure-portal"></a>Azure Portal の監視機能
 ほとんどの Azure リソースの監視データには、Azure portal のリソースのメニューからアクセスできます。 これにより、標準の Azure Monitor ツールを使用して、1 つのリソースのデータにアクセスできるようになります。 一部の Azure サービスでは異なるオプションが提供されるため、そのサービスのドキュメントで追加情報を参照する必要があります。 すべての監視対象リソースのデータを分析するには、 **[Azure Monitor]** メニューを使用します。 

### <a name="overview"></a>概要
多くのサービスでは、その操作をひとめで確認できるように、 **[概要]** ページに監視データが含まれています。 通常、これは Azure Monitor メトリックに格納されているプラットフォーム メトリックのサブセットに基づいています。 他の監視オプションは、通常、サービスのメニューの **[監視]** セクションで使用できます。

![[概要] ページ](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>分析情報とソリューション 
一部のサービスでは、Azure Monitor の標準機能を超えるツールが提供されています。 [分析情報](../monitor-reference.md)では、Azure Monitor データ プラットフォームと標準機能に基づいてカスタマイズされた監視エクスペリエンスが提供されます。 [ソリューション](../insights/solutions.md)では、Azure Monitor ログに基づいて構築された定義済みの監視ロジックが提供されます。 

サービスに Azure Monitor の分析情報がある場合は、各リソースのメニューの **[監視]** からアクセスできます。 すべての分析情報とソリューションには、**Azure Monitor** のメニューからアクセスします。

![Azure portal での分析情報](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>メトリック
Azure portal でメトリックを分析するには、ほとんどのサービスの **[メトリック]** メニュー項目から使用できる [メトリックスエクス プローラー](./metrics-getting-started.md)を使用します。 このツールを使用すると、個々のメトリックまたは複数の組み合わせを使用して、相関関係や傾向を特定することができます。 

- メトリックス エクスプローラーの使用について詳しくは、「[Azure メトリックス エクスプローラーの概要](./metrics-getting-started.md)」を参照してください。
- 複数のメトリックの使用や、フィルターや分割の適用など、メトリックス エクスプローラーの高度な機能については、「[Azure メトリックス エクスプローラーの高度な機能](../essentials/metrics-charts.md)」を参照してください。

![Azure portal でのメトリック エクスプローラー](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>アクティビティ ログ 
Azure portal でアクティビティ ログのエントリを表示します。初期フィルターは現在のリソースに設定されています。 アクティビティ ログにアクセスしてログ クエリやブックで使用するには、アクティビティ ログを Log Analytics ワークスペースにコピーします。 

- さまざまな方法を使用したアクティビティ ログの表示とエントリの取得について詳しくは、「[Azure アクティビティ ログ イベントを表示して取得する](../essentials/activity-log.md#view-the-activity-log)」を参照してください。
- ログに記録される特定のイベントについては、お使いの Azure サービスのドキュメントを参照してください。

![アクティビティ ログ](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Azure Monitor ログ
Azure Monitor ログを使用すると、複数のサービスや他のデータ ソースからのログとメトリックが統合され、強力なクエリ ツールで分析することができます。 前に説明したように、Azure Monitor の Log Analytics ワークスペースにプラットフォーム メトリック、アクティビティ ログ、リソース ログを収集するには、診断設定を作成します。

[Log Analytics](../logs/log-analytics-tutorial.md) では[ログ クエリ](../logs/log-query-overview.md)を使用できます。これは、すべての機能を備えたクエリ言語を使用してログ データの高度な分析を実行できる、Azure Monitor の強力な機能です。 [クエリ スコープ](../logs/scope.md#query-scope)としてリソースを使用してログ クエリを操作するには、Azure リソースの **[監視]** メニューの **[ログ]** から Log Analytics を開きます。 これにより、そのリソースだけについて複数のテーブルのデータを分析できます。 すべてのリソースのログにアクセスするには、Azure Monitor のメニューから **[ログ]** を使用します。 

- ログ クエリの記述に使用されるクエリ言語の使用に関するチュートリアルについては、「[Azure Monitor でログ クエリの使用を開始する](../logs/get-started-queries.md)」を参照してください。
- Azure Monitor ログでのリソース ログの収集方法と、クエリでそれらにアクセスする方法の詳細については、「[Azure Monitor の Log Analytics ワークスペースで Azure リソース ログを収集する](./resource-logs.md#send-to-log-analytics-workspace)」を参照してください。
- Azure Monitor ログでのリソース ログ データの構造については、「[収集モード](./resource-logs.md#send-to-log-analytics-workspace)」を参照してください。
- Azure Monitor ログでのそのテーブルの詳細については、各 Azure サービスのドキュメントを参照してください。

![Azure Portal の Log Analytics](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>コマンド ラインからの監視
リソースから収集された監視データは、コマンド ラインからアクセスしたり、[Azure PowerShell](/powershell/azure/) または [Azure コマンド ライン インターフェイス](/cli/azure/)を使用してスクリプトに含めたりすることができます。 

- CLI からのメトリック データへのアクセスについては、[CLI メトリック リファレンス](/cli/azure/monitor/metrics)のページを参照してください。
- CLI からログ クエリを使用して Azure Monitor ログのデータにアクセスするには、[CLI の Log Analytics リファレンス](/cli/azure/ext/log-analytics/monitor/log-analytics)のページを参照してください。
- Azure PowerShell からのメトリック データへのアクセスについては、[Azure PowerShell メトリック リファレンス](/powershell/module/azurerm.insights/get-azurermmetric)のページを参照してください。
- Azure PowerShell からログ クエリを使用して Azure Monitor ログ データにアクセスする場合は、[Azure PowerShell ログ クエリ リファレンス](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery)のページを参照してください。

## <a name="monitoring-from-rest-api"></a>REST API からの監視
REST API を使用して、リソースから収集された監視データをカスタム アプリケーションに組み込みます。

- Azure Monitor REST API からのメトリックへのアクセスの詳細については、「[Azure 監視 REST API のチュートリアル](./rest-api-walkthrough.md)」を参照してください。
- Azure PowerShell からログ クエリを使用して Azure Monitor のログ データにアクセスする方法については、「[Azure Log Analytics REST API](https://dev.loganalytics.io/)」を参照してください。

## <a name="alerts"></a>警告
[アラート](../alerts/alerts-overview.md)では、監視データで重要な状態が見つかったきに、事前に通知され、場合によっては対処されます。 アラートのターゲット、アラートを作成する条件、応答で実行するアクションを定義する、警告ルールを作成します。

警告ルールの種類に応じて、異なる種類の監視データが使用されます。

- [アクティビティ ログ アラート](../alerts/alerts-activity-log.md) - 特定の条件に一致するエントリがアクティビティ ログに作成されたら、アラートを作成します。 これにより、たとえば特定の種類のリソースが作成されたときや、構成の変更が失敗したときなどに、通知を受け取ることができます。
- [メトリック アラート](../alerts/alerts-metric.md) - メトリックの値が特定のしきい値を超えたら、アラートを作成します。 メトリック アラートは他のアラートより応答性が高く、問題が修正されたら自動的に解決できます。
- [ログ クエリ アラート](../alerts/alerts-log.md) - 一定の間隔でログ クエリを実行し、特定の条件が検出されたらアラートを作成します。 これにより、複数のデータ セットの間で複雑な分析を実行できます。

そのリソースのアラートを表示し、警告ルールを管理するには、リソースのメニューの **[アラート]** を使用します。 個々の Azure リソースがターゲットとして使用されるのは、アクティビティ ログ アラートとメトリック アラートのみです。 ログ クエリ アラートでは、Log Analytics ワークスペースがターゲットとして使用され、そのワークスペースに格納されているすべてのログにアクセスできるクエリが基になります。 すべてのリソースのアラートを表示および管理し、ログ クエリ警告ルールを管理するには、Azure Monitor のメニューを使用します。

- 警告ルールの作成に関する詳細については、上記のさまざまな種類のアラートに関する記事を参照してください。
- アラートへの応答を管理できるアクション グループの作成の詳細については、「[Azure portal でのアクション グループの作成および管理](../alerts/action-groups.md)」を参照してください。



## <a name="next-steps"></a>次のステップ

* さまざまな Azure サービスのリソース ログの詳細については、「[Azure リソース ログでサポートされているサービス、スキーマ、カテゴリ](./resource-logs-schema.md)」を参照してください。