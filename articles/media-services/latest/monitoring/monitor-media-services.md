---
title: Media Services の監視
description: Media Services を監視する方法については、こちらでご確認ください
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femilia
ms.topic: how-to
ms.service: media-services
ms.date: 03/17/2021
ms.openlocfilehash: 783d9e1b4ab86f6580cf3418a0676921aef2db6a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598203"
---
# <a name="monitor-media-services"></a>Media Services の監視

Azure リソースに依存するクリティカルなアプリケーションとビジネス プロセスがある場合は、それらのリソースの可用性、パフォーマンス、操作を監視する必要があります。 この記事では、Media Services によって生成される監視データと、Azure Monitor の機能を使用してこのデータについての分析とアラートを行う方法について説明します。

## <a name="metrics-are-useful"></a>メトリックは役に立つ

次に示すいくつかの例は、Media Services のメトリックの監視がアプリの実行状況を理解するためにどのように役立つかを示しています。 Media Services のメトリックを使用して対処できる内容は次のとおりです。

- 制限値を超過したタイミングを把握するために、Standard ストリーミング エンドポイントを監視する方法。
- 十分な Premium ストリーミング エンドポイント スケール ユニットがあるかどうかを把握する方法。
- アラートを設定して、ストリーミング エンドポイントをスケールアップするタイミングを把握する方法。
- アラートを設定して、アカウントで構成されている最大エグレスに達したタイミングを把握する方法。
- 失敗した要求の内訳を表示し、エラーの原因を確認する方法。
- パッケージャーからプルされている DASH または HLS 要求の数を確認する方法。
- アラートを設定して、失敗した要求がしきい値に達したタイミングを把握する方法。

<!--THIS DOESN'T BELONG HERE Concurrency becomes a concern for the number of Streaming Endpoints used in a single account over time. You need to keep in mind the relationship between the number of concurrent streams with complex publishing parameters like dynamic packaging to multiple protocols, multiple DRM encryptions etc. Each additional published live stream adds to the CPU and output bandwidth on the Streaming Endpoint. With that in mind, you should use Azure Monitor to closely watch the Streaming Endpoint's utilization (CPU and Egress capacity) to make certain that you are scaling it appropriately (or split traffic out between multiple Streaming Endpoints if you are getting into very high concurrency).-->

<!-- Optional diagram showing monitoring for your service. If you need help creating one, contact 
robb@microsoft.com -->

## <a name="what-is-azure-monitor"></a>Azure Monitor とは

Media Services では、[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) を使用して監視データを作成します。Azure Monitor は Azure のフル スタックの監視サービスであり、他のクラウドやオンプレミスのリソースに加えて、Azure リソースを監視するための完全な機能セットが提供されます。

まず「[Azure Monitor を使用した Azure リソースの監視](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)」の記事にある次の概念の説明をお読みください。

- Azure Monitor とは
- 監視に関連するコスト
- Azure で収集される監視データ
- データ収集の構成
- 監視データの分析とアラート生成のための Azure の標準ツール

## <a name="monitoring-data"></a>データの監視

Media Services では、他の Azure リソースと同じ種類の監視データが収集されます。これについては、[Azure リソースの監視データ](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data-from-Azure-resources)に関するページをご覧ください。

Azure Monitor によって収集されたすべてのデータは、2 つの基本的な種類であるメトリックとログのどちらかに該当します。 これらの 2 つの種類を使用すると、次のことができます。

- メトリックス エクスプローラーを使用して、メトリック データを視覚化し、分析する。
- Media Services の診断ログを監視し、それらに対してメトリックおよびログのアラートと通知を作成する。
- ログを使用すると、次のことができます。
  - Azure Storage に送信する
  - Azure Event Hubs にストリーミングする
  - Log Analytics にエクスポートする
  - サード パーティーのサービスを使用する

Media Services によって作成されるメトリックとログ メトリックの詳細については、[Media Services 監視データのリファレンス](monitor-media-services-data-reference.md)に関するページを参照してください。

## <a name="collection-and-routing"></a>収集とルーティング

*プラットフォーム メトリック* と *アクティビティ ログ* は自動的に収集および格納されますが、診断設定を使用して他の場所にルーティングすることもできます。  

*リソース ログ* は、診断設定を作成して 1 つ以上の場所にルーティングするまでは収集および格納 **されません**。

Azure portal、CLI、または PowerShell を使用して診断設定を作成するプロセスの詳細については、記事「[Azure でプラットフォーム ログとメトリックを収集するための診断設定を作成する](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)」を参照してください。

診断設定を作成するときは、収集するログのカテゴリを指定します。 Media Services のカテゴリは、[Media Services 監視データのリファレンス](monitor-media-services-data-reference.md)に関するページに記載されています。

## <a name="analyzing-metrics"></a>メトリックの分析

**Azure Monitor** のメニューから **[メトリック]** を開き、メトリックス エクスプローラーを使用して、Media Services のメトリックを、他の Azure サービスからのメトリックと一緒に分析することができます。 このツールの使用方法の詳細については、「[Azure メトリックス エクスプローラーの概要](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)」を参照してください。

Media Services 用に収集されるメトリックの一覧については、[Media Services 監視データのリファレンス](monitor-media-services-data-reference.md)に関するページを参照してください。

## <a name="analyzing-logs"></a>ログの分析

Azure Monitor ログのデータはテーブルに格納され、各テーブルには独自の一意のプロパティ セットがあります。  

Azure Monitor 内のすべてのリソース ログには、同じフィールドの後にサービス固有のフィールドがあります。 共通のスキーマの概要については、[Azure Monitor リソース ログのスキーマ](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-schema#top-level-resource-logs-schema)に関する記事をご覧ください。

Media Services リソース ログのスキーマは、[Media Services 監視データのリファレンス](monitor-media-services-data-reference.md)に関するページあります。

[アクティビティ ログ](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log)は、Azure のプラットフォーム ログであり、サブスクリプション レベルのイベントの分析情報が提供されます。 個別に表示できるほか、Azure Monitor ログにルーティングして、Log Analytics を使用してより複雑なクエリを実行することもできます。

Media Services 用に収集されるリソース ログの一覧については、[Media Services 監視データのリファレンス](monitor-media-services-data-reference.md)に関するページを参照してください。

### <a name="why-would-i-want-to-use-diagnostic-logs"></a>診断ログを使用する理由

診断ログで調べることができるいくつかのことを次に示します。

- DRM の種類ごとに配信されるライセンスの数。
- ポリシーごとに配信されるライセンスの数。
- DRM またはポリシーの種類ごとのエラー。
- クライアントからの認可されていないライセンス要求の数。

## <a name="alerts"></a>警告

Azure Monitor のアラートは、監視データで重要な状態が見つかると事前に通知します。 これにより、ユーザーが気付く前に、管理者が問題を識別して対処できます。 アラートは[メトリック](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview)、[ログ](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)、[アクティビティ ログ](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-alerts)に対して設定できます。

Media Services のメトリックは、値の変化とは無関係に、一定の間隔で収集されます。 頻繁にサンプリングできるためアラート発信に役立ちます。 アラートは比較的シンプルなロジックで迅速に発生させることができます。

<!--
The following table lists common and recommended alert rules for Media Services.

<!-- Fill in the table with metric and log alerts that would be valuable for your service. Change the format as necessary to make it more readable
**PLACEHOLDER** table

| Alert type | Condition | Description  |
|:---|:---|:---|
| | | |
| | | |
-->

## <a name="next-steps"></a>次のステップ

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
