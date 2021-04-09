---
title: Azure Functions を使用してカスタム可用性テストを作成して実行する
description: このドキュメントでは、TimerTrigger 関数で指定された構成に従って定期的に実行される Azure 関数を TrackAvailability() で作成する方法について説明します。 このテストの結果は、Application Insights リソースに送信されます。そこでは、可用性の結果データに対してクエリを実行し、アラートを生成することができます。 カスタマイズされたテストを使用すると、ポータル UI を使用してできるものより複雑な可用性テストを記述したり、Azure VNET 内のアプリを監視したり、エンドポイントのアドレスを変更したり、リージョンで利用できない場合に可用性テストを作成したりすることができます。
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 98d9eaadb31ffdeabe85752f7c76bdd4f7c0d4f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589932"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Azure Functions を使用してカスタム可用性テストを作成して実行する

この記事では、独自のビジネス ロジックを使用して TimerTrigger 関数で指定された構成に従って定期的に実行される Azure 関数を TrackAvailability() で作成する方法について説明します。 このテストの結果は、Application Insights リソースに送信されます。そこでは、可用性の結果データに対してクエリを実行し、アラートを生成することができます。 これにより、ポータルでの[可用性の監視](./monitor-web-app-availability.md)を使用して実行できるものと同様のカスタマイズされたテストを作成できます。 カスタマイズされたテストを使用すると、ポータル UI を使用してできるものより複雑な可用性テストを記述したり、Azure VNET 内のアプリを監視したり、エンドポイントのアドレスを変更したり、リージョンでこの機能を利用できない場合でも可用性テストを作成したりすることができます。

> [!NOTE]
> この例は、Azure Functions 内で TrackAvailability () API 呼び出しが機能する仕組みを示すためだけに設計されています。 基になる HTTP テスト コード/ビジネス ロジックを記述する方法を示すものではありません (それは、これを完全に機能する可用性テストにする場合に必要になります)。 既定で、この例を実行すると、常にエラーを生成する可用性テストが作成されます。

## <a name="create-timer-triggered-function"></a>タイマーによってトリガーされる関数を作成する

- Application Insights リソースがある場合:
    - Azure Functions では既定で Application Insights リソースが作成されますが、既に作成されているリソースのいずれかを使用したい場合は、作成時にそれを指定する必要があります。
    - [Azure Functions リソースとタイマーによってトリガーされる関数を作成する](../../azure-functions/functions-create-scheduled-function.md)方法の手順に従い (クリーンアップの前で止めます)、次のように選択します。
        -  上部近くにある **[監視]** タブを選択します。

            ![ 独自の App Insights リソースを使用して Azure Functions アプリを作成する](media/availability-azure-functions/create-function-app.png)

        - [Application Insights] ドロップダウン ボックスを選択し、リソースの名前を入力または選択します。

            ![既存の Application Insights リソースの選択](media/availability-azure-functions/app-insights-resource.png)

        - **[確認と作成]** を選択します
- タイマーによってトリガーされる関数用の Application Insights リソースをまだ作成していない場合:
    - 既定では、Azure Functions アプリケーションを作成するときに、Application Insights リソースが自動的に作成されます。
    - [Azure Functions リソースとタイマーによってトリガーされる関数を作成する](../../azure-functions/functions-create-scheduled-function.md)方法の手順に従います (クリーンアップの前で止めます)。

## <a name="sample-code"></a>サンプル コード

次のコードを run.csx ファイルにコピーします (既存のコードを置き換えます)。 これを行うには、Azure Functions アプリケーションに移動し、左側のタイマー トリガー関数を選択します。

