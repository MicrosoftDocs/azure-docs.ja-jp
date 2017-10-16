---
title: "Azure Service Fabric のアプリケーション レベルの監視 | Microsoft Docs"
description: "Azure Service Fabric クラスターの監視と診断に使用するアプリケーションおよびサービス レベルのイベントとログについて説明します。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/26/2017
ms.author: dekapur
ms.openlocfilehash: 3c472904641108b7383cd0f1416c47460f8de11a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="application-and-service-level-event-and-log-generation"></a>アプリケーションおよびサービス レベルのイベントとログの生成

## <a name="instrumenting-the-code-with-custom-events"></a>カスタム イベントを使用してコードをインストルメント化する

コードのインストルメント化は、サービスの監視の他のほとんどの側面の基礎となります。 インストルメンテーションは、問題点を把握し、修正する必要があるものを診断する唯一の方法です。 運用環境のサービスへのデバッガーの接続は技術的には可能ですが、一般的な方法ではありません。 そのため、詳細なインストルメンテーション データを入手することが重要です。

コードを自動的にインストルメント化する製品もあります。 これらのソリューションが役立つこともありますが、ほとんどの場合、手動でのインストルメント化が必要となります。 最終的には、アプリケーションのフォレンジックなデバッグを実行できるだけの十分な情報が必要です。 このドキュメントでは、コードをインストルメント化するためのさまざまな方法と使用する方法の選択について説明します。

## <a name="eventsource"></a>EventSource
Visual Studio でテンプレートから Service Fabric ソリューションを作成すると、**EventSource** 派生クラス (**ServiceEventSource** または **ActorEventSource**) が生成されます。 アプリケーションまたはサービスのイベントを追加できるテンプレートが作成されます。 **EventSource** の名前は一意である**必要があり**、"MyCompany-&lt;solution&gt;-&lt;project&gt;" という既定のテンプレート文字列から名前を変更する必要があります。 同じ名前を使用する複数の **EventSource** 定義があると、実行時に問題が発生します。 定義済みの各イベントには一意の識別子が必要です。 識別子が一意でない場合、ランタイム エラーが発生します。 個々の開発チーム間での競合を回避するために、識別子の値の範囲を事前に割り当てている組織もあります。 詳細については、[Vance のブログ](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)または [MSDN ドキュメント](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx)をご覧ください。

### <a name="using-structured-eventsource-events"></a>構造化された EventSource イベントの使用

このセクションのコード例の各イベントは、サービスの種類の登録時など、特定のケース用に定義されています。 ユース ケースごとにメッセージを定義すると、データをエラー テキストと共にパッケージ化できます。また、指定したプロパティの名前や値に基づいて、検索とフィルター処理をより簡単に実行できます。 インストルメンテーション出力を構造化すると使いやすくなりますが、ユース ケースごとに新しいイベントを定義するためによく考える必要があり、時間がかかります。 アプリケーション全体で共有できるイベント定義もあります。 たとえば、メソッド開始/停止イベントは、アプリケーション内の多くのサービスで再利用されます。 注文システムのようなドメイン固有のサービスでは、独自のイベントを含む **CreateOrder** イベントを使用することがあります。 この方法では多数のイベントが生成される場合があり、プロジェクト チーム間で識別子の調整が必要になる可能性があります。 

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
```

構造化されたインストルメンテーションと汎用のインストルメンテーションの組み合わせを使用するのが適している場合もあります。 構造化されたインストルメンテーションは、エラーとメトリックの報告に使用します。 汎用イベントは、エンジニアがトラブルシューティングに使用する詳細なログ記録に使用できます。

## <a name="aspnet-core-logging"></a>ASP.NET Core のログ記録

コードをインストルメント化する方法を入念に計画することが重要です。 適切なインストルメンテーション計画により、コード ベースの安定性が失われ、コードの再インストルメント化が必要になる状況を回避できます。 リスクを軽減するために、Microsoft ASP.NET Core の一部である [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/) などのインストルメンテーション ライブラリを選択できます。 ASP.NET Core には、既存のコードへの影響を最小限に抑えながら、選択したプロバイダーで使用できる [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) インターフェイスがあります。 Windows および Linux 上の ASP.NET Core 内と完全な .NET Framework 内でコードを使用できるので、インストルメンテーション コードが標準化されます。 これは以下でさらに詳しく説明します。

### <a name="using-microsoftextensionslogging-in-service-fabric"></a>Service Fabric 内での Microsoft.Extensions.Logging の使用

1. インストルメント化するプロジェクトに、Microsoft.Extensions.Logging NuGet パッケージを追加します。 また、任意のプロバイダー パッケージも追加します (サード パーティ製パッケージについては、次の例を参照してください)。 詳細については、[ASP.NET Core のログ記録](https://docs.microsoft.com/aspnet/core/fundamentals/logging)に関する記事をご覧ください。
2. Microsoft.Extensions.Logging の **using** ディレクティブをサービス ファイルに追加します。
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

## <a name="using-other-logging-providers"></a>他のログ記録プロバイダーの使用

[Serilog](https://serilog.net/)、[NLog](http://nlog-project.org/)、[Loggr](https://github.com/imobile3/Loggr.Extensions.Logging) などの一部のサード パーティ プロバイダーでは、前のセクションで説明した方法を使用します。 これらの各プロバイダーは、ASP.NET Core のログ記録に接続することも、個別に使用することもできます。 Serilog は、ロガーから送信されるすべてのメッセージを強化する機能を備えています。 この機能は、サービスの名前、種類、パーティションの情報を出力する際に役立ちます。 ASP.NET Core インフラストラクチャ内でこの機能を使用するには、次の手順を実行します。

1. Serilog、Serilog.Extensions.Logging、Serilog.Sinks.Observable の各 NuGet パッケージをプロジェクトに追加します。 次の例では、Serilog.Sinks.Literate も追加しています。 さらに優れた方法を後ほど紹介します。
2. Serilog で LoggerConfiguration とロガー インスタンスを作成します。

  ```csharp
  Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
  ```

3. Serilog.ILogger 引数をサービス コンストラクターに追加し、新しく作成されたロガーを渡します。

  ```csharp
  ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
  ```

4. サービス コンストラクターに次のコードを追加します。このコードでは、サービスの **ServiceTypeName**、**ServiceName**、**PartitionId**、**InstanceId** の各プロパティのプロパティ エンリッチャーを作成します。 また、ASP.NET Core のログ ファクトリにもプロパティ エンリッチャーを追加します。これにより、コードで Microsoft.Extensions.Logging.ILogger を使用できるようになります。

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
  >前の例で静的 Log.Logger を使用しないことをお勧めします。 Service Fabric は、1 つのプロセス内で同じサービスの種類の複数のインスタンスをホストできます。 静的 Log.Logger を使用すると、プロパティ エンリッチャーの最後のライターが、実行されているすべてのインスタンスの値を示すことになります。 これが、_logger 変数がサービス クラスのプライベート メンバー変数である理由の 1 つです。 また、サービス間で使用できる共通コードで _logger を使用できるようにする必要があります。

## <a name="choosing-a-logging-provider"></a>ログ記録プロバイダーの選択

アプリケーションが高パフォーマンスに依存する場合は、通常は **EventSource** が有効な方法です。 **EventSource** は*一般に*使用するリソースが少なく、ASP.NET Core のログ記録や使用可能なサード パーティ製ソリューションよりもパフォーマンスが向上します。  多くのサービスではパフォーマンスは問題になりませんが、サービスがパフォーマンス指向の場合は、**EventSource** を使用することをお勧めします。 ただし、**EventSource** でこうした構造化されたログ記録の利点を得るには、エンジニアリング チームによる、より大規模な投資が必要となります。 可能であれば、いくつかのログ オプションの簡単なプロトタイプを作成して、ニーズに最も合うものを選択してください。

## <a name="next-steps"></a>次のステップ

アプリケーションとサービスのインストルメント化にログ プロバイダーを選択したら、任意の分析プラットフォームに送信できるようにログとイベントを集計する必要があります。 [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) に関する記事と [WAD](service-fabric-diagnostics-event-aggregation-wad.md) に関する記事を読んで、推奨されるオプションについて理解を深めてください。
