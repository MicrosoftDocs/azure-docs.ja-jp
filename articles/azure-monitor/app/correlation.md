---
title: Azure Application Insights におけるテレメトリの関連付け | Microsoft Docs
description: Application Insights におけるテレメトリの相関付け
services: application-insights
documentationcenter: .net
author: lgayhardt
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.author: lagayhar
ms.openlocfilehash: 030259f7773435760c09afd25ca674b63bb1b3ca
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073235"
---
# <a name="telemetry-correlation-in-application-insights"></a>Application Insights におけるテレメトリの相関付け

マイクロ サービスの世界では、すべての論理操作において、サービスのさまざまなコンポーネント内での作業の実行が必要になります。 これらの各コンポーネントを、[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) によって個別に監視できます。 Web アプリ コンポーネントは、ユーザーの資格情報を検証するために認証プロバイダー コンポーネントと通信し、視覚化するためのデータを取得するために API コンポーネントと通信します。 API コンポーネントは、他のサービスに対してデータのクエリを実行し、キャッシュ プロバイダー コンポーネントを使用して、課金コンポーネントにこの呼び出しについて通知することができます。 Application Insights では、分散しているテレメトリの関連付けがサポートされています。これを使用して、エラーやパフォーマンス低下の原因になっているコンポーネントを検出できます。

この記事では、複数のコンポーネントから送信されるテレメトリを関連付けるために Application Insights によって使用されるデータ モデルについて説明します。 これにはコンテキストの伝達方法とプロトコルが含まれます。 また、さまざまな言語とプラットフォームにおける関連付けの概念の実装も含まれます。

## <a name="data-model-for-telemetry-correlation"></a>テレメトリの関連付けのためのデータ モデル

Application Insights は、分散しているテレメトリを相関付けるための[データ モデル](../../azure-monitor/app/data-model.md)を定義しています。 テレメトリを論理操作と関連付けるために、すべてのテレメトリ項目には `operation_Id` と呼ばれるコンテキスト フィールドがあります。 この ID は、分散トレース内のすべてのテレメトリ項目で共有されます。 このため、1 つのレイヤーからテレメトリが失われた場合でも、他のコンポーネントから報告されたテレメトリを関連付けることができます。

分散型論理操作は、通常は、一連の小さな操作 (いずれかのコンポーネントによって処理される要求) で構成されます。 これらの操作は、[要求テレメトリ](../../azure-monitor/app/data-model-request-telemetry.md)によって定義されます。 すべての要求テレメトリには、それをグローバルに一意に識別する独自の `id` があります。 この要求に関連付けられているすべてのテレメトリ項目 (トレースや例外など) では、`operation_parentId` を要求の `id` の値に設定する必要があります。

すべての発信操作 (別のコンポーネントに対する HTTP 呼び出しなど) は、[依存関係テレメトリ](../../azure-monitor/app/data-model-dependency-telemetry.md)で表されます。 依存関係テレメトリも、グローバルに一意である独自の `id` を定義します。 この依存関係呼び出しによって開始された要求テレメトリでは、この `id` をその `operation_parentId` として使用します。

`operation_Id`、`operation_parentId`、および `request.id` を `dependency.id` と共に使用して、分散型論理操作のビューを構築できます。 これらのフィールドでは、テレメトリ呼び出しの因果関係の順序も定義します。

マイクロ サービス環境では、コンポーネントからのトレースがさまざまなストレージ項目に送信される可能性があります。 すべてのコンポーネントが、Application Insights 内に独自のインストルメンテーション キーを持つことができます。 論理操作のテレメトリを取得する目的で、Application Insights UX では、すべてのストレージ項目に対してデータのクエリが実行されます。 ストレージ項目の数が膨大な場合は、次に検索する場所に関するヒントが必要になります。 Application Insights データ モデルでは、この問題を解決するために `request.source` と `dependency.target` という 2 つのフィールドが定義されています。 最初のフィールドは依存関係要求を開始したコンポーネントを識別し、2 つ目のフィールドは依存関係呼び出しの応答を返したコンポーネントを識別します。

## <a name="example"></a>例

`Stock` という名前の外部 API を使用して株の現在の市場価格を表示する、Stock Prices という名前の アプリケーションの例を見てみましょう。 Stock Prices アプリケーションには、クライアント Web ブラウザーが `GET /Home/Stock` を使用して開く、`Stock page` という名前のページがあります。 このアプリケーションは、HTTP 呼び出し `GET /api/stock/value` を使用して `Stock` API に対するクエリを実行します。

結果として生成されたテレメトリを、クエリを実行して分析できます。

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

結果において、すべてのテレメトリ項目がルートの `operation_Id` を共有していることに注目してください。 このページから Ajax の呼び出しが行われると、新しい一意の ID (`qJSXU`) が依存関係テレメトリに割り当てられ、pageView の ID が `operation_ParentId` として使用されます。 その後、サーバー要求で Ajax の ID が `operation_ParentId` として使用されます。

| itemType   | name                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock page                |              | STYz               | STYz         |
| dependency | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| request    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependency | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

呼び出し `GET /api/stock/value` が外部サービスに対して行われる場合、外部サービスの ID を把握して、それに応じて `dependency.target` フィールドを設定できるようにする必要があります。 外部サービスで監視がサポートされていない場合、`target` はそのサービスのホスト名 (たとえば `stock-prices-api.com`) に設定されます。 ただし、そのサービスが定義済みの HTTP ヘッダーを返すことで自身を識別している場合、`target` にはサービスの ID が含まれます。Application Insights はそれを使用して、そのサービスに対してテレメトリのクエリを実行することによって分散トレースを構築できます。

## <a name="correlation-headers"></a>相関付けヘッダー

Microsoft は、[相関付け HTTP プロトコル](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)の RFC 提案に取り組んでいます。 この提案では、2 つのヘッダーを定義しています。

- `Request-Id`:呼び出しのグローバルに一意の ID を記述します。
- `Correlation-Context`:分散トレースのプロパティの名前と値のペアのコレクションを記述します。

この標準では、`Request-Id` 生成の 2 つのスキーマ (フラットと階層) も定義しています。 フラット スキーマでは、既知の `Id` キーが `Correlation-Context` コレクションに対して定義されます。

Application Insights は、相関付け HTTP プロトコル用の[拡張機能](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md)を定義します。 これは `Request-Context` の名前と値のペアを使用して、直接の呼び出し元または呼び出し先によって使用されたプロパティのコレクションを伝達します。 Application Insights SDK は、このヘッダーを使用して、`dependency.target` フィールドと `request.source` フィールドを設定します。

### <a name="w3c-distributed-tracing"></a>W3C 分散トレース

Microsoft では、[W3C 分散トレース形式](https://w3c.github.io/trace-context/)への切り替えを実施しています。 次のように定義します。

- `traceparent`:呼び出しのグローバルに一意の操作 ID と一意識別子を記述します。
- `tracestate`:トレース システム固有のコンテキストを記述します。

#### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>クラシック ASP.NET アプリの W3C 分散トレース サポートを有効にする

この機能は、バージョン 2.8.0-beta1 以降の `Microsoft.ApplicationInsights.Web` および `Microsoft.ApplicationInsights.DependencyCollector` パッケージで利用できます。
既定では無効になっています。 有効にするには、`ApplicationInsights.config` を次のように変更します。

- `RequestTrackingTelemetryModule` の下に、値が `true` に設定されている `EnableW3CHeadersExtraction` 要素を追加します。
- `DependencyTrackingTelemetryModule` の下に、値が `true` に設定されている `EnableW3CHeadersInjection` 要素を追加します。

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>ASP.NET Core アプリの W3C 分散トレース サポートを有効にする

この機能は、`Microsoft.ApplicationInsights.AspNetCore` バージョン 2.5.0-beta1 および `Microsoft.ApplicationInsights.DependencyCollector` バージョン 2.8.0-beta1 で利用できます。
既定では無効になっています。 有効にするには、`ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` を `true` に設定します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

#### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Java アプリの W3C 分散トレース サポートを有効にする

- **受信の構成**

  - Java EE アプリの場合は、ApplicationInsights.xml 内の `<TelemetryModules>` タグに以下を追加します。

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
  - Spring Boot アプリの場合は、次のプロパティを追加します。

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **送信の構成**

  AI-Agent.xml に次のコードを追加します。

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > 下位互換性モードは既定で有効になっており、`enableW3CBackCompat` パラメーターは省略可能です。 これは下位互換性をオフにする場合にのみ使用してください。
  >
  > ご利用のすべてのサービスが W3C プロトコルをサポートする新しいバージョンの SDK に更新されたときに、これをオフにするのが理想的です。 できるだけ早く、これらの新しい SDK に移行することを強くお勧めします。

> [!IMPORTANT]
> 受信と送信の両方の構成が正確に同じであることを確認してください。

## <a name="opentracing-and-application-insights"></a>OpenTracing と Application Insights

[OpenTracing データ モデルの仕様](https://opentracing.io/)と Application Insights のデータ モデルの対応を次に示します。

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`、`PageView`                 | `span.kind = server` を含む `Span`                  |
| `Dependency`                          | `span.kind = client` を含む `Span`                  |
| `Request` と `Dependency` の `Id`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | タイプ `ChildOf` の `Reference` (親スパン)   |

詳細については、「[Application Insights Telemetry のデータ モデル](../../azure-monitor/app/data-model.md)」をご覧ください。 

OpenTracing の概念の定義については、OpenTracing の[仕様](https://github.com/opentracing/specification/blob/master/specification.md)と [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md) に関するページをご覧ください。

## <a name="telemetry-correlation-in-net"></a>.NET におけるテレメトリの相関付け

.NET では、長い時間をかけて、テレメトリと診断ログを関連付けるためのいくつかの方法を定義してきました。

- `System.Diagnostics.CorrelationManager` では、[LogicalOperationStack and ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx) を追跡できます。 
- `System.Diagnostics.Tracing.EventSource` および Windows イベント トレーシング (ETW) では、[SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) メソッドが定義されています。
- `ILogger` は [ログ スコープ](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) を使用します。 
- Windows Communication Foundation (WCF) および HTTP では、"現在の" コンテキストの伝達が接続されます。

ただし、これらの方法では、自動分散トレースがサポートされていませんでした。 `DiagnosticSource` は、マシン間の自動的な関連付けをサポートする 1 つの方法です。 .NET ライブラリは 'DiagnosticSource' をサポートしており、HTTP などのトランスポート経由で、関連付けのコンテキストをマシン間で自動的に伝達できます。

アクティビティの追跡の基本については、`DiagnosticSource` の [アクティビティのガイド](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)に関するページをご覧ください。

ASP.NET Core 2.0 では、HTTP ヘッダーの抽出と新しいアクティビティの開始がサポートされています。

バージョン 4.1.0 以降の `System.Net.HttpClient` では、関連付け HTTP ヘッダーの自動挿入と、アクティビティとしての HTTP 呼び出しの追跡がサポートされています。

クラシック ASP.NET 用には、新しい HTTP モジュール [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/) があります。 このモジュールは `DiagnosticSource` を使用してテレメトリの関連付けを実装します。 これは、受信要求ヘッダーに基づいてアクティビティを開始します。 また、インターネット インフォメーション サービス (IIS) の処理の各段階が別のマネージド スレッド上で実行される場合でも、要求処理のさまざまな段階からのテレメトリを関連付けます。

バージョン 2.4.0-beta1 以降の Application Insights SDK は、`DiagnosticSource` と `Activity` を使用してテレメトリを収集し、それを現在のアクティビティに関連付けます。

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Java SDK におけるテレメトリの相関付け

バージョン 2.0.0 以降の [Application Insights SDK for Java](../../azure-monitor/app/java-get-started.md) では、テレメトリの自動関連付けがサポートされています。 要求のスコープ内で発行されたすべてのテレメトリ (トレース、例外、カスタム イベントなど) に対して `operation_id` が自動的に設定されます。 また、[Java SDK エージェント](../../azure-monitor/app/java-agent.md)が構成されている場合は、HTTP 経由でのサービス間呼び出しのための関連付けヘッダー (前述) が伝達されます。

> [!NOTE]
> この関連付け機能では、Apache HTTPClient を使用して行われた呼び出しのみがサポートされます。 Spring RestTemplate または Feign を使用している場合、どちらも Apache HTTP クライアントで内部的に使用できます。

現時点では、メッセージング テクノロジ (Kafka、RabbitMQ、Azure Service Bus など) 間でのコンテキストの自動伝達はサポートされていません。 ただし、`trackDependency` および `trackRequest` API を使用してそのようなシナリオを手動でコーディングすることはできます。 これらの API では、依存関係テレメトリはプロデューサーによってエンキューされるメッセージを表し、要求はコンシューマーによって処理されるメッセージを表します。 この場合、`operation_id` と `operation_parentId` の両方を、メッセージのプロパティで伝達する必要があります。

### <a name="telemetry-correlation-in-asynchronous-java-application"></a>非同期 Java アプリケーションにおけるテレメトリの関連付け

非同期の Spring Boot アプリケーションでテレメトリを関連付けるには、[こちら](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications)の詳細な記事に従ってください。 Spring の [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) と [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html) をインストルメント化するためのガイダンスが提供されています。 


<a name="java-role-name"></a>
## <a name="role-name"></a>ロール名

[アプリケーション マップ](../../azure-monitor/app/app-map.md)にコンポーネント名を表示する方法をカスタマイズすることが必要な場合があります。 そのためには、次のいずれかを実行して `cloud_RoleName` を手動で設定します。

- Application Insights Spring Boot スターターで Spring Boot を使用する場合、必要な変更は application.properties ファイルにアプリケーションのカスタム名を設定することだけです。

  `spring.application.name=<name-of-app>`

  Spring Boot スターターによって、`spring.application.name` プロパティに入力した値に対して `cloudRoleName` が自動的に割り当てられます。

- `WebRequestTrackingFilter` を使用している場合、`WebAppNameContextInitializer` によってアプリケーション名が自動的に設定されます。 次を構成ファイル (ApplicationInsights.xml) に追加します。

  ```XML
  <ContextInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebAppNameContextInitializer" />
  </ContextInitializers>
  ```

- クラウドのコンテキスト クラスを使用する場合、次のようにします。

  ```Java
  telemetryClient.getContext().getCloud().setRole("My Component Name");
  ```

## <a name="next-steps"></a>次の手順

- [カスタム テレメトリ](../../azure-monitor/app/api-custom-events-metrics.md)を記述します。
- 他の SDK における [cloud_RoleName の設定](../../azure-monitor/app/app-map.md#set-cloud-role-name)について、詳細を確認します。
- Application Insights でマイクロサービスのすべてのコンポーネントの利用を開始します。 [サポートされているプラットフォーム](../../azure-monitor/app/platforms.md)を調べます。
- [データ モデル](../../azure-monitor/app/data-model.md)に関するページを参照して、Application Insights の種類を確認します。
- [テレメトリの拡張とフィルター処理](../../azure-monitor/app/api-filtering-sampling.md)を行う方法を確認します。
- [Application Insights の構成リファレンス](configuration-with-applicationinsights-config.md)に関するページを確認します。
