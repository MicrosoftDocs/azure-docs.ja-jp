---
title: Azure Application Insights のログベースのメトリックと事前に集計されたメトリック | Microsoft Docs
description: Azure Application Insights でログベースのメトリックと事前に集計されたメトリックを使用する理由
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: acbe535d740eb527d165be1675f31e759851a987
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101717827"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Application Insights のログベースのメトリックと事前に集計されたメトリック

この記事では、ログに基づく "従来の" Application Insights メトリックと、事前に集計されたメトリックとの違いについて説明します。 Application Insights のユーザーは両方の種類のメトリックを利用でき、各メトリックではアプリケーションの正常性、診断および分析の監視における一意の値が示されます。 アプリケーションをインストルメント化する開発者は、アプリケーションのサイズ、予想されるテレメトリの量、メトリックの精度とアラートに関するビジネス要件に応じて、特定のシナリオに最適な種類のメトリックを決めることができます。

## <a name="log-based-metrics"></a>ログベースのメトリック

これまで、Application Insights でテレメトリ データ モデルを監視するアプリケーションは、要求、例外、依存関係呼び出し、ページ ビューなど、事前に定義された種類の少数のイベントのみに基づいていました。開発者は SDK を使用して、(SDK を明示的に呼び出すコードを記述することで) これらのイベントを手動で出力することも、自動インストルメンテーションからのイベントの自動収集を利用することもできます。 いずれの場合も、収集されたすべてのイベントが Application Insights バックエンドでログとして格納され、Azure portal の Application Insights ブレードは、ログからのイベントベースのデータを視覚化するための分析および診断ツールとして動作します。

ログを使用してイベントの完全なセットを保持することで、優れた分析および診断の値が得られます。 たとえば、特定の URL に対する要求の正確な数と、これらの呼び出しを行った個別のユーザーの数を取得できます。 また、任意のユーザー セッションの例外や依存関係呼び出しを含む、詳細な診断トレースを取得できます。 この種の情報があれば、アプリケーションの正常性や使用状況をより詳細に把握でき、アプリに関する問題の診断に必要な時間を短縮できます。

それと同時に、イベントの完全なセットを収集することは、大量のテレメトリを生成するアプリケーションには実用的でない (または不可能である) 場合があります。 イベントの量が多くなりすぎた場合、Application Insights では、収集されて格納されたイベントの数を減らすための[サンプリング](./sampling.md)や[フィルター処理](./api-filtering-sampling.md)など、テレメトリ量の削減手法がいくつか適用されます。 残念ながら、格納イベントの数を減らすと、ログに格納されるイベントのクエリ時間集計をバックグラウンドで行う必要があるメトリックの精度も下がります。

> [!NOTE]
> Application Insights では、ログに格納されているイベントと測定値のクエリ時間集計に基づくメトリックのことを、ログベースのメトリックといいます。 これらのメトリックは通常、イベント プロパティからの多くのディメンションがあり、分析には適していますが、これらのメトリックの精度はサンプリングやフィルター処理によって悪影響を受けます。

## <a name="pre-aggregated-metrics"></a>事前に集計されたメトリック

ログベースのメトリックに加え、2018 年後半に、Application Insights チームによってパブリック プレビュー版のメトリックが提供されました。このメトリックは、タイム シリーズ用に最適化された特殊なリポジトリに格納されます。 新しいメトリックは、多くのプロパティを含む個々のイベントとして保持されなくなりました。 代わりに、事前に集計されたタイム シリーズとして格納され、主なディメンションのみが含まれます。 これにより、新しいメトリックはクエリ時に適したものとなります。データはより速く取得され、必要となるコンピューティング能力が減ります。 その結果、[メトリックのディメンションに関するほぼリアルタイムのアラート](../alerts/alerts-metric-near-real-time.md)や応答性の高い[ダッシュボード](./overview-dashboard.md)など、新しいシナリオが有効になります。

> [!IMPORTANT]
> Application Insights では、ログベースのメトリックと事前に集計されたメトリックの両方が共存します。 2 つを区別するため、Application Insights UX では、事前に集計されたメトリックを "標準メトリック (プレビュー)" と呼ぶようになり、イベントからの従来のメトリックは "ログベースのメトリック" という名前に変更されました。

