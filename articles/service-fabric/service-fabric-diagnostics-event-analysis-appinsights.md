---
title: "Application Insights を使用した Azure Service Fabric イベント分析 | Microsoft Docs"
description: "Azure Service Fabric クラスターの監視と診断に Application Insights を使用したイベントの視覚化と分析について説明します。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/26/2017
ms.author: dekapur
ms.openlocfilehash: 4085a607b800f4f4f155cdc266bc203b0858fd7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Application Insights を使用したイベント分析と視覚化

Azure Application Insights は、アプリケーションの監視と診断のための拡張可能なプラットフォームです。 このプラットフォームには、強力な分析ツールとクエリ ツール、カスタマイズ可能なダッシュボードや視覚化、さらには自動アラート通知を含むオプションが含まれています。 これは、Service Fabric アプリケーションとサービスの監視と診断用に推奨されるプラットフォームです。

## <a name="setting-up-application-insights"></a>Application Insights の設定

### <a name="creating-an-ai-resource"></a>AI リソースの作成

AI リソースを作成するには、Azure Marketplace に行き、"Application Insights" を検索します。 Application Insights は最初のソリューションとして表示されます ("Web + モバイル" カテゴリの下にあります)。 正しいリソース (パスが下の画像と一致することを確認してください) が表示されたら、[**作成**] をクリックします。

![New Application Insights resource](media/service-fabric-diagnostics-event-analysis-appinsights/create-new-ai-resource.png)

リソースを正しくプロビジョニングするには、情報をいくつか記入する必要があります。 "*アプリケーションの種類*" フィールドでは、Service Fabric のプログラミング モデルを使用する場合や、クラスターに .NET アプリケーションを発行する場合は、"ASP.NET Web アプリケーション" を使用します。 ゲスト実行可能ファイルとコンテナーを展開する場合は、"一般" を使用します。 一般的には、既定で "ASP.NET Web アプリケーション" を使用し、将来に備えて選択肢を残しておきます。 名前は好みに応じて付けることができ、リソース グループとサブスクリプションは両方とも、リソースの展開後に変更できます。 AI リソースはクラスターと同じリソース グループに展開することをお勧めします。 詳しくは、「[Application Insights リソースの作成](../application-insights/app-insights-create-new-resource.md)」をご覧ください

イベント集計ツールを使用して AI を構成するには、AI インストルメンテーション キーが必要です。 AI リソースが設定されたら (展開の検証後、数分かかります)、そのリソースに移動し、左側のナビゲーション バーの [**プロパティ**] セクションを見つけます。 新しいブレードが開き、*インストルメンテーション キー*が表示されます。 リソースのサブスクリプションまたはリソース グループを変更する必要がある場合は、ここでも変更できます。

### <a name="configuring-ai-with-wad"></a>WAD を使用した AI の設定

WAD から Azure AI にデータを送信する主な方法は 2 つあります。この方法は、[この記事](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md)で説明しているように、AI シンクを WAD 構成に追加することで実行できます。

#### <a name="add-an-ai-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Azure ポータルでクラスターを作成するときに、AI インストルメンテーション キーを追加する

