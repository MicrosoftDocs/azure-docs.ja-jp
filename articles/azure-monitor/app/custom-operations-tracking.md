---
title: Azure Application Insights .NET SDK でカスタム操作を追跡する | Microsoft Docs
description: Azure Application Insights .NET SDK でカスタム操作を追跡する
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/30/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 2c33c481d96a9edecc6360a9a91c095c2bca220b
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798344"
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Application Insights .NET SDK でカスタム操作を追跡する

Application Insights SDK は、受信 HTTP 要求および依存関係にあるサービスへの呼び出し (HTTP 要求や SQL クエリなど) を自動的に追跡します。 要求と依存関係の追跡と関連付けによってアプリケーションを構成しているすべてのマイクロサービスの応答性と信頼性を確認することで、アプリケーション全体の状態を把握することができます。 

ただし、一律に対応することのできない類のアプリケーション パターンも存在します。 そうしたパターンを適切に監視するためには、手動によるコードのインストルメンテーションが必要となります。 この記事では、手動のインストルメンテーションを必要とする可能性があるいくつかのパターン (カスタムのキュー処理や実行時間の長いバックグラウンド タスクの実行など) を取り上げます。

このドキュメントでは、ApplicationInsights SDK を使用してカスタム操作を追跡する方法についてのガイドラインを示します。 このドキュメントの対象を以下に示します。

- Application Insights for .NET (別名 Base SDK) バージョン 2.4 以降。
- Application Insights for Web Applications (ASP.NET 実行) バージョン 2.4 以降。
- Application Insights for ASP.NET Core バージョン 2.1 以降。

## <a name="overview"></a>概要
操作は、アプリケーションによって実行される処理の 1 つの論理部分です。 操作には、名前、開始時刻、継続時間、および実行コンテキストがあります (ユーザー名、プロパティ、結果など)。 操作 A が 操作 B によって開始された場合は、操作 B が A の親として設定されます。1 つの操作は、親を 1 つだけ持つことができますが、複数の子操作を持つことができます。 操作とテレメトリの関連付けの詳細については、「[Application Insights におけるテレメトリの相関付け](correlation.md)」を参照してください。

Application Insights .NET SDK では、操作は、抽象クラス [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) とその子孫である [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) と [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs) によって表されます。

## <a name="incoming-operations-tracking"></a>受信操作の追跡 
Application Insights Web SDK は、IIS パイプラインで実行される ASP.NET アプリケーションとすべての ASP.NET Core アプリケーションを対象に、HTTP 要求を自動的に収集します。 それ以外のプラットフォームとフレームワークについては、コミュニティによってサポートされるソリューションが存在します。 ただし、標準のソリューションとコミュニティによってサポートされるソリューションの対象外であるアプリケーションについては、独自にインストルメント化することができます。

キューからアイテムを受け取る worker も、独自の追跡が必要なケースです。 キューによっては、そのキューにメッセージを追加する呼び出しが、依存関係として追跡されます。 ただし、メッセージの処理を記述する上位の操作については、自動的には収集されません。

そのような操作がどのように追跡されるかを見てみましょう。

簡潔に言うと、このタスクとは、`RequestTelemetry` を作成して既知のプロパティを設定することです。 操作が完了したら、このテレメトリを追跡します。 次の例は、このタスクを示しています。

### <a name="http-request-in-owin-self-hosted-app"></a>Owin 自己ホスト型アプリにおける HTTP 要求
この例では、トレース コンテキストは、[関連付け用の HTTP プロトコル](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)に従って反映されます。 ここに記載されているヘッダーが受信されることを予期してください。

