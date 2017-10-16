---
title: "Azure Service Fabric の監視と診断の概要 | Microsoft Docs"
description: "Azure Service Fabric のクラスター、アプリケーション、およびサービスの監視と診断について説明します。"
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
ms.date: 07/17/2017
ms.author: dekapur
ms.openlocfilehash: 88f4a23f89a1c8fd88db1df3a7ff03ae5df64c0f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Azure Service Fabric での監視と診断

監視と診断は、あらゆる環境でアプリケーションやサービスを開発、テスト、およびデプロイするために非常に重要です。 アプリケーションやサービスがローカル開発環境や運用環境で想定どおり機能するように監視と診断を計画し、実装する場合に、Service Fabric ソリューションは最も効果的に機能します。

監視と診断の主な目標は次のとおりです。
* ハードウェアとインフラストラクチャの問題の検出と診断
* ソフトウェアおよびアプリケーションの問題の検出と、サービス ダウンタイムの削減
* リソースの消費量の把握とドライブ操作の決定の支援
* アプリケーション、サービス、およびインフラストラクチャのパフォーマンスの最適化
* ビジネス上の洞察の創出と改善する領域の特定


監視と診断の全体的なワークフローは、次の 3 つの手順で構成されます。

1. **イベントの生成**: これには、インフラストラクチャ (クラスター)、プラットフォーム、およびアプリケーション/サービス レベルでのイベント (ログ、トレース、カスタム イベント) が含まれます。
2. **イベントの集計**: 生成されたイベントを表示するには、生成されたイベントを収集して集計する必要があります。
3. **分析**: イベントは、必要に応じて分析して表示できるように、視覚化して、特定の形式でアクセスできるようにする必要があります。

この 3 つの領域すべてに対応する多数の製品があり、それぞれで異なるテクノロジを自由に選択できます。 各製品を連携させて、クラスターのエンド ツー エンドの監視ソリューションを実現できるようにすることが重要です。

## <a name="event-generation"></a>イベントの生成

監視と診断のワークフローの最初の手順は、イベントおよびログの作成と生成です。 これらのイベント、ログ、およびトレースは、プラットフォーム レイヤー (クラスター、コンピューター、または Service Fabric アクションを含む) またはアプリケーション レイヤー (クラスターにデプロイされたアプリとサービスに追加されるインストルメンテーション) という 2 つのレベルで生成されます。 これらの各レベルのイベントはカスタマイズ可能ですが、Service Fabric は既定でインストルメンテーションを提供します。

提供される内容とインストルメンテーションの追加方法については、[プラットフォーム レベルのイベント](service-fabric-diagnostics-event-generation-infra.md)と[アプリケーション レベルのイベント](service-fabric-diagnostics-event-generation-app.md)に関するページを参照してください。

利用するログ記録プロバイダーを決定したら、ログが適切に集計され、保存されていることを確認する必要があります。

## <a name="event-aggregation"></a>イベントの集計

アプリケーションやクラスターによって生成されるログとイベントの収集では通常、[Azure 診断](service-fabric-diagnostics-event-aggregation-wad.md) (エージェント ベースのログの収集に近い) または [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) (プロセス内ログ収集) を使用することをお勧めしています。

Azure 診断拡張機能を使用したアプリケーション ログの収集は、ログの送信元と送信先のセットがあまり変わらず、送信元と送信先の間に単純なマッピングがある場合に、Service Fabric サービスに適したオプションです。 その理由は、Azure 診断の構成はリソース マネージャー レイヤーで行われるため、構成に大幅な変更を加えるにはクラスターを更新または再デプロイすることが必要となるためです。 さらに、ログをもう少し永続的な場所に保存し、さまざまな分析プラットフォームからアクセスできるようにするのにも最適です。 EventFlow のようなオプションを使用してアクセスするよりも、パイプラインの効率は下がることになります。

