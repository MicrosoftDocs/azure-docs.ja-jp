---
title: "Azure Service Fabric の監視と診断の概要 | Microsoft Docs"
description: "Azure、開発環境、またはオンプレミスでホストされる Microsoft Azure Service Fabric アプリケーションを監視および診断する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: mfussell
editor: 
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/9/2017
ms.author: dekapur
translationtype: Human Translation
ms.sourcegitcommit: ebbb29ee031fb477ce284f7a0d27c1522317f4f0
ms.openlocfilehash: 46a35fa4ec341561ab234f7ec19fb20658fcb2c4
ms.lasthandoff: 02/27/2017


---
# <a name="monitor-and-diagnose-azure-service-fabric-applications"></a>Azure Service Fabric アプリケーションの監視と診断

監視と診断は実際の運用環境において不可欠です。 Azure Service Fabric を使用すると、1 台のコンピューターのローカル開発環境と実際の運用クラスターのセットアップの両方でサービスがシームレスに動作できるように、サービスの開発時に監視と診断を実装できます。

監視と診断により、サービスの開発中に次のことが可能になります。
* 顧客のエクスペリエンスの中断を最小限に抑える。
* ビジネスの知見を提供する。
* リソース使用量を監視する。
* ハードウェアおよびソフトウェアの障害やパフォーマンスの問題を検出する。
* サービスの潜在的な問題を診断する。

監視は意味の広い言葉です。監視には次の作業が含まれます。
* コードのインストルメント化
* インストルメンテーション ログの収集
* ログの分析
* ログ データに基づく洞察の視覚化
* ログの値と洞察に基づくアラートの設定
* インフラストラクチャの監視
* 顧客に影響する問題の検出と診断

この記事では、Azure またはオンプレミスでホストされ、Windows または Linux 上にデプロイされているか、Microsoft .NET Framework を使用している Service Fabric クラスターの監視の概要を説明します。 監視と診断の次の&3; つの重要な側面について説明します。
- コードまたはインフラストラクチャのインストルメント化
- 生成されたイベントの収集
- 保存、集計、視覚化、および分析

この&3; つの領域のすべてに対応する多数の製品がありますが、多くのお客様が監視の各側面に異なるテクノロジを選択しています。 各部分を連携させて、アプリケーションのエンド ツー エンドの監視ソリューションを実現することが重要です。

## <a name="monitoring-infrastructure"></a>インフラストラクチャの監視

Service Fabric を使用すると、インフラストラクチャの障害時にもアプリケーションを実行し続けることができますが、エラーがアプリケーションと基になるインフラストラクチャのどちらで発生しているのかを理解する必要があります。 また、インフラストラクチャを追加または削除する時期を把握するために、容量計画にもインフラストラクチャの監視が必要となります。 Service Fabric デプロイを構成するインフラストラクチャとアプリケーションの両方を監視し、トラブルシューティングを行うことが重要です。 顧客がアプリケーションを使用できる場合でも、インフラストラクチャに問題が発生している可能性があります。

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) を使用して、Service Fabric クラスターの基盤となる Azure リソースの多くを監視できます。 [仮想マシン スケール セット](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)と個々の[仮想マシン](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines)の一連のメトリックが自動的に収集され、Azure Portal に表示されます。 収集された情報を表示するには、Azure Portal でService Fabric クラスターが含まれているリソース グループを選択します。 次に、表示する仮想マシン スケール セットを選択します。 **[監視]** セクションで**メトリック**を選択すると、値のグラフが表示されます。

![収集されたメトリック情報の Azure Portal での表示](./media/service-fabric-diagnostics-overview/azure-monitoring-metrics.PNG)

