---
title: Time Series Insights の監視 | Microsoft Docs
description: Time Series Insights で可用性、パフォーマンス、操作を監視します。
author: deepakpalled
ms.author: lyhughes
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: a46ddeddfcefcd4d6e7f87747fe36cfc6ec82e35
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737564"
---
# <a name="monitoring-time-series-insights"></a>Time Series Insights の監視

Azure リソースに依存するクリティカルなアプリケーションとビジネス プロセスがある場合は、それらのリソースの可用性、パフォーマンス、操作を監視する必要があります。 この記事では、Time Series Insights によって生成される監視データと、Azure Monitor の機能を使用してこのデータについての分析とアラートを行う方法について説明します。

## <a name="monitor-overview"></a>監視の概要

Azure portal の各 Time Series Insights 環境に対する **[概要]** ページには、受信したメッセージの数や格納されているバイト数など、リソースの使用状況が簡単に表示されます。 この情報は役に立ちますが、このペインで入手できる監視データの量はごくわずかです。 このデータの一部は自動的に収集され、リソースを作成するとすぐに分析に使用できるようになります。 一定の構成によって追加の種類のデータ収集を有効にすることができます。

## <a name="what-is-azure-monitor"></a>Azure Monitor とは

Time Series Insights により、[Azure Monitor](../azure-monitor/overview.md) を使用して監視データが作成されます。Azure Monitor は Azure のフル スタックの監視サービスであり、他のクラウドやオンプレミスのリソースに加えて、Azure リソースを監視するための完全な機能セットが提供されます。

まず「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」の記事にある次の概念の説明をお読みください。

- Azure Monitor とは
- 監視に関連するコスト
- Azure で収集される監視データ
- データ収集の構成
- 監視データの分析とアラート生成のための Azure の標準ツール

以下のセクションでは、この記事に基づき、Azure Time Series Insights のために収集される特定のデータについて説明します。 これらのセクションでは、Azure ツールを使用してデータ収集を構成し、このデータを分析する例も紹介します。