新しい SDK (.NET 用の [Application Insights 2.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK 以降) では、収集時にメトリックが事前に集計されます。 これは、[既定で送信される標準メトリック](../essentials/metrics-supported.md#microsoftinsightscomponents)に適用されるため、正確性がサンプリングやフィルター処理の影響を受けることはありません。 また、[GetMetric](./api-custom-events-metrics.md#getmetric) を使用して送信されるカスタム メトリックにも適用されるため、データ インジェストが減り、コストが削減されます。

事前に集計しない SDK の場合 (つまり、古いバージョンの Application Insights SDK、またはブラウザー インストルメンテーションの場合)、Application Insights バックエンドでは引き続き、Application Insights イベント収集エンドポイントによって受信されるイベントを集計することで、新しいメトリックが設定されます。 つまり、ネットワーク経由で送信されるデータの量を減らせる利点はありませんが、引き続き事前に集計されたメトリックを使用でき、収集時にメトリックを事前に集計しない SDK でのほぼリアルタイムのディメンション アラートのパフォーマンスとサポートが向上します。

ここで触れておく必要があることは、インジェスト サンプリングの前に収集エンドポイントでイベントが事前に集計されるということです。つまり、アプリケーションで使用する SDK のバージョンに関係なく、[インジェスト サンプリング](./sampling.md)が事前に集計されたメトリックの精度に影響することはありません。  

### <a name="sdk-supported-pre-aggregated-metrics-table"></a>SDK でサポートされている事前集計されたメトリック テーブル

| 現在の本番環境 SDK | 標準メトリック (SDK 事前集計) | カスタム メトリック (SDK 事前集計なし) | カスタム メトリック (SDK 事前集計あり)|
|------------------------------|-----------------------------------|----------------------------------------------|---------------------------------------|
| .NET Core と .NET Framework | サポート対象 (V2.13.1 以降)| [TrackMetric](api-custom-events-metrics.md#trackmetric) を介してサポート対象| [GetMetric](get-metric.md) を介してサポート対象 (V 2.7.2 以降) |
| Java                         | サポートされていません       | [TrackMetric](api-custom-events-metrics.md#trackmetric) を介してサポート対象| サポートされていません                           |
| Node.js                      | サポートされていません       | [TrackMetric](api-custom-events-metrics.md#trackmetric) を介してサポート対象| サポートされていません                           |
| Python                       | サポートされていません       | サポートされています                                 | [OpenCensus.stats](opencensus-python.md#metrics) を介して部分的にサポート対象 |  

> [!NOTE]
>  OpenCensus を使用した Python のメトリックの実装は、GetMetric とは異なります。 詳細については、[Python のメトリックに関するドキュメント](./opencensus-python.md#metrics)をご覧ください。

### <a name="codeless-supported-pre-aggregated-metrics-table"></a>コード不要でサポートされる事前集計されたメトリック テーブル

| 現在の本番環境 SDK | 標準メトリック (SDK 事前集計) | カスタム メトリック (SDK 事前集計なし) | カスタム メトリック (SDK 事前集計あり)|
|-------------------------|--------------------------|-------------------------------------------|-----------------------------------------|
| ASP.NET                 | サポート対象 <sup>1<sup>    | サポートされていません                             | サポートされていません                           |
| ASP.NET Core            | サポート対象 <sup>2<sup>    | サポートされていません                             | サポートされていません                           |
| Java                    | サポートされていません            | サポートされていません                             | [サポートされています](java-in-process-agent.md#metrics) |
| Node.js                 | サポートされていません            | サポートされていません                             | サポートされていません                           |

1. App Service での ASP.NET コード不要アタッチでは、"完全" 監視モードでのみメトリックが出力されます。 App Service、VM/VMSS、およびオンプレミスの ASP.NET コード不要アタッチでは、ディメンションのない標準メトリックが出力されます。 すべてのディメンションに SDK が必要です。
2. App Service での ASP.NET Core コード不要アタッチでは、ディメンションのない標準メトリックが出力されます。 すべてのディメンションに SDK が必要です。

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Application Insights カスタム メトリックでの事前集計の使用

カスタム メトリックで事前集計を使用することができます。 2 つの主な利点は、カスタム メトリックのディメンションの構成およびアラート生成が可能であり、SDK から Application Insights 収集エンドポイントに送信されるデータ量を減らせることです。

[Application Insights SDK からカスタム メトリックを送信する方法](./api-custom-events-metrics.md)がいくつかあります。 ご利用のバージョンの SDK で [GetMetric および TrackValue](./api-custom-events-metrics.md#getmetric) メソッドが提供される場合、これはカスタム メトリックを送信するための推奨される方法です。SDK の内部で事前集計が行われた場合、Azure に格納されているデータの量だけでなく、SDK から Application Insights に送信されるデータの量も減らせるからです。 それ以外の場合は、[trackMetric](./api-custom-events-metrics.md#trackmetric) メソッドを使用します。このメソッドでは、データ インジェスト中にメトリック イベントが事前に集計されます。

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>カスタム メトリックのディメンションと事前集計

[trackMetric](./api-custom-events-metrics.md#trackmetric) または [GetMetric and TrackValue](./api-custom-events-metrics.md#getmetric) API 呼び出しを使用して送信するすべてのメトリックは、ログとメトリック ストアの両方に自動的に格納されます。 しかし、ログベース バージョンのカスタム メトリックでは常にすべてのディメンションが保持されますが、事前集計バージョンのメトリックは既定でディメンションなしで格納されます。 [Enable alerting on custom metric dimensions]\(カスタム メトリック ディメンションのアラートを有効にする\) をオンにすることで、[[使用量と推定コスト]](./pricing.md) タブでカスタム メトリックのディメンションの収集を有効にすることができます。 

![使用量と推定コスト](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>既定でカスタム メトリック ディメンションの収集が無効になる理由

カスタム メトリック ディメンションの収集は既定で無効になります。今後、ディメンションがあるカスタム メトリックを格納する場合に、Application Insights とは別に課金されますが、ディメンションのないカスタム メトリックを格納する場合は (クォータに達するまで) 無料のままであるためです。 今後の価格モデル変更の詳細については、Microsoft の公式の[価格ページ](https://azure.microsoft.com/pricing/details/monitor/)で確認できます。

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>グラフを作成して、ログベースのメトリックと標準の事前に集計されたメトリックを確認する

[Azure Monitor メトリックス エクスプローラー](../essentials/metrics-getting-started.md)を使用して、事前に集計されたメトリックとログベースのメトリックに基づいてグラフを描画し、そのグラフを使ってダッシュボードを作成します。 目的の Application Insights リソースを選択した後、名前空間ピッカーを使用して、標準 (プレビュー) メトリックとログベースのメトリックを切り替えるか、カスタム メトリック名前空間を作成します。

![メトリック名前空間](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="pricing-models-for-application-insights-metrics"></a>Application Insights メトリックの価格モデル

メトリックを Application Insights に取り込むと、[こちら](./pricing.md#pricing-model)で説明されているように、ログベースか事前に集計されているかにかかわらず、取り込まれたデータのサイズに基づいてコストが発生します。 すべてのディメンションを含むカスタム メトリックは常に Application Insights ログストアに格納されます。さらに、カスタム メトリックの事前に集計されたバージョン (ディメンションなし) は既定でメトリック ストアに転送されます。

[[カスタム メトリック ディメンションに関するアラートを有効にします]](#custom-metrics-dimensions-and-pre-aggregation) オプションを選択して、事前に集計されたメトリックのすべてのディメンションをメトリック ストアに格納すると、[カスタム メトリックの価格](https://azure.microsoft.com/pricing/details/monitor/)に基づいて **追加の** コストが生じる可能性があります。

## <a name="next-steps"></a>次のステップ

* [ほぼリアルタイムのアラート](../alerts/alerts-metric-near-real-time.md)
* [GetMetric と TrackValue](./api-custom-events-metrics.md#getmetric)
