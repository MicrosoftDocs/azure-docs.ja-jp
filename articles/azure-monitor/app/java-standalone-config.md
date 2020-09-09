---
title: 任意の場所の Java アプリケーションを監視する - Azure Monitor Application Insights
description: アプリをインストルメント化することなく、任意の環境で実行されている Java アプリケーションのアプリケーション パフォーマンスをコード不要で監視します。 分散トレースとアプリケーション マップを使用して、問題の根本原因を見つけます。
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: devx-track-java
ms.openlocfilehash: ca3094197deb7c74ba9b51422a78ee0f5d3687d2
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374288"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>構成オプション - Azure Monitor Application Insights の Java スタンドアロン エージェント



## <a name="connection-string-and-role-name"></a>接続文字列とロール名

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
    "preview": {
      "roleName": "my cloud role name"
    }
  }
}
```

接続文字列は必須です。ロール名は、異なるアプリケーションから同じ Application Insights リソースにデータを送信する場合に常に重要です。

詳細については、以下の詳細と追加の構成オプションをご覧ください。

## <a name="configuration-file-path"></a>構成ファイルのパス

既定では Application Insights Java 3.0 Preview は、構成ファイルが `ApplicationInsights.json` という名前で、`applicationinsights-agent-3.0.0-PREVIEW.5.jar` と同じディレクトリに配置されていることが想定されています。

独自の構成ファイルのパスを指定するには、以下のいずれかを使用します

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE`環境変数、または
* `applicationinsights.configurationFile` Java システム プロパティ

相対パスを指定すると、`applicationinsights-agent-3.0.0-PREVIEW.5.jar` が配置されているディレクトリからの相対でパスが解決されます。

## <a name="connection-string"></a>接続文字列

これは必須です。 接続文字列は、Application Insights リソースで確認できます。

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights の接続文字列":::

環境変数 `APPLICATIONINSIGHTS_CONNECTION_STRING` を使用して、接続文字列を設定することもできます。

## <a name="cloud-role-name"></a>クラウド ロール名

クラウド ロール名は、アプリケーション マップのコンポーネントにラベルを付けるために使用します。

クラウド ロール名を設定する場合:

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
  }
}
```

クラウド ロール名が設定されていない場合は、アプリケーション マップのコンポーネントにラベルを付けるために、Application Insights リソースの名前が使用されます。

環境変数 `APPLICATIONINSIGHTS_ROLE_NAME` を使用して、クラウド ロール名を設定することもできます。

## <a name="cloud-role-instance"></a>クラウド ロール インスタンス

クラウド ロール インスタンスの既定値は、コンピューター名です。

クラウド ロール インスタンスをコンピューター名以外のものに設定する場合は、次のように設定します。

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

環境変数 `APPLICATIONINSIGHTS_ROLE_INSTANCE` を使用して、クラウド ロール インスタンスを設定することもできます。

## <a name="application-log-capture"></a>アプリケーション ログ キャプチャ

Application Insights Java 3.0 Preview では、Log4j、Logback、java.util.logging を使用して、アプリケーションのログが自動的にキャプチャされます。

既定では、`WARN` レベル以上で実行されるすべてのログがキャプチャされます。

このしきい値を変更する場合は、次のように設定します。

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "logging": {
          "threshold": "ERROR"
        }
      }
    }
  }
}
```

以下に、`ApplicationInsights.json` ファイルに指定できる有効な `threshold` 値と、それらが、各種ログ フレームワークでどのようにログ レベルに対応するかを示しています。

| `threshold`  | Log4j  | Logback | JUL     |
|--------------|--------|---------|---------|
| OFF          | OFF    | OFF     | OFF     |
| FATAL        | FATAL  | ERROR   | SEVERE  |
| ERROR/SEVERE | ERROR  | ERROR   | SEVERE  |
| WARN/WARNING | WARN   | WARN    | WARNING |
| INFO         | INFO   | INFO    | INFO    |
| CONFIG       | DEBUG  | DEBUG   | CONFIG  |
| DEBUG/FINE   | DEBUG  | DEBUG   | FINE    |
| FINER        | DEBUG  | DEBUG   | FINER   |
| TRACE/FINEST | TRACE  | TRACE   | FINEST  |
| ALL          | ALL    | ALL     | ALL     |

## <a name="jmx-metrics"></a>JMX メトリック

キャプチャしたい JMX メトリックがある場合は、次のように設定します。

