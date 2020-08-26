---
title: Java アプリケーションを任意の環境で監視する - Azure Monitor Application Insights
description: アプリをインストルメント化することなく、任意の環境で実行されている Java アプリケーションのアプリケーション パフォーマンスを監視します。 分散トレースとアプリケーション マップです。
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: e1442d1b1fb1bf8fbef82354b8aa1d2354640aa9
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87902084"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights---public-preview"></a>Azure Monitor Application Insights を監視する Java のコード不要のアプリケーション - パブリックプレビュー

Java のコード不要のアプリケーション監視は、シンプルさがすべてです。コードの変更がなく、いくつかの構成変更を行うだけで、Java エージェントを有効にすることができます。

 Java エージェントは、任意の環境で動作し、すべての Java アプリケーションを監視できます。 つまり、Java アプリを実行しているのが、VM、オンプレミス、AKS、Windows、Linux などのいずれであっても、Java 3.0 エージェントがアプリを監視します。

3\.0 エージェントが要求、依存関係、ログをすべて独自に収集するため、アプリケーションに Application Insights Java SDK を追加する必要がなくなりました。

アプリケーションからカスタム テレメトリを送信することはできます。 3\.0 エージェントは、自動収集されたすべてのテレメトリと連動して、追跡と関連付けを行います。

## <a name="quickstart"></a>クイック スタート

**1.エージェントのダウンロード**

[applicationinsights-agent-3.0.0-PREVIEW.5.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.5/applicationinsights-agent-3.0.0-PREVIEW.5.jar) をダウンロードします。

**2.JVM をエージェントにポイントする**

アプリケーションの JVM 引数に `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.5.jar` を追加します

一般的な JVM 引数には、`-Xmx512m` と `-XX:+UseG1GC` があります。 これらの引数の追加先がわかれば、これの追加先もわかります。

アプリケーションの JVM 引数の構成に関する追加のヘルプについては、「[3.0 Preview: Tips for updating your JVM args](./java-standalone-arguments.md)」 (3.0 プレビュー: JVM の引数の更新に関するヒント) を参照してください。

**3.エージェントを Application Insights リソースにポイントする**

Application Insights リソースをまだ持っていない場合は、[リソース作成ガイド](./create-new-resource.md)の手順に従って、新しいリソースを作成できます。