![AIKey の追加](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

クラスターを作成するときに、診断が "オン" になっている場合、Application Insights のインストルメンテーション キーを入力するオプションのフィールドが表示されます。 ここに AI IKey を貼り付けると、クラスターの展開に使用する Resource Manager テンプレートで AI シンクが自動的に構成されます。

#### <a name="add-the-ai-sink-to-the-resource-manager-template"></a>Resource Manager テンプレートに AI シンクを追加する

Resource Manager テンプレートの "WadCfg" に、次の 2 つの変更を含めることによって "シンク" を追加します。

1. シンク構成を追加します。

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. "WadCfg" の "DiagnosticMonitorConfiguration" に次の行を追加して、DiagnosticMonitorConfiguration にシンクを含めます。

    ```json
    "sinks": "applicationInsights"
    ```

上記の両方のコード スニペットには、シンクを記述するために "applicationInsights" という名前が使用されていました。 これは要件ではなく、シンクの名前が "sinks" に含まれている限り、任意の文字列で名前を設定できます。

現時点では、クラスターのログは AI のログ ビューアーにトレースとして表示されます。 プラットフォームからのトレースのほとんどは "情報" レベルであるため、シンクの構成を変更して "重大" または "エラー" タイプのログのみを送信することも検討できます。 これは、[この記事](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md)で説明するように、シンクに "チャネル" を追加することで実現できます。

>[!NOTE]
>ポータルまたは Resource Manager テンプレートのいずれかで間違った AI IKey を使用した場合、手動でキーを変更してクラスターを更新するか、再デプロイする必要があります。 

### <a name="configuring-ai-with-eventflow"></a>EventFlow を使用した AI の設定

EventFlow を使用してイベントを集計する場合は、必ず `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`NuGet パッケージをインポートしてください。 *eventFlowConfig.json* の *outputs* セクションには、次の内容が含まれている必要があります。

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        // (replace the following value with your AI resource's instrumentation key)
        "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
]
```

フィルターに必要な変更を加えるだけでなく、他の入力を (それぞれの NuGet パッケージとともに) 含めるようにしてください。

## <a name="aisdk"></a>AI.SDK

一般的に EventFlow と WAD は集計ソリューションとして使用することをお勧めします。これは診断と監視を行うモジュール性の高いアプローチが可能になるためです。つまり、EventFlow からの出力を変更する場合、実際のインストルメンテーションを変更する必要はなく、構成ファイルを簡単に変更するだけで済みます。 ただし、Application Insights に投資することとし、別のプラットフォームに変更する可能性が低い場合、AI の新しい SDK を使用してイベントを集計し、AI に送信することを検討した方がよいでしょう。 つまり、データを AI に送信するように EventFlow を設定する必要はなくなり、代わりに ApplicationInsight の Service Fabric NuGet パッケージをインストールすることになります。 パッケージの詳細は、[こちら](https://github.com/Microsoft/ApplicationInsights-ServiceFabric)をご覧ください。

[Application Insights のマイクロサービスとコンテナーのサポート](https://azure.microsoft.com/app-insights-microservices/)により、開発中の新機能 (現在はまだベータ版) が表示され、AI を使用した、より豊かで、細かい設定が不要なモニタリング オプションを利用できます。 これには、依存関係の追跡 (クラスター内すべてのサービスやアプリケーションおよびそれらの間の通信の AppMap 構築に使用されます)、サービスからのトレースの関連付けの向上 (アプリやサービスのワークフローの問題点を特定するのに役立ちます) が含まれます。

.NET で開発中で、Service Fabric のプログラミング モデルの一部を使用する可能性があり、イベントとログ データを視覚化して分析するためのプラットフォームとして AI を使用する場合は、AI SDK ルートをたどる監視および診断ワークフローを作成することをお勧めします。 AI を使用したログの収集と表示を開始するには、[こちら](../application-insights/app-insights-asp-net-more.md)と[こちら](../application-insights/app-insights-asp-net-trace-logs.md)をご覧ください。

## <a name="navigating-the-ai-resource-in-azure-portal"></a>Azure ポータルで AI リソースをナビゲートする

イベントとログの出力として AI を構成すると、数分後に AI リソースに情報が表示されます。 AI リソースに移動すると、AI リソース ダッシュボードに移動します。 AI タスク バーの [**検索**] をクリックすると、受信した最新のトレースが表示され、それらのトレースをフィルター処理できます。

*メトリックス エクスプローラー*は、アプリケーションやサービス、クラスターが報告するメトリックに基づいたカスタム ダッシュボードを作成する便利なツールです。 収集するデータに基づいたグラフを自分でいくつか設定するには、「[Application Insights を使用したメトリックの探索](../application-insights/app-insights-metrics-explorer.md)」を参照してください。

[**Analytics**] をクリックすると、Application Insights Analytics ポータルに移動します。このポータルでは、より多くのオプションを使用し、より広い範囲でイベントとトレースを照会できます。 この詳細については、「[Application Insights の Analytics](../application-insights/app-insights-analytics.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

* [AI のアラートを設定して](../application-insights/app-insights-alerts.md)、パフォーマンスまたは使用状況の変化について通知を受けます
* [Application Insights のスマート検出](../application-insights/app-insights-proactive-diagnostics.md)は、 AI に送信されるテレメトリのプロアクティブ分析を実行し、潜在的なパフォーマンスの問題を警告します