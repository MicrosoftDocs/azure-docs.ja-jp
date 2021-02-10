---
title: Azure Monitor ログのクエリを実行して Azure HDInsight クラスターを監視する
description: Azure Monitor ログでクエリを実行し、HDInsight クラスターで実行されているジョブを監視する方法を説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: f9213f36ec33939c3df3b56d21822aa3b6a17c03
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945606"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Azure Monitor ログでクエリを実行して HDInsight クラスターを監視する

Azure Monitor ログを使用して Azure HDInsight クラスターを監視する基本的なシナリオを説明します。

* [HDInsight クラスターのメトリックを分析する](#analyze-hdinsight-cluster-metrics)
* [イベント アラートを作成する](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>前提条件

Azure Monitor ログを使用するように HDInsight クラスターを構成し、HDInsight クラスター固有の Azure Monitor ログ監視ソリューションをワークスペースに追加しておく必要があります。 手順については、「[Use Azure Monitor logs with HDInsight clusters](hdinsight-hadoop-oms-log-analytics-tutorial.md)」(HDInsight クラスターでの Azure Monitor ログの使用) を参照してください。

## <a name="analyze-hdinsight-cluster-metrics"></a>HDInsight クラスターのメトリックを分析する

HDInsight クラスターの特定のメトリックを検索する方法を説明します。

1. Azure Portal から HDInsight クラスターに関連付けられた Log Analytics ワークスペースを開きます。
1. **[全般]** で **[ログ]** を選択します。
1. 検索ボックスに、Azure Monitor ログを使用するように構成したすべての HDInsight クラスターで使用できるすべてのメトリックを検索する次のクエリを入力して、 **[実行]** を選択します。 結果を確認します。

    ```kusto
    search *
    ```

    ![Apache Ambari の分析によってすべてのメトリックを検索する](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "すべてのメトリックを検索する")

1. 左側のメニューから、 **[フィルター]** タブを選択します。

1. **[種類]** で **[ハートビート]** を選択します。 次に、 **[適用して実行]** を選択します。

    ![ログ分析での特定のメトリックの検索](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "特定のメトリックを検索する")

1. テキスト ボックス内のクエリが次のように変更されていることに注意してください。

    ```kusto
    search *
    | where Type == "Heartbeat"
    ```

1. 左側のメニューにあるオプションを使用して、詳しく調べることができます。 次に例を示します。

    - 特定のノードからのログを表示するには、次のようにします。

        ![特定のエラーの output1 を検索する](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-node.png "特定のエラーの output1 を検索する")

    - 特定の時刻のログを表示するには、次のようにします。

        ![特定のエラーの output2 を検索する](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-time.png "特定のエラーの output2 を検索する")

1. **[適用して実行]** を選択し、結果を確認します。 クエリが次のように更新されたことにも注意してください。

    ```kusto
    search *
    | where Type == "Heartbeat"
    | where (Computer == "zk2-myhado") and (TimeGenerated == "2019-12-02T23:15:02.69Z" or TimeGenerated == "2019-12-02T23:15:08.07Z" or TimeGenerated == "2019-12-02T21:09:34.787Z")
    ```

### <a name="additional-sample-queries"></a>追加のサンプル クエリ

10 分間に使用されたリソースの平均値に基づくサンプル クエリ。クラスター名によって分類されます。

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)
```

使用されたリソースの平均値に基づいて結果を調整する代わりに、次のクエリを使用すると、10 分間で最大 (および 90 パーセンタイルと 95 パーセンタイル) のリソースが使用された時刻に基づいて結果を調整できます。

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)
```

## <a name="create-alerts-for-tracking-events"></a>イベント追跡用のアラートを作成する

アラートを作成する最初の手順は、アラートをトリガーする基準となるクエリを作成することです。 アラートの作成には任意のクエリを使用できます。

1. Azure Portal から HDInsight クラスターに関連付けられた Log Analytics ワークスペースを開きます。
1. **[全般]** で **[ログ]** を選択します。
1. アラートを作成する次のクエリを実行して、 **[実行]** を選択します。

    ```kusto
    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0
    ```

    このクエリは、HDInsight クラスターで実行されている失敗したアプリケーションの一覧を提供します。

1. ページの先頭にある **[新しいアラート ルール]** を選択します。

    ![クエリを入力して alert1 を作成する](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "クエリを入力して alert1 を作成する")

1. **[ルールの作成]** ウィンドウで、クエリとその他の情報を入力してアラートを作成し、 **[アラート ルールの作成]** を選択します。

    ![クエリを入力して alert2 を作成する](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "クエリを入力して alert2 を作成する")

### <a name="edit-or-delete-an-existing-alert"></a>既存のアラートを編集または削除する

1. Azure Portal から Log Analytics ワークスペースを開きます。

1. 左側のメニューの **[監視]** で **[アラート]** を選択します。

1. 上部にある **[アラート ルールの管理]** を選択します。

1. 編集または削除するアラートを選択します。

1. **[保存]** 、 **[破棄]** 、 **[無効化]** 、および **[削除]** の各オプションがあります。

    ![HDInsight Azure Monitor ログ アラートの削除または編集](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

詳細については、「[Azure Monitor を使用してメトリック アラートを作成、表示、管理する](../azure-monitor/platform/alerts-metric.md)」を参照してください。

## <a name="see-also"></a>参照

* [Azure Monitor でログ クエリの使用を開始する](../azure-monitor/log-query/get-started-queries.md)
* [Azure Monitor のビュー デザイナーを使用してカスタム ビューを作成する](../azure-monitor/platform/view-designer.md)
