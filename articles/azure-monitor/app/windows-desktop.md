---
title: Windows デスクトップ アプリでの使用状況とパフォーマンスの監視
description: Application Insights を使用して、Windows デスクトップ アプリの使用状況とパフォーマンスを分析します。
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 19040746-3315-47e7-8c60-4b3000d2ddc4
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: mbullwin
ms.openlocfilehash: 95ff8d1a70325357fee4bc24fd96c1a1c7a73845
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54077608"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>従来の Windows デスクトップ アプリケーションでの使用状況とパフォーマンスの監視

Application Insights は、オンプレミスや Azure、その他各種クラウドでホストされているすべてのアプリケーションが活用ができます。 唯一の制約は、Application Insights サービスとの[通信を許可](../../azure-monitor/app/ip-addresses.md)する必要があることです。 ユニバーサル Windows プラットフォーム (UWP) アプリケーションの監視には、[Visual Studio App Center](../../azure-monitor/learn/mobile-center-quickstart.md) をお勧めします。

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>従来の Windows アプリケーションから Application Insights にテレメトリを送信するには
1. [Azure Portal](https://portal.azure.com) で、[Application Insights のリソースを作成します](../../azure-monitor/app/create-new-resource.md )。 アプリケーションの種類として ASP.NET アプリを選択します。
2. インストルメンテーション キーをコピーします。 先ほど作成した新しいリソースの [要点] ボックスの一覧で、キーを探します。 
3. Visual Studio でアプリ プロジェクトの NuGet パッケージを編集し、Microsoft.ApplicationInsights.WindowsServer を追加します (または、標準テレメトリ コレクション モジュールを含まないベア API だけが必要な場合は、Microsoft.ApplicationInsights を選択します)。
4. インストルメンテーション キーの設定はコードまたは ApplicationInsights.config で行います。コードの場合:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *自分のキー* `";`
   
    ApplicationInsights.config の場合 (いずれかの標準テレメトリ パッケージをインストールした場合):
   
    `<InstrumentationKey>`*自分のキー*`</InstrumentationKey>` 
   
    ApplicationInsights.config を使用する場合は、ソリューション エクスプローラーでプロパティが **Build Action = Content、Copy to Output Directory = Copy**に設定されていることを確認します。
5. [API を使用して](../../azure-monitor/app/api-custom-events-metrics.md) テレメトリを送信します。
6. アプリケーションを実行し、Azure Portal で作成したリソースのテレメトリを表示します。

## <a name="telemetry"></a>コード例
```csharp

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>次の手順
* [ダッシュボードを作成する](../../azure-monitor/app/app-insights-dashboards.md)
* [診断検索](../../azure-monitor/app/diagnostic-search.md)
* [メトリックを探索する](../../azure-monitor/app/metrics-explorer.md)
* [Analytics クエリを作成する](../../azure-monitor/app/analytics.md)

