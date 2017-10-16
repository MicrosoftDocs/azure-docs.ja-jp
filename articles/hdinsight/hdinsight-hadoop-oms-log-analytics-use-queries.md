---
title: "Azure Log Analytics でクエリを実行して Azure HDInsight クラスターを監視する | Microsoft Docs"
description: "Azure Log Analytics でクエリを実行し、HDInsight クラスターで実行されているジョブを監視する方法を説明します。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: nitinme
ms.openlocfilehash: 8fe91bed69a1c06367346041d8caba4aaee4c82a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters-preview"></a>Azure Log Analytics でクエリを実行して Azure HDInsight クラスターを監視する (プレビュー)

この記事では、Azure HDInsight クラスターで Azure Log Analytics を使用する方法をいくつかのシナリオを使って説明します。 よく使用されるシナリオは 3 つあります。

* OMS で HDInsight クラスターのメトリックを分析する
* HDInsight クラスターに関する特定のログ メッセージを検索する
* クラスターで発生するイベントに基づいてアラートを作成する

## <a name="prerequisites"></a>前提条件

* Azure Log Analytics を使用するように HDInsight クラスターを構成しておく必要があります。 手順については、「[Use Azure Log Analytics with HDInsight clusters](hdinsight-hadoop-oms-log-analytics-tutorial.md)」(HDInsight クラスターでの Azure Log Analytics の使用) を参照してください。

* 「[Log Analytics に HDInsight クラスター管理ソリューションを追加する](hdinsight-hadoop-oms-log-analytics-management-solutions.md)」の手順に従って、HDInsight クラスター固有の管理ソリューションを OMS ワークスペースに追加しておく必要があります。

## <a name="analyze-hdinsight-cluster-metrics-in-oms"></a>OMS で HDInsight クラスターのメトリックを分析する

ここでは、HDInsight クラスターの特定のメトリックを確認する手順を説明します。

1. OMS ダッシュボードを開きます。 Azure Portal で、Azure Log Analytics と関連付けた HDInsight クラスターのブレードを開き、[監視] タブをクリックして **[OMS ダッシュボードを開く]** をクリックします。

    ![OMS ダッシュボードを開く](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "OMS ダッシュボードを開く")

2. OMS ダッシュボードのホーム画面で **[ログ検索]** をクリックします。

    ![ログ検索を開く](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "ログ検索を開く")

3. [ログ検索] ウィンドウの **[Begin search here]\(ここから検索を開始\)** テキスト ボックスに「`*`」と入力し、Azure Log Analytics を使用するように構成した HDInsight クラスターで使用できるすべてのメトリックを検索します。 Enter キーを押します。

    ![すべてのメトリックを検索](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "すべてのメトリックを検索")

4. 次のような結果が表示されます。

    ![すべてのメトリックの検索結果](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "すべてのメトリックの検索結果")

5. 左側のウィンドウの **[種類]** カテゴリの下で、詳しく調べたいメトリックを探します。 このチュートリアルでは、`metrics_resourcemanager_queue_root_default_CL` を選択します。 このメトリックのチェック ボックスをオンにして、**[適用]** をクリックします。

    > [!NOTE]
    > 目的のメトリックが表示されていない場合は、**[[+]More]\([+] その他\)** ボタンをクリックします。 また、**[適用]** ボタンはリストの一番下にあるので、このボタンを表示するには下までスクロールする必要があります。
    > 
    >    
    テキスト ボックスのクエリが、下記のスクリーンショットの赤枠で示された内容に変わっていることを確認してください。

    ![特定のメトリックの検索](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "特定のメトリックの検索")

6. これで、このメトリックのデータを詳しく調べることができます。 たとえば、クラスター名ごとに、10 分間に使用されたリソースの平均値に基づいて出力結果を調整することができます。 クエリのテキスト ボックスに次のクエリを入力します。

        * (Type=metrics_resourcemanager_queue_root_default_CL) | measure avg(UsedAMResourceMB_d) by ClusterName_s interval 10minute

    ![特定のメトリックの検索](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-more-specific-metrics.png "特定のメトリックの検索")

7. 使用されたリソースの平均値に基づいて結果を調整する代わりに、次のクエリを使用すると、10 分間で最大 (および 90 パーセンタイルと 95 パーセンタイル) のリソースが使用された時刻に基づいて結果を調整できます。

        * (Type=metrics_resourcemanager_queue_root_default_CL) | measure max(UsedAMResourceMB_d) , pct95(UsedAMResourceMB_d), pct90(UsedAMResourceMB_d)  by ClusterName_s interval 10minute

    ![特定のメトリックの検索](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-more-specific-metrics-1.png "特定のメトリックの検索")

## <a name="search-for-specific-log-messages-in-hdinsight-clusters"></a>HDInsight クラスターに関する特定のログ メッセージを検索する

