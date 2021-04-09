---
title: Azure Diagnostics 拡張機能の概要
description: Azure Diagnostics は、Cloud Services、Virtual Machines、および Service Fabric でのデバッグ、パフォーマンス測定、監視、トラフィック分析に使用できます。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 90addb74811819dec563ba072e9a6aea2cf72ca4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102036539"
---
# <a name="azure-diagnostics-extension-overview"></a>Azure Diagnostics 拡張機能の概要
Azure Diagnostics 拡張機能は、仮想マシンを含む Azure コンピューティング リソースのゲスト オペレーティング システムから監視データを収集する、[Azure Monitor のエージェント](../agents/agents-overview.md)です。 この記事では、Azure Diagnostics 拡張機能の概要と、サポートされている特定の機能、およびインストールと構成のオプションについて説明します。 

> [!NOTE]
> Azure Diagnostics 拡張機能は、コンピューティング リソースのゲスト オペレーティング システムから監視データを収集するために使用できるエージェントの 1 つです。 さまざまなエージェントの説明と、要件に適したエージェントを選択するためのガイダンスについては、「[Azure Monitor エージェントの概要](../agents/agents-overview.md)」を参照してください。

## <a name="primary-scenarios"></a>主なシナリオ
Diagnostics 拡張機能によって解決される主なシナリオは次のとおりです。

- Azure Monitor メトリックへのゲストのメトリックを収集する。
- ゲストのログおよびメトリックをアーカイブ用に Azure Storage に送信する。
- ゲストのログおよびメトリックを Azure の外部に送信するために Azure Event Hubs に送信する。


## <a name="comparison-to-log-analytics-agent"></a>Log Analytics エージェントとの比較
Azure Monitor の Log Analytics エージェントは、仮想マシンのゲスト オペレーティング システムから監視データを収集することにも使用できます。 ご自分の要件に応じて、いずれかまたは両方を選択できます。 Azure Monitor エージェントの詳細な比較については、「[Azure Monitor エージェントの概要](../agents/agents-overview.md)」を参照してください。 

考慮すべき主な違いは次のとおりです。