>[!div class="mx-imgBorder"]
>![Azure portal での Azure 関数の run.csx](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> エンドポイント アドレスには、`EndpointAddress= https://dc.services.visualstudio.com/v2/track` を使用します。 リソースが Azure Government や Azure China などのリージョンにある場合は、[既定のエンド ポイントのオーバーライド](./custom-endpoints.md#regions-that-require-endpoint-modification)に関する記事を参考にして、お使いのリージョンに適したテレメトリ チャネル エンドポイントを選択してください。

```C#
#load "runAvailabilityTest.csx"
 
using System;
using System.Diagnostics;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
 
// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
// DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string instrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");
 
//[CONFIGURATION_REQUIRED]
// If your resource is in a region like Azure Government or Azure China, change the endpoint address accordingly.
// Visit https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification for more details.
private const string EndpointAddress = "https://dc.services.visualstudio.com/v2/track";
 
private static readonly TelemetryConfiguration telemetryConfiguration = new TelemetryConfiguration(instrumentationKey, new InMemoryChannel { EndpointAddress = EndpointAddress });
private static readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
 
public async static Task Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Entering Run at: {DateTime.Now}");
 
    if (myTimer.IsPastDue)
    {
        log.LogWarning($"[Warning]: Timer is running late! Last ran at: {myTimer.ScheduleStatus.Last}");
    }
 
    // [CONFIGURATION_REQUIRED] provide {testName} accordingly for your test function
    string testName = "AvailabilityTestFunction";
 
    // REGION_NAME is a default environment variable that comes with App Service
    string location = Environment.GetEnvironmentVariable("REGION_NAME");
 
    log.LogInformation($"Executing availability test run for {testName} at: {DateTime.Now}");
    string operationId = Guid.NewGuid().ToString("N");
 
    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = testName,
        RunLocation = location,
        Success = false
    };
 
    var stopwatch = new Stopwatch();
    stopwatch.Start();
 
    try
    {
        await RunAvailbiltyTestAsync(log);
        availability.Success = true;
    }
    catch (Exception ex)
    {
        availability.Message = ex.Message;
 
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", testName);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        telemetryClient.TrackException(exceptionTelemetry);
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;
 
        telemetryClient.TrackAvailability(availability);
        // call flush to ensure telemetry is sent
        telemetryClient.Flush();
    }
}

```

右側の [ファイルの表示] で、 **[追加]** を選択します。 次の構成を使用して、新しいファイル **function.proj** を呼び出します。

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights" Version="2.15.0" /> <!-- Ensure you’re using the latest version -->
    </ItemGroup>
</Project>

```

>[!div class="mx-imgBorder"]
>![右側で [追加] を選択する。 ファイルに "function.proj" という名前を付ける](media/availability-azure-functions/addfile.png)

右側の [ファイルの表示] で、 **[追加]** を選択します。 次の構成を使用して、新しいファイル **runAvailabilityTest.csx** を呼び出します。

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>可用性を確認する

すべてが動作していることを確認するには、Application Insights リソースの [可用性] タブでグラフを確認します。

> [!NOTE]
> runAvailabilityTest.csx で独自のビジネス ロジックを実装した場合は、次のスクリーンショットのように、成功した結果が表示されます。そうしなかった場合は、失敗した結果が表示されます。 `TrackAvailability()` で作成されたテスト名の横には、 **[CUSTOM]** と表示されます。

>[!div class="mx-imgBorder"]
>![成功した結果が表示された [可用性] タブ](media/availability-azure-functions/availability-custom.png)

エンドツーエンドのトランザクションの詳細を表示するには、[詳細の表示] で **[成功]** または **[失敗]** を選択してから、サンプルを選択します。 グラフでデータ ポイントを選択することにより、エンドツーエンドのトランザクションの詳細も取得できます。

>[!div class="mx-imgBorder"]
>![サンプルの可用性テストを選択する](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![エンドツーエンド トランザクションの詳細](media/availability-azure-functions/end-to-end.png)

(ビジネス ロジックを追加せずに) すべてをそのまま実行した場合は、テストが失敗したことがわかります。

## <a name="query-in-logs-analytics"></a>ログ (Analytics) でのクエリ

ログ (Analytics) を使用して、可用性の結果、依存関係、その他を見ることができます。 ログの詳細については、[ログ クエリの概要](../logs/log-query-overview.md)に関するページを参照してください。

>[!div class="mx-imgBorder"]
>![可用性の結果](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![依存関係が 50 に制限された [新しいクエリ] タブが表示されたスクリーンショット。](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>次のステップ

- [アプリケーション マップ](./app-map.md)
- [トランザクションの診断](./transaction-diagnostics.md)