環境変数を設定して、エージェントを Application Insights リソースにポイントします。

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000
```

または、次の内容で、`ApplicationInsights.json` という名前の構成ファイルを作成し、`applicationinsights-agent-3.0.0-PREVIEW.5.jar` と同じディレクトリに配置します。

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

接続文字列は、Application Insights リソースで確認できます。

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights の接続文字列":::

**4.これで完了です。**

次に、アプリケーションを起動し、Azure portal の Application Insights リソースにアクセスして、監視データを表示します。

> [!NOTE]
> 監視データがポータルに表示されるまで、数分かかる場合があります。


## <a name="configuration-options"></a>構成オプション

`ApplicationInsights.json` ファイルでは、次の構成を追加できます。

* クラウド ロール名
* クラウド ロール インスタンス
* アプリケーション ログ キャプチャ
* JMX メトリック
* Micrometer
* Heartbeat
* サンプリング
* HTTP Proxy
* 自己診断

詳細については、「[3.0 Public Preview: Configuration Options](./java-standalone-config.md)」 (3.0 パブリック プレビュー: 自動収集された要求、依存関係、ログ、およびメトリック) を参照してください。

## <a name="autocollected-requests-dependencies-logs-and-metrics"></a>自動収集された要求、依存関係、ログ、およびメトリック

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
* Log4j
* SLF4J/Logback

### <a name="metrics"></a>メトリック

* マイクロメーター (Spring Boot アクチュエータ メトリックを含む)
* JMX メトリック

## <a name="sending-custom-telemetry-from-your-application"></a>アプリケーションからカスタム テレメトリを送信する

3\.0 以降での目標は、標準 API を使用してカスタム テレメトリを送信できるようにすることです。

Micrometer、OpenTelemetry API、および一般的なログ記録フレームワークがサポートされています。 Application Insights Java 3.0 では、テレメトリが自動的にキャプチャされ、自動収集されたすべてのテレメトリと連動して、関連付けが行われます。

### <a name="supported-custom-telemetry"></a>サポートされているカスタム テレメトリ

次の表は、現在サポートされているカスタム テレメトリの種類を示しています。これを有効にすると、Java 3.0 エージェントを補完することができます。 要約すると、カスタム メトリックはマイクロメーターを通じてサポートされ、カスタムの例外とトレースはログ記録フレームワークを使用して有効にできます。また、カスタム テレメトリの種類は、[Application Insights Java 2.x SDK](#sending-custom-telemetry-using-application-insights-java-sdk-2x) を通じてサポートされます。 

|                     | Micrometer | Log4j、logback、JUL | 2.x SDK |
|---------------------|------------|---------------------|---------|
| **[カスタム イベント]**   |            |                     |  Yes    |
| **カスタム メトリック**  |  はい       |                     |  はい    |
| **依存関係**    |            |                     |  Yes    |
| **例外**      |            |  はい                |  はい    |
| **ページ ビュー**      |            |                     |  Yes    |
| **要求**        |            |                     |  Yes    |
| **トレース**          |            |  はい                |  はい    |

現時点では、Application Insights 3.0 を使用した SDK をリリースする予定はありません。

Application Insights Java 3.0 では、Application Insights Java SDK 2.x に送信されるテレメトリを既にリッスンしています。 この機能は、既存の 2.x ユーザーのアップグレード ストーリーの重要な部分であり、OpenTelemetry API が GA になるまでの、カスタム テレメトリ サポートの重要なギャップを埋めるものです。

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>Application Insights Java SDK 2.x を使用するカスタム テレメトリを送信する

アプリケーションに `applicationinsights-core-2.6.0.jar` を追加します (すべての 2.x バージョンは Application Insights Java 3.0 でサポートされていますが、選択が可能な場合は、最新のバージョンを使用することをお勧めします)。

```xml
  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-core</artifactId>
    <version>2.6.0</version>
  </dependency>
```

TelemetryClient を作成します。

  ```java
private static final TelemetryClient telemetryClient = new TelemetryClient();
```

また、これを使用して、カスタム テレメトリを送信します。

### <a name="events"></a>events

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>メトリック

[Micrometer](https://micrometer.io) を使用して、メトリック テレメトリを送信できます。

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

または、Application Insights Java SDK 2.x を使用することもできます。

```java
  telemetryClient.trackMetric("queueLength", 42.0);
```

### <a name="dependencies"></a>依存関係

```java
  boolean success = false;
  long startTime = System.currentTimeMillis();
  try {
      success = dependency.call();
  } finally {
      long endTime = System.currentTimeMillis();
      RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
      telemetry.setTimestamp(new Date(startTime));
      telemetry.setDuration(new Duration(endTime - startTime));
      telemetryClient.trackDependency(telemetry);
  }
```

### <a name="logs"></a>ログ
お気に入りのログ記録フレームワークを介してカスタム ログ テレメトリを送信できます。

または、Application Insights Java SDK 2.x を使用することもできます。

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>例外
お気に入りのログ記録フレームワークを介してカスタム例外テレメトリを送信できます。

または、Application Insights Java SDK 2.x を使用することもできます。

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```

## <a name="upgrading-from-application-insights-java-sdk-2x"></a>Application Insights Java SDK 2.x からのアップグレード

アプリケーションで Application Insights Java SDK 2.x を既に使用している場合は、削除する必要はありません。 Java 3.0 エージェントでは、Java SDK 2.x を介して送信しているカスタム テレメトリの検出、キャプチャ、関連付けを行います。一方で、Java SDK 2.x によって実行される自動収集を抑制し、キャプチャの重複を防止します。

> [!NOTE]
> 注:3.0 エージェントを使用している場合、Java SDK 2.x TelemetryInitializers と TelemetryProcessors は実行されません。