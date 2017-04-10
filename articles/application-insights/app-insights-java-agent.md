---
title: "Azure Application Insights での Java Web アプリのパフォーマンス監視 | Microsoft Docs"
description: "Application Insights を使用した Java Web サイトのパフォーマンスおよび利用状況の監視拡張。"
services: application-insights
documentationcenter: java
author: harelbr
manager: douge
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 08ce387dd37ef2fec8f4dded23c20217a36e9966
ms.openlocfilehash: a481c7c62383c92a5dfab0e3f2b1b4f4f0e5ddf5


---
# <a name="monitor-dependencies-exceptions-and-execution-times-in-java-web-apps"></a>Java Web アプリでの依存関係、例外、および実行時間の監視


[Java Web アプリを Application Insights][java] でインストルメント化した場合、Java エージェントを使用して、コードを変更することなく、詳細な分析を行うことができます。

* **依存関係:** アプリケーションが他のコンポーネントに対して行った呼び出しについてのデータであり、次のものを含みます。
  * **REST 呼び出し** : HttpClient、OkHttp、および RestTemplate (Spring) 経由で行われた呼び出しです。
  * **Redis 呼び出し** : Jedis クライアント経由で行われた呼び出しです。 呼び出しにかかる時間が 10 秒を超えた場合、エージェントは呼び出し引数も取得します。
  * **[JDBC 呼び出し](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)**: MySQL、SQL Server、PostgreSQL、SQLite、Oracle DB、または Apache Derby DB。 "executeBatch" 呼び出しがサポートされます。 MySQL と PostgreSQL で呼び出しにかかる時間が 10 秒を超えた場合、エージェントはクエリ プランをレポートします。
* **例外の検出:** コードで処理される例外に関するデータ。
* **メソッドの実行時間:** 特定のメソッドの実行にかかる時間に関するデータ。

Java エージェントを使用するには、これをサーバーにインストールします。 Web アプリを [Application Insights Java SDK][java] を使用してインストルメント化する必要があります。 

## <a name="install-the-application-insights-agent-for-java"></a>Jave 用の Application Insights エージェントのインストール
1. Java サーバーを実行しているコンピューターで [エージェントをダウンロード](https://aka.ms/aijavasdk)します。
2. アプリケーション サーバーのスタートアップ スクリプトを編集し、次の JVM を追加します。
   
    `javaagent:`*エージェント JAR ファイルへの完全パス*
   
    たとえば、Linux マシンの Tomcat で以下を実行します。
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. アプリケーション サーバーを再起動します。

## <a name="configure-the-agent"></a>エージェントの構成
`AI-Agent.xml` という名前のファイルを作成し、エージェントの JAR ファイルの場所と同じフォルダーに配置します。

xml ファイルの内容を設定します。 次の例を編集して、必要に応じて、機能を含めるか省略します。

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>

        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>

           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne"
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>

      </Instrumentation>
    </ApplicationInsightsAgent>

```

レポートの例外や、個々のメソッドのメソッド タイミングを有効にする必要があります。

既定では、`reportExecutionTime` は true、`reportCaughtExceptions` は false です。

## <a name="view-the-data"></a>データの表示
Application Insights リソースでは、集計されたリモートの依存関係やメソッドの実行時間が [[パフォーマンス] タイル][metrics]に表示されます。

依存関係、例外、メソッドのレポートの個々のインスタンスを検索するには、[[検索]][diagnostic] を開きます。

「[依存関係の問題の診断](app-insights-asp-net-dependencies.md#diagnosis)」を参照してください。

## <a name="questions-problems"></a>疑問がある場合 問題が発生した場合
* データが表示されない場合 [ファイアウォール例外の設定](app-insights-ip-addresses.md)
* [Java のトラブルシューティング](app-insights-java-troubleshoot.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md



<!--HONumber=Jan17_HO4-->


