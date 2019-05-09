---
title: Azure 監視エージェントの概要 | Microsoft Docs
description: この記事では、Azure またはハイブリッド環境にホストされている仮想マシンの監視をサポートする Azure エージェントについて概説します。
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: magoedte
ms.openlocfilehash: 8b10cb0d66103410159a09ca156be3ea180c068b
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371929"
---
# <a name="overview-of-the-azure-monitoring-agents"></a>Azure 監視エージェントの概要 
Microsoft Azure では、Azure、データセンター、または他のクラウド プロバイダーにホストされている、Microsoft Windows や Linux を実行する仮想マシンからの各種のデータをさまざまな方法で収集できます。 VM の監視に使用可能な 3 種類のエージェントがあります。

* Azure Diagnostics の拡張機能
* Linux および Windows 用 Log Analytics エージェント
* 依存関係エージェント

この記事では、それぞれの違いや機能について説明し、自分の IT サービス マネジメントや一般的な監視の要件をサポートするのはどれであるかを判断できるようにします。  

## <a name="azure-diagnostic-extension"></a>Azure 診断の拡張機能
2010 年に一般公開されて以降、Azure Cloud Services 用に提供されている [Azure Diagnostics 拡張機能](../../azure-monitor/platform/diagnostics-extension-overview.md) (一般に、Windows Azure Diagnostic (WAD) または Linux Azure Diagnostic (LAD) 拡張機能と呼ばれる) は、VM などの Azure コンピューティング リソースから診断データを収集して、Azure ストレージに保持するエージェントです。 データがストレージに保持されたら、[Visual Studio のサーバー エクスプローラー](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)や [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) などの提供されているツールの 1 つで表示することができます。

次のものを収集することを選択できます。

* 事前に定義された一連のオペレーティング システム パフォーマンス カウンターとイベント ログ。どちらを収集するかを選択できます。 
* IIS Web サーバーに対するすべての要求/失敗した要求
* .NET アプリのトレース出力のログ
* Windows イベント トレーシング (ETW) イベント 
* Syslog からのログ イベント  
* クラッシュ ダンプ 

次の場合に Azure Diagnostics エージェントを使用する必要があります。

* ログとメトリックを Azure ストレージにアーカイブする
* 監視データをサード パーティ製のツールと統合する。 これらのツールでは、ストレージ アカウントへのクエリ、[Event Hubs](../../event-hubs/event-hubs-about.md) への転送、[Azure 監視 REST API](../../azure-monitor/platform/rest-api-walkthrough.md) を使用したクエリなど、さまざまな方法が使用されます
* Azure Monitor にデータをアップロードして、Azure portal 上でメトリック グラフを作成するか、ほぼリアルタイムで[メトリック アラート](../../azure-monitor/platform/alerts-metric-overview.md)を作成する。 
* 仮想マシン スケール セットと従来の Cloud Services をゲスト OS メトリックに基づいて自動スケーリングする。
* [ブート診断](../../virtual-machines/troubleshooting/boot-diagnostics.md)を使用して VM ブートの問題を調査する。
* [Application Insights](../../azure-monitor/overview.md) を使用して、ご利用のアプリケーションのパフォーマンスを把握し、影響している問題点を先を見越して特定する。
* Cloud Services、従来の VM、および Azure ストレージ アカウントに格納されている Service Fabric ノードから収集したメトリックとログ データをインポートするように Azure Monitor を構成する。

## <a name="log-analytics-agent"></a>Log Analytics エージェント
メトリックやログのサブセットを収集すること以上の作業が必要な高度な監視を行う場合は、Windows 用および Linux 用の Log Analytics エージェントが必要です (Windows 用は、Microsoft Monitoring Agent (MMA) とも呼ばれます)。 Log Analytics エージェントは、オンプレミスの物理および仮想マシン、System Center Operations Manager によって監視されるコンピューター、その他のクラウドにホストされている VM を包括的に管理するために開発されました。 Windows および Linux エージェントは、監視ソリューション ベースのデータと、自分で構成するカスタム データ ソースの両方を収集するために、Azure Monitor で Log Analytics ワークスペースに接続します。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

次の場合に Log Analytics 診断エージェントを使用する必要があります。

* Azure、他のクラウド プロバイダー、オンプレミス リソース内のさまざまなソースからデータを収集する。 
* [Azure Monitor for VMs](../insights/vminsights-overview.md)、[コンテナー用 Azure Monitor](../insights/container-insights-overview.md) などの Azure Monitor 監視ソリューションのいずれかを使用する。  
* [Azure Security Center](../../security-center/security-center-intro.md)、[Azure Automation](../../automation/automation-intro.md) など、他の Azure 管理サービスのいずれかを使用する。

以前は、複数の Azure サービスが *Operations Management Suite* としてバンドルされていたので、Log Analytics エージェントは Azure Security Center や Azure Automation などのサービス間で共有されています。  これには、提供される機能の完全なセットが含まれ、Azure VM の包括的な管理がライフ サイクル全体を通じて提供されます。  この例を次にいくつか示します。

* [Azure Automation Update](../../automation/automation-update-management.md) による、オペレーティング システム更新プログラムの管理。
* [Azure Automation Desired State Configuration](../../automation/automation-dsc-overview.md) による、構成状態の一貫性の維持。
* [Azure Automation Change Tracking および Inventory](../../automation/automation-change-tracking.md) による、構成変更の追跡。
* データが直接 Log Analytics にネイティブに保管される、[Application Insights](https://docs.microsoft.com/azure/application-insights/) や [Azure Security Center](https://docs.microsoft.com/azure/security-center/) などの Azure サービス。  

## <a name="dependency-agent"></a>依存関係エージェント
依存関係エージェントは、当初は Microsoft の外部で開発された Service Map ソリューションの一部として開発されました。 [Service Map](../insights/service-map.md) と [Azure Monitor for VMs](../insights/vminsights-overview.md) は Windows および Linux 仮想マシン上の依存関係エージェントを必要とします。また、Log Analytics エージェントと統合されて、仮想マシン上で実行されているプロセスや外部プロセスの依存関係について検出されたデータを収集します。 このデータは Log Analytics ワークスペースに格納され、検出された相互接続コンポーネントが視覚化されます。

VM を監視するには、これらのエージェント組み合わせが必要な場合があります。 エージェントは Azure 拡張機能と共存するようにインストールできますが、Linux では、Log Analytics エージェントを最初にインストールする "*必要があり*"、そうしないとインストールが失敗します。 

## <a name="next-steps"></a>次の手順

- [Log Analytics エージェントの概要](../../azure-monitor/platform/log-analytics-agent.md)に関する記事を参照して、Azure、データ センター、または他のクラウド環境でホストされているマシンにエージェントをデプロイするための要件とサポートされている方法を確認します。

