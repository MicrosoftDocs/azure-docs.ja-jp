---
title: Azure Monitor の用語の更新 | Microsoft Docs
description: Azure 監視サービスに対して行われた最近の用語の変更について説明します。
author: bwren
manager: carmonm
editor: tysonn
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2019
ms.author: bwren
ms.openlocfilehash: 8f645f7d569546a8362d0149806a2b4636567fd0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59789993"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Azure Monitor の名称と用語の変更
最近、Azure Monitor に大きな変更が加えられ、Azure のお客様が監視を簡単に行えるようにするためにさまざまなサービスが統合されました。 この記事では、Azure Monitor のドキュメント内で最近行われた名称と用語の変更について説明します。

## <a name="february-2019---log-analytics-terminology"></a>2019 年 2 月 - Log Analytics の用語
Azure Monitor にさまざまなサービスを統合した後、次の段階として、Azure Monitor サービスとそのさまざまなコンポーネントをよりわかりやすく説明するためにドキュメント内の用語を変更しています。 

### <a name="log-analytics"></a>Log Analytics
Azure Monitor ログ データは引き続き Log Analytics ワークスペースに格納され、同じ Log Analytics サービスによって収集および分析されますが、_Log Analytics_ という用語は多くの場所で _Azure Monitor ログ_ に変更されています。 この用語は、Azure Monitor におけるその役割をより適切に反映するものであり、[Azure Monitor のメトリック](platform/data-platform-metrics.md)の一貫性を向上させます。

現在、_ログ分析_ という用語は、クエリの記述と実行およびログ データの分析に使用する Azure portal 内のページに主に適用されています。 これは、メトリック データの分析に使用する Azure portal 内のページである[メトリックス エクスプローラー](platform/metrics-charts.md)と同等の機能です。

### <a name="log-analytics-workspaces"></a>Log Analytics ワークスペース
Azure Monitor 内のログ データを保持する[ワークスペース](platform/manage-access.md)は引き続き Log Analytics ワークスペースと呼ばれます。 Azure portal 内の **[Log Analytics]** メニューの名称は **[Log Analytics ワークスペース]** に変更されました。ここでは、[新しいワークスペースを作成](learn/quick-create-workspace.md)し、データ ソースを構成します。 **Azure Monitor** ではログおよびその他の監視データを分析し、**[Log Analytics ワークスペース]** ではワークスペースを構成します。

### <a name="management-solutions"></a>管理ソリューション
[管理ソリューション](insights/solutions.md)の名称は、その機能をよりわかりやすく示すために _監視ソリューション_ に変更されました。


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>2018 年 8 月 - 監視サービスを Azure Monitor に統合
Log Analytics と Application Insights は、Azure リソースとハイブリッド環境を監視する単一の統合エクスペリエンスを提供するために Azure Monitor に統合されました。 これらのサービスから削除された機能はありません。機能を失ったり妥協したりすることなく、これまでと同じシナリオを実行できます。

これらの各サービスのドキュメントは、Azure Monitor の単一のコンテンツ セットに統合されています。 そのため、特定の監視シナリオに関するすべてのコンテンツを 1 つの場所で見つけることができます。複数のコンテンツ セットを参照する必要はありません。 統合サービスの進化に伴って、今後もコンテンツの統合を進める予定です。

エージェントやビューなど、Log Analytics の一部と見なされていた他の機能も、Azure Monitor の機能に変更されました。 機能は、Azure portal のエクスペリエンスに加えられる機能の改善以外に変更点はありません。


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>2018 年 4 月 - Operations Management Suite ブランドの廃止
Operations Management Suite (OMS) は、ライセンス目的で以下の Azure 管理サービスをバンドルしたものです。

- Application Insights
- Azure Automation
- Azure Backup
- Log Analytics
- Site Recovery

[これらのサービスに新しい価格設定が導入されました](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)。新規のお客様は OMS バンドルを入手できなくなりました。 前述の Azure Monitor への統合を除いて、OMS の一部だったサービスは変更されていません。 




## <a name="next-steps"></a>次の手順

- さまざまなコンポーネントと機能については、[Azure Monitor の 概要](overview.md)に関するページを参照してください。
- [OMS ポータルの移行](../log-analytics/log-analytics-oms-portal-transition.md)に関するページを参照してください。