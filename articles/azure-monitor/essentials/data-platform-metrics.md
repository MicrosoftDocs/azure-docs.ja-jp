---
title: Azure Monitor のメトリック | Microsoft Docs
description: ほぼリアルタイムのシナリオをサポートできる軽量な監視データである、Azure Monitor のメトリックについて説明します。
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 8ecfd74a4d486a83add490501c2f7af4a4003b85
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700976"
---
# <a name="azure-monitor-metrics-overview"></a>Azure Monitor メトリックの概要
Azure Monitor メトリックは、[監視対象のリソース](../monitor-reference.md)から時系列データベースに数値データを収集する Azure Monitor の機能です。 メトリックは、一定の間隔で収集される数値であり、特定の時刻におけるシステムの何らかの特性を表しています。 Azure Monitor のログは軽量であり、ほぼリアルタイムのシナリオをサポートできるため、アラートと問題の迅速な検出に特に役立ちます。 メトリック エクスプローラーを使用すると、対話形式で分析することができます。値がしきい値を超えるときにアラートで事前に通知したり、ブックやダッシュボードで視覚化したりすることができます。


> [!NOTE]
> Azure Monitor メトリックは、Azure Monitor をサポートするデータ プラットフォームの半分を担っています。 もう半分は、ログとパフォーマンス データを収集して整理し、豊富なクエリ言語で分析できる [Azure Monitor ログ](../logs/data-platform-logs.md)です。 メトリックは、Azure Monitor ログのデータよりも軽量であり、ほぼリアルタイムのシナリオに対応しており、問題の通知や迅速な検出に特に役立ちます。 ただし、メトリックが特定の構造に数値データを格納することしかできないのに対し、ログは、それぞれ独自の構造を持つさまざまなデータ型を格納できます。 メトリック データの分析に使用できないログ クエリを使用して、ログ データで複雑な分析を実行することもできます。


## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Azure Monitor のメトリックでできること
Azure Monitor のメトリックを使用できるさまざまな方法を次の表に示します。

