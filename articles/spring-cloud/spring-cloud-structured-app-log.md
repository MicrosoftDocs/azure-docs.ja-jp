---
title: Azure Spring Cloud の構造化アプリケーション ログ | Microsoft Docs
description: この記事では、Azure Spring Cloud で構造化アプリケーション ログ データを生成および収集する方法について説明します。
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/13/2021
ms.author: brendanm
ms.custom: devx-track-java
ms.openlocfilehash: c5c35fe8a352a1bc3467e9512a7fcbc068375bfb
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056911"
---
# <a name="structured-application-log-for-azure-spring-cloud"></a>Azure Spring Cloud の構造化アプリケーション ログ

この記事では、Azure Spring Cloud で構造化アプリケーション ログ データを生成および収集する方法について説明します。 正しく構成すれば、Azure Spring Cloud で、アプリケーション ログの有用なクエリと分析が Log Analytics を通じて提供されます。

## <a name="log-schema-requirements"></a>ログ スキーマの要件
ログ クエリのエクスペリエンスを向上させるには、アプリケーション ログが JSON 形式であり、スキーマに準拠している必要があります。 Azure Spring Cloud では、このスキーマを使用してアプリケーションを解析し、Log Analytics にストリームします。 

**JSON スキーマの要件:**

| JSON キー      | JSON 値の型|  必須 | Log Analytics の列| 説明 |
| --------------| ------------|-----------|-----------------|--------------------------|
| timestamp     | string      |     はい   | AppTimestamp    | UTC 形式のタイムスタンプ  |
| logger        | string      |     いいえ    | ロガー          | logger                   |
| level         | string      |     いいえ    | CustomLevel     | ログ レベル                |
| スレッド (thread)        | string      |     いいえ    | スレッド          | スレッド (thread)                   |
| message       | string      |     いいえ    | Message         | ログ メッセージ              |
| stackTrace    | string      |     いいえ    | StackTrace      | 例外スタック トレース    |
| exceptionClass| string      |     いいえ    | ExceptionClass  | 例外クラス名     |
| mdc           | 入れ子の JSON |     いいえ    |                 | マップされた診断コンテキスト|
| mdc.traceId   | string      |     いいえ    | TraceId         |分散トレース用のトレース ID|
| mdc.spanId    | string      |     いいえ    | SpanId          |分散トレース用のスパン ID |
|               |             |           |                 |                          |

* "timestamp" フィールドは必須であり、UTC 形式である必要があります。他のすべてのフィールドは省略可能です。
* "mdc" フィールドの "traceId" と "spanId" はトレースに使用されます。
* 個々の JSON レコードはログに 1 行で記録されます。 

**ログ レコードのサンプル** 
 ```
{"timestamp":"2021-01-08T09:23:51.280Z","logger":"com.example.demo.HelloController","level":"ERROR","thread":"http-nio-1456-exec-4","mdc":{"traceId":"c84f8a897041f634","spanId":"c84f8a897041f634"},"stackTrace":"java.lang.RuntimeException: get an exception\r\n\tat com.example.demo.HelloController.throwEx(HelloController.java:54)\r\n\","message":"Got an exception","exceptionClass":"RuntimeException"}
```

