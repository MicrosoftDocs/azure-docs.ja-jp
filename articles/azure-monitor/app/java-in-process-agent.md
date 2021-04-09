---
title: Azure Monitor Application Insights Java
description: コードを変更することなく、任意の環境で実行されている Java アプリケーションのアプリケーション パフォーマンスを監視します。 分散トレースとアプリケーション マップです。
ms.topic: conceptual
ms.date: 03/29/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: b695df29b7a4704ee9e4e25e402fa0de8f2b7685
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008214"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights"></a>Azure Monitor Application Insights を監視する Java のコード不要のアプリケーション

Java のコード不要のアプリケーション監視は、シンプルさがすべてです。コードの変更がなく、いくつかの構成変更を行うだけで、Java エージェントを有効にすることができます。

 Java エージェントは、任意の環境で動作し、すべての Java アプリケーションを監視できます。 つまり、Java アプリを実行しているのが、VM、オンプレミス、AKS、Windows、Linux などのいずれであっても、Java 3.0 エージェントがアプリを監視します。

3\.0 エージェントによって要求、依存関係、ログがすべて独自に自動収集されるため、アプリケーションに Application Insights Java SDK を追加する必要がなくなりました。

アプリケーションからカスタム テレメトリを送信することはできます。 3\.0 エージェントによって、自動収集されたすべてのテレメトリと連動して、追跡と関連付けが行われます。

3\.0 エージェントは、Java 8 以降をサポートしています。

## <a name="quickstart"></a>クイック スタート

**1.エージェントのダウンロード**

> [!WARNING]
> **3.0 Preview からアップグレードする場合**
>
> ファイル名自体がすべて小文字になったのに加えて、JSON 構造体が完全に変更されたため、すべての[構成オプション](./java-standalone-config.md)を注意深く確認してください。

[applicationinsights-agent-3.0.2.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.2/applicationinsights-agent-3.0.2.jar) をダウンロードします

**2.JVM をエージェントにポイントする**

アプリケーションの JVM 引数に `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` を追加します

一般的な JVM 引数には、`-Xmx512m` と `-XX:+UseG1GC` があります。 これらの引数の追加先がわかれば、これの追加先もわかります。

アプリケーションの JVM 引数の構成に関する追加のヘルプについては、[JVM の引数の更新に関するヒント](./java-standalone-arguments.md)のページを参照してください。

**3.エージェントを Application Insights リソースにポイントする**

Application Insights リソースをまだ持っていない場合は、[リソース作成ガイド](./create-new-resource.md)の手順に従って、新しいリソースを作成できます。

環境変数を設定して、エージェントを Application Insights リソースにポイントします。

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

または、次の内容で、`applicationinsights.json` という名前の構成ファイルを作成し、`applicationinsights-agent-3.0.2.jar` と同じディレクトリに配置します。

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

接続文字列は、Application Insights リソースで確認できます。

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights の接続文字列":::

**4.これで完了です。**

次に、アプリケーションを起動し、Azure portal の Application Insights リソースにアクセスして、監視データを表示します。

> [!NOTE]
> 監視データがポータルに表示されるまで、数分かかる場合があります。


## <a name="configuration-options"></a>構成オプション

`applicationinsights.json` ファイルでは、次の構成を追加できます。

* クラウド ロール名
* クラウド ロール インスタンス
* サンプリング
* JMX メトリック
* カスタム ディメンション
* テレメトリ プロセッサ (プレビュー)
* 自動収集されるログ
* 自動収集される Micrometer メトリック (Spring Boot アクチュエータ メトリックを含む)
* Heartbeat
* HTTP Proxy
* 自己診断

詳細については、[構成オプション](./java-standalone-config.md)に関するページを参照してください。

## <a name="auto-collected-requests-dependencies-logs-and-metrics"></a>自動収集された要求、依存関係、ログ、およびメトリック

### <a name="requests"></a>Requests

* JMS コンシューマー
* Kafka コンシューマー
* Netty/WebFlux
* Servlets
* Spring スケジュール

