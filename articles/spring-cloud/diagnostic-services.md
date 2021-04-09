---
title: Azure Spring Cloud でログとメトリックを分析する |Microsoft Docs
description: Azure Spring Cloud で診断データを分析する方法について説明します
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: c354435f689f5182dd368fd53fdab28f1ab63d48
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218793"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>診断設定でログとメトリックを分析する

**この記事の適用対象:** ✔️ Java ✔️ C#

Azure Spring Cloud の診断機能を使用することで、次のいずれかのサービスを使用してログとメトリックを分析できます。

* データが Azure Storage に書き込まれる Azure Log Analytics を使用する。 Log Analytics にログをエクスポートするときに遅延が発生します。
* 監査や手動での検査に使用するために、ログをストレージ アカウントに保存する。 保持期間を指定できます (日数)。
* サード パーティのサービスやカスタム分析ソリューションで取り込むために、ログをイベント ハブにストリーム配信する。

監視するログ カテゴリとメトリック カテゴリを選択します。

> [!TIP]
> ログをストリーミングするだけの場合は、 こちらの [Azure CLI コマンド](/cli/azure/ext/spring-cloud/spring-cloud/app#ext-spring-cloud-az-spring-cloud-app-logs)を確認してください。

## <a name="logs"></a>ログ

|ログ | 説明 |
|----|----|
| **ApplicationConsole** | すべての顧客アプリケーションのコンソール ログ。 |
| **SystemLogs** | 現在、このカテゴリーでは [Spring Cloud Config Server](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) ログのみ。 |

## <a name="metrics"></a>メトリック

メトリックの完全な一覧については、[Spring Cloud のメトリック](./spring-cloud-concept-metrics.md#user-metrics-options)に関する記事を参照してください。

まず、これらのサービスのいずれかを有効にしてデータを受信します。 Log Analytics の構成については、「[Azure Monitor で Log Analytics の使用を開始する](../azure-monitor/logs/log-analytics-tutorial.md)」を参照してください。

## <a name="configure-diagnostics-settings"></a>診断設定の構成

1. Azure portal で Azure Spring Cloud インスタンスに移動します。
1. **[診断設定]** オプションを選択し、 **[診断設定の追加]** を選択します。
1. 設定の名前を入力してから、ログを送信する場所を選択します。 次の 3 つのオプションを自由に組み合わせて選択できます。
    * **ストレージ アカウントへのアーカイブ**
    * **イベント ハブへのストリーム**
    * **Log Analytics への送信**

1. 監視するログ カテゴリとメトリック カテゴリを選択してから、保持期間 (日数) を指定します。 保持期間はストレージ アカウントにのみ適用されます。
1. **[保存]** を選択します。

> [!NOTE]
> 1. ログまたはメトリックが生成されてから、ストレージ アカウント、イベント ハブ、または Log Analytics に表示されるまでに、最大 15 分のギャップがある場合があります。
> 1. Azure Spring Cloud インスタンスが削除または移動された場合、この操作は **診断設定** リソースには連鎖しません。 **診断設定** リソースは、その親 (つまり、Azure Spring Cloud インスタンス) に対して操作を行う前に手動で削除する必要があります。 そうしないと、新しい Azure Spring Cloud インスタンスが、削除されたものと同じリソース ID でプロビジョニングされる場合、または Azure Spring Cloud インスタンスが戻った場合は、以前の **診断設定** リソースが引き続きそれを拡張します。

## <a name="view-the-logs-and-metrics"></a>ログとメトリックの表示
次の見出しで説明しているように、ログとメトリックを表示するにはさまざまな方法があります。

### <a name="use-the-logs-blade"></a>ログ ブレードの使用

1. Azure portal で Azure Spring Cloud インスタンスに移動します。
1. **[ログ検索]** ウィンドウを開くには、 **[ログ]** を選択します。
1. **[テーブル]** 検索ボックスでは、以下を行うことができます
   * ログを表示するには、次のような単純なクエリを入力します。

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * メトリックを表示するには、次のような単純なクエリを入力します。

    ```sql
    AzureMetrics
    | limit 50
    ```
1. 検索結果を表示するには、 **[実行]** を選択します。

### <a name="use-log-analytics"></a>Log Analytics の使用

1. Azure portal の左ウィンドウで、 **[Log Analytics]** を選択します。
1. 診断設定を追加したときに選択した Log Analytics ワークスペースを選択します。
1. **[ログ検索]** ウィンドウを開くには、 **[ログ]** を選択します。
1. **[テーブル]** 検索ボックスでは、以下を行うことができます。
   * ログを表示するには、次のような単純なクエリを入力します。

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * メトリックを表示するには、次のような単純なクエリを入力します。

    ```sql
    AzureMetrics
    | limit 50
    ```

1. 検索結果を表示するには、 **[実行]** を選択します。
1. フィルター条件を設定することで、特定のアプリケーションまたはインスタンスのログを検索できます。

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]
> `==` では大文字と小文字が区別されますが、`=~` では区別されません。

Log Analytics で使用されるクエリ言語の詳細については、「[Azure Monitor ログ クエリ](/azure/data-explorer/kusto/query/)」を参照してください。

### <a name="use-your-storage-account"></a>ストレージ アカウントを使用する

1. Azure portal で、左側のナビゲーション パネルまたは検索ボックスから **ストレージ アカウント** を検索します。
1. 診断設定を追加したときに選択したストレージ アカウントを選択します。
1. **[BLOB コンテナー]** ウィンドウを開くには、 **[BLOB]** を選択します。
1. アプリケーション ログを確認するには、**insights-logs-applicationconsole** という名前のコンテナーを検索します。
1. アプリケーション メトリックを確認するには、**insights-metrics-pt1m** という名前のコンテナーを検索します。

ストレージ アカウントへの診断情報の送信の詳細については、「[Azure Storage への診断データの保存と表示](../storage/common/storage-introduction.md)」を参照してください。

### <a name="use-your-event-hub"></a>イベント ハブを使用する

1. Azure portal で、左側のナビゲーション パネルまたは検索ボックスから **[Event Hubs]** を検索します。

1. 診断設定を追加したときに選択したイベント ハブを検索し、選択します。
1. **[イベント ハブの一覧]** ウィンドウを開くには、 **[イベント ハブ]** を選択します。
1. アプリケーション ログを確認するには、**insights-logs-applicationconsole** という名前のイベント ハブを検索します。
1. アプリケーション メトリックを確認するには、**insights-metrics-pt1m** という名前のイベント ハブを検索します。

イベント ハブへの診断情報の送信の詳細については、「[Event Hubs を利用してホット パスの Azure Diagnostics データをストリーム配信する](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md)」を参照してください。

## <a name="analyze-the-logs"></a>ログの分析

Azure Log Analytics は Kusto エンジンを使用して実行されているので、ログに対してクエリを実行して分析することができます。 Kusto を使用したログのクエリの簡単な概要については、[Log Analytics チュートリアル](../azure-monitor/logs/log-analytics-tutorial.md)を確認してください。

アプリケーション ログには、アプリケーションの正常性やパフォーマンスなどに関する重要な情報と詳細ログが記載されています。 次のセクションでは、アプリケーションの現在の状態と過去の状態を理解するのに役立つ単純なクエリを示します。

### <a name="show-application-logs-from-azure-spring-cloud"></a>Azure Spring Cloud からアプリケーション ログを表示する

Azure Spring Cloud からアプリケーション ログの一覧 (時間順に並べ替え、最新のログを最初に表示) を確認するには、次のクエリを実行します。

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>エラーまたは例外を含むログ エントリを表示する

エラーまたは例外を示す並べ替えられていないログ エントリを確認するには、次のクエリを実行します。

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

このクエリを使用してエラーを検出するか、クエリ用語を変更して特定のエラー コードまたは例外を検索します。

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>過去 1 時間にアプリケーションから報告されたエラーと例外の数を表示する

過去 1 時間にアプリケーションによってログに記録されたエラーと例外の数を表示する円グラフを作成するには、次のクエリを実行します。

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>アプリケーション ログのクエリについての詳細情報

Azure Monitor では、Log Analytics を使用したアプリケーション ログのクエリが広範にサポートされています。 このサービスについて詳しくは、「[Azure Monitor でログ クエリの使用を開始する](../azure-monitor/logs/get-started-queries.md)」を参照してください。 アプリケーション ログを分析するクエリの作成の詳細については、「[Azure Monitor のログ クエリの概要](../azure-monitor/logs/log-query-overview.md)」を参照してください。

## <a name="frequently-asked-questions-faq"></a>よく寄せられる質問 (FAQ)

### <a name="how-to-convert-multi-line-java-stack-traces-into-a-single-line"></a>複数行の Java スタック トレースを 1 行に変換する方法は?

複数行のスタック トレースを 1 行に変換する回避策があります。 Java ログ出力を変更してスタック トレース メッセージを再フォーマットし、改行文字をトークンで置換できます。 Java Logback ライブラリを使用する場合、次のように `%replace(%ex){'[\r\n]+', '\\n'}%nopex` を追加することでスタック トレース メッセージを再フォーマットできます。

```xml
<configuration>
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>
                level: %level, message: "%logger{36}: %msg", exceptions: "%replace(%ex){'[\r\n]+', '\\n'}%nopex"%n
            </pattern>
        </encoder>
    </appender>
    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
    </root>
</configuration>
```
その後、下のように、Log Analytics で再び、トークンを改行文字で置換できます。

```sql
AppPlatformLogsforSpring
| extend Log = array_strcat(split(Log, '\\n'), '\n')
```
場合によっては、他の Java ログ ライブラリに同じ方法を利用することもできます。

## <a name="next-steps"></a>次の手順

* [クイック スタート: 初めての Azure Spring Cloud アプリケーションをデプロイする](spring-cloud-quickstart.md)