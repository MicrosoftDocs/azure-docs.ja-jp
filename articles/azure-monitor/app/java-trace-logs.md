---
title: Azure Application Insights で Java トレース ログを探索する
description: Application Insights を使用して Log4J または Logback のトレースを検索します
ms.topic: conceptual
ms.date: 05/18/2019
ms.custom: devx-track-java
ms.openlocfilehash: fca5c9b1bde8429d829ab6113804602c8baa2ebe
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374271"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Application Insights を使用した Java トレース ログの探索
トレース用に Logback または Log4J (v1.2 または v2.0) を使用している場合は、トレース ログを自動的に Application Insights に送信して、Application Insights でトレース ログを探索および検索できます。

> [!TIP]
> Application Insights インストルメンテーション キーは、アプリケーションに対して 1 回だけ設定する必要があります。 Java Spring のようなフレームワークを使用している場合は、アプリの構成の別の場所で既にキーを登録済みである可能性があります。

## <a name="using-the-application-insights-java-agent"></a>Application Insights Java エージェントを使用する

既定では、Application Insights Java エージェントは、`WARN` レベル以上で実行されたログを自動的にキャプチャします。

`AI-Agent.xml` ファイルを使用して、キャプチャされるログのしきい値を変更できます。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging threshold="info"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

`AI-Agent.xml` ファイルを使用して、Java エージェントのログ キャプチャを無効にすることができます。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging enabled="false"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="alternatively-as-opposed-to-using-the-java-agent-you-can-follow-the-instructions-below"></a>別の方法として (Java エージェントは使用する代わりに)、次の手順に従うこともできます。

### <a name="install-the-java-sdk"></a>Java SDK をインストールする

[Application Insights SDK for Java][java] をまだインストールしていない場合は、インストールする手順に従います。

### <a name="add-logging-libraries-to-your-project"></a>プロジェクトへのログ ライブラリの追加
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
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v1.2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Gradle を使用している場合:
プロジェクトが既に Gradle を使用してビルドする設定になっている場合は、次のいずれかの行を build.gradle ファイルの `dependencies` グループに追加します。

次に、バイナリがダウンロードされるように、プロジェクトの依存関係を更新します。

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J v2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J v1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>それ以外の場合:
Application Insights Java SDK を手動でインストールするガイドラインに従って、適切なアペンダーの Jar (Maven Central ページに移動した後、ダウンロード セクションの 'jar' リンクをクリック) をダウンロードし、ダウンロードされたアペンダー Jar をプロジェクトに追加します。

| ロガー | ダウンロード | ライブラリ |
| --- | --- | --- |
| Logback |[Logback アペンダー Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logging-logback |
| Log4J v2.0 |[Log4J v2 アペンダー Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4J v1.2 |[Log4J v1.2 アペンダー Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


### <a name="add-the-appender-to-your-logging-framework"></a>ログ フレームワークへのアペンダーの追加
トレースの取得を開始するには、適切なコード スニペットを Log4J または Logback の構成ファイルに追加します。 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
        <instrumentationKey>[APPLICATION_INSIGHTS_KEY]</instrumentationKey>
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" instrumentationKey="[APPLICATION_INSIGHTS_KEY]" />
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
        <param name="instrumentationKey" value="[APPLICATION_INSIGHTS_KEY]" />
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

Application Insights のアペンダーは、上記のコード サンプルに示されているように、構成済みの任意のロガーによって参照でき、必ずしもルート ロガーを使用する必要はありません。

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Application Insights ポータルでのトレースの探索
これで、Application Insights にトレースを送信するようにプロジェクトが構成されました。これらのトレースは、Application Insights ポータルの [[検索]][diagnostic] ブレードで表示して検索できます。

ロガー経由で送信された例外は、例外のテレメトリとしてポータルに表示されます。

![Application Insights ポータルで [検索] を開きます](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>次のステップ
[診断検索][diagnostic]

<!--Link references-->

[diagnostic]: ./diagnostic-search.md
[java]: java-get-started.md