### <a name="dependencies-with-distributed-trace-propagation"></a>分散トレース伝達での依存関係

* Apache HttpClient と HttpAsyncClient
* gRPC
* java.net.HttpURLConnection
* JMS
* Kafka
* Netty クライアント
* OkHttp

### <a name="other-dependencies"></a>その他の依存関係

* Cassandra
* JDBC
* MongoDB (非同期および同期)
* Redis (Lettuce および Jedis)

### <a name="logs"></a>ログ

* java.util.logging
* Log4j (MDC プロパティを含む)
* SLF4J/Logback (MDC プロパティを含む)

### <a name="metrics"></a>メトリック

* マイクロメーター (Spring Boot アクチュエータ メトリックを含む)
* JMX メトリック

## <a name="send-custom-telemetry-from-your-application"></a>アプリケーションからカスタム テレメトリを送信する

3\.0 以降での目標は、標準 API を使用してカスタム テレメトリを送信できるようにすることです。

現時点では、Micrometer、一般的なログ記録フレームワーク、および Application Insights Java 2.x SDK をサポートしています。
Application Insights Java 3.0 を使用すると、これらの API を使用して送信されたテレメトリが自動的にキャプチャされ、自動収集されたテレメトリと関連付けられます。

### <a name="supported-custom-telemetry"></a>サポートされているカスタム テレメトリ