|  |  |
|:---|:---|
| **分析** | [メトリックス エクスプローラー](metrics-charts.md)を使用して、収集されたメトリックをグラフで分析し、異なるリソースからのメトリックを比較します。 |
| **Alert** | メトリックがしきい値を超えたときに、通知を送信または[自動化されたアクション](../alerts/action-groups.md)を実行する[メトリック アラート ルール](../alerts/alerts-metric.md)を構成します。 |
| **視覚化** | メトリックス エクスプローラーのグラフを [Azure ダッシュボード](../app/tutorial-app-dashboards.md)にピン留めします。<br>[ブック](../visualize/workbooks-overview.md)を作成して、複数のデータのセットを対話型のレポートにまとめます。クエリの結果を [Grafana](../visualize/grafana-plugin.md) にエクスポートし、そのダッシュボードを利用して他のデータ ソースと組み合わせます。 |
| **自動化** |  [自動スケーリング](../autoscale/autoscale-overview.md)を使用して、しきい値を超えるメトリック値に基づいてリソースを増加または減少させます。 |
| **取得** | [PowerShell コマンドレット](/powershell/module/az.applicationinsights)を使用して、コマンド ラインからメトリック値にアクセスします。<br>[REST API](./rest-api-walkthrough.md) を使用して、カスタム アプリケーションからメトリック値にアクセスします。<br>[CLI](/cli/azure/monitor/metrics) を使用して、コマンド ラインからメトリック値にアクセスします。 |
| **エクスポート** | [メトリックをログにルーティング](./resource-logs.md#send-to-azure-storage)して、Azure Monitor メトリックのデータと Azure Monitor ログのデータを一緒に分析し、93 日間より長くメトリック値を保存します。<br>メトリックを [Event Hub](./stream-monitoring-data-event-hubs.md) にストリーミングして、外部システムにルーティングします。 |
| **Archive** | コンプライアンス、監査、オフライン レポートの目的で、リソースのパフォーマンスや正常性の履歴を[アーカイブ](./platform-logs-overview.md)します。 |

![メトリックの概要](media/data-platform-metrics/metrics-overview.png)


## <a name="data-collection"></a>データ コレクション
Azure Monitor によって収集されるメトリックのソースには、基本的なものが 3 つあります。 Azure Monitor メトリック データベースでこれらのメトリックが収集されると、そのソースとは無関係に一緒に評価できます。

**Azure リソース**。 プラットフォームのメトリックは Azure リソースによって作成され、リソースの正常性とパフォーマンスについての可視化を提供します。 リソースの種類ごとに[別個のメトリックのセット](./metrics-supported.md)が作成され、必要な構成はありません。 メトリックの定義で特に指定がない限り、プラットフォーム メトリックは 1 分間隔で Azure リソースから収集されます。 

**アプリケーション**。 メトリックは、監視対象のアプリケーションについて Application Insights によって作成され、パフォーマンスの問題を検出し、アプリケーションがどのように使用されているかの傾向を追跡するために役立ちます。 これには、_サーバー応答時間_ と _ブラウザー例外_ などの値が含まれます。

**仮想マシン エージェント**。 メトリックは、仮想マシンのゲスト オペレーティング システムから収集されます。 Windows 仮想マシンの場合は [Windows Diagnostic Extension (WAD)](../agents/diagnostics-extension-overview.md) を使用し、Linux 仮想マシンの場合は [InfluxData Telegraf エージェント](https://www.influxdata.com/time-series-platform/telegraf/)を使用して、ゲスト OS メトリックを有効にします。

**カスタム メトリック**。 自動的に使用できる標準メトリックに加えて、メトリックを定義することができます。 Application Insights によって監視される[カスタム メトリックをアプリケーション内で定義](../app/api-custom-events-metrics.md)したり、[カスタム メトリック API](./metrics-store-custom-rest-api.md) を使用して Azure サービスのカスタム メトリックを作成したりできます。

- Azure Monitor メトリックにデータを送信できるデータ ソースの完全なリストについては、「[Azure Monitor によって監視される内容](../monitor-reference.md)」を参照してください。

## <a name="metrics-explorer"></a>メトリックス エクスプローラー
[メトリックス エクスプ ローラー](metrics-charts.md)を使用して、メトリック データベース内のデータを対話的に分析し、一定期間にわたる複数のメトリックの値をグラフにします。 グラフをダッシュボードにピン留めして、他の視覚化と一緒に表示できます。 [Azure monitoring REST API](./rest-api-walkthrough.md) を使用してメトリックを取得することもできます。

![メトリックス エクスプローラー](media/data-platform-metrics/metrics-explorer.png)

- メトリックス エクスプローラーの使用を開始するには、「[Azure Monitor メトリックス エクスプローラーの概要](./metrics-getting-started.md)」を参照してください。

## <a name="data-structure"></a>データ構造
Azure Monitor メトリックによって収集されるデータは、タイムスタンプ付きのデータの分析用に最適化された時系列データベースに保存されます。 メトリック値の各セットは、次のプロパティを持つ時系列です。

* 値が収集された時刻
* 値が関連付けられているリソース
* メトリックのカテゴリのように機能する名前空間
* メトリック名
* 値そのもの
* 「[多次元メトリック](#multi-dimensional-metrics)」で説明されているように、一部のメトリックは複数のディメンションを持ちます。 カスタム メトリックは最大 10 個のディメンションを持つことができます。

## <a name="multi-dimensional-metrics"></a>多次元メトリック
メトリック データの課題の 1 つは、収集された値のコンテキストを提供するための情報が限定されている場合が多いことです。 Azure Monitor では、多次元メトリックを使用してこの課題に対処します。 メトリックのディメンションは、メトリックの値を記述するための追加データを保持する名前と値のペアです。 たとえば、_使用可能なディスク領域_ メトリックは、_C:_ 、_D:_ という値がある _ドライブ_ と呼ばれるディメンションを持つことができ、これらの値を使用して、すべてのドライブまたは個別のドライブで使用可能なディスク領域を表示できます。

次の例は、_ネットワーク スループット_ という名前の架空のメトリック用の 2 つのデータセットを示しています。 最初のデータセットにはディメンションがありません。 2 番目のデータセットは、_IP アドレス_ と _方向_ という 2 つのディメンションの値を示しています。

### <a name="network-throughput"></a>ネットワーク スループット

| Timestamp     | メトリック値 |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8 Kbps |
| 8/9/2017 8:15 | 1,141.4 Kbps |
| 8/9/2017 8:16 | 1,110.2 Kbps |

このディメンションのないメトリックは、「特定の時点でのネットワークのスループットは?」のような基本的な質問にのみ答えることができます。

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>ネットワーク スループット + 2 つのディメンション ("IP" と "方向")

| Timestamp     | ディメンション "IP"   | ディメンション "方向" | メトリック値|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP="192.168.5.2" | 方向 = "送信"    | 646.5 Kbps |
| 8/9/2017 8:14 | IP="192.168.5.2" | 方向 = "受信" | 420.1 Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | 方向 = "送信"    | 150.0 Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | 方向 = "受信" | 115.2 Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | 方向 = "送信"    | 515.2 Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | 方向 = "受信" | 371.1 Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | 方向 = "送信"    | 155.0 Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | 方向 = "受信" | 100.1 Kbps |

このメトリックは、「各 IP アドレスのネットワーク スループットは?」 や 「データの送信量と受信量?」といった質問に応えることができます。 多次元メトリックは、ディメンションを持たないメトリックに比べ、分析と診断に使用できる多数の値を提供します。

## <a name="retention-of-metrics"></a>メトリックの保有期間
Azure の多くのリソースでは、メトリックは 93 日間保存されます。 ただし、例外があります。

**ゲスト OS メトリック**
-   **クラシック ゲストの OS メトリック**。 これらは [Windows Diagnostic Extension (WAD)](../agents/diagnostics-extension-overview.md) または [Linux Diagnostic Extension (LAD)](../../virtual-machines/extensions/diagnostics-linux.md) によって収集され、Azure ストレージ アカウントにルーティングされるパフォーマンス カウンターです。 これらのメトリックの保有期間は 14 日です。
-   **Azure Monitor メトリックに送信されるゲスト OS メトリック**。 これらは [Windows Diagnostic 拡張機能 (WAD)](../agents/diagnostics-extension-overview.md) によって収集され、[Azure Monitor データ シンク](../agents/diagnostics-extension-overview.md#data-destinations)に送信されるか、Linux マシン上の [InfluxData Telegraf エージェント](https://www.influxdata.com/time-series-platform/telegraf/)を介して収集されるパフォーマンス カウンターです。 これらのメトリックの保有期間は 93 日です。
-   **Log Analytics エージェントによって収集されるゲスト OS メトリック**。 これらは、Log Analytics エージェントによって収集され、Log Analytics ワークスペースに送信されるパフォーマンス カウンターです。 これらのメトリックの保有期間は 31 日で、最大 2 年間まで延長できます。

**Application Insights ログベースのメトリック** 
- バック グラウンドで[ログ ベースのメトリック](../app/pre-aggregated-metrics-log-metrics.md)をログ クエリに変換します。 そのリテンション期間は、基になるログのイベントのリテンション期間と一致します。 Application Insights リソースの場合、ログは 90 日間保存されます。


> [!NOTE]
> 長期的な傾向を見るために、[Azure Monitor リソースのプラットフォーム メトリックを Log Analytics ワークスペースに送信](./resource-logs.md#send-to-azure-storage)できます。





## <a name="next-steps"></a>次のステップ

- [Azure Monitor データ プラットフォーム](../data-platform.md)の詳細を確認します。
- [Azure Monitor のログ データ](../logs/data-platform-logs.md)について確認します。
- Azure のさまざまなリソースで[入手できる監視データ](../agents/data-sources.md)を確認します。