```json
{
  "instrumentationSettings": {
    "preview": {
        "jmxMetrics": [
        {
          "objectName": "java.lang:type=ClassLoading",
          "attribute": "LoadedClassCount",
          "display": "Loaded Class Count"
        },
        {
          "objectName": "java.lang:type=MemoryPool,name=Code Cache",
          "attribute": "Usage.used",
          "display": "Code Cache Used"
        }
      ]
    }
  }
}
```

## <a name="micrometer-including-metrics-from-spring-boot-actuator"></a>Micrometer (Spring Boot アクチュエータのメトリックを含む)

アプリケーションで [Micrometer](https://micrometer.io) が使用されている場合、Application Insights 3.0 (Preview.2 以降) では、Micrometer のグローバル レジストリに送信されたメトリックがキャプチャされるようになりました。

アプリケーションで [Spring Boot アクチュエータ](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)が使用されている場合、Application Insights 3.0 (Preview.4 以降) では、Spring Boot アクチュエータ (Micrometer を使用するが、Micrometer のグローバル レジストリは使用しない) によって構成されたメトリックがキャプチャされるようになりました。

これらの機能を無効にする場合は、次のようにします。

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "micrometer": {
          "enabled": false
        }
      }
    }
  }
}
```

## <a name="heartbeat"></a>Heartbeat

既定では Application Insights Java 3.0 Preview は、15 分ごとにハートビート メトリックを送信します。 ハートビート メトリックを使用してアラートをトリガーする場合は、このハートビートの頻度を増やすことができます。

```json
{
  "instrumentationSettings": {
    "preview": {
        "heartbeat": {
            "intervalSeconds": 60
        }
    }
  }
}
```

> [!NOTE]
> ハートビートデータは Application Insights の使用状況を追跡するためにも使用されるため、このハートビートの頻度を下げることはできません。

## <a name="sampling"></a>サンプリング

サンプリングは、コストの削減が必要な場合に役立ちます。
サンプリングは、操作 ID (トレース ID とも呼ばれます) で関数として実行されるため、操作 ID が同じ場合は常にサンプリング決定が同じになります。 これにより、分散トランザクションの一部がサンプリング対象となって、その他の部分がサンプリング対象外となることがないように保証されます。

たとえば、サンプリングを 10% に設定した場合、トランザクションの 10% のみが表示されますが、これらの 10% のそれぞれに完全なエンドツーエンドのトランザクション詳細が含まれます。

ここでは、サンプリングを**すべてのトランザクションの 10%** に設定する方法の例を示します。必ず、ユースケースに適したサンプリング レートを設定してください。

```json
{
  "instrumentationSettings": {
    "preview": {
        "sampling": {
            "fixedRate": {
                "percentage": 10
            }
          }
        }
    }
}
```

## <a name="http-proxy"></a>HTTP Proxy

アプリケーションがファイアウォールの背後にあり、Application Insights に直接接続できない場合 ([Application Insights によって使用される IP アドレス](./ip-addresses.md)に関するページを参照)、HTTP プロキシを使用するように Application Insights Java 3.0 Preview を構成できます。

```json
{
  "instrumentationSettings": {
    "preview": {
      "httpProxy": {
        "host": "myproxy",
        "port": 8080
      }
    }
  }
}
```

## <a name="self-diagnostics"></a>自己診断

"自己診断" では、Application Insights Java 3.0 Preview からの内部ログを参照します。

これは、Application Insights 自体の問題を発見して診断する場合に役立ちます。

既定では、この構成に対応するレベル `warn` でコンソールにログが記録されます。

```json
{
  "instrumentationSettings": {
    "preview": {
        "selfDiagnostics": {
            "destination": "console",
            "level": "WARN"
        }
    }
  }
}
```

有効なレベルは、`OFF`、`ERROR`、`WARN`、`INFO`、`DEBUG`、`TRACE` です。

コンソールにログ記録するのではなく、ファイルにログを記録する場合は、次のように設定します。

```json
{
  "instrumentationSettings": {
    "preview": {
        "selfDiagnostics": {
            "destination": "file",
            "directory": "/var/log/applicationinsights",
            "level": "WARN",
            "maxSizeMB": 10
        }    
    }
  }
}
```

ファイルのログ記録を使用する場合、ファイルが `maxSizeMB` に達すると、ロールオーバーされ、現在のログ ファイルに加えて最後に完了したログ ファイルのみが保持されます。