次の表は、現在サポートされているカスタム テレメトリの種類を示しています。これを有効にすると、Java 3.0 エージェントを補完することができます。 要約すると、カスタム メトリックはマイクロメーターを通じてサポートされ、カスタムの例外とトレースはログ記録フレームワークを使用して有効にできます。また、カスタム テレメトリの種類は、[Application Insights Java 2.x SDK](#send-custom-telemetry-using-the-2x-sdk) を通じてサポートされます。

|                     | Micrometer | Log4j、logback、JUL | 2.x SDK |
|---------------------|------------|---------------------|---------|
| **[カスタム イベント]**   |            |                     |  Yes    |
| **カスタム メトリック**  |  はい       |                     |  はい    |
| **依存関係**    |            |                     |  Yes    |
| **例外**      |            |  Yes                |  Yes    |
| **ページ ビュー**      |            |                     |  Yes    |
| **要求**        |            |                     |  Yes    |
| **トレース**          |            |  はい                |  はい    |

現時点では、Application Insights 3.0 を使用した SDK をリリースする予定はありません。

Application Insights Java 3.0 では、Application Insights Java 2.x SDK に送信されるテレメトリを既にリッスンしています。 この機能は、既存の 2.x ユーザーのアップグレード ストーリーの重要な部分であり、OpenTelemetry API が GA になるまでの、カスタム テレメトリ サポートの重要なギャップを埋めるものです。

### <a name="send-custom-metrics-using-micrometer"></a>Micrometer を使用してカスタム　メトリックを送信する

アプリケーションに Micrometer を追加します。

```xml
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-core</artifactId>
  <version>1.6.1</version>
</dependency>
```

Micrometer の[グローバル レジストリ](https://micrometer.io/docs/concepts#_global_registry)を使用して、メーターを作成します。

```java
static final Counter counter = Metrics.counter("test_counter");
```

次に、それを使用してメトリックを記録します。

```java
counter.increment();
```

### <a name="send-custom-traces-and-exceptions-using-your-favorite-logging-framework"></a>お気に入りのログ記録フレームワークを使用してカスタム トレースと例外を送信する

Log4j、Logback、java.util.logging は自動的にインストルメント化され、これらのログ記録フレームワークを介して実行されるログは、トレースおよび例外テレメトリとして自動収集されます。

既定では、INFO レベル以上でログ記録が実行された場合にのみ、ログが収集されます。
このレベルを変更する方法については、[構成オプション](./java-standalone-config.md#auto-collected-logging)に関する記事を参照してください。

カスタム ディメンションをログに添付する場合は、[Log4j 1.2 MDC](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html)、[Log4j 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html)、または [Logback MDC](http://logback.qos.ch/manual/mdc.html) を使用できます。また、Application Insights Java 3.0 を使用すると、トレースおよび例外テレメトリでそれらの MDC プロパティがカスタム ディメンションとして自動的にキャプチャされます。

### <a name="send-custom-telemetry-using-the-2x-sdk"></a>2\.x SDK を使用してカスタム テレメトリを送信する

アプリケーションに `applicationinsights-core-2.6.2.jar` を追加します (すべての 2.x バージョンは Application Insights Java 3.0 でサポートされていますが、選択が可能な場合は、最新のバージョンを使用することをお勧めします)。

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-core</artifactId>
  <version>2.6.2</version>
</dependency>
```

TelemetryClient を作成します。

  ```java
static final TelemetryClient telemetryClient = new TelemetryClient();
```

次に、それを使用して、カスタム テレメトリを送信します。

##### <a name="events"></a>events

```java
telemetryClient.trackEvent("WinGame");
```

##### <a name="metrics"></a>メトリック

```java
telemetryClient.trackMetric("queueLength", 42.0);
```

##### <a name="dependencies"></a>依存関係

```java
boolean success = false;
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
} finally {
    long endTime = System.currentTimeMillis();
    RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
    telemetry.setSuccess(success);
    telemetry.setTimestamp(new Date(startTime));
    telemetry.setDuration(new Duration(endTime - startTime));
    telemetryClient.trackDependency(telemetry);
}
```

##### <a name="logs"></a>ログ

```java
telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

##### <a name="exceptions"></a>例外

```java
try {
    ...
} catch (Exception e) {
    telemetryClient.trackException(e);
}
```

### <a name="add-request-custom-dimensions-using-the-2x-sdk"></a>2\.x SDK を使用して要求カスタム ディメンションを追加する

> [!NOTE]
> この機能は、3.0.2 以降にのみ存在します

アプリケーションに `applicationinsights-web-2.6.2.jar` を追加します (すべての 2.x バージョンは Application Insights Java 3.0 でサポートされていますが、選択が可能な場合は、最新のバージョンを使用することをお勧めします)。

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

コードにカスタム ディメンションを追加します。

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getProperties().put("mydimension", "myvalue");
```

### <a name="set-the-request-telemetry-user_id-using-the-2x-sdk"></a>2\.x SDK を使用して要求テレメトリ user_Id 設定する

> [!NOTE]
> この機能は、3.0.2 以降にのみ存在します

アプリケーションに `applicationinsights-web-2.6.2.jar` を追加します (すべての 2.x バージョンは Application Insights Java 3.0 でサポートされていますが、選択が可能な場合は、最新のバージョンを使用することをお勧めします)。

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

コードに `user_Id` を設定します。

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getContext().getUser().setId("myuser");
```

### <a name="override-the-request-telemetry-name-using-the-2x-sdk"></a>2\.x SDK を使用して要求テレメトリ名をオーバーライドする

> [!NOTE]
> この機能は、3.0.2 以降にのみ存在します

アプリケーションに `applicationinsights-web-2.6.2.jar` を追加します (すべての 2.x バージョンは Application Insights Java 3.0 でサポートされていますが、選択が可能な場合は、最新のバージョンを使用することをお勧めします)。

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

コードに名前を設定します。

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.setName("myname");
```

### <a name="get-the-request-telemetry-id-and-the-operation-id-using-the-2x-sdk"></a>2\.x SDK を使用して要求テレメトリ ID と操作 ID を取得します。

> [!NOTE]
> この機能は、3.0.3 以降のベータ版でのみ提供されています。

アプリケーションに `applicationinsights-web-2.6.2.jar` を追加します (すべての 2.x バージョンは Application Insights Java 3.0 でサポートされていますが、選択が可能な場合は、最新のバージョンを使用することをお勧めします)。

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

次のコードで、要求テレメトリ ID と操作 ID を取得します。

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
String requestId = requestTelemetry.getId();
String operationId = requestTelemetry.getContext().getOperation().getId();
```
