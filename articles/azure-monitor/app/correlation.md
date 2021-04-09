---
title: Azure Application Insights におけるテレメトリの関連付け | Microsoft Docs
description: Application Insights におけるテレメトリの相関付け
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: beaeb0131a2c9b326d663f6fcbb8273a9b52b412
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100969"
---
# <a name="telemetry-correlation-in-application-insights"></a>Application Insights におけるテレメトリの相関付け

マイクロ サービスの世界では、すべての論理操作において、サービスのさまざまなコンポーネント内での作業の実行が必要になります。 [Application Insights](../../azure-monitor/app/app-insights-overview.md) を使用して、これらの各コンポーネントを個別に監視できます。 Application Insights では、分散しているテレメトリの関連付けがサポートされています。これを使用して、エラーやパフォーマンス低下の原因になっているコンポーネントを検出できます。

この記事では、複数のコンポーネントから送信されるテレメトリを関連付けるために Application Insights によって使用されるデータ モデルについて説明します。 これにはコンテキストの伝達方法とプロトコルが含まれます。 また、さまざまな言語とプラットフォームにおける関連付けの戦術の実装も含まれます。

## <a name="data-model-for-telemetry-correlation"></a>テレメトリの関連付けのためのデータ モデル

Application Insights は、分散しているテレメトリを相関付けるための[データ モデル](../../azure-monitor/app/data-model.md)を定義しています。 テレメトリを論理操作と関連付けるために、すべてのテレメトリ項目には `operation_Id` と呼ばれるコンテキスト フィールドがあります。 この ID は、分散トレース内のすべてのテレメトリ項目で共有されます。 このため、1 つのレイヤーからテレメトリが失われた場合でも、他のコンポーネントから報告されたテレメトリを関連付けることができます。

分散型論理操作は、通常は、一連の小さな操作 (いずれかのコンポーネントによって処理される要求) で構成されます。 これらの操作は、[要求テレメトリ](../../azure-monitor/app/data-model-request-telemetry.md)によって定義されます。 すべての要求テレメトリ項目には、それをグローバルに一意に識別する独自の `id` があります。 要求に関連付けられているすべてのテレメトリ項目 (トレースや例外など) では、`operation_parentId` を要求の `id` 値に設定する必要があります。

すべての発信操作 (別のコンポーネントに対する HTTP 呼び出しなど) は、[依存関係テレメトリ](../../azure-monitor/app/data-model-dependency-telemetry.md)で表されます。 依存関係テレメトリも、グローバルに一意である独自の `id` を定義します。 この依存関係呼び出しによって開始された要求テレメトリでは、この `id` をその `operation_parentId` として使用します。

`operation_Id`、`operation_parentId`、および `request.id` を `dependency.id` と共に使用して、分散型論理操作のビューを構築できます。 これらのフィールドでは、テレメトリ呼び出しの因果関係の順序も定義します。

マイクロ サービス環境では、コンポーネントからのトレースがさまざまなストレージ項目に送信される可能性があります。 すべてのコンポーネントが、Application Insights 内に独自のインストルメンテーション キーを持つことができます。 論理操作のテレメトリを取得するために、Application Insights では、すべてのストレージ項目に対してデータのクエリが実行されます。 ストレージ項目の数が膨大な場合は、次に検索する場所に関するヒントが必要になります。 Application Insights データ モデルでは、この問題を解決するために `request.source` と `dependency.target` という 2 つのフィールドが定義されています。 最初のフィールドでは、依存関係の要求を開始したコンポーネントを特定します。 2 番目のフィールドでは、依存関係の呼び出しの応答を返したコンポーネントを特定します。

## <a name="example"></a>例

1 つ例を見てみましょう。 Stock Prices という名前アプリケーションでは、Stock という名前の外部 API を使用して株の現在の市場価格を表示します。 Stock Prices アプリケーションには、クライアント Web ブラウザーが `GET /Home/Stock` を使用して開く、Stock ページという名前のページがあります。 アプリケーションにより、HTTP 呼び出し `GET /api/stock/value` を使用して Stock API にクエリが実行されます。

結果として生成されたテレメトリを、クエリを実行して分析できます。

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