グラフをカスタマイズするには、[Microsoft Azure のメトリック](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)に関する記事に記載された手順に従います。 また、「[Azure Monitor での Azure サービス アラートの作成](../monitoring-and-diagnostics/insights-alerts-portal.md)」で説明するように、これらのメトリックに基づいてアラートを作成することもできます。 「[Azure メトリック アラートでの webhook の構成](../monitoring-and-diagnostics/insights-webhooks-alerts.md)」で説明するように、webhook を使用してアラートを通知サービスに送信できます。 Azure Monitor では、サブスクリプションを&1; つだけサポートします。 複数のサブスクリプションを監視する必要がある場合や、追加機能が必要な場合は、Microsoft Operations Management Suite に含まれる [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) が、オンプレミスとクラウド ベースの両方のインフラストラクチャに対応する包括的な IT 管理ソリューションを提供します。 Azure Monitor のデータを Log Analytics に直接ルーティングできるので、環境全体のメトリックとログを&1; か所で確認できます。

オンプレミスのインフラストラクチャの監視には、Operations Management Suite を使用することをお勧めします。ただし、組織でインフラストラクチャの監視に使用している既存のソリューションを使用することもできます。

### <a name="service-fabric-support-logs"></a>Service Fabric のサポート ログ

Azure Service Fabric クラスターに関する支援を得るために Microsoft サポートに連絡する必要があるときは、ほとんどの場合、サポート ログが必要となります。 クラスターが Azure 内でホストされている場合は、クラスターの作成の一環として、サポート ログが自動的に構成され、収集されます。 ログは、クラスターのリソース グループにある専用のストレージ アカウントに保存されます。 このストレージ アカウントに固定名はありませんが、アカウント内に名前が *fabric* で始まる BLOB コンテナーとテーブルがあります。 スタンドアロン クラスターのログ収集の設定については、[スタンドアロン Azure Service Fabric クラスターの作成と管理](service-fabric-cluster-creation-for-windows-server.md)に関する記事および「[スタンドアロン Windows クラスターの構成設定](service-fabric-cluster-manifest.md)」をご覧ください。 スタンドアロン Service Fabric インスタンスの場合、ログをローカルのファイル共有に送信する必要があります。 これらのログは、サポートを受けるために**必須**となりますが、Microsoft カスタマー サポート チーム以外の人が使用するためのものではありません。

## <a name="instrument-your-code"></a>コードをインストルメント化する

コードのインストルメント化は、サービスの監視の他のほとんどの側面の基礎となります。 インストルメンテーションは、問題点を把握し、修正する必要があるものを診断する唯一の方法です。 運用環境のサービスへのデバッガーの接続は技術的には可能ですが、一般的な方法ではありません。 そのため、詳細なインストルメンテーション データを入手することが重要です。 この大量の情報を生成するときに、すべてのイベントをローカル ノードから配布すると、コストがかかる可能性があります。 多くのサービスでは、大量のインストルメンテーション データを処理するために、次のような二部戦略を使用しています。
1.  すべてのイベントは、短期間、ローカルのローリング ログ ファイルに保持され、デバッグに必要な場合にのみ収集されます。 通常、コストとリソース使用率を削減するために、詳細な診断に必要なイベントはノード上に残されます。
2.  サービスの正常性を示すイベントは、中央リポジトリに送信されます。中央リポジトリでこれらのイベントを使用して、問題のあるサービスのアラートを生成できます。 サービス正常性イベントには、エラー イベント、ハートビート イベント、パフォーマンス イベントなどがあります。

コードを自動的にインストルメント化する製品もあります。 これらのソリューションが役立つこともありますが、ほとんどの場合、手動でのインストルメント化が必要となります。 最終的には、アプリケーションのフォレンジックなデバッグを実行できるだけの十分な情報が必要です。 以下のセクションでは、コードをインストルメント化するためのさまざまな方法と使用する方法の選択について説明します。

### <a name="eventsource"></a>EventSource

Visual Studio でテンプレートから Service Fabric ソリューションを作成すると、**EventSource** 派生クラス (**ServiceEventSource** または **ActorEventSource**) が生成されます。 アプリケーションまたはサービスのイベントを追加できるテンプレートが作成されます。 **EventSource** の名前は一意である**必要があり**、"MyCompany-&lt;solution&gt;-&lt;project&gt;" という既定のテンプレート文字列から名前を変更する必要があります。 同じ名前を使用する複数の **EventSource** 定義があると、実行時に問題が発生します。 定義済みの各イベントには一意の識別子が必要です。 識別子が一意でない場合、ランタイム エラーが発生します。 個々の開発チーム間での競合を回避するために、識別子の値の範囲を事前に割り当てている組織もあります。 詳細については、[Vance のブログ](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)または [MSDN ドキュメント](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx)をご覧ください。

#### <a name="using-structured-eventsource-events"></a>構造化された EventSource イベントの使用

このセクションのコード例の各イベントは、サービスの種類の登録時など、特定のケース用に定義されています。 ユース ケースごとにメッセージを定義すると、データをエラー テキストと共にパッケージ化できます。また、指定したプロパティの名前や値に基づいて、検索とフィルター処理をより簡単に実行できます。 インストルメンテーション出力を構造化すると使いやすくなりますが、ユース ケースごとに新しいイベントを定義するためによく考える必要があり、時間がかかります。 アプリケーション全体で共有できるイベント定義もあります。 たとえば、メソッド開始/停止イベントは、アプリケーション内の多くのサービスで再利用されます。 注文システムのようなドメイン固有のサービスでは、独自のイベントを含む **CreateOrder** イベントを使用することがあります。 この方法では多数のイベントが生成される場合があり、プロジェクト チーム間で識別子の調整が必要になる可能性があります。 Service Fabric の構造化された **EventSource** イベントの完全な例については、パーティ クラスター サンプル内の **PartyCluster.ApplicationDeployService** イベントを参照してください。

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

#### <a name="using-eventsource-generically"></a>EventSource の汎用的な使用

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

### <a name="aspnet-core-logging"></a>ASP.NET Core のログ記録

コードをインストルメント化する方法を入念に計画することが重要です。 適切なインストルメンテーション計画により、コード ベースの安定性が失われ、コードの再インストルメント化が必要になる状況を回避できます。 リスクを軽減するために、Microsoft ASP.NET Core の一部である [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/) などのインストルメンテーション ライブラリを選択できます。 ASP.NET Core には、既存のコードへの影響を最小限に抑えながら、選択したプロバイダーで使用できる [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) インターフェイスがあります。 Windows および Linux 上の ASP.NET Core 内と完全な .NET Framework 内でコードを使用できるので、インストルメンテーション コードが標準化されます。

#### <a name="using-microsoftextensionslogging-in-service-fabric"></a>Service Fabric 内での Microsoft.Extensions.Logging の使用

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

#### <a name="using-other-logging-providers"></a>他のログ記録プロバイダーの使用

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

4. サービス コンストラクターに次のコードを追加します。 このコードでは、サービスの **ServiceTypeName**、**ServiceName**、**PartitionId**、**InstanceId** の各プロパティのプロパティ エンリッチャーを作成します。 また、ASP.NET Core のログ ファクトリにもプロパティ エンリッチャーを追加します。これにより、コードで Microsoft.Extensions.Logging.ILogger を使用できるようになります。

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

  > [!NOTE]
  > 前の例で静的 Log.Logger を使用しないことをお勧めします。 Service Fabric は、1 つのプロセス内で同じサービスの種類の複数のインスタンスをホストできます。 静的 Log.Logger を使用すると、プロパティ エンリッチャーの最後のライターが、実行されているすべてのインスタンスの値を示すことになります。 これが、_logger 変数がサービス クラスのプライベート メンバー変数である理由の&1; つです。 また、サービス間で使用できる共通コードで _logger を使用できるようにする必要があります。

### <a name="choosing-a-logging-provider"></a>ログ記録プロバイダーの選択

アプリケーションが高パフォーマンスに依存する場合は、**EventSource** が最適な方法です。 **EventSource** は*一般に*使用するリソースが少なく、ASP.NET Core のログ記録や使用可能なサード パーティ製ソリューションよりもパフォーマンスが向上します。  多くのサービスではパフォーマンスは問題になりませんが、サービスがパフォーマンス指向の場合は、**EventSource** を使用することをお勧めします。 **EventSource** で構造化されたログ記録と同じ利点を得るには、エンジニアリング チームによる大規模な投資が必要となります。 プロジェクトに使用する方法を決定するときは、各オプションで必要となるものの簡易プロトタイプを実行し、ニーズに最適な方法を選択します。

## <a name="event-and-log-collection"></a>イベントとログの収集

### <a name="azure-diagnostics"></a>Azure 診断

Azure Monitor で提供される情報に加え、Azure では各サービスのイベントを中央の場所で収集します。 詳細については、[Windows](service-fabric-diagnostics-how-to-setup-wad.md) および [Linux](service-fabric-diagnostics-how-to-setup-lad.md) でイベント収集を構成する方法をご覧ください。 これらの記事では、イベント データを収集し、Azure Storage に送信する方法を説明しています。 これは、診断を有効にすることによって、Azure Portal または Azure Resource Manager テンプレートで実行できます。 Azure 診断では、Service Fabric で自動的に生成されるイベント ソースから収集します。

- Reliable Actors プログラミング モデルを使用している場合は、**EventSource** イベントとパフォーマンス カウンター。 イベントは、「[Reliable Actors の診断とパフォーマンス監視](service-fabric-reliable-actors-diagnostics.md)」で列挙されています。
- Reliable Services プログラミング モデルを使用している場合は **EventSource** イベント。 イベントは、「[ステートフル Reliable Services の診断機能](service-fabric-reliable-services-diagnostics.md)」で列挙されています。
- システム イベントは、Event Tracing for Windows (ETW) イベントとして出力されます。 サービス配置や開始/停止イベントなど、このカテゴリに含まれる多数のイベントが Service Fabric から出力されます。 出力されたイベントを確認する最良の方法は、ローカル コンピューターで [Visual Studio の診断イベント ビューアー](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)を使用することです。 これらのイベントはネイティブ ETW イベントであるため、収集方法にいくつかの制限があります。
- Service Fabric リリース 5.4 で、正常性と負荷のメトリック イベントが公開されました。 これにより、イベントの収集を履歴レポートとアラートで使用できるようになります。 これらのイベントもネイティブ ETW イベントであるため、収集方法にいくつかの制限があります。

診断が有効になっていれば、構成時に、イベントはクラスターを作成したときに作成された Azure ストレージ アカウントに表示されます。 テーブル名は、WADServiceFabricReliableActorEventTable、WADServiceFabricReliableServiceEventTable、WADServiceFabricSystemEventTable です。 正常性イベントは既定で追加されません。これらのイベントを追加するには、Resource Manager テンプレートを変更する必要があります。 詳細については、「[Azure 診断でログを収集する方法](service-fabric-diagnostics-how-to-setup-wad.md)」をご覧ください。

このセクションで示すこれらの記事では、カスタム イベントを Azure Storage に取得する方法も説明している場合があります。 パフォーマンス カウンターの構成に関する Azure 診断の他の記事や、Azure 診断で収集される仮想マシンの他の監視情報が含まれた記事は、Service Fabric クラスターにも適用されます。 たとえば、送信先として Azure Table Storage を使用しない場合は、「[Event Hubs を利用してホット パスの Azure 診断データをストリーム配信する](../event-hubs/event-hubs-streaming-azure-diags-data.md)」をご覧ください。 イベントが Azure Event Hubs 内にある場合、それらのイベントを読み取り、選択した場所に送信できます。 Azure 診断情報と Application Insights の統合の詳細については、[こちら](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)をご覧ください。

Azure 診断を使用する欠点の&1; つは、Resource Manager テンプレートを使用して設定することです。 診断は仮想マシン スケール セット レベルでのみ実行されます。 仮想マシン スケール セットは Service Fabric 内のノードの種類に対応します。 その種類のノードで実行される可能性のあるすべてのアプリケーションとサービス用に各ノードの種類を構成します。 そのため、構成されているアプリケーションとサービスの数によっては、多数の **EventSource** イベントが使用される可能性があります。 また、アプリケーションの構成が変更されるたびに、Resource Manager をデプロイする必要があります。 監視構成はサービス構成と共に移動するのが理想的です。

Azure 診断は、Azure にデプロイされている Service Fabric クラスターでのみ機能しますが、 Windows クラスターと Linux クラスターの両方で利用できます。

### <a name="eventflow"></a>EventFlow

[Microsoft Diagnostics EventFlow](https://github.com/Azure/diagnostics-eventflow) では、ノードから&1; つ以上の監視対象にイベントをルーティングすることができます。 EventFlow は NuGet パッケージとしてサービス プロジェクトに含まれるため、EventFlow のコードと構成はサービスと共に移動します。これにより、Azure 診断で説明したノードごとの構成の問題が排除されます。 EventFlow はサービス プロセス内で実行され、構成済みの出力に直接接続されます。 この直接接続により、EventFlow は Azure、コンテナー、オンプレミスの各サービス デプロイで機能します。 各 EventFlow パイプラインは外部接続を行うため、コンテナーなどの高密度シナリオで EventFlow を実行する場合は注意してください。 多数のプロセスをホストしている場合、多数の送信接続が発生することになります。 Service Fabric アプリケーションでは、`ServiceType` のすべてのレプリカが同じプロセス内で実行され、送信接続の数が限られるため、これはそれほど問題にはなりません。 また、EventFlow ではイベントのフィルター処理も行われるので、指定したフィルターと一致するイベントだけが送信されます。 Service Fabric で EventFlow を使用する方法の詳細については、「[Azure Service Fabric サービス プロセスからログを直接収集する](service-fabric-diagnostic-collect-logs-without-an-agent.md)」をご覧ください。

EventFlow を使用するには、次の手順に従います。

1. NuGet パッケージをサービス プロジェクトに追加します。
2. サービスの **Main** 関数内で EventFlow パイプラインを作成し、出力を構成します。 次の例では、出力として Serilog を使用しています。

  ```csharp
  internal static class Program
  {
      /// <summary>
      /// This is the entry point of the service host process.
      /// </summary>
      private static void Main()
      {
          try
          {
              using (var pipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MonitoringE2E-Stateless-Pipeline"))
              {
                  Log.Logger = new LoggerConfiguration().WriteTo.EventFlow(pipeline).CreateLogger();

                  // The ServiceManifest.xml file defines one or more service type names.
                  // Registering a service maps a service type name to a .NET type.
                  // When Service Fabric creates an instance of this service type,
                  // an instance of the class is created in this host process.

                  ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
                  ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless).Name);

                  // Prevents this host process from terminating, so services keep running.
                  Thread.Sleep(Timeout.Infinite);
              }
          }
          catch (Exception e)
          {
              ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
              throw;
          }
      }
  }
  ```

3. サービスの \\PackageRoot\\Config フォルダーに、eventFlowConfig.json という名前のファイルを作成します。 このファイル内の構成は次のようになります。

  ```json
      {
      "inputs": [
          {
          "type": "EventSource",
          "sources": [
              { "providerName": "Microsoft-ServiceFabric-Services" },
              { "providerName": "Microsoft-ServiceFabric-Actors" },
              { "providerName": "MyCompany-MonitoringE2E-Stateless" }
          ]
          },
          {
          "type": "Serilog"
          }
      ],
      "filters": [
          {
          "type": "drop",
          "include": "Level == Verbose"
          },
          {
          "type": "metadata",
          "metadata": "request",
          "requestNameProperty": "RequestName",
          "include":  "RequestName==MyRequest",
          "durationProperty": "Duration",
          "durationUnit": "milliseconds"
          }
      ],
      "outputs": [
          {
          "type": "StdOutput"
          },
          {
          "type": "ApplicationInsights",
          "instrumentationKey": "== instrumentation key here =="
          }
      ],
      "schemaVersion": "2016-08-11",
      "extensions": []
      }
  ```
    この構成では、2 つの入力が定義されています。Service Fabric によって作成された&2; つの **EventSource** ベースのソースとサービスの **EventSource** があります。 ETW を使用するシステム レベルのイベントと正常性イベントは、EventFlow では使用できません。 これは、ETW ソースをリッスンするには高レベルの権限が必要ですが、高い権限でサービスを実行してはいけないためです。 もう&1; つの入力は Serilog です。 Serilog は **Main** メソッド内で構成されています。  フィルターがいくつか適用されます。 最初のフィルターは、イベント レベルが verbose (詳細) であるすべてのイベントを破棄するよう EventFlow に指示します。 Visual Studio 内の出力ウィンドウに書き込む標準出力と ApplicationInsights の&2; つの出力が構成されています。 インストルメンテーション キーを必ず追加してください。

4. コードをインストルメント化します。 次の例では、例を示すためにいくつかの異なる方法で `RunAsync` をインストルメント化しています。 次のコードでは、引き続き Serilog を使用しています。 使用している構文の一部は Serilog に固有のものです。 選択したログ記録ソリューションの特定の機能に注意してください。 ここでは&3; つのイベントが生成されます。1 つはデバッグ レベルのイベント、2 つは情報イベントです。 2 つ目の情報イベントは要求の期間を追跡します。 前述の EventFlow の構成では、デバッグ レベルのイベントは出力に送信されません。

  ```csharp
      Stopwatch sw = Stopwatch.StartNew();

      while (true)
      {
          cancellationToken.ThrowIfCancellationRequested();

          sw.Restart();

          // Delay a random interval, to provide a more interesting request duration.
          await Task.Delay(TimeSpan.FromMilliseconds(DateTime.Now.Millisecond), cancellationToken);

          ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);
          _logger.LogDebug("Debug level event from Microsoft.Logging");
          _logger.LogInformation("Informational level event from Microsoft.Logging");
          _logger.LogInformation("{RequestName} {Duration}", "MyRequest", sw.ElapsedMilliseconds);
      }
  ```

Azure Application Insights でイベントを表示するには、Azure Portal で Application Insights リソースに移動します。 イベントを表示するには、**[検索]** ボックスを選択します。

![Application Insights のイベントの [検索] ビュー](./media/service-fabric-diagnostics-overview/ai-search-events.PNG)

上記のスクリーンショットの下部にトレースが表示されています。 ここではイベントが&2; つしか表示されていません。これは、デバッグ レベルのイベントが EventFlow によって破棄されたことを示しています。 トレースの上の要求エントリは、3 番目の `_logger` インストルメンテーション行です。 この行は、イベントが Application Insights 内で要求メトリックに変換されたことを示しています。

フィルター定義では、type が **metadata** になっています。 これは、`MyRequest` という値が指定された `RequestName` プロパティを持つイベントと、`Duration` という別のプロパティに、要求の期間がミリ秒単位で含まれることを宣言しています。 これが、Application Insights で要求イベントに表示されているものです。 **EventSource** を含め、EventFlow のサポートされているどの入力でも同じ方法を使用できます。

ポリシー上の理由からクラウド ベースのソリューションに接続できないスタンドアロン クラスターがある場合は、Elasticsearch を出力として使用できます。 ただし、他の出力を書き込むこともでき、プル要求が促進されます。 ASP.NET Core のログ記録の一部のサード パーティ プロバイダーには、オンプレミスのインストールをサポートするソリューションもあります。

## <a name="azure-service-fabric-health-and-load-reporting"></a>Azure Service Fabric の正常性と負荷のレポート

Service Fabric には、次の記事で詳述する独自の正常性モデルがあります。
- [Service Fabric の正常性モニタリングの概要](service-fabric-health-introduction.md)
- [サービス正常性のレポートとチェック](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Service Fabric のカスタム正常性レポートの追加](service-fabric-report-health.md)
- [Service Fabric の正常性レポートの確認](service-fabric-view-entities-aggregated-health.md)

正常性監視は、サービスの運用のさまざまな側面に不可欠です。 正常性監視は、Service Fabric が指定されたアプリケーションのアップグレードを実行するときに特に重要となります。 サービスの各アップグレード ドメインがアップグレードされ、顧客が利用できるようになった後、デプロイが次のアップグレード ドメインに移る前に、現在のアップグレード ドメインが正常性チェックに合格する必要があります。 正常な状態を実現できない場合、デプロイがロールバックされ、アプリケーションは既知の良好な状態に維持されます。 サービスがロールバックされる前に一部の顧客が影響を受ける場合もありますが、ほとんどの顧客では問題が発生することはありません。 また、人間のオペレーターによる操作を待たなくても、比較的速やかに問題が解決されます。 コードに組み込まれている正常性チェックが増えるほど、デプロイの問題に対するサービスの耐性が向上します。

サービスの正常性のもう&1; つの側面は、サービスのメトリックの報告です。 メトリックはリソース使用量のバランスを取るために使用されるため、Service Fabric において重要です。 また、メトリックはシステムの正常性を示すインジケーターにもなります。 たとえば、多数のサービスを使用するアプリケーションがあり、各インスタンスから&1; 秒あたりの要求数 (RPS) メトリックが報告されているとします。 あるサービスが別のサービスよりも多くのリソースを使用している場合、Service Fabric はクラスター内でサービス インスタンスを移動させ、リソース使用率を均等に維持することを試みます。 リソース使用率のしくみの詳細については、「[Service Fabric のリソース使用量と負荷をメトリックで管理する](service-fabric-cluster-resource-manager-metrics.md)」をご覧ください。

メトリックにより、サービスの実行状況に関する知見も得られます。 長期間にわたり、メトリックを使用して、サービスが求められているパラメーターの範囲内で動作していることを確認することもできます。 たとえば、月曜日の午前 9 時の時点での平均 RPS が 1,000 であることを傾向が示している場合、RPS が 500 を下回るか、1,500 を超えたら通知する正常性レポートを設定できます。 すべてがまったく問題ない場合もありますが、顧客の優れたエクスペリエンスを確保するうえで、正常性レポートは一見の価値があります。 サービスでは、正常性チェックのために報告対象にすることができ、クラスターのリソースのバランスには影響を与えない一連のメトリックを定義できます。 これを行うには、メトリックの重みを&0; に設定します。 どのメトリックも最初は重みを&0; にし、メトリックの重み付けがクラスターのリソースのバランスに及ぼす影響を確実に理解できるまで重みを増やさないようにすることをお勧めします。

> [!TIP]
> 重み付けされたメトリックを使いすぎないようにしてください。 バランスを取るためにサービス インスタンスが移動されている理由を理解しにくくなる可能性があります。 少数のメトリックでも十分に役立ちます。

アプリケーションの正常性とパフォーマンスを示すことができる情報が、メトリックと正常性レポートの候補となります。 CPU パフォーマンス カウンターではノードの使用状況がわかりますが、1 つのノードで複数のサービスが実行されている場合があるため、特定のサービスが正常かどうかを示すわけではありません。 しかし、RPS、処理された項目数、要求の待機時間などのメトリックは、いずれも特定のサービスの正常性を示すことができます。

正常性を報告するには、次のようなコードを使用します。

  ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
  ```

メトリックを報告するには、次のようなコードを使用します。

  ```csharp
    this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
  ```

## <a name="watchdogs"></a>ウォッチドッグ

ウォッチドッグは、複数のサービスにわたって正常性と負荷を監視し、正常性モデル階層内のあらゆるものの正常性を報告することができる別のサービスです。 このサービスにより、1 つのサービスの観点では検出されないエラーを防ぐことができます。 また、ウォッチドッグは、ユーザーが介入せずに既知の状況に対して修復アクションを実行できるコードをホストするのに適した場所でもあります。

## <a name="visualization-analysis-and-alerts"></a>視覚化、分析、およびアラート

監視の最後の部分は、イベント ストリームの視覚化、サービスのパフォーマンスに関するレポート、問題が検出されたときのアラートです。 監視のこの側面には、さまざまなソリューションを使用できます。 Azure Application Insights や Operations Management Suite を使用して、イベントのストリームに基づくアラートを作成できます。 Microsoft Power BI または [Kibana](https://www.elastic.co/products/kibana) や [Splunk](https://www.splunk.com/) などのサード パーティ製ソリューションを使用して、データを視覚化できます。

## <a name="next-steps"></a>次のステップ

* [Azure 診断でログを収集する](service-fabric-diagnostics-how-to-setup-wad.md)
* [Azure Service Fabric サービス プロセスからログを直接収集する](service-fabric-diagnostic-collect-logs-without-an-agent.md)
*  [Service Fabric のリソース使用量と負荷をメトリックで管理する](service-fabric-cluster-resource-manager-metrics.md)