> [!TIP]
> Azure Monitor に関連したコストを把握するには、[使用量と推定コスト](../azure-monitor//usage-estimated-costs.md)に関する記事を参照してください。 データが Azure Monitor に表示されるまでにかかる時間を把握するには、[ログ データのインジェスト時間](../azure-monitor/logs/data-ingestion-time.md)に関する記事を参照してください。

## <a name="monitoring-data-from-azure-time-series-insights"></a>Azure Time Series Insights からのデータの監視

Azure Time Series Insights により、他の Azure リソースと同じ種類の監視データが収集されます。これについては、[Azure リソースからの監視データ](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)に関するページを参照してください。 

収集できるログとメトリックの詳細なリファレンスについては、[Azure Time Series Insights の監視データのリファレンス](how-to-monitor-tsi-reference.md)に関するページを参照してください。

## <a name="collection-and-routing"></a>収集とルーティング

プラットフォームのメトリックは自動的に収集および格納されますが、診断設定を使用して他の場所にルーティングすることもできます。

リソース ログは、診断設定を作成して 1 つ以上の場所にルーティングするまでは収集および格納されません。
Azure portal、CLI、または PowerShell を使用して診断設定を作成するプロセスの詳細については、「[Azure でプラットフォーム ログとメトリックを収集するための診断設定を作成する](../azure-monitor/essentials/diagnostic-settings.md)」を参照してください。 診断設定を作成するときは、収集するログのカテゴリを指定します。

Azure Time Series Insights については、次のカテゴリからログを収集できます。

   | カテゴリ | 説明 |
   |---|---|
   | イングレス  | イングレス カテゴリでは、イングレス パイプラインで発生したエラーが追跡されます。 このカテゴリには、イベントの受信時に発生するエラー (イベント ソースへの接続エラーなど) とイベントの処理時に発生するエラー (イベント ペイロード解析時のエラーなど) が含まれます。 |

## <a name="analyzing-metrics"></a>メトリックの分析

Azure Monitor のメニューから [メトリック] を開くことにより、Azure Time Series Insights のメトリックを、他の Azure サービスからのメトリックと一緒に分析することができます。 このツールの使用方法の詳細については、「[Azure メトリックス エクスプローラーの概要](../azure-monitor/essentials/metrics-getting-started.md)」を参照してください。

収集されるプラットフォーム メトリックの一覧については、「[Azure Time Series Insights 監視データ リファレンス](how-to-monitor-tsi-reference.md#metrics)」を参照してください

この例では、Azure Time Series Insights 環境ですべてのイベント ソースから受信したバイト数を示します。

**イングレス受信バイト数** [![Azure Time Series イングレス受信バイト数](media/how-to-monitor-tsi/ingress-received-bytes.png)](media/how-to-monitor-tsi/ingress-received-bytes.png#lightbox)

この例では、正常に処理され、Azure Time Series Insights 環境でのクエリに使用可能なバイト数を示します。

**イングレス格納バイト数** [![Azure Time Series イングレス格納バイト数](media/how-to-monitor-tsi/ingress-stored-bytes.png)](media/how-to-monitor-tsi/ingress-stored-bytes.png#lightbox)

## <a name="analyzing-logs"></a>ログの分析
リソース ログには、ストレージ アカウント内の BLOB として、イベント データとして、またはログ分析クエリを使用してアクセスできます。

Azure Monitor のログのデータはテーブルに格納され、各テーブルには独自の一意のプロパティ セットがあります。

Azure Monitor 内のすべてのリソース ログには、同じフィールドの後にサービス固有のフィールドがあります。 共通のスキーマの概要については、[Azure Monitor リソース ログのスキーマ](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)に関する記事をご覧ください。 Azure Time Series Insights に関して収集されるリソース ログの種類の一覧については、[Azure Time Series Insights 監視データ リファレンス](how-to-monitor-tsi-reference.md#resource-logs)に関するページを参照してください。

Azure Time Series Insights のデータは、次のテーブルに格納されます。

| テーブル | 説明 |
|:---|:---|
| TSIIngress | イングレス カテゴリからのデータが格納されるテーブル。 イングレス カテゴリでは、イングレス パイプラインで発生したエラーが追跡されます。 このカテゴリには、イベントの受信時に発生するエラー (イベント ソースへの接続エラーなど) とイベントの処理時に発生するエラー (イベント ペイロード解析時のエラーなど) が含まれます。

データを Azure Monitor ログにルーティングするには、リソース ログまたはプラットフォーム メトリックを Log Analytics ワークスペースに送信するための診断設定を作成する必要があります。 詳細については、「[コレクションとルーティング](../iot-hub/monitor-iot-hub.md#collection-and-routing)」を参照してください。

## <a name="sample-queries"></a>サンプル クエリ

次に示すのは、Azure Time Series Insights 環境の監視に使用できるクエリです。

+ 過去 5 日間に発生したイベント ソース接続エラーの詳細を取得します。

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/connect"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```
+ 過去 5 日間に受信した無効なメッセージの詳細を取得します。

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/deserialize"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```

## <a name="alerts"></a>警告

Azure Monitor のアラートは、監視データで重要な状態が見つかると事前に通知します。 これにより、ユーザーが気付く前に、管理者が問題を識別して対処できます。 アラートは[メトリック](../azure-monitor/alerts/alerts-metric-overview.md)、[ログ](../azure-monitor/alerts/alerts-unified-log.md)、[アクティビティ ログ](../azure-monitor/alerts/activity-log-alerts.md)に対して設定できます。 アラートの種類に応じて、さまざまな利点と欠点があります。

プラットフォーム メトリックに基づいてアラート ルールを作成する場合は、カウント単位で収集される Time Series Insights のプラットフォーム メトリックについて、一部の集計が有効でない場合や使用できない場合があることにご注意ください。

## <a name="next-steps"></a>次の手順

* Azure Time Series Insights によって作成されるログとメトリックのリファレンスについては、[Azure Time Series Insights の監視データのリファレンス](how-to-monitor-tsi-reference.md)に関するページを参照してください。
* Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。