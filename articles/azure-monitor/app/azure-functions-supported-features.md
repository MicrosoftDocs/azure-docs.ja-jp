---
title: Azure Application Insights - Azure Functions でサポートされる機能
description: Azure Functions でサポートされる Application Insights の機能
ms.topic: reference
author: TimothyMothra
ms.author: tilee
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 333bba2b1d3cd83457196e38b827daa78199f235
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87033515"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Azure Functions でサポートされる Application Insights の機能

Azure Functions では、ILogger インターフェイス経由で使用できる、Application Insights との[組み込みの統合](../../azure-functions/functions-monitoring.md)を提供しています。 現在サポートされている機能の一覧を次に示します。 [概要](../../azure-functions/functions-monitoring.md#enable-application-insights-integration)については、Azure Functions のガイドを確認してください。

Functions ランタイム バージョンの詳細については、[こちら](../../azure-functions/functions-versions.md)をご覧ください。

Application Insights の互換性のあるバージョンの詳細については、「[依存関係](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/)」を参照してください。

## <a name="supported-features"></a>サポートされている機能

| Azure Functions                       | V1                | V2 および V3   | 
|-----------------------------------    |---------------    |------------------ |
| | | | 
| **自動収集の対象**        |                 |                   |               
| &bull; 要求                     | はい             | はい               | 
| &bull; 例外                   | はい             | はい               | 
| &bull; パフォーマンス カウンター         | はい             | はい               |
| &bull; 依存関係                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | はい               | 
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|                 | はい               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | はい               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | はい               | 
| | | | 
| **サポートされる機能**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | はい             | はい               | 
| &nbsp;&nbsp;&nbsp;&mdash; コントロール チャネルの保護|                 | はい               | 
| &bull; サンプリング                     | はい             | はい               | 
| &bull; ハートビート                   |                 | はい               | 
| | | | 
| **相関関係**                       |                   |                   |               
| &bull; ServiceBus                     |                   | はい               | 
| &bull; EventHub                       |                   | はい               | 
| | | | 
| **構成可否**                      |                   |                   |           
| &bull;完全に構成可能。<br/>手順については、[Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) を確認する。<br/>すべてのオプションについては、[Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) を確認する。               |                   | はい                   | 


## <a name="performance-counters"></a>パフォーマンス カウンター

パフォーマンス カウンターの自動収集は、Windows マシンでのみ機能します。


## <a name="live-metrics--secure-control-channel"></a>Live Metrics とコントロール チャネルの保護

指定したカスタム フィルター条件は、Application Insights SDK の Live Metrics コンポーネントに送信されます。 フィルターに顧客 ID などの機密情報が含まれている可能性があります。 シークレット API キーを使用してチャネルをセキュリティで保護できます。 手順については、「[コントロール チャネルの保護](./live-stream.md#secure-the-control-channel)」を参照してください。

## <a name="sampling"></a>サンプリング

Azure Functions では、構成の中で、サンプリングが既定で有効になっています。 詳細については、[サンプリングの構成](../../azure-functions/functions-monitoring.md#configure-sampling)に関するページをご覧ください。

プロジェクトで Application Insights SDK に依存してテレメトリ追跡を手動で行っている場合、ご使用のサンプリング構成が Functions のサンプリング構成と異なっていると、予想外の動作が起こることがあります。 

Functions と同じ構成を使用することをお勧めします。 **Functions v2** では、コンストラクターで依存関係の挿入を使用することで、同じ構成を取得できます。

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;

public class Function1 
{

    private readonly TelemetryClient telemetryClient;

    public Function1(TelemetryConfiguration configuration)
    {
        this.telemetryClient = new TelemetryClient(configuration);
    }

    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger logger)
    {
        this.telemetryClient.TrackTrace("C# HTTP trigger function processed a request.");
    }
}
```