```csharp
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    // you may create a new TelemetryConfiguration instance, reuse one you already have
    // or fetch the instance created by Application Insights SDK.
    private readonly TelemetryConfiguration telemetryConfiguration = TelemetryConfiguration.CreateDefault();
    private readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry.
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // If there is a Request-Id received from the upstream service, set the telemetry context accordingly.
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows correlation of 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry items.
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // Process the request.
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // Update status code and success as appropriate.
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // Now it's time to stop the operation (and track telemetry).
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // Returns the root ID from the '|' to the first '.' if any.
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

この関連付け用の HTTP プロトコルも、`Correlation-Context` ヘッダーを宣言しますが、 ここでは、簡潔にするために省略されています。

## <a name="queue-instrumentation"></a>キューのインストルメンテーション
関連付けの詳細を HTTP 要求で渡す[関連付け用の HTTP プロトコル](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)はありますが、各キュー プロトコルで、同じ詳細をキュー メッセージに沿って渡す方法を定義する必要があります。 一部のキュー プロトコル (AMQP など) は、追加のメタデータを渡すことを許可しています。また、コンテキストをメッセージ ペイロードにエンコードする必要があるキュー プロトコルもあります (Azure Storage Queue など)。

### <a name="service-bus-queue"></a>Service Bus キュー
Application Insights は、新しい [Microsoft Azure ServiceBus Client for .NET](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) バージョン 3.0.0 以降で Service Bus Messaging の呼び出しを追跡します。
[メッセージ ハンドラー パターン](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler)を使用してメッセージを処理すると完了です。サービスによって実行されたすべての Service Bus の呼び出しは自動的に追跡され、他のテレメトリ項目と関連付けられます。 手動でメッセージを処理する場合は、[Microsoft Application Insights による Service Bus クライアントのトレース](../../service-bus-messaging/service-bus-end-to-end-tracing.md)に関するページを参照してください。

[WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) パッケージを使用している場合は以下を参照してください。Service Bus キューは AMQP プロトコルを使用し、Application Insights はキュー操作を自動的に追跡しないため、次の例では、Service Bus の呼び出しを追跡する (そして関連付ける) 方法を示します。
相関 ID はメッセージ プロパティで渡されます。

#### <a name="enqueue"></a>Enqueue

```csharp
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // Service Bus queue allows the property bag to pass along with the message.
    // We will use them to pass our correlation identifiers (and other context)
    // to the consumer.
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>Process
```csharp
public async Task Process(BrokeredMessage message)
{
    // After the message is taken from the queue, create RequestTelemetry to track its processing.
    // It might also make sense to get the name from the message.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Azure Storage キュー
[Azure Storage キュー](../../storage/queues/storage-dotnet-how-to-use-queues.md)の操作を追跡し、プロデューサー、コンシューマー、Azure Storage 間でテレメトリを相互に関連付ける例を次に示します。 

Storage キューには HTTP API があります。 キューに対するすべての呼び出しは、Application Insights の HTTP 要求の依存関係コレクターによって追跡されます。
ASP.NET および ASP.NET Core アプリケーションでは既定で構成されます。他の種類のアプリケーションでの構成については、[コンソール アプリケーションに関するドキュメント](../../azure-monitor/app/console.md)を参照してください。

Application Insights の操作 ID を Storage の要求 ID に関連付けることもできます。 Storage の要求クライアントとサーバーの要求 ID の設定および取得方法については、「[Azure Storage の監視、診断、およびトラブルシューティング](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing)」を参照してください。

#### <a name="enqueue"></a>Enqueue
Storage キューは HTTP API をサポートしているため、キューを使ったすべての操作は自動的に ApplicationInsights によって追跡されます。 多くのケースには、このインストルメンテーションで対応することができます。 ただし、コンシューマー側のトレースとプロデューサー側のトレースを相互に関連付けるには、関連付け用の HTTP プロトコルでの実行方法に似た関連付けコンテキストを渡す必要があります。 

この例は、`Enqueue` 操作を追跡する方法を示しています。 次のようにすることができます。

 - **再試行を関連付ける (存在する場合)** :すべての再試行には、`Enqueue` 操作という共通の親が 1 つ存在します。 また、受信要求の子として追跡されます。 キューに対する論理要求が複数存在する場合、どの呼び出しが再試行されたかを見極めることは難しいことがあります。
 - **Storage ログを関連付ける (存在する場合に必要に応じて)** :Storage ログは Application Insights のテレメトリに関連付けられます。

`Enqueue` 操作は、親操作 (受信 HTTP 要求など) の子です。 HTTP の依存関係呼び出しは、`Enqueue` 操作の子であり、受信要求の孫でもあります。

```csharp
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload.
    // For example, if you choose to pass payload serialized to JSON, it might look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Storage logs and Application Insights telemetry.
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}  
```

アプリケーションから報告されるテレメトリの量を減らすため、またはその他の理由で `Enqueue` 操作を追跡しない場合は、`Activity` API を直接使用します。

- Application Insights 操作を開始する代わりに、新しい `Activity` を作成 (および開始) します。 操作名以外のプロパティを割り当てる必要は*ありません*。
- `operation.Telemetry.Id` ではなく `yourActivity.Id` をメッセージ ペイロードに対してシリアル化します。 `Activity.Current.Id` を使用することもできます。


#### <a name="dequeue"></a>Dequeue
`Enqueue` と同様、Storage キューに対する実際の HTTP 要求は Application Insights によって自動的に追跡されます。 ただし、`Enqueue` 操作の発生源は親のコンテキスト (受信要求のコンテキストなど) であると推測できます。 そのような操作 (および HTTP 部分) は、Application Insights SDK によって自動的に親要求や同じスコープ内で報告される他のテレメトリと相互に関連付けられます。

`Dequeue` 操作は注意が必要です。 Application Insights SDK は、自動的に HTTP 要求を追跡します。 ただし、メッセージを解析するまでは、関連付けのコンテキストは不明です。 メッセージを取得するための HTTP 要求を他のテレメトリに関連付けることは不可能です。

多くの場合、他の追跡と同じように、HTTP 要求をキューに関連付けると有用である可能性があります。 これを行う方法を次の例に示します。

```csharp
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var telemetry = new DependencyTelemetry
    {
        Type = "Queue",
        Name = "Dequeue " + queue.Name
    };

    telemetry.Start();

    try
    {
        var message = await queue.GetMessageAsync();

        if (message != null)
        {
            var payload = JsonConvert.DeserializeObject<MessagePayload>(message.AsString);

            // If there is a message, we want to correlate the Dequeue operation with processing.
            // However, we will only know what correlation ID to use after we get it from the message,
            // so we will report telemetry after we know the IDs.
            telemetry.Context.Operation.Id = payload.RootId;
            telemetry.Context.Operation.ParentId = payload.ParentId;

            // Delete the message.
            return payload;
        }
    }
    catch (StorageException e)
    {
        telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        telemetry.Success = false;
        telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetry.Stop();
        telemetryClient.Track(telemetry);
    }

    return null;
}
```

#### <a name="process"></a>Process

次の例では、受信メッセージは受信 HTTP 要求と同様の方法で追跡されます。

```csharp
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };
    // It might also make sense to get the name from the message.
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

