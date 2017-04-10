---
title: "Azure Application Insights を使用した Java トレース ログの探索 | Microsoft Docs"
description: "Application Insights を使用して Log4J または Logback のトレースを検索します"
services: application-insights
documentationcenter: java
author: alancameronwills
manager: carmonm
ms.assetid: fc0a9e2f-3beb-4f47-a9fe-3f86cd29d97a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 086091bb3be6659436ec8e371acbe4b8e86bfacb
ms.openlocfilehash: 3ef5f6240d7e4704118b86fb0cfee8cf502859ff


---
# <a name="explore-java-trace-logs-in-application-insights"></a>Application Insights を使用した Java トレース ログの探索
トレース用に Logback または Log4J (v1.2 または v2.0) を使用している場合は、トレース ログを自動的に Application Insights に送信して、Application Insights でトレース ログを探索および検索できます。

## <a name="install-the-java-sdk"></a>Java SDK をインストールする

[Application Insights SDK for Java][java] をインストールしていない場合はインストールします。

(HTTP 要求を追跡したくない場合は、.xml 構成ファイルのほとんどを省略できますが、少なくとも `InstrumentationKey` 要素を含める必要があります。 また、`new TelemetryClient()` を呼び出して SDK を初期化する必要があります。)


## <a name="add-logging-libraries-to-your-project"></a>プロジェクトへのログ ライブラリの追加
*プロジェクトに適した方法を選択してください。*

#### <a name="if-youre-using-maven"></a>Maven を使用している場合:
プロジェクトが既に Maven を使用してビルドする設定になっている場合は、pom.xml ファイルに次のいずれかのコード スニペットをマージします。

次に、バイナリがダウンロードされるように、プロジェクトの依存関係を更新します。

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[1.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[1.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v1.2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[1.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Gradle を使用している場合:
プロジェクトが既に Gradle を使用してビルドする設定になっている場合は、次のいずれかの行を build.gradle ファイルの `dependencies` グループに追加します。

次に、バイナリがダウンロードされるように、プロジェクトの依存関係を更新します。

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '1.0.+'
```

**Log4J v2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '1.0.+'
```

**Log4J v1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '1.0.+'
```

#### <a name="otherwise-"></a>それ以外の場合:
適切なアペンダーをダウンロードして展開し、適切なライブラリをプロジェクトに追加します。

| ロガー | ダウンロード | ライブラリ |
| --- | --- | --- |
| Logback |[Logback アペンダーを含む SDK](https://aka.ms/xt62a4) |applicationinsights-logging-logback |
| Log4J v2.0 |[Log4J v2 アペンダーを含む SDK](https://aka.ms/qypznq) |applicationinsights-logging-log4j2 |
| Log4J v1.2 |[Log4J v1.2 アペンダーを含む SDK](https://aka.ms/ky9cbo) |applicationinsights-logging-log4j1_2 |

## <a name="add-the-appender-to-your-logging-framework"></a>ログ フレームワークへのアペンダーの追加
トレースの取得を開始するには、適切なコード スニペットを Log4J または Logback の構成ファイルに追加します。 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.Log4j">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J v1.2*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

Application Insights のアペンダーは、上記のコード サンプルに示されているように、構成済みの任意のロガーによって参照でき、必ずしもルート ロガーを使用する必要はありません。

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Application Insights ポータルでのトレースの探索
これで、Application Insights にトレースを送信するようにプロジェクトが構成されました。これらのトレースは、Application Insights ポータルの [[検索]][diagnostic] ブレードで表示して検索できます。

![Application Insights ポータルで [検索] を開きます](./media/app-insights-java-trace-logs/10-diagnostics.png)

## <a name="next-steps"></a>次のステップ
[診断検索][diagnostic]

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md





<!--HONumber=Dec16_HO2-->