ここでは、特定の期間に発生したエラー メッセージを確認する手順を説明します。 ここで説明する手順は、目的のエラー メッセージを探すためのひとつの方法にすぎません。 使用可能な任意のプロパティを使用して、目的のエラーを探すことができます。

1. OMS ダッシュボードを開きます。 Azure Portal で、Azure Log Analytics と関連付けた HDInsight クラスターのブレードを開き、[監視] タブをクリックして **[OMS ダッシュボードを開く]** をクリックします。

    ![OMS ダッシュボードを開く](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "OMS ダッシュボードを開く")

2. OMS ダッシュボードのホーム画面で **[ログ検索]** をクリックします。

    ![ログ検索を開く](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "ログ検索を開く")

3. [ログ検索] ウィンドウの **[Begin search here]\(ここから検索を開始\)** テキスト ボックスに引用符も含めて「`"Error"`」と入力し、Azure Log Analytics を使用するように構成したすべての HDInsight クラスターに関するすべてのエラー メッセージを検索します。 Enter キーを押します。

    ![すべてのエラーを検索](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors.png "すべてのエラーを検索")

4. 次のような結果が表示されます。

    ![すべてのエラーの検索結果](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "すべてのエラーの検索結果")

5. 左側のウィンドウの **[種類]** カテゴリの下で、詳しく調べたいエラーの種類を探します。 このチュートリアルでは、`log_sparkappsexecutors_CL` を選択します。 このメトリックのチェック ボックスをオンにして、**[適用]** をクリックします。

    ![特定のエラーの検索](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error.png "特定のエラーの検索")

        
6. テキスト ボックス内のクエリが下記の赤枠の内容に変わり、結果には選択したエラーだけが表示されています。

    ![特定のエラーの検索結果](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-output.png "特定のエラーの検索結果")

7. 左側のウィンドウにあるオプションを使って、このエラーの一覧を詳しく調べることができます。 たとえば、クエリを調整して、特定の worker ノードからのエラー メッセージだけを表示できます。

    ![特定のエラーの検索結果](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "特定のエラーの検索結果")

8. 左側のウィンドウから適切な時刻値を選んで、エラーが発生したと思われる時刻にさらに絞り込むことができます。

    ![特定のエラーの検索結果](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "特定のエラーの検索結果")

9. 探していたエラーを特定できました。 **[[+]show more]\([+] もっと見る\)** をクリックすると、実際のエラー メッセージを確認できます。

    ![特定のエラーの検索結果](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "特定のエラーの検索結果")

## <a name="create-alerts-to-track-events"></a>アラートを作成してイベントを追跡する

アラートを作成する最初の手順は、アラートをトリガーする基準となるクエリを作成することです。 わかりやすくするため、ここでは HDInsight クラスターで実行が失敗したアプリケーションの一覧を返す次のクエリを使用します。

    * (Type=metrics_resourcemanager_queue_root_default_CL) AppsFailed_d>0 

アラートの作成には任意のクエリを使用できます。

1. OMS ダッシュボードを開きます。 Azure Portal で、Azure Log Analytics と関連付けた HDInsight クラスターのブレードを開き、[監視] タブをクリックして **[OMS ダッシュボードを開く]** をクリックします。

    ![OMS ダッシュボードを開く](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "OMS ダッシュボードを開く")

2. OMS ダッシュボードのホーム画面で **[ログ検索]** をクリックします。

    ![ログ検索を開く](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "ログ検索を開く")

3. [ログ検索] ウィンドウの **[Begin search here]\(ここから検索を開始\)** テキスト ボックスに、アラート作成の基準となるクエリを貼り付けて Enter キーを押し、**[アラート]** ボタンをクリックします。

    ![クエリを入力してアラートを作成する](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "クエリを入力してアラートを作成する")

4. **[アラート ルールの追加]** ウィンドウで、クエリとその他の情報を入力してアラートを作成し、**[保存]** をクリックします。

    ![クエリを入力してアラートを作成する](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "クエリを入力してアラートを作成する")

    このスクリーンショットの設定では、アラート クエリが出力を取得した場合に、単に通知の電子メールを送信します。

5. 既存のアラートを編集または削除することができます。 そのためには、OMS ポータルの任意のページで **[設定]** アイコンをクリックします。

    ![クエリを入力してアラートを作成する](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png "クエリを入力してアラートを作成する")

6. **[設定]** ページで **[アラート]** をクリックし、作成したアラートを確認します。 アラートの有効/無効を切り替えたり、アラートを編集、削除したりすることもできます。 詳細については、「[Log Analytics のアラート ルールの操作](../log-analytics/log-analytics-alerts-creating.md)」を参照してください。

## <a name="see-also"></a>関連項目

* [OMS Log Analytics の使用](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Log Analytics でのアラート ルールの作成](../log-analytics/log-analytics-alerts-creating.md)