## <a name="generate-schema-compliant-json-log"></a>スキーマ準拠の JSON ログを生成する  
Spring アプリケーションの場合、[logback](http://logback.qos.ch/) や [log4j2](https://logging.apache.org/log4j/2.x/) などの一般的な[ログ記録フレームワーク](https://docs.spring.io/spring-boot/docs/2.1.13.RELEASE/reference/html/boot-features-logging.html#boot-features-custom-log-configuration)を使用して、期待される JSON ログ形式を生成できます。 

### <a name="log-with-logback"></a>logback でログを記録する 
Spring Boot スターターを使用する場合は、既定で logback が使用されます。 logback アプリの場合は、[logstash-encoder](https://github.com/logstash/logstash-logback-encoder) を使用して JSON 形式のログを生成します。 この方法は Spring Boot バージョン 2.1 以降でサポートされています。 

手順:

1. logstash の依存関係を pom.xml ファイルに追加します。 

    ```json
    <dependency>
        <groupId>net.logstash.logback</groupId>
        <artifactId>logstash-logback-encoder</artifactId>
        <version>6.5</version>
    </dependency>
    ```
1. logback.xml 構成ファイルを更新して、JSON 形式を設定します。
    ```json
    <configuration>
        <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
            <encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
            <providers>
                <timestamp>
                    <fieldName>timestamp</fieldName>
                    <timeZone>UTC</timeZone>
                </timestamp>
                <loggerName>
                    <fieldName>logger</fieldName>
                </loggerName>
                <logLevel>
                    <fieldName>level</fieldName>
                </logLevel>
                <threadName>
                    <fieldName>thread</fieldName>
                </threadName>
                <nestedField>
                    <fieldName>mdc</fieldName>
                    <providers>
                        <mdc/>
                    </providers>
                </nestedField>
                <stackTrace>
                    <fieldName>stackTrace</fieldName>
                </stackTrace>
                <message/>
                <throwableClassName>
                    <fieldName>exceptionClass</fieldName>
                </throwableClassName>
            </providers>
            </encoder>
        </appender>
        <root level="info">
            <appender-ref ref="stdout"/>
        </root>
    </configuration>
    ```

### <a name="log-with-log4j2"></a>log4j2 でログを記録する 

log4j2 アプリの場合、[json-template-layout](https://logging.apache.org/log4j/2.x/manual/json-template-layout.html) を使用して JSON 形式のログを生成します。 この方法は Spring Boot バージョン 2.1 以降でサポートされています。

手順:

1. `spring-boot-starter-logging` を `spring-boot-starter` から除外し、依存関係 `spring-boot-starter-log4j2`、`log4j-layout-template-json` を pom.xml ファイルに追加します。

    ```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                    <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-log4j2</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-layout-template-json</artifactId>
            <version>2.14.0</version>
        </dependency>
    ```

2. JSON レイアウト テンプレート ファイル jsonTemplate.json をクラス パスに準備します。

    ```json
        {
            "mdc": {
                "$resolver": "mdc"
            },
            "exceptionClass": {
                "$resolver": "exception",
                "field": "className"
            },
            "stackTrace": {
                "$resolver": "exception",
                "field": "stackTrace",
                "stringified": true
            },
            "message": {
                "$resolver": "message",
                "stringified": true
            },
            "thread": {
                "$resolver": "thread",
                "field": "name"
            },
            "timestamp": {
                "$resolver": "timestamp",
                "pattern": {
                    "format": "yyyy-MM-dd'T'HH:mm:ss.SSS'Z'",
                    "timeZone": "UTC"
                }
            },
            "level": {
                "$resolver": "level",
                "field": "name"
            },
            "logger": {
                "$resolver": "logger",
                "field": "name"
            }
        }
    ```

3. この JSON レイアウト テンプレートを log4j2.xml 構成ファイルで使用します。 

    ```json
        <configuration>
            <appenders>
                <console name="Console" target="SYSTEM_OUT">
                <JsonTemplateLayout eventTemplateUri="classpath:jsonTemplate.json"/>
                </console>
            </appenders>
            <loggers>
                <root level="info">
                <appender-ref ref="Console"/>
                </root>
            </loggers>
        </configuration>
    ```

## <a name="analyze-the-logs-in-log-analytics"></a>Log Analytics でログを分析する

アプリケーションが正しくセットアップされると、アプリケーション コンソールのログが Log Analytics にストリームされます。 Log Analytics で効率的なクエリができる構造になっています。

### <a name="check-log-structure-in-log-analytics"></a>Log Analytics でログの構造を確認する
次の手順に従います。
1. サービス インスタンスのサービスの概要ページに移動します。
2. `Monitoring` セクションの `Logs` エントリをクリックします。
3. 次のクエリを実行します。

   ```
   AppPlatformLogsforSpring
   | where TimeGenerated > ago(1h)
   | project AppTimestamp, Logger, CustomLevel, Thread, Message, ExceptionClass, StackTrace, TraceId, SpanId
   ```

4. 次の図に示すように、アプリケーション ログが返されます。

![JSON ログの表示](media/spring-cloud-structured-app-log/json-log-query.png)

### <a name="show-log-entries-containing-errors"></a>エラーを含むログ エントリを表示する

エラーがあるログ エントリを確認するには、次のクエリを実行します。

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h) and CustomLevel == "ERROR" 
| project AppTimestamp, Logger, ExceptionClass, StackTrace, Message, AppName 
| sort by AppTimestamp
```

このクエリを使用してエラーを検出するか、クエリ用語を変更して特定の例外クラスまたはエラー コードを検索します。 

### <a name="show-log-entries-for-a-specific-traceid"></a>特定のトレース ID のログ エントリを表示する
特定のトレース ID "trace_id" のログ エントリを確認するには、次のクエリを実行します。

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where TraceId == "trace_id" 
| project AppTimestamp, Logger, TraceId, SpanId, StackTrace, Message, AppName 
| sort by AppTimestamp
```

## <a name="next-steps"></a>次の手順
* ログ クエリの詳細については「[Azure Monitor でログ クエリの使用を開始する](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)」を参照してください
