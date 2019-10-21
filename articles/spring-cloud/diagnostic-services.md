---
title: Azure Spring Cloud でログとメトリックを分析する |Microsoft Docs
description: Azure Spring Cloud で診断データを分析する方法について説明します
services: spring-cloud
author: jpconnock
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: e6b90fb09c536f68bee7fd5d57507fe3920bcf1e
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038718"
---
# <a name="analyze-logs-and-metrics-with-diagnostic-settings"></a>診断設定でログとメトリックを分析する

Azure Spring Cloud の診断機能では、次のいずれかのサービスを使用してログとメトリックを分析できます。

* Azure Log Analytics で分析する。この場合は、データが Azure Log Analytics に即座に書き込まれ、データを最初にストレージに出力する必要はありません。
* 監査や手動での検査に使用するためにストレージ アカウントに保存する。 保持期間を指定できます (日数)。
* サード パーティのサービスやカスタム分析ソリューションで取り込むために Event Hubs にストリーム配信する。

まず、これらのサービスのいずれかを有効にしてデータを受信する必要があります。  Log Analytics の構成については、[このチュートリアル](../azure-monitor/log-query/get-started-portal.md)を確認してください。  

## <a name="configure-diagnostic-settings"></a>診断設定を構成する

1. Azure portal で Azure Spring Cloud インスタンスに移動します。
1. **[診断設定]** メニュー オプションを選択します。
1. **[診断設定を追加する]** ボタンを選択します。
1. 設定の名前を入力し、ログを送信する場所を選択します。 次の 3 つのオプションを自由に組み合わせて選択できます。
    * ストレージ アカウントへのアーカイブ
    * イベント ハブへのストリーミング
    * Log Analytics への送信

1. 監視するログ カテゴリとメトリック カテゴリを選択し、保持期間 (日数) を指定します。 保持期間はストレージ アカウントにのみ適用されます。
1. **[保存]** を選んで変更を適用します。

> [!NOTE]
> ログまたはメトリックが生成されてから、ストレージ アカウント/イベントハブ/Log Analytics に表示されるまでに、最大 15 分かかる場合があります。

## <a name="viewing-logs"></a>ログの表示

### <a name="using-log-analytics"></a>Log Analytics の使用

1. Azure portal で、左側のナビゲーション メニューから [Log Analytics] を選択します。
1. 診断設定を追加するときに選択した Log Analytics ワークスペースを選択します。
1. `Logs` を選択して、[ログ検索] ブレードを開きます。
1. [ログ検索] ボックスに単純なクエリを入力します。  例:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

1. `Run` を選択して、検索結果を表示します。
1. フィルター条件を設定することで、特定のアプリケーションまたはインスタンスのログを検索できます。

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```

Log Analytics で使用されるクエリ言語の詳細については、[この記事](../azure-monitor/log-query/query-language.md)を参照してください。

### <a name="using-logs-and-metrics-in-storage-account"></a>ストレージ アカウントでのログとメトリックの使用

1. Azure portal で、左側のナビゲーション メニューから [ストレージ アカウント] を選択します。
1. 診断設定を追加するときに選択したストレージ アカウントを選択します。
1. `Blobs` エントリを選択して、[BLOB コンテナー] ブレードを開きます。
1. `insights-logs-applicationconsole` という名前のコンテナーを見つけて、アプリケーション ログを確認します。
1. `insights-metrics-pt1m` という名前のコンテナーを見つけて、アプリケーション メトリックを確認します。

[ストレージ アカウントへの診断情報の送信に関する詳細を確認してください。](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-to-storage)

### <a name="using-event-hubs"></a>Event Hubs の使用

1. Azure portal で、左側のナビゲーション メニューから [Event Hubs] を選択します。
1. 診断設定を追加するときに選んだイベント ハブを見つけて選択します。
1. `Event Hubs` を選択して、イベント ハブの一覧ブレードを開きます。
1. `insights-logs-applicationconsole` という名前のイベント ハブを見つけて、アプリケーション ログを確認します。
1. `insights-metrics-pt1m` という名前のイベント ハブを見つけて、アプリケーション メトリックを確認します。

[イベント ハブへの診断情報の送信に関する詳細を確認してください。](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs)

## <a name="analyzing-logs"></a>ログの分析

Azure Log Analytics には Kusto が用意されているので、ログをクエリして分析することができます。  Kusto を使用したログのクエリの簡単な概要については、[Log Analytics チュートリアル](../azure-monitor/log-query/get-started-portal.md)を確認してください。

アプリケーション ログには、アプリケーションの正常性やパフォーマンスなどに関する重要な情報が記載されています。  次に、アプリケーションの現在の状態と過去の状態を理解するのに役立つ単純なクエリを示します。

### <a name="show-application-logs-from-azure-spring-cloud"></a>Azure Spring Cloud からアプリケーション ログを表示する

Azure Spring Cloud からアプリケーション ログの一覧 (時間順に並べ替え、最新のログを最初に表示) を確認するには

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>エラーまたは例外を含むログ エントリを表示する

このクエリを使用すると、エラーまたは例外を示すログ エントリを確認できます。  結果は並べ替えられません。

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

このクエリを使用してエラーを検出するか、クエリ用語を変更して特定のエラー コードまたは例外を検索します。  

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>過去 1 時間にアプリケーションから報告されたエラーと例外の数を表示する

このクエリでは、過去 1 時間にアプリケーションによってログに記録されたエラーと例外の数を表示する円グラフが作成されます。

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>アプリケーション ログのクエリについての詳細情報

Azure Monitor では、Log Analytics を使用したアプリケーション ログのクエリが広範にサポートされています。  このサービスの詳細については、Azure Monitor を使用した[ログ クエリ](../azure-monitor/log-query/get-started-queries.md)のチュートリアルを確認してください。 「[Azure Monitor でのログ クエリの概要](../azure-monitor/log-query/log-query-overview.md)」で、アプリケーション ログを分析するクエリの作成の詳細が説明されています。