[EventFlow](https://github.com/Azure/diagnostics-eventflow) を使用すると、サービスから直接、分析および視覚化プラットフォーム、またはストレージにログを送信することができます。 ほかのライブラリ (ILogger や Serilog など) を同じ目的に使うこともできますが、EventFlow はプロセス内ログ収集専用に設計されており、Service Fabric サービスをサポートするという利点があります。 これにより、いくつかの潜在的な利点が得られます。

* 構成とデプロイが容易
    * 診断データ収集の構成は、サービス構成のごく一部に過ぎません。 ほかのアプリケーションと容易に常時「同期を保つ」ことができます
    * アプリケーションごと、またはサービスごとの構成が簡単に行えます
    * EventFlow を使用したデータ送信先の構成は、適切な NuGet パッケージを追加し、*eventFlowConfig.json* ファイルを変更するだけで実行できます
* 柔軟性
    * 対象データ ストレージ システムをサポートするクライアント ライブラリがある場合、必要なときにいつでもアプリケーションからデータを送信できます。 必要に応じて、新しい送信先を追加できます
    * 複雑なキャプチャ、フィルター処理、データ集計ルールを実装できます
* 内部アプリケーション データおよびコンテキストへのアクセス
    * アプリケーション/サービス プロセス内で実行される診断サブシステムは、コンテキスト情報を使用してトレースを簡単に強化できます

注意すべき点は、これら 2 つのオプションは相互に排他的でないということです。どちらか一方で同様のジョブを実行できますが、両方設定しても構いません。 多くの場合、エージェントをプロセス内収集と組み合わせると、より信頼性の高い監視ワークフローとなります。 Azure 診断拡張機能 (エージェント) がプラットフォーム レベルのログで選択できるのに対して、EventFlow (インプロセス収集) はアプリケーション レベルのログで使用できます。 お客様にとって最適な方法がわかったら、今度はデータの表示方法と分析方法を検討します。

## <a name="event-analysis"></a>イベントの分析

監視データと診断データの分析と視覚化に関しては、優れたプラットフォームが市場にいくつか存在します。 Microsoft では、Service Fabric との統合に適しているという点で [OMS](service-fabric-diagnostics-event-analysis-oms.md) と [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) の 2 つを推奨していますが、[Elastic Stack](https://www.elastic.co/products) (特に、オフライン環境でクラスターの実行を検討している場合)、[Splunk](https://www.splunk.com/)、またはその他お好きなプラットフォームについても調べてみるとよいでしょう。

プラットフォーム選びの重要なポイントは、ユーザー インターフェイスやクエリのオプションをどれだけ使い慣れているかということ、データを視覚化し、簡単に読み取れるダッシュボードを作成する機能や、監視を強化するための追加ツール (自動アラートなど) が含まれているということです。

選択したプラットフォームだけでなく、Service Fabric クラスターを Azure リソースとして設定する場合には、Azure のすぐに使用できるマシン用監視機能も使用できます。この機能は、特定のパフォーマンスとメトリックの監視に役立ちます。

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) を使用して、Service Fabric クラスターの基盤となる Azure リソースの多くを監視できます。 [仮想マシン スケール セット](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)と個々の[仮想マシン](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines)の一連のメトリックが自動的に収集され、Azure Portal に表示されます。 収集された情報を表示するには、Azure Portal でService Fabric クラスターが含まれているリソース グループを選択します。 次に、表示する仮想マシン スケール セットを選択します。 **[監視]** セクションで**メトリック**を選択すると、値のグラフが表示されます。

![収集されたメトリック情報の Azure Portal での表示](media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

グラフをカスタマイズするには、[Microsoft Azure のメトリック](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)に関する記事に記載された手順に従います。 また、「[Azure Monitor での Azure サービス アラートの作成](../monitoring-and-diagnostics/insights-alerts-portal.md)」で説明するように、これらのメトリックに基づいてアラートを作成することもできます。 「[Azure メトリック アラートでの webhook の構成](../monitoring-and-diagnostics/insights-webhooks-alerts.md)」で説明するように、webhook を使用してアラートを通知サービスに送信できます。 Azure Monitor では、サブスクリプションを 1 つだけサポートします。 複数のサブスクリプションを監視する必要がある場合や、追加機能が必要な場合、Microsoft Operations Management Suite に含まれる [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) で、オンプレミスとクラウド ベースの両方のインフラストラクチャに対応する包括的な IT 管理ソリューションが提供されます。 Azure Monitor のデータを Log Analytics に直接ルーティングできるので、環境全体のメトリックとログを 1 か所で確認できます。

## <a name="next-steps"></a>次のステップ

### <a name="watchdogs"></a>ウォッチドッグ

ウォッチドッグは、複数のサービスにわたって正常性と負荷を監視し、正常性モデル階層内のあらゆるものの正常性を報告することができる別のサービスです。 このサービスにより、1 つのサービスの観点では検出されないエラーを防ぐことができます。 またウォッチドッグは、ユーザー操作なしで修復アクションを実行するコードをホストする場所としても適しています。たとえば、特定の時間間隔でストレージ内のログ ファイル クリーンアップを行うなどです。 ウォッチドッグ サービスの実装サンプルは、[ここ](https://github.com/Azure-Samples/service-fabric-watchdog-service)で見つけることができます。

イベントとログが、[プラットフォーム レベル](service-fabric-diagnostics-event-generation-infra.md)と[アプリケーション レベル](service-fabric-diagnostics-event-generation-app.md)でどのように生成されるかについて理解しましょう。