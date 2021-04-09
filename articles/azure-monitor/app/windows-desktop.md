---
title: Windows デスクトップ アプリでの使用状況とパフォーマンスの監視
description: Application Insights を使用して、Windows デスクトップ アプリの使用状況とパフォーマンスを分析します。
ms.topic: conceptual
ms.date: 06/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 15331494b40021f10c162fba75abf6fe88d2d419
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100583311"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>従来の Windows デスクトップ アプリケーションでの使用状況とパフォーマンスの監視

Application Insights は、オンプレミスや Azure、その他各種クラウドでホストされているすべてのアプリケーションが活用ができます。 唯一の制約は、Application Insights サービスとの[通信を許可](./ip-addresses.md)する必要があることです。 ユニバーサル Windows プラットフォーム (UWP) アプリケーションの監視には、[Visual Studio App Center](../app/mobile-center-quickstart.md) をお勧めします。

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>従来の Windows アプリケーションから Application Insights にテレメトリを送信するには
1. [Azure Portal](https://portal.azure.com) で、[Application Insights のリソースを作成します](./create-new-resource.md)。 
2. インストルメンテーション キーをコピーします。
3. Visual Studio でアプリ プロジェクトの NuGet パッケージを編集し、Microsoft.ApplicationInsights.WindowsServer を追加します (または、標準テレメトリ コレクション モジュールを含まないベース API だけが必要な場合は、Microsoft.ApplicationInsights を選択します)。
4. インストルメンテーション キーの設定はコードまたは ApplicationInsights.config で行います。コードの場合:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "`*自分のキー*`";`
   
    ApplicationInsights.config の場合 (いずれかの標準テレメトリ パッケージをインストールした場合):
   
    `<InstrumentationKey>`*自分のキー*`</InstrumentationKey>` 
   
    ApplicationInsights.config を使用する場合は、ソリューション エクスプローラーでプロパティが **Build Action = Content、Copy to Output Directory = Copy** に設定されていることを確認します。
5. [API を使用して](./api-custom-events-metrics.md) テレメトリを送信します。
6. アプリケーションを実行し、Azure portal で作成したリソースのテレメトリを表示します。

## <a name="example-code"></a><a name="telemetry"></a>コード例

```csharp
using Microsoft.ApplicationInsights;

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnFormClosing(System.Windows.Forms.FormClosingEventArgs e)
        {
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnFormClosing(e);
        }
        
        ...
```

## <a name="override-storage-of-computer-name"></a>コンピューター名のストレージを上書きする

既定では、この SDK を使うと、テレメトリを出力するシステムのコンピューター名が収集されて保存されます。

コンピューター名は、内部課金の目的で Application Insights の[従来の Enterprise (Per Node) 価格レベル](./pricing.md#legacy-enterprise-per-node-pricing-tier)によって使用されます。 既定では、テレメトリ初期化子を使用して `telemetry.Context.Cloud.RoleInstance` をオーバーライドする場合、別のプロパティ `ai.internal.nodeName` が送信されますが、これにはまだコンピューター名の値が含まれています。 この値は Application Insights のテレメトリでは保存されませんが、従来のノード ベースの課金モデルとの下位互換性を確保するために、取り込み時に内部的に使用されます。

[従来の Enterprise (Per Node) 価格レベル](./pricing.md#legacy-enterprise-per-node-pricing-tier)を使用していて、単にコンピューター名のストレージをオーバーライドする必要がある場合は、テレメトリ初期化子を使用します。

**以下のようにカスタム TelemetryInitializer を作成します。**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // Set custom role name here. Providing an empty string will result
                // in the computer name still be sent via this property.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

`Program.cs` の `Main()` メソッドのインストルメンテーション キーの設定の下で、初期化子をインスタンス化します。

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
            //...
        }
```

## <a name="override-transmission-of-computer-name"></a>コンピューター名の転送をオーバーライドする

[従来の Enterprise (Per Node) 価格レベル](./pricing.md#legacy-enterprise-per-node-pricing-tier)を利用しておらず、コンピューター名を含むテレメトリが一切送信されないようにするには、テレメトリ プロセッサを使用する必要があります。

### <a name="telemetry-processor"></a>テレメトリ プロセッサ

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;


namespace WindowsFormsApp2
{
    public class CustomTelemetryProcessor : ITelemetryProcessor
    {
        private readonly ITelemetryProcessor _next;

        public CustomTelemetryProcessor(ITelemetryProcessor next)
        {
            _next = next;
        }

        public void Process(ITelemetry item)
        {
            if (item != null)
            {
                item.Context.Cloud.RoleInstance = string.Empty;
            }

            _next.Process(item);
        }
    }
}
```

インストルメンテーション キーを設定する以下の `Program.cs` `Main()` メソッドでテレメトリ プロセッサをインスタンス化します。

```csharp
using Microsoft.ApplicationInsights.Extensibility;

namespace WindowsFormsApp2
{
    static class Program
    {
        static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
            builder.Use((next) => new CustomTelemetryProcessor(next));
            builder.Build();
            //...
        }
    }
}

```

> [!NOTE]
> [従来の Enterprise (Per Node) 価格レベル](./pricing.md#legacy-enterprise-per-node-pricing-tier)を使用している場合でも、技術的には前述のテレメトリ プロセッサを使用できますが、ノードのノードあたりの料金を適切に区別できないため、課金が過剰になる可能性があります。

## <a name="next-steps"></a>次のステップ
* [ダッシュボードを作成する](./overview-dashboard.md)
* [診断検索](./diagnostic-search.md)
* [メトリックを探索する](../essentials/metrics-charts.md)
* [Analytics クエリを作成する](../logs/log-query-overview.md)

