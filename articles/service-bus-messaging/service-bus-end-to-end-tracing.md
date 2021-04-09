---
title: Azure Service Bus のエンドツーエンド トレースと診断 | Microsoft Docs
description: Service Bus のクライアント診断とエンドツーエンド トレース (クライアントから、処理に関連するすべてのサービスまで) の概要。
ms.topic: article
ms.date: 02/03/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 19b284aceb83fbbc2bcf662b2b58941e6a5b36f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99539215"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Service Bus メッセージングを介した分散トレースおよび相関付け

マイクロサービス開発に共通する問題の 1 つは、クライアントからの操作を、処理に関係するすべてのサービスにおいてトレースする機能です。 これは、デバッグ、パフォーマンス分析、A/B テスト、およびその他の一般的な診断シナリオで役立ちます。
この問題の 1 つとして、処理の論理的な部分の追跡があります。 これにはメッセージの処理結果、待機時間、外部依存関係呼び出しが含まれます。 もう 1 つの部分は、これらの診断イベントのプロセス境界を越えた相関付けです。

プロデューサーがキューを介してメッセージを送信すると、通常、そのメッセージは他の論理操作 (他のクライアントまたはサービスが開始した) のスコープ内で発生します。 メッセージを受信すると、同じ操作がコンシューマーによって続行されます。 プロデューサーとコンシューマーの両方 (および操作を処理する他のサービス) は、操作のフローと結果をトレースするためにテレメトリ イベントを出力すると推定されます。 このようなイベントを相関付けて、操作をエンドツーエンドでトレースするために、テレメトリをレポートする各サービスは、すべてのイベントにトレース コンテキストをスタンプする必要があります。

Microsoft Azure Service Bus メッセージングによってペイロード プロパティが定義され、プロデューサーとコンシューマーはこれを使用してトレース コンテキストを渡す必要があります。
プロトコルは [HTTP 相関関係プロトコル](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)に基づいています。

# <a name="azuremessagingservicebus-sdk-latest"></a>[Azure.Messaging.ServiceBus SDK (最新)](#tab/net-standard-sdk-2)
| プロパティ名        | 説明                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostic-Id       | プロデューサーからキューへの外部呼び出しの一意識別子。 論理的根拠、考慮事項、形式について詳しくは、[HTTP プロトコルの Request-Id](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) に関するページをご覧ください。 |

## <a name="service-bus-net-client-autotracing"></a>Service Bus .NET クライアントの自動トレース
[.NET 用 Azure Messaging Service Bus クライアント](/dotnet/api/azure.messaging.servicebus.servicebusprocessor)の `ServiceBusProcessor` クラスにより、トレース インストルメンテーション ポイントが提供され、トレーシング システムまたはクライアント コードの一部でこれを受け取ることができます。 インストルメンテーションによって、すべての呼び出しをクライアント側から Service Bus メッセージング サービスまで追跡できるようになります。 メッセージの処理が [`ServiceBusProcessor` の `ProcessMessageAsync`](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) (メッセージ ハンドラー パターン) を使用して行われる場合は、メッセージの処理もインストルメント化されます。

### <a name="tracking-with-azure-application-insights"></a>Azure Application Insights で追跡する

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) では、自動的な要求と依存関係の追跡も含め、豊富なパフォーマンス監視機能が提供されます。

プロジェクト タイプに応じて次のいずれかの Application Insights SDK をインストールします。
- [ASP.NET](../azure-monitor/app/asp-net.md) - バージョン 2.5-beta2 以上をインストールします
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md) - バージョン 2.2.0-beta2 以上をインストールします。
これらのリンクには、SDK のインストール、リソースの作成、SDK の構成 (必要な場合) に関する説明があります。 ASP.NET 以外のアプリケーションについては、[コンソール アプリケーションのための Azure Application Insights](../azure-monitor/app/console.md) に関する記事をご覧ください。

