---
title: 構成オプション - Azure Monitor Application Insights for Java
description: Azure Monitor Application Insights for Java を構成する方法
ms.topic: conceptual
ms.date: 11/04/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: e58d69634712a9cc640ba9e4785a7bf1effaf88c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103224658"
---
# <a name="configuration-options---azure-monitor-application-insights-for-java"></a>構成オプション - Azure Monitor Application Insights for Java

> [!WARNING]
> **3.0 Preview からアップグレードする場合**
>
> ファイル名自体がすべて小文字になったのに加えて、JSON 構造体が完全に変更されたため、以下のすべての構成オプションを注意深く確認してください。

## <a name="connection-string-and-role-name"></a>接続文字列とロール名

接続文字列とロール名は、作業を開始するために必要な最も一般的な設定です。

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  }
}
```

接続文字列は必須です。ロール名は、異なるアプリケーションから同じ Application Insights リソースにデータを送信する場合に常に重要です。

詳細と追加の構成オプションについては、以下をご覧ください。

## <a name="configuration-file-path"></a>構成ファイルのパス

Application Insights Java 3.0 は、既定では構成ファイルが `applicationinsights.json` という名前で、`applicationinsights-agent-3.0.2.jar` と同じディレクトリに配置されていることが想定されています。

独自の構成ファイルのパスを指定するには、以下のいずれかを使用します

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE`環境変数、または
* `applicationinsights.configuration.file` Java システム プロパティ

相対パスを指定すると、`applicationinsights-agent-3.0.2.jar` が配置されているディレクトリからの相対でパスが解決されます。

## <a name="connection-string"></a>接続文字列

接続文字列は必須です。 接続文字列は、Application Insights リソースで確認できます。

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights の接続文字列":::


```json
{
  "connectionString": "InstrumentationKey=..."
}
```

環境変数 `APPLICATIONINSIGHTS_CONNECTION_STRING` を使用して接続文字列を設定することもできます (接続文字列が JSON 構成でも指定されている場合に優先されます)。

接続文字列を設定しないと、Java エージェントが無効になります。

## <a name="cloud-role-name"></a>クラウド ロール名

クラウド ロール名は、アプリケーション マップのコンポーネントにラベルを付けるために使用します。

クラウド ロール名を設定する場合:

```json
{
  "role": {   
    "name": "my cloud role name"
  }
}
```

クラウド ロール名が設定されていない場合は、アプリケーション マップのコンポーネントにラベルを付けるために、Application Insights リソースの名前が使用されます。

環境変数 `APPLICATIONINSIGHTS_ROLE_NAME` を使用してクラウド ロール名を設定することもできます (クラウド ロール名が JSON 構成でも指定されている場合に優先されます)。

## <a name="cloud-role-instance"></a>クラウド ロール インスタンス

クラウド ロール インスタンスの既定値は、コンピューター名です。

クラウド ロール インスタンスをコンピューター名以外のものに設定する場合は、次のように設定します。

```json
{
  "role": {
    "name": "my cloud role name",
    "instance": "my cloud role instance"
  }
}
```

環境変数 `APPLICATIONINSIGHTS_ROLE_INSTANCE` を使用してクラウド ロール インスタンスを設定することもできます (クラウド ロール インスタンスが JSON 構成でも指定されている場合に優先されます)。

## <a name="sampling"></a>サンプリング

サンプリングは、コストの削減が必要な場合に役立ちます。
サンプリングは、操作 ID (トレース ID とも呼ばれます) で関数として実行されるため、操作 ID が同じ場合は常にサンプリング決定が同じになります。 これにより、分散トランザクションの一部がサンプリング対象となって、その他の部分がサンプリング対象外となることがないように保証されます。

たとえば、サンプリングを 10% に設定した場合、トランザクションの 10% のみが表示されますが、これらの 10% のそれぞれに完全なエンドツーエンドのトランザクション詳細が含まれます。

ここでは、**すべてのトランザクションの約 3 分の 1** をキャプチャするようにサンプリングを設定する方法の例を示します。必ず、ユースケースに適したサンプリング レートを設定してください。

```json
{
  "sampling": {
    "percentage": 33.333
  }
}
```

環境変数 `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` を使用してサンプリング率を設定することもできます (サンプリング率が JSON 構成でも指定されている場合に優先されます)。

> [!NOTE]
> サンプリング率には、N を整数として 100/N に近い割合を選択します。 サンプリングでは現在、その他の値はサポートされていません。

## <a name="jmx-metrics"></a>JMX メトリック

追加の JMX メトリックを収集する場合は、次のようにします。

```json
{
  "jmxMetrics": [
    {
      "name": "JVM uptime (millis)",
      "objectName": "java.lang:type=Runtime",
      "attribute": "Uptime"
    },
    {
      "name": "MetaSpace Used",
      "objectName": "java.lang:type=MemoryPool,name=Metaspace",
      "attribute": "Usage.used"
    }
  ]
}
```

