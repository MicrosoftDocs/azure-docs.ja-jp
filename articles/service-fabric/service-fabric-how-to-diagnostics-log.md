---
title: .NET アプリからのログ イベントを生成する
description: Azure クラスターまたはスタンドアロン クラスターでホストされているご自身の .NET Service Fabric アプリケーションにログ記録を追加する方法について説明します。
author: srrengar
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: srrengar
ms.custom: devx-track-csharp
ms.openlocfilehash: a36425acf42a469c7f48b2e954bdacfdfcce1b10
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "89011482"
---
# <a name="add-logging-to-your-service-fabric-application"></a>Service Fabric アプリケーションにログ記録を追加する

ご利用のアプリケーションでは、問題が発生したときにフォレンジックなデバッグを実行できるだけの十分な情報を提供する必要があります。 ログ記録は、使用している Service Fabric アプリケーションに追加できる最も重要な機能の 1 つです。 問題が発生したとき、適切なログ記録は、エラーを調査するうえで役に立ちます。 ログのパターンを分析することで、お使いのアプリケーションのパフォーマンスや設計を改善する方法が見つかる場合があります。 このドキュメントでは、さまざまなログ記録オプションをいくつか紹介します。

## <a name="eventflow"></a>EventFlow

[EventFlow ライブラリ](https://github.com/Azure/diagnostics-eventflow) スイートを使用すると、収集する診断データとその出力先をアプリケーションで定義することができます。 パフォーマンス カウンターからアプリケーション トレースまで、あらゆるデータを診断データとして使用できます。 アプリケーションと同じプロセスで実行されるため、通信のオーバーヘッドも最小限に抑えられます。 EventFlow および Service Fabric の詳細については、[EventFlow を使用した Azure Service Fabric イベントの集計](service-fabric-diagnostics-event-aggregation-eventflow.md)に関するページをご覧ください。

### <a name="using-structured-eventsource-events"></a>構造化された EventSource イベントの使用

ユース ケースによってメッセージ イベントを定義すると、イベントに関するデータをイベントのコンテキストでパッケージ化できます。 指定したイベント プロパティの名前や値に基づいて検索やフィルター処理を実行できるため、このような操作がさらに簡単になります。 インストルメンテーション出力を構造化すると読み取りやすくなりますが、ユース ケースごとにイベントを定義するためによく考える必要があり、時間がかかります。 

アプリケーション全体で共有できるイベント定義もあります。 たとえば、メソッド開始/停止イベントは、アプリケーション内の多くのサービスで再利用されます。 注文システムのようなドメイン固有のサービスでは、独自のイベントを含む **CreateOrder** イベントを使用することがあります。 この方法では多数のイベントが生成される場合があり、プロジェクト チーム間で識別子の調整が必要になる可能性があります。 

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The instance constructor is private to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    // The ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceTypeRegisteredEventId = 3;
    [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
    public void ServiceTypeRegistered(int hostProcessId, string serviceType)
    {
        WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
    }

    // The ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceHostInitializationFailedEventId = 4;
    [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
    public void ServiceHostInitializationFailed(string exception)
    {
        WriteEvent(ServiceHostInitializationFailedEventId, exception);
    }

    ...

```

### <a name="using-eventsource-generically"></a>EventSource の汎用的な使用

特定のイベントの定義は難しい場合があるため、多くの開発者は、一般に情報を文字列として出力するパラメーターの共通セットを使用して少数のイベントを定義しています。 構造化された側面の大部分が失われるので、結果の検索とフィルター処理が困難になります。 この方法では、ログ記録レベルに通常は対応する少数のイベントを定義します。 次のスニペットでは、デバッグおよびエラー メッセージを定義しています。

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The Instance constructor is private, to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    private const int DebugEventId = 10;
    [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
    public void Debug(string msg)
    {
        WriteEvent(DebugEventId, msg);
    }

    private const int ErrorEventId = 11;
    [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
    public void Error(string error, string msg)
    {
        WriteEvent(ErrorEventId, error, msg);
    }

    ...

```

構造化されたインストルメンテーションと汎用のインストルメンテーションの組み合わせを使用するのが適している場合もあります。 構造化されたインストルメンテーションは、エラーとメトリックの報告に使用します。 汎用イベントは、エンジニアがトラブルシューティングに使用する詳細なログ記録に使用できます。

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

ASP.NET Core のログ記録 ([Microsoft.Extensions.Logging NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Extensions.Logging)) は、ご利用のアプリケーションに対して標準的なログ記録 API を提供するログ記録フレームワークです。 他のログ記録バックエンドのサポートは、ASP.NET Core のログ記録にプラグインできます。 これにより大きなコード変更を行わなくても、ご自身のアプリケーションを処理しているときに、ログ記録に関するさまざまなサポートを利用することができます。

1. インストルメント化するプロジェクトに、**Microsoft.Extensions.Logging NuGet** パッケージを追加します。 また、すべてのプロバイダーのパッケージを追加します。 詳細については、[ASP.NET Core のログ記録](/aspnet/core/fundamentals/logging)に関する記事をご覧ください。
2. **Microsoft.Extensions.Logging** の **using** ディレクティブをサービス ファイルに追加します。
3. サービス クラス内でプライベート変数を定義します。

   ```csharp
   private ILogger _logger = null;
   ```

4. サービス クラスのコンストラクターに、次のコードを追加します。

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. 独自の方法でコードのインストルメント化を開始します。 いくつかのサンプルを次に示します。

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>他のログ記録プロバイダーの使用

[Serilog](https://serilog.net/)、[NLog](https://nlog-project.org/)、[Loggr](https://github.com/imobile3/Loggr.Extensions.Logging) などの一部のサード パーティ プロバイダーでは、前のセクションで説明した方法を使用します。 これらの各プロバイダーは、ASP.NET Core のログ記録に接続することも、個別に使用することもできます。 Serilog は、ロガーから送信されるすべてのメッセージを強化する機能を備えています。 この機能は、サービスの名前、種類、パーティションの情報を出力する際に役立ちます。 ASP.NET Core インフラストラクチャ内でこの機能を使用するには、次の手順を実行します。

1. **Serilog**、**Serilog.Extensions.Logging**、**Serilog.Sinks.Literate**、**Serilog.Sinks.Observable** の各 NuGet パッケージをプロジェクトに追加します。 
2. `LoggerConfiguration` とロガー インスタンスを作成します。

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. `Serilog.ILogger` 引数をサービス コンストラクターに追加し、新しく作成されたロガーを渡します。

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. サービス コンストラクターで、**ServiceTypeName**、**ServiceName**、**PartitionId**、および **InstanceId** のプロパティ エンリッチャーを作成します。

   ```csharp
   public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
       : base(context)
   {
       PropertyEnricher[] properties = new PropertyEnricher[]
       {
           new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
           new PropertyEnricher("ServiceName", context.ServiceName),
           new PropertyEnricher("PartitionId", context.PartitionId),
           new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
       };

       serilog.ForContext(properties);

       _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
   }
   ```

5. Serilog を使用せずに ASP.NET Core を使用する場合と同様にコードをインストルメント化します。

   >[!NOTE]
   >前の例では、静的 `Log.Logger` を使用 "*しない*" ことをお勧めします。 Service Fabric は、1 つのプロセス内で同じサービスの種類の複数のインスタンスをホストできます。 静的 `Log.Logger` を使用すると、プロパティ エンリッチャーの最後のライターが、実行されているすべてのインスタンスの値を示すことになります。 これが、_logger 変数がサービス クラスのプライベート メンバー変数である理由の 1 つです。 また、サービス間で使用できる共通コードで `_logger` を使用できるようにする必要があります。

## <a name="next-steps"></a>次のステップ

- [Service Fabric での監視アプリケーション](service-fabric-diagnostics-event-generation-app.md)の詳細を確認する。
- [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) および [Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) でのログ記録について確認する。