[`ServiceBusProcessor` の `ProcessMessageAsync`](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) (メッセージ ハンドラー パターン) を使用してメッセージを処理する場合、メッセージの処理もインストルメント化されます。 サービスによって実行されたすべての Service Bus の呼び出しは自動的に追跡され、他のテレメトリ項目と関連付けられます。 それ以外の場合は、手動でのメッセージ追跡について次の例をご覧ください。

#### <a name="trace-message-processing"></a>メッセージ処理のトレース

```csharp
async Task ProcessAsync(ProcessMessageEventArgs args)
{
    ServiceBusReceivedMessage message = args.Message;
    if (message.ApplicationProperties.TryGetValue("Diagnostic-Id", out var objectId) && objectId is string diagnosticId)
    {
        var activity = new Activity("ServiceBusProcessor.ProcessMessage");
        activity.SetParentId(diagnosticId);
        // If you're using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
        using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
        {
            telemetryClient.TrackTrace("Received message");
            try 
            {
            // process message
            }
            catch (Exception ex)
            {
                telemetryClient.TrackException(ex);
                operation.Telemetry.Success = false;
                throw;
            }

            telemetryClient.TrackTrace("Done");
        }
    }
}
```

この例では、処理されるメッセージごとに、タイムスタンプ、期間、結果 (成功) を含む要求テレメトリが報告されます。 テレメトリには、相関関係プロパティのセットも含まれます。 メッセージ処理中に報告された入れ子のトレースと例外にも、`RequestTelemetry` の "子" であることを表す相関関係プロパティがスタンプされます。

サポートされる外部コンポーネントへの呼び出しをメッセージ処理中に行った場合は、それも自動的に追跡されて相関付けられます。 手動での追跡と相関付けについて詳しくは、「[Application Insights .NET SDK でカスタム操作を追跡する](../azure-monitor/app/custom-operations-tracking.md)」をご覧ください。

Application Insights SDK に加えて外部コードを実行している場合は、Application Insights ログを表示するときの **所要時間** が長くなることが予想されます。 

![Application Insights ログの期間が長くなる](./media/service-bus-end-to-end-tracing/longer-duration.png)

これは、メッセージの受信で遅延があったことを意味するわけではありません。 このシナリオでは、メッセージは SDK コードにパラメーターとして渡されるため、メッセージは既に受信されています。 また、App Insights ログの **name** タグ (**Process**) は、メッセージが現在、外部イベント処理コードによって処理されていることを示しています。 この問題は、Azure に関連したものではありません。 メッセージが Service Bus から既に受信されていることを考えると、これらのメトリックは外部コードの効率を意味します。 

### <a name="tracking-without-tracing-system"></a>トレース システムなしで追跡する
ご使用のトレーシング システムで Service Bus 呼び出しの自動追跡がサポートされない場合は、トレーシング システムまたはアプリケーションにそのようなサポートを追加することを検討してください。 このセクションでは、Service Bus .NET クライアントによって送信される診断イベントについて説明します。  

Service Bus .NET クライアントは、.NET トレース プリミティブ [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) および [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) を使用してインストルメント化されます。

`Activity` はトレース コンテキストとして使用され、`DiagnosticSource` は通知メカニズムです。 

DiagnosticSource イベントのリスナーがない場合は、インストルメント化はオフになり、インストルメンテーション コストは 0 のままになります。 DiagnosticSource はすべてのコントロールをリスナーに与えます。
- リスナーは、リッスンするソースとイベントを制御します。
- リスナーは、イベント レートおよびサンプリングを制御します。
- イベントは、すべてのコンテキストを提供するペイロードと一緒に送信されるため、ユーザーがイベント中にメッセージ オブジェクトにアクセスして変更できます。

実装を進める前に、[DiagnosticSource ユーザー ガイド](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)を読むことをお勧めします。

Microsoft.Extension.Logger でログを書き込む ASP.NET Core アプリに、Service Bus イベントのリスナーを作成します。
これは [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) ライブラリを使用して DiagnosticSource をサブスクライブします (このライブラリなしでも簡単に DiagnosticSource をサブスクライブできます)。

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Azure.Messaging.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Azure.Messaging.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

この例では、リスナーが、Service Bus の各操作の期間、結果、一意識別子、開始時刻を記録します。