`name` は、この JMX メトリックに割り当てられるメトリック名です (何でもかまいません)。

`objectName` は、収集する JMX MBean の[オブジェクト名](https://docs.oracle.com/javase/8/docs/api/javax/management/ObjectName.html)です。

`attribute` は、収集する JMX MBean 内の属性名です。

数値およびブール型の JMX メトリック値がサポートされています。 ブール型の JMX メトリックは、false の場合は `0` に、true の場合は `1` にマップされます。

## <a name="custom-dimensions"></a>カスタム ディメンション

すべてのテレメトリにカスタム ディメンションを追加する場合は、次のようにします。

```json
{
  "customDimensions": {
    "mytag": "my value",
    "anothertag": "${ANOTHER_VALUE}"
  }
}
```

`${...}` を使用すると、起動時に指定した環境変数から値を読み取ることができます。

> [!NOTE]
> バージョン 3.0.2 以降では、`service.version` という名前のカスタム ディメンションを追加した場合、値はカスタム ディメンションとしてではなく、Application Insights ログ テーブルの `application_Version` 列に格納されます。

## <a name="telemetry-processors-preview"></a>テレメトリ プロセッサ (プレビュー)

この機能はプレビュー段階にあります。

要求、依存関係、トレースのテレメトリに適用されるルールを構成できます。次に例を示します。
 * 機密データをマスクする
 * 条件付きでカスタム ディメンションを追加する
 * 集計や表示に使用されるテレメトリ名を更新する

詳細については、[テレメトリ プロセッサ](./java-standalone-telemetry-processors.md)を確認してください。

## <a name="auto-collected-logging"></a>自動収集されるログ

Log4j、Logback、java.util.logging は自動的にインストルメント化され、これらのログ記録フレームワークを介して実行されるログは自動収集されます。

ログ記録は、ログ記録フレームワークに構成されているレベルを最初に満たし、次に Application Insights に構成されているレベルも満たす場合にのみキャプチャされます。

たとえば、パッケージ `com.example` から `WARN` (以上) をログに記録するようにログ記録フレームワークが構成されており、`INFO` (以上) をキャプチャするように Application Insights が構成されている場合、Application Insights では、パッケージ `com.example` から `WARN` (以上) しかキャプチャしません。

Application Insights に構成されている既定のレベルは `INFO` です。 このレベルを変更する場合:

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

環境変数 `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL` を使用してレベルを設定することもできます (レベルが JSON 構成でも指定されている場合に優先されます)。

以下に、`applicationinsights.json` ファイルに指定できる有効な `level` 値と、それらが各種ログ フレームワークのログ レベルにどのように対応するかを示します。

| level             | Log4j  | Logback | JUL     |
|-------------------|--------|---------|---------|
| OFF               | OFF    | OFF     | OFF     |
| FATAL             | FATAL  | ERROR   | SEVERE  |
| ERROR (または SEVERE) | ERROR  | ERROR   | SEVERE  |
| WARN (または WARNING) | WARN   | WARN    | WARNING |
| INFO              | INFO   | INFO    | INFO    |
| CONFIG            | DEBUG  | DEBUG   | CONFIG  |
| DEBUG (または FINE)   | DEBUG  | DEBUG   | FINE    |
| FINER             | DEBUG  | DEBUG   | FINER   |
| TRACE (または FINEST) | TRACE  | TRACE   | FINEST  |
| ALL               | ALL    | ALL     | ALL     |

> [!NOTE]
> ロガーに例外オブジェクトが渡されると、Azure portal 内で `traces` テーブルではなく `exceptions` テーブルの下にログ メッセージ (および例外オブジェクトの詳細) が表示されます。

## <a name="auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics"></a>自動収集される Micrometer メトリック (Spring Boot アクチュエータ メトリックを含む)

アプリケーションで [Micrometer](https://micrometer.io) が使用されている場合、Micrometer のグローバル レジストリに送信されたメトリックは自動収集されます。

また、アプリケーションで [Spring Boot アクチュエータ](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)が使用されている場合、Spring Boot アクチュエータによって構成されたメトリックも自動収集されます。

Micrometer メトリック (Spring Boot アクチュエータ メトリックを含む) の自動収集を無効にするには、次のようにします。

> [!NOTE]
> カスタム メトリックの料金は別途請求され、追加のコストが発生する可能性があります。 必ず、詳しい[価格情報](https://azure.microsoft.com/pricing/details/monitor/)を確認してください。 Micrometer と Spring Actuator のメトリックを無効にするには、以下の構成を構成ファイルに追加します。

```json
{
  "instrumentation": {
    "micrometer": {
      "enabled": false
    }
  }
}
```

## <a name="suppressing-specific-auto-collected-telemetry"></a>特定の自動収集テレメトリの抑制

バージョン 3.0.2 以降では、こちらの構成オプションを使用して、特定の自動収集テレメトリを抑制できます。

```json
{
  "instrumentation": {
    "cassandra": {
      "enabled": false
    },
    "jdbc": {
      "enabled": false
    },
    "kafka": {
      "enabled": false
    },
    "micrometer": {
      "enabled": false
    },
    "mongo": {
      "enabled": false
    },
    "redis": {
      "enabled": false
    }
  }
}
```

## <a name="heartbeat"></a>Heartbeat

Application Insights Java 3.0 は、既定では 15 分ごとにハートビート メトリックを送信します。 ハートビート メトリックを使用してアラートをトリガーする場合は、このハートビートの頻度を増やすことができます。

```json
{
  "heartbeat": {
    "intervalSeconds": 60
  }
}
```

> [!NOTE]
> ハートビート データは Application Insights の使用状況の追跡にも使用されるため、間隔を 15 分より長くすることはできません。

## <a name="http-proxy"></a>HTTP Proxy

アプリケーションがファイアウォールの背後にあり、Application Insights に直接接続できない場合 ([Application Insights によって使用される IP アドレス](./ip-addresses.md)に関するページを参照)、HTTP プロキシを使用するように Application Insights Java 3.0 を構成できます。

```json
{
  "proxy": {
    "host": "myproxy",
    "port": 8080
  }
}
```

グローバルの `-Dhttps.proxyHost` と `-Dhttps.proxyPort` が設定されている場合、Application Insights Java 3.0 によってそれらも考慮されます。

## <a name="metric-interval"></a>メトリックの間隔

この機能はプレビュー段階にあります。

既定では、メトリックは 60 秒ごとにキャプチャされます。

バージョン 3.0.3-BETA 以降では、この間隔を変更できます。

```json
{
  "preview": {
    "metricIntervalSeconds": 300
  }
}
```

この設定は、次のすべてのメトリックに適用されます。

* 既定のパフォーマンス カウンター (CPU やメモリなど)
* 既定のカスタム メトリック (ガベージ コレクションのタイミングなど)
* 構成済み JMX メトリック ([上記を参照](#jmx-metrics))
* Micrometer メトリック ([上記を参照](#auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics))


[//]: # "注: OpenTelemetry API が 1.0 になるまで、OpenTelemetry のサポートはプライベート プレビュー段階です"

[//]: # "## 1.0 より前の OpenTelemetry API リリースのサポート"

[//]: # "OpenTelemetry API はまだ安定していないため、1.0 より前の OpenTelemetry API バージョンのサポートはオプトインです。"
[//]: # "そのため、エージェントの各バージョンでは、1.0 より前の特定の OpenTelemetry API バージョンのみがサポートされています "
[//]: # "(この制限は、OpenTelemetry API 1.0 がリリースされた後は適用されません)。"

[//]: # "```json"
[//]: # "{"
[//]: # "  \"プレビュー\": {"
[//]: # "    \"openTelemetryApiSupport\": true"
[//]: # "  }"
[//]: # "}"
[//]: # "```"

## <a name="self-diagnostics"></a>自己診断

"自己診断" では、Application Insights Java 3.0 からの内部ログを参照します。

この機能は、Application Insights 自体の問題を発見して診断する場合に役立ちます。

Application Insights Java 3.0 は、既定では `applicationinsights.log` ファイルとコンソールの両方に `INFO` レベルでログを記録します。これらは次の構成に対応します。

```json
{
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```

`destination` には、`file`、`console`、`file+console` のいずれかを指定できます。

`level` には、`OFF`、`ERROR`、`WARN`、`INFO`、`DEBUG`、`TRACE` のいずれかを指定できます。

`path` には、絶対パスまたは相対パスを指定できます。 相対パスは、`applicationinsights-agent-3.0.2.jar` があるディレクトリを基準にして解決されます。

`maxSizeMb` は、ロールオーバーされる前のログ ファイルの最大サイズです。

`maxHistory` は、(現在のログ ファイルに加えて) 保持される、ロールオーバーされたログ ファイルの数です。

バージョン 3.0.2 以降では、環境変数 `APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL` を使用して自己診断 `level` を設定することもできます (自己診断 `level` が JSON 構成でも指定されている場合に優先されます)。

## <a name="an-example"></a>使用例

これは、単に複数のコンポーネントを含む構成ファイルがどのように表示されるかを示す例です。
各自のニーズに応じて特定のオプションを構成してください。

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  },
  "sampling": {
    "percentage": 100
  },
  "jmxMetrics": [
  ],
  "customDimensions": {
  },
  "instrumentation": {
    "logging": {
      "level": "INFO"
    },
    "micrometer": {
      "enabled": true
    }
  },
  "proxy": {
  },
  "preview": {
    "processors": [
    ]
  },
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```