他のキュー操作も、同じようにインストルメント化できます。 Peek 操作をインストルメント化する場合は、デキューと同様の方法を使用する必要があります。 キューの管理操作をインストルメント化する必要はありません。 HTTP などの操作は Application Insights によって追跡されます。ほとんどの場合はそれで十分です。

メッセージの削除をインストルメント化するときは、必ず操作 (関連付け) ID を設定してください。 別の方法として、`Activity` API を使用することもできます。 その場合は、テレメトリ項目に対する操作 ID を自分で設定する必要はありません。それは Application Insights SDK によって自動的に行われます。

- キューから項目を取得した後、新しい `Activity` を作成します。
- `Activity.SetParentId(message.ParentId)` を使用して、コンシューマーとプロデューサーのログを相互に関連付けます。
- `Activity` を開始します。
- `Start/StopOperation` ヘルパーを使用して、Dequeue、Process、Delete の各操作を追跡します。 追跡は、同じ非同期制御フロー (実行コンテキスト) から行ってください。 そうすることで、相互の関連付けが適切に行われます。
- `Activity` を停止します。
- `Start/StopOperation` を使用するか、`Track` テレメトリを直接呼び出します。

### <a name="batch-processing"></a>バッチ処理
一部のキューでは、1 つの要求で複数のメッセージをデキューできます。 このようなメッセージの処理には依存関係はなく、異なる論理操作に属していると推測されます。 この場合、`Dequeue` 操作を特定のメッセージ処理に関連付けることはできません。

各メッセージは、独自の非同期制御フローの中で処理する必要があります。 詳細については、「[出力方向の依存関係の追跡](#outgoing-dependencies-tracking)」セクションを参照してください。

## <a name="long-running-background-tasks"></a>長時間実行されるバックグラウンド タスク

アプリケーションの中には、ユーザーの要求によって発生する、長時間実行される操作を開始するものがあります。 トレース/インストルメンテーションの観点から見ると、これは要求や依存関係のインストルメンテーションと変わりません。 

```csharp
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // Process the task.
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // Update status code and success as appropriate.
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Update status code and success as appropriate.
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

この例では、`telemetryClient.StartOperation` で `DependencyTelemetry` を作成し、関連付けのコンテキストを設定しています。 操作のスケジュールが設定された受信要求によって作成された親操作があるとします。 `BackgroundTask` が受信要求と同じ非同期制御フローで開始されていれば、受信要求はその親操作と関連付けられます。 `BackgroundTask` と、入れ子になっているすべてのテレメトリ項目は、(要求の終了後も) それを発生させた要求と自動的に関連付けられます。

いずれの操作 (`Activity`) も関連付けられていないバックグラウンド スレッドからタスクが開始された場合、`BackgroundTask` には親が存在しません。 ただし、入れ子になった操作が存在する可能性があります。 そのタスクから報告されるすべてのテレメトリ項目は、`BackgroundTask` で作成された `DependencyTelemetry` に関連付けられます。

## <a name="outgoing-dependencies-tracking"></a>出力方向の依存関係の追跡
Application Insights がサポートしていない独自の依存関係や操作を追跡することができます。

このようなカスタム追跡の例として、Service Bus キューまたは Azure Storage キューの `Enqueue` メソッドを挙げることができます。

カスタム依存関係の追跡に使用される一般的な手法は次のとおりです。

- 関連付けに必要な `DependencyTelemetry` のプロパティとその他のプロパティ (開始のタイムスタンプ、継続時間) の値を設定する `TelemetryClient.StartOperation` (拡張機能) メソッドを呼び出します。
- `DependencyTelemetry` のその他のカスタム プロパティ (名前やその他の必要なプロパティ) を設定します。
- 依存関係呼び出しを行い、応答が返るまで待機します。
- 操作が完了したら、`StopOperation` を使用して操作を停止します。
- 例外を処理します。

```csharp
public async Task RunMyTaskAsync()
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1"))
    {
        try 
        {
            var myTask = await StartMyTaskAsync();
            // Update status code and success as appropriate.
        }
        catch(...) 
        {
            // Update status code and success as appropriate.
        }
    }
}
```

操作を破棄すると操作が停止されるため、`StopOperation` を呼び出す代わりに実行することもできます。

*警告*: 場合によっては、未処理の例外で `finally` が[呼び出されなくなる](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-finally)ことがあるため、操作が追跡されない可能性があります。

### <a name="parallel-operations-processing-and-tracking"></a>並列操作の処理と追跡

`StopOperation` だけが開始された操作を停止できます。 現在実行中の操作が停止する操作と一致しない場合、`StopOperation` は何も行いません。 この状況は、同じ実行コンテキストで複数の操作を並行して開始した場合に起こることがあります。

```csharp
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// FAILURE!!! This will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active.
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

並行して実行されている操作を分離するには、常に同じ **async** メソッドで `StartOperation` を呼び出して操作を処理する必要があります。 操作が同期の場合 (つまり非同期ではない場合) は、プロセスをラップし、`Task.Run` を使用して追跡します。

```csharp
public void RunMyTask(string name)
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>(name))
    {
        Process();
        // Update status code and success as appropriate.
    }
}

public async Task RunAllTasks()
{
    var task1 = Task.Run(() => RunMyTask("task 1"));
    var task2 = Task.Run(() => RunMyTask("task 2"));
    
    await Task.WhenAll(task1, task2);
}
```

## <a name="next-steps"></a>次の手順

- Application Insights における[テレメトリの相関付け](correlation.md)について基本的な知識を身に付けます。
- Application Insights の型とデータ モデルについては、[データ モデル](../../azure-monitor/app/data-model.md)に関するページを参照してください。
- カスタムの[イベントとメトリック](../../azure-monitor/app/api-custom-events-metrics.md)を Application Insights にレポートします。
- コンテキスト プロパティ コレクションの標準的な[構成](configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)を確認します。
- テレメトリを相互に関連付ける方法を [System.Diagnostics.Activity ユーザー ガイド](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)で確認します。