### <a name="events"></a>events
すべてのイベントには、公開されているテレメトリ仕様 (https://github.com/open-telemetry/opentelemetry-specification/blob/master/specification/trace/api.md ) に準拠する次のプロパティがあります。

- `message_bus.destination` – キュー/トピック/サブスクリプションのパス
- `peer.address` – 完全修飾名前空間
- `kind` – producer、consumer、または client のいずれか。 メッセージを送信する場合は producer、受信する場合は consumer、設定する場合は client を使用します。
- `component` – `servicebus`

すべてのイベントに 'Entity' と 'Endpoint' の各プロパティもありますが、次の表では省略しています。
  * `string Entity` - エンティティ (キュー、トピックなど) の名前
  * `Uri Endpoint` - Service Bus エンドポイント URL

### <a name="instrumented-operations"></a>インストルメント化された操作
インストルメント化された操作の全一覧を次に示します。

| 操作の名前 | 追跡される API |
| -------------- | ----------- | 
| ServiceBusSender.Send | ServiceBusSender.SendMessageAsync<br/>ServiceBusSender.SendMessagesAsync |
| ServiceBusSender.Schedule | ServiceBusSender.ScheduleMessageAsync<br/>ServiceBusSender.ScheduleMessagesAsync | 
| ServiceBusSender.Cancel | ServiceBusSender.CancelScheduledMessageAsync<br/>ServiceBusSender.CancelScheduledMessagesAsync |
| ServiceBusReceiver.Receive | ServiceBusReceiver.ReceiveMessageAsync<br/>ServiceBusReceiver.ReceiveMessagesAsync |
| ServiceBusReceiver.ReceiveDeferred | ServiceBusReceiver.ReceiveDeferredMessagesAsync |
| ServiceBusReceiver.Peek | ServiceBusReceiver.PeekMessageAsync<br/>ServiceBusReceiver.PeekMessagesAsync |
| ServiceBusReceiver.Abandon | ServiceBusReceiver.AbandonMessagesAsync |
| ServiceBusReceiver.Complete | ServiceBusReceiver.CompleteMessagesAsync |
| ServiceBusReceiver.DeadLetter | ServiceBusReceiver.DeadLetterMessagesAsync |
| ServiceBusReceiver.Defer |  ServiceBusReceiver.DeferMessagesAsync |
| ServiceBusReceiver.RenewMessageLock | ServiceBusReceiver.RenewMessageLockAsync |
| ServiceBusSessionReceiver.RenewSessionLock | ServiceBusSessionReceiver.RenewSessionLockAsync |
| ServiceBusSessionReceiver.GetSessionState | ServiceBusSessionReceiver.GetSessionStateAsync |
| ServiceBusSessionReceiver.SetSessionState | ServiceBusSessionReceiver.SetSessionStateAsync |
| ServiceBusProcessor.ProcessMessage | ServiceBusProcessor で設定されるプロセッサ コールバック。 ProcessMessageAsync プロパティ |
| ServiceBusSessionProcessor.ProcessSessionMessage | ServiceBusSessionProcessor で設定されるプロセッサ コールバック。 ProcessMessageAsync プロパティ |

### <a name="filtering-and-sampling"></a>フィルター処理およびサンプリング

場合によっては、パフォーマンスのオーバーヘッドや記憶域の消費量を抑えるためにイベントの一部分のみを記録する必要があります。 'Stop' イベントのみを記録するか (前の例と同様)、イベントの一定の割合をサンプリングします。 
`DiagnosticSource` では、`IsEnabled` 術後を使用してこれを実現する方法が提供されます。 詳しくは、[DiagnosticSource でのコンテキストに基づくフィルター処理](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering)に関する記事をご覧ください。

`IsEnabled` を 1 つの操作で複数回呼び出して、パフォーマンスへの影響を最小限にすることができます。

`IsEnabled` は次のように呼び出します。

1. `IsEnabled(<OperationName>, string entity, null)` を、たとえば `IsEnabled("ServiceBusSender.Send", "MyQueue1")` と指定します。 最後に 'Start' も 'Stop' もないことに注意してください。 これを使用して、特定の操作またはキューをフィルター処理で除外します。 コールバック メソッドから `false` が返されると、その操作のイベントは送信されません。

   * 'Process' 操作および 'ProcessSession' 操作では `IsEnabled(<OperationName>, string entity, Activity activity)` コールバックも受け取ります。 これを使用して、`activity.Id` または Tags プロパティに基づいてイベントをフィルター処理します。
  
2. `IsEnabled(<OperationName>.Start)` を、たとえば `IsEnabled("ServiceBusSender.Send.Start")` と指定します。 'Start' イベントが起動されるかどうかを確認します。 この結果は 'Start' イベントのみに影響し、それ以降のインストルメンテーションがそれに依存することはありません。

'Stop' イベントに対する `IsEnabled` はありません。

一部の操作の結果が例外になると、`IsEnabled("ServiceBusSender.Send.Exception")` が呼び出されます。 'Exception' イベントをサブスクライブするだけで、その後のインストルメンテーションを防ぐことができます。 このケースでは、例外も処理する必要があります。 他のインストルメンテーションが無効になっているため、トレース コンテキストがメッセージと一緒にコンシューマーからプロデューサーに送られることを期待しないでください。

`IsEnabled` を使用して、サンプリング戦略を実装することもできます。 `Activity.Id` または `Activity.RootId` に基づくサンプリングにより、すべての層で一貫性のあるサンプリングが保証されます (トレーシング システムまたはユーザー独自のコードによって伝達される場合)。

同じソースに対して複数の `DiagnosticSource` リスナーが存在するとき、イベントを受け取るには 1 つのリスナーだけで十分です。したがって、`IsEnabled` の呼び出しは保証されません。



# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

| プロパティ名        | 説明                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostic-Id       | プロデューサーからキューへの外部呼び出しの一意識別子。 論理的根拠、考慮事項、形式について詳しくは、[HTTP プロトコルの Request-Id](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) に関するページをご覧ください。 |
|  Correlation-Context | 操作の処理に関連するすべてのサービスに伝達される操作コンテキスト。 詳しくは、[HTTP プロトコルの Correlation-Context](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) に関するページをご覧ください。 |

## <a name="service-bus-net-client-autotracing"></a>Service Bus .NET クライアントの自動トレース

バージョン 3.0.0 以降の [Microsoft Azure ServiceBus Client for .NET](/dotnet/api/microsoft.azure.servicebus.queueclient) ではトレース インストルメンテーション ポイントが提供され、トレーシング システムまたはクライアント コードの一部でこれを受け取ることができます。
インストルメンテーションによって、すべての呼び出しをクライアント側から Service Bus メッセージング サービスまで追跡できるようになります。 メッセージの処理が[メッセージ ハンドラー パターン](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler)を使用して行われる場合は、メッセージの処理もインストルメント化されます。

### <a name="tracking-with-azure-application-insights"></a>Azure Application Insights で追跡する

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) では、自動的な要求と依存関係の追跡も含め、豊富なパフォーマンス監視機能が提供されます。

プロジェクト タイプに応じて次のいずれかの Application Insights SDK をインストールします。
- [ASP.NET](../azure-monitor/app/asp-net.md) - バージョン 2.5-beta2 以上をインストールします
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md) - バージョン 2.2.0-beta2 以上をインストールします。
これらのリンクには、SDK のインストール、リソースの作成、SDK の構成 (必要な場合) に関する説明があります。 ASP.NET 以外のアプリケーションについては、[コンソール アプリケーションのための Azure Application Insights](../azure-monitor/app/console.md) に関する記事をご覧ください。

[メッセージ ハンドラー パターン](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler)を使用してメッセージを処理すると完了です。サービスによって実行されたすべての Service Bus の呼び出しは自動的に追跡され、他のテレメトリ項目と関連付けられます。 それ以外の場合は、手動でのメッセージ追跡について次の例をご覧ください。

#### <a name="trace-message-processing"></a>メッセージ処理のトレース

```csharp
private readonly TelemetryClient telemetryClient;

async Task ProcessAsync(Message message)
{
    var activity = message.ExtractActivity();
    
    // If you're using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
    using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
    {
        telemetryClient.TrackTrace("Received message");
        try 
        {
           // process message
        }
        catch (Exception ex)
        {
             telemetryClient.TrackException(ex);
             operation.Telemetry.Success = false;
             throw;
        }

        telemetryClient.TrackTrace("Done");
   }
}
```

この例では、処理されるメッセージごとに、タイムスタンプ、期間、結果 (成功) を含む `RequestTelemetry` が報告されます。 テレメトリには、相関関係プロパティのセットも含まれます。
メッセージ処理中に報告された入れ子のトレースと例外にも、`RequestTelemetry` の "子" であることを表す相関関係プロパティがスタンプされます。

サポートされる外部コンポーネントへの呼び出しをメッセージ処理中に行った場合は、それも自動的に追跡されて相関付けられます。 手動での追跡と相関付けについて詳しくは、「[Application Insights .NET SDK でカスタム操作を追跡する](../azure-monitor/app/custom-operations-tracking.md)」をご覧ください。

Application Insights SDK に加えて外部コードを実行している場合は、Application Insights ログを表示するときの **所要時間** が長くなることが予想されます。 

![Application Insights ログの期間が長くなる](./media/service-bus-end-to-end-tracing/longer-duration.png)

これは、メッセージの受信で遅延があったことを意味するわけではありません。 このシナリオでは、メッセージは SDK コードにパラメーターとして渡されるため、メッセージは既に受信されています。 また、App Insights ログの **name** タグ (**Process**) は、メッセージが現在、外部イベント処理コードによって処理されていることを示しています。 この問題は、Azure に関連したものではありません。 メッセージが Service Bus から既に受信されていることを考えると、これらのメトリックは外部コードの効率を意味します。 [GitHub のこのファイル](https://github.com/Azure/azure-sdk-for-net/blob/4bab05144ce647cc9e704d46d3763de5f9681ee0/sdk/servicebus/Microsoft.Azure.ServiceBus/src/ServiceBusDiagnosticsSource.cs)を参照して、メッセージが Service Bus から受信された後に **Process** タグが生成されて割り当てられていることを確認してください。 

### <a name="tracking-without-tracing-system"></a>トレース システムなしで追跡する
ご使用のトレーシング システムで Service Bus 呼び出しの自動追跡がサポートされない場合は、トレーシング システムまたはアプリケーションにそのようなサポートを追加することを検討してください。 このセクションでは、Service Bus .NET クライアントによって送信される診断イベントについて説明します。  

Service Bus .NET クライアントは、.NET トレース プリミティブ [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) および [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) を使用してインストルメント化されます。

`Activity` はトレース コンテキストとして使用され、`DiagnosticSource` は通知メカニズムです。 

DiagnosticSource イベントのリスナーがない場合は、インストルメント化はオフになり、インストルメンテーション コストは 0 のままになります。 DiagnosticSource はすべてのコントロールをリスナーに与えます。
- リスナーは、リッスンするソースとイベントを制御します。
- リスナーは、イベント レートおよびサンプリングを制御します。
- イベントは、すべてのコンテキストを提供するペイロードと一緒に送信されるため、ユーザーがイベント中にメッセージ オブジェクトにアクセスして変更できます。

実装を進める前に、[DiagnosticSource ユーザー ガイド](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)を読むことをお勧めします。

Microsoft.Extension.Logger でログを書き込む ASP.NET Core アプリに、Service Bus イベントのリスナーを作成します。
これは [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) ライブラリを使用して DiagnosticSource をサブスクライブします (このライブラリなしでも簡単に DiagnosticSource をサブスクライブできます)。

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Microsoft.Azure.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Microsoft.Azure.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

この例では、リスナーが、Service Bus の各操作の期間、結果、一意識別子、開始時刻を記録します。

#### <a name="events"></a>events

どの操作でも、次の 2 つのイベントが送信されます:"Start" と "Stop"。 おそらく、ユーザーにとって関心があるのは 'Stop' イベントのみです。 これにより、操作の結果、および開始時刻と所要時間が Activity プロパティとして提供されます。

イベントのペイロードがリスナーに操作のコンテキストを提供すると、リスナーが API 入力パラメーターと戻り値をレプリケートします。 'Stop' イベントのペイロードは、'Start' イベントのペイロードのプロパティをすべて含んでいるため、'Start' イベントは完全に無視することができます。

すべてのイベントに 'Entity' と 'Endpoint' の各プロパティもありますが、次の表では省略しています。
  * `string Entity` - エンティティ (キュー、トピックなど) の名前
  * `Uri Endpoint` - Service Bus エンドポイント URL

各 'Stop'イベントには、非同期操作の完了に関する `TaskStatus` を含む `Status` プロパティがあります。これも、わかりやすくするために次の表では省略しています。

インストルメント化された操作の全一覧を次に示します。

| 操作の名前 | 追跡される API | 固有のペイロード プロパティ|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages` - 送信されるメッセージの一覧 |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message` - 処理されるメッセージ<br/>`DateTimeOffset ScheduleEnqueueTimeUtc` - スケジュールされたメッセージのオフセット<br/>`long SequenceNumber` - スケジュールされたメッセージのシーケンス番号 ('Stop' イベント ペイロード) |
| Microsoft.Azure.ServiceBus.Cancel | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber` - 取り消されるメッセージのシーケンス番号 | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount` - 受信できたメッセージの最大数<br/>`IList<Message> Messages` - 受信したメッセージの一覧 ('Stop' イベント ペイロード) |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber` - メッセージのバッチの参照を開始する始点<br/>`int RequestedMessageCount` - 取得するメッセージの最大数<br/>`IList<Message> Messages` - 受信したメッセージの一覧 ('Stop' イベント ペイロード) |
| Microsoft.Azure.ServiceBus.ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers` - 受信するシーケンス番号を含む一覧<br/>`IList<Message> Messages` - 受信したメッセージの一覧 ('Stop' イベント ペイロード) |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens` - 完了させるメッセージに対応するロック トークンを含む一覧|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken` - 中止するメッセージに対応するロック トークン |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken` - 保留するメッセージに対応するロック トークン | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken` - 配信不能に対応するメッセージのロック トークン | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken` - ロックの更新に対応するメッセージのロック トークン<br/>`DateTime LockedUntilUtc` - 新しいロック トークンの有効期限の日時 (UTC 形式) ('Stop' イベント ペイロード)|
| Microsoft.Azure.ServiceBus.Process | [IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) で提供されるメッセージ ハンドラーのラムダ関数 | `Message Message` - 処理されるメッセージ |
| Microsoft.Azure.ServiceBus.ProcessSession | [IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) で提供されるメッセージ せしょん ハンドラーのラムダ関数 | `Message Message` - 処理されるメッセージ<br/>`IMessageSession Session` - 処理されるセッション |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule` - 追加するルールを提供するルールの説明 |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName`- 削除するルールの名前 |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules`- サブスクリプションに関連付けられているすべてのルール ('Stop' ペイロードのみ) |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId` - メッセージ内に存在する sessionId |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId` - メッセージ内に存在する sessionId<br/>`byte [] State` - セッション状態 ('Stop' イベント ペイロード) |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId` - メッセージ内に存在する sessionId<br/>`byte [] State` - セッションの状態 |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId` - メッセージ内に存在する sessionId |
| Microsoft.Azure.ServiceBus.Exception | インストルメント化された任意の API| `Exception Exception` - 例外インスタンス |

すべてのイベントで、現在の操作コンテキストを保持する `Activity.Current` にアクセスできます。

#### <a name="logging-more-properties"></a>その他のプロパティのログ記録

`Activity.Current` は、現在の操作とその親について詳しいコンテキストを提供します。 詳細については、[Activity のドキュメント](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)を参照してください。
Service Bus インストルメンテーションによって、追加の情報が `Activity.Current.Tags` で提供されます。`MessageId` と `SessionId` がある場合、これらは常に含まれます。

'Receive'、'Peek'、'ReceiveDeferred' の各イベントを追跡するアクティビティには、`RelatedTo` タグが含まれることもあります。 これは、結果として受信されたメッセージの `Diagnostic-Id` が重複しないリストを保持します。
そのような操作の結果として、関係しないいくつかのメッセージが受信される場合があります。 また、操作の開始時には `Diagnostic-Id` は不明です。そのため、'Receive' 操作を 'Process' 操作に関連付けるには、このタグを使用するしかありません。 パフォーマンスの問題を分析するときは、メッセージの受信にどれだけ時間がかかったかを調べると役立ちます。

タグを記録する効率的な方法は反復処理することです。タグを前の例に追加すると次のようになります。 

```csharp
Activity currentActivity = Activity.Current;
TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");

var tagsList = new StringBuilder();
foreach (var tags in currentActivity.Tags)
{
    tagsList.Append($", {tags.Key}={tags.Value}");
}

serviceBusLogger.LogInformation($"{currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}{tagsList}");
```

#### <a name="filtering-and-sampling"></a>フィルター処理およびサンプリング

場合によっては、パフォーマンスのオーバーヘッドや記憶域の消費量を抑えるためにイベントの一部分のみを記録する必要があります。 'Stop' イベントのみを記録するか (前の例と同様)、イベントの一定の割合をサンプリングします。 
`DiagnosticSource` では、`IsEnabled` 術後を使用してこれを実現する方法が提供されます。 詳しくは、[DiagnosticSource でのコンテキストに基づくフィルター処理](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering)に関する記事をご覧ください。

`IsEnabled` を 1 つの操作で複数回呼び出して、パフォーマンスへの影響を最小限にすることができます。

`IsEnabled` は次のように呼び出します。

1. `IsEnabled(<OperationName>, string entity, null)` を、たとえば `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")` と指定します。 最後に 'Start' も 'Stop' もないことに注意してください。 これを使用して、特定の操作またはキューをフィルター処理で除外します。 コールバック メソッドから `false` が返されると、その操作のイベントは送信されません。

   * 'Process' 操作および 'ProcessSession' 操作では `IsEnabled(<OperationName>, string entity, Activity activity)` コールバックも受け取ります。 これを使用して、`activity.Id` または Tags プロパティに基づいてイベントをフィルター処理します。
  
2. `IsEnabled(<OperationName>.Start)` を、たとえば `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")` と指定します。 'Start' イベントが起動されるかどうかを確認します。 この結果は 'Start' イベントのみに影響し、それ以降のインストルメンテーションがそれに依存することはありません。

'Stop' イベントに対する `IsEnabled` はありません。

一部の操作の結果が例外になると、`IsEnabled("Microsoft.Azure.ServiceBus.Exception")` が呼び出されます。 'Exception' イベントをサブスクライブするだけで、その後のインストルメンテーションを防ぐことができます。 このケースでは、例外も処理する必要があります。 他のインストルメンテーションが無効になっているため、トレース コンテキストがメッセージと一緒にコンシューマーからプロデューサーに送られることを期待しないでください。

`IsEnabled` を使用して、サンプリング戦略を実装することもできます。 `Activity.Id` または `Activity.RootId` に基づくサンプリングにより、すべての層で一貫性のあるサンプリングが保証されます (トレーシング システムまたはユーザー独自のコードによって伝達される場合)。

同じソースに対して複数の `DiagnosticSource` リスナーが存在するとき、イベントを受け取るには 1 つのリスナーだけで十分です。したがって、`IsEnabled` の呼び出しは保証されません。

---

## <a name="next-steps"></a>次のステップ

* [Application Insights の相関付け](../azure-monitor/app/correlation.md)
* [Application Insights による依存関係の監視](../azure-monitor/app/asp-net-dependencies.md): REST、SQL、その他の外部リソースによる処理速度の低下が発生しているかどうかを確認します。
* [Application Insights .NET SDK でカスタム操作を追跡する](../azure-monitor/app/custom-operations-tracking.md)