- Azure Diagnostics 拡張機能は、Azure の仮想マシンでのみ使用できます。 Log Analytics エージェントは、Azure、他のクラウド、およびオンプレミスの仮想マシンで使用できます。
- Azure Diagnostics 拡張機能では、Azure Storage、[Azure Monitor メトリック](../essentials/data-platform-metrics.md) (Windows のみ)、および Event Hubs にデータが送信されます。 Log Analytics エージェントでは、[Azure Monitor ログ](../logs/data-platform-logs.md)にデータが収集されます。
- Log Analytics エージェントは、[ソリューション](../monitor-reference.md#insights-and-core-solutions)、[VM insights](../vm/vminsights-overview.md)、および [Azure Security Center](../../security-center/index.yml) などのその他のサービスに必要です。

## <a name="costs"></a>コスト
Azure Diagnostic 拡張機能には料金はかかりませんが、取り込まれたデータの料金が発生する場合があります。 データの収集先については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」を参照してください。

## <a name="data-collected"></a>収集されるデータ
次の表は、Windows と Linux の Diagnostics 拡張機能によって収集できるデータを一覧表示しています。

### <a name="windows-diagnostics-extension-wad"></a>Windows Diagnostics 拡張機能 (WAD)

| Data Source | 説明 |
| --- | --- |
| Windows イベント ログ   | Windows イベント ログからのイベント。 |
| パフォーマンス カウンター | オペレーティング システムとワークロードのさまざまな側面のパフォーマンスを測定する数値。 |
| IIS ログ             | ゲスト オペレーティング システムで実行されている IIS Web サイトの使用状況に関する情報。 |
| アプリケーション ログ     | アプリケーションによって書き込まれたメッセージをトレースする。 |
| .NET EventSource ログ |.NET [EventSource](/dotnet/api/system.diagnostics.tracing.eventsource) クラスを使用するコード作成イベント |
| [マニフェスト ベースの ETW ログ](/windows/desktop/etw/about-event-tracing) |すべてのプロセスによって生成された Windows イベント トレーシング イベント。 |
| クラッシュ ダンプ (ログ)   | アプリケーションがクラッシュした場合のプロセスの状態に関する情報。 |
| ファイル ベース ログ    | アプリケーションまたはサービスで作成されたログ。 |
| エージェント診断ログ | Azure Diagnostics 自体に関する情報。 |


### <a name="linux-diagnostics-extension-lad"></a>Linux Diagnostics 拡張機能 (LAD)

| Data Source | 説明 |
| --- | --- |
| syslog | Linux イベント ログ システムに送信されるイベント。   |
| パフォーマンス カウンター  | オペレーティング システムとワークロードのさまざまな側面のパフォーマンスを測定する数値。 |
| ログ ファイル | ファイル ベースのログに送信されたエントリ。  |

## <a name="data-destinations"></a>データの保存先
Azure Diagnostic 拡張機能では、Windows 向けと Linux 向けのどちらも常に Azure Storage アカウントにデータが収集されます。 このデータが収集される特定のテーブルと BLOB の一覧については、「[Windows Azure Diagnostic 拡張機能 (WAD) のインストールと構成](diagnostics-extension-windows-install.md)」および「[Linux Diagnostic 拡張機能を使用して、メトリックとログを監視する](../../virtual-machines/extensions/diagnostics-linux.md)」を参照してください。

他の送信先にデータを送信するには、1 つ以上の "*データ シンク*" を構成します。 以降のセクションに、Windows と Linux の Diagnostic 拡張機能で使用できるシンクの一覧を示します。

### <a name="windows-diagnostics-extension-wad"></a>Windows Diagnostics 拡張機能 (WAD)

| 宛先 | 説明 |
|:---|:---|
| Azure Monitor メトリック | パフォーマンス データを Azure Monitor メトリックに収集します。 [Azure Monitor メトリック ストアへのゲスト OS メトリックの送信](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)に関する記事を参照してください。  |
| Event Hubs | Azure の外部でデータを送信するには、Azure Event Hubs を使用します。 [Event Hubs への Azure Diagnostics データのストリーミング](diagnostics-extension-stream-event-hubs.md)に関する記事を参照してください。 |
| Azure Storage BLOB | テーブルに加えて Azure Storage の BLOB にもデータを書き込みます。 |
| Application Insights | ご利用の VM で実行されているアプリケーションから Application Insights にデータを収集して、他のアプリケーション監視と統合します。 [診断データの Application Insights への送信](diagnostics-extension-to-application-insights.md)に関する記事を参照してください。 |

この機能には、通常、Log Analytics エージェントが使用されますが、ストレージから Log Analytics ワークスペースに WAD データを収集して、Azure Monitor ログで分析することもできます。 データを Log Analytics ワークスペースに直接送信し、追加の機能を提供するソリューションと分析情報をサポートできます。  [Azure 診断ログの Azure Storage からの収集](../agents/diagnostics-extension-logs.md)に関する記事を参照してください。 


### <a name="linux-diagnostics-extension-lad"></a>Linux Diagnostics 拡張機能 (LAD)
LAD では Azure Storage 内のテーブルにデータが書き込まれます。 次の表に示すシンクがサポートされています。

| 宛先 | 説明 |
|:---|:---|
| Event Hubs | Azure の外部でデータを送信するには、Azure Event Hubs を使用します。 |
| Azure Storage BLOB | テーブルに加えて Azure Storage の BLOB にもデータを書き込みます。 |
| Azure Monitor メトリック | LAD に加えて、Telegraf エージェントもインストールします。 「[Linux VM のカスタム メトリックを InfluxData Telegraf エージェントを使用して収集する](../essentials/collect-custom-metrics-linux-telegraf.md)」を参照してください。


## <a name="installation-and-configuration"></a>インストールと構成
Diagnostic 拡張機能は、Azure では[仮想マシン拡張機能](../../virtual-machines/extensions/overview.md)として実装されているため、Resource Manager テンプレート、PowerShell、および CLI を使用した場合と同じインストール オプションがサポートされています。 仮想マシン拡張機能のインストールとメンテナンスの詳細については、「[Windows 用の仮想マシンの拡張機能と機能](../../virtual-machines/extensions/features-windows.md)」および「[Linux 用の仮想マシンの拡張機能とその機能](../../virtual-machines/extensions/features-linux.md)」を参照してください。

また、Windows と Linux の両方の Diagnostics 拡張機能は、Azure portal で、仮想マシンのメニューの **[監視]** セクションの **[診断設定]** の下でインストールおよび構成することができます。

Windows および Linux の Diagnostics 拡張機能のインストールと構成の詳細については、次の記事を参照してください。

- [Windows Azure Diagnostics 拡張機能 (WAD) のインストールと構成](diagnostics-extension-windows-install.md)
- [Linux Diagnostic 拡張機能を使用して、メトリックとログを監視する](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>その他のドキュメント

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Azure クラウド サービス (クラシック) の Web ロールと Worker ロール
- [クラウド サービスの監視の概要](../../cloud-services/cloud-services-how-to-monitor.md)
- [Azure Cloud Services での Azure Diagnostics の有効化](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Azure クラウド サービス向けの Application Insights](../app/cloudservices.md)<br>[Azure Diagnostics で Cloud Services アプリケーションのフローをトレースする](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [ローカル コンピューター開発のセットアップでサービスを監視して診断する](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>次のステップ


* [Azure Diagnostics でのパフォーマンス カウンターの使用](../../cloud-services/diagnostics-performance-counters.md)について説明します。
* Azure Storage テーブルでの診断の開始またはデータの検索に問題がある場合は、[Azure Diagnostics のトラブルシューティング](diagnostics-extension-troubleshooting.md)に関するページを参照してください。