結果において、すべてのテレメトリ項目がルートの `operation_Id` を共有していることに注目してください。 ページから Ajax の呼び出しが行われると、新しい一意の ID (`qJSXU`) が依存関係テレメトリに割り当てられ、pageView の ID が `operation_ParentId` として使用されます。 その後、サーバー要求で Ajax の ID が `operation_ParentId` として使用されます。

| itemType   | name                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock page                | STYz         |                    | STYz         |
| dependency | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| request    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependency | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

呼び出し `GET /api/stock/value` が外部サービスに対して行われる場合、`dependency.target` フィールドを適切に設定できるように、該当のサーバーの ID を把握しておく必要があります。 外部サービスで監視がサポートされていない場合、`target` はそのサービスのホスト名 (たとえば `stock-prices-api.com`) に設定されます。 しかし、そのサービスが定義済みの HTTP ヘッダーを返すことでそれ自体を識別している場合、`target` にはサービスの ID が含まれます。Application Insights はそれを使用して、そのサービスに対してテレメトリのクエリを実行することによって分散トレースを構築できます。

## <a name="correlation-headers-using-w3c-tracecontext"></a>W3C TraceContext を使用した相関付けヘッダー

Application Insights では、以下を定義する [W3C Trace-Context](https://w3c.github.io/trace-context/) に移行しています。

- `traceparent`:呼び出しのグローバルに一意の操作 ID と一意識別子を記述します。
- `tracestate`:システム固有のトレース コンテキストを記述します。

Application Insights SDK の最新バージョンでは Trace-Context プロトコルがサポートされますが、それを利用する必要が生じる場合があります (Application Insights SDK によってサポートされている以前の相関付けプロトコルによる下位互換性は、引き続き利用できます)。

[相関付け HTTP プロトコル (Request-Id とも呼ばれます)](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) は、非推奨になる予定です。 このプロトコルは、2 つのヘッダーを定義しています。

- `Request-Id`:呼び出しのグローバルに一意の ID を記述します。
- `Correlation-Context`:分散トレースのプロパティの名前と値のペアのコレクションを記述します。

Application Insights では、相関付け HTTP プロトコル用の[拡張機能](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md)も定義されています。 これは `Request-Context` の名前と値のペアを使用して、直接の呼び出し元または呼び出し先によって使用されたプロパティのコレクションを伝達します。 Application Insights SDK は、このヘッダーを使用して、`dependency.target` フィールドと `request.source` フィールドを設定します。

[W3C Trace-Context](https://w3c.github.io/trace-context/) と Application Insights のデータ モデル マップを次に示します。

| Application Insights                   | W3C TraceContext                                      |
|------------------------------------    |-------------------------------------------------|
| `Request` と `Dependency` の `Id`     | [parent-id](https://w3c.github.io/trace-context/#parent-id)                                     |
| `Operation_Id`                         | [trace-id](https://w3c.github.io/trace-context/#trace-id)                                           |
| `Operation_ParentId`                   | この範囲の親範囲の [parent-id](https://w3c.github.io/trace-context/#parent-id)。 これがルート範囲の場合は、このフィールドを空にする必要があります。     |

詳細については、「[Application Insights Telemetry のデータ モデル](../../azure-monitor/app/data-model.md)」をご覧ください。

### <a name="enable-w3c-distributed-tracing-support-for-net-apps"></a>.NET アプリの W3C 分散トレース サポートを有効にする

W3C TraceContext ベースの分散トレースは、最近のすべての .NET Framework/.NET Core Sdk で既定で有効になっていることに加え、従来の Request-Id プロトコルとの下位互換性があります。

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Java アプリの W3C 分散トレース サポートを有効にする

#### <a name="java-30-agent"></a>Java 3.0 エージェント

  Java 3.0 エージェントでは、既定で W3C がサポートされ、追加構成をする必要がありません。 

#### <a name="java-sdk"></a>Java SDK
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
> 受信と送信の構成がまったく同じであることを確認してください。

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Web アプリの W3C 分散トレース サポートを有効にする

この機能は `Microsoft.ApplicationInsights.JavaScript` にあります。 既定では無効になっています。 有効にするには、`distributedTracingMode` config を使用します。AI_AND_W3C は、Application Insights によってインストルメント化されたレガシ サービスとの下位互換性のために提供されています。

- **[npm ベースのセットアップ](./javascript.md#npm-based-setup)**

次の構成を追加します。
  ```JavaScript
    distributedTracingMode: DistributedTracingModes.W3C
  ```

- **[スニペット ベースのセットアップ](./javascript.md#snippet-based-setup)**

次の構成を追加します。
  ```
      distributedTracingMode: 2 // DistributedTracingModes.W3C
  ```
> [!IMPORTANT] 
> 相関付けを有効にするために必要なすべての構成を表示するには、[JavaScript の相関付けに関するドキュメント](./javascript.md#enable-correlation)を参照してください。

## <a name="telemetry-correlation-in-opencensus-python"></a>OpenCensus Python におけるテレメトリの相関付け

OpenCensus Python は、構成を必要とすることなく、[W3C Trace-Context](https://w3c.github.io/trace-context/) をサポートします。

参照として、OpenCensus データ モデルは、[こちら](https://github.com/census-instrumentation/opencensus-specs/tree/master/trace)で見つけることができます。

### <a name="incoming-request-correlation"></a>着信要求の相関付け

OpenCensus Python は、受信要求の W3C Trace-Context ヘッダーを、要求自体から生成された範囲に関連付けます。 OpenCensus では、次の一般的な Web アプリケーション フレームワークの統合によって、これを自動的に行います。Flask、Django、および Pyramid です。 W3C Trace-Context ヘッダーを[正しい形式](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)で入力して、要求と共に送信するだけでかまいません。 これを示すサンプルの Flask アプリケーションは、次のとおりです。

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

このコードは、ポート `8080` をリッスンして、サンプルの Flask アプリケーションをローカル コンピューター上で実行します。 トレース コンテキストを関連付けるために、エンドポイントに要求を送信します。 この例では、`curl` コマンドを使用できます。
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
[Trace-Context ヘッダー形式](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)を見ると、次の情報が得られます。

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Azure Monitor に送信された要求エントリを確認すると、トレース ヘッダー情報が入力されたフィールドを確認できます。 このデータは、Azure Monitor Application Insights リソースの [ログ (Analytics)] の下にあります。

![ログ (Analytics) 内の要求テレメトリ](./media/opencensus-python/0011-correlation.png)

`id` フィールドの形式は `<trace-id>.<span-id>` です。ここで、`trace-id` は要求で渡されたトレース ヘッダーから取得され、`span-id` はこのスパンで生成された 8 バイト配列です。

`operation_ParentId` フィールドの形式は `<trace-id>.<parent-id>` です。ここで、`trace-id` と `parent-id` は両方とも、要求内で渡されたトレース ヘッダーから取得されます。

### <a name="log-correlation"></a>ログの関連付け

OpenCensus Python を使用すると、トレース ID、スパン ID、およびサンプリング フラグをログ レコードに追加することで、ログを関連付けることができます。 OpenCensus の[ログ統合](https://pypi.org/project/opencensus-ext-logging/)をインストールすることで、これらの属性を追加します。 Python `LogRecord` オブジェクトに属性 `traceId`、`spanId`、および `traceSampled` が追加されます。 これは、統合後に作成されたロガーに対してのみ有効になることに注意してください。

これを示すサンプル アプリケーションは、次のとおりです。

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
このコードを実行すると、コンソール上に次の情報が表示されます。
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
スパン内にあるログ メッセージには、`spanId` が存在することがわかります。 これは、`hello` という名前のスパンに属するものと同じ `spanId` です。

`AzureLogHandler` を使用して、ログ データをエクスポートできます。 詳細については、 [こちらの記事](./opencensus-python.md#logs)を参照してください。

また、適切な相関付けのために、あるコンポーネントから別のものにトレース情報を渡すこともできます。 たとえば、`module1` と `module2` の 2 つのコンポーネントがあるシナリオについて考えてみます。 Module1 が Module2 の関数を呼び出し、`module1` と `module2` の両方から単一のトレースでログを取得するためには、次の方法を使用できます。

```python
# module1.py
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer
from module2 import function_1

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
   logger.warning('In the span')
   function_1(tracer)
logger.warning('After the span')


# module2.py

import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

def function_1(parent_tracer=None):
    if parent_tracer is not None:
        tracer = Tracer(
                    span_context=parent_tracer.span_context,
                    sampler=AlwaysOnSampler(),
                )
    else:
        tracer = Tracer(sampler=AlwaysOnSampler())

    with tracer.span("function_1"):
        logger.info("In function_1")
```

## <a name="telemetry-correlation-in-net"></a>.NET におけるテレメトリの相関付け

.NET ランタイムは、[アクティビティ](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)と [DiagnosticSource](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) を使用した配布をサポートしています。

Application Insights .NET SDK は、`DiagnosticSource` と `Activity` を使用して、テレメトリを収集し、相関付けます。

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-java"></a>Java におけるテレメトリの相関付け

[Java エージェント](./java-in-process-agent.md)および [Java SDK](../../azure-monitor/app/java-get-started.md) バージョン 2.0.0 以降では、テレメトリの自動関連付けがサポートされています。 要求のスコープ内で発行されたすべてのテレメトリ (トレース、例外、カスタム イベントなど) に対して `operation_id` が自動的に設定されます。 また、[Java SDK エージェント](../../azure-monitor/app/java-agent.md)が構成されている場合は、HTTP 経由でのサービス間呼び出しのための関連付けヘッダー (前述) が伝達されます。

> [!NOTE]
> Application Insights Java エージェントでは、JMS、Kafka、Netty/Webflux などの要求と依存関係が自動収集されます。 Java SDK については、関連付け機能では、Apache HttpClient を使用して行われた呼び出しのみがサポートされます。 SDK では、メッセージング テクノロジ (Kafka、RabbitMQ、Azure Service Bus など) 間でのコンテキストの自動伝達はサポートされていません。 

> [!NOTE]
> カスタム テレメトリを収集するには、Java 2.6 SDK を使用してアプリケーションをインストルメント化する必要があります。 

### <a name="role-names"></a>ロール名

[アプリケーション マップ](../../azure-monitor/app/app-map.md)にコンポーネント名を表示する方法をカスタマイズする必要が生じる場合があります。 そのためには、次のいずれかのアクションを実行して、`cloud_RoleName` を手動で設定します。

- Application Insights Java エージェント3.0 の場合は、クラウド ロール名を次のように設定します。

    ```json
    {
      "role": {
        "name": "my cloud role name"
      }
    }
    ```
    代わりに、`APPLICATIONINSIGHTS_ROLE_NAME` の環境変数を使用して、クラウド ロール名を設定することもできます。

- Application Insights Java SDK 2.5.0 以降では、`<RoleName>` を ApplicationInsights.xml ファイルに追加することで、`cloud_RoleName` を指定できます。

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Application Insights Spring Boot スターターで Spring Boot を使用する場合、必要なのは application.properties ファイルにアプリケーションのカスタム名を設定することだけです。

  `spring.application.name=<name-of-app>`

  Spring Boot スターターによって、`spring.application.name` プロパティに入力した値に対して `cloudRoleName` が自動的に割り当てられます。

## <a name="next-steps"></a>次のステップ

- [カスタム テレメトリ](../../azure-monitor/app/api-custom-events-metrics.md)を記述します。
- ASP.NET Core と ASP.NET の高度な相関シナリオについては、[カスタム操作の追跡](custom-operations-tracking.md)に関する記事を参照してください。
- 他の SDK における [cloud_RoleName の設定](./app-map.md#set-or-override-cloud-role-name)について、詳細を確認します。
- Application Insights でマイクロサービスのすべてのコンポーネントの利用を開始します。 [サポートされているプラットフォーム](./platforms.md)を調べます。
- [データ モデル](./data-model.md)に関するページを参照して、Application Insights の種類を確認します。
- [テレメトリの拡張とフィルター処理](./api-filtering-sampling.md)を行う方法を確認します。
- [Application Insights の構成リファレンス](configuration-with-applicationinsights-config.md)に関するページを確認します。