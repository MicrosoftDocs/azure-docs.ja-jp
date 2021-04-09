---
title: Azure HDInsight で Apache Ambari を使用してクラスターの可用性を監視する方法
description: Apache Ambari を使用してクラスターの正常性と可用性を監視する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/01/2020
ms.openlocfilehash: dcbe44defeb89ad2f67833b263e5f4983070a46c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863532"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-in-azure-hdinsight"></a>Azure HDInsight で Apache Ambari を使用してクラスターの可用性を監視する方法

HDInsight クラスターには、状態に関する概要と定義済みのアラートを提供する Apache Ambari が含まれています。

この記事では、Ambari を使用してクラスターを監視する方法を示し、Ambari アラートの構成、ノードの可用性率の監視、および 5 時間以内に 1 つ以上のノードからハートビートが受信されなかった場合に発生する Azure Monitor アラートの作成に関して、いくつかの例を紹介します。

## <a name="dashboard"></a>ダッシュボード

以下に示した Azure portal の [HDInsight Overview]\(HDInsight の概要\) の **[Cluster dashboards]\(クラスター ダッシュボード\)** セクションにある **[Ambari home]\(Ambari ホーム\)** リンクを選択して、Ambari ダッシュボードにアクセスできます。 または、ブラウザーで `https://CLUSTERNAME.azurehdinsight.net` に移動してアクセスすることもできます。ここで、CLUSTERNAME はクラスターの名前です。

:::image type="content" source="media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png" alt-text="HDInsight リソース ポータルのビュー":::

次に、クラスター ログインのユーザー名とパスワードの入力を求められます。 クラスターを作成した時に選んだ資格情報を入力します。

Ambari ダッシュボードが開かれ、その中にあるウィジェットには、HDInsight クラスターの正常性についての簡単な概要がわかる、いくつかのメトリックが表示されます。 これらのウィジェットには、ライブの DataNodes (ワーカー ノード) および JournalNodes (zookeeper ノード) の数、NameNodes (ヘッド ノード) の稼働時間などのメトリックに加えて、Spark および Hadoop クラスターに対する YARN の ResourceManager の稼働時間など、特定のクラスターの種類に固有のメトリックも表示されます。

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-dashboard.png" alt-text="Apache Ambari の使用状況に関するダッシュボード表示":::

## <a name="hosts--view-individual-node-status"></a>ホスト – 個々のノードの状態を表示する

個々のノードの状態情報を表示することもできます。 **[ホスト]** タブを選択して、クラスター内のすべてのノードの一覧を表示し、各ノードに関する基本情報を確認します。 各ノード名の左にある緑のチェックは、すべてのコンポーネントがノード上で稼働していることを示します。 ノード上でコンポーネントがダウンしている場合、緑のチェックの代わりに赤いアラートの三角形が表示されます。

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-hosts1.png" alt-text="HDInsight Apache Ambari の [ホスト] のビュー":::

次に、ノードの **名前** を選択して、その特定のノードに関するより詳細なホスト メトリックを表示します。 このビューには、個々のコンポーネントごとの状態/可用性が表示されます。

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-hosts-node.png" alt-text="Apache Ambari の [ホスト] にある単一ノードのビュー":::

## <a name="ambari-alerts"></a>Ambari のアラート

Ambari では、特定のイベントの通知を提供できる構成可能なアラートもいくつか提供しています。 アラートがトリガーされると、アラート数を記した赤いバッジとして Ambari の左上隅に表示されます。 このバッジを選択すると、現在のアラートの一覧が表示されます。

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-alerts.png" alt-text="Apache Ambari の現在のアラート数":::

アラートの定義と状態の一覧を表示するには、次に示すように **[アラート]** タブをクリックします。

:::image type="content" source="media/hdinsight-cluster-availability/ambari-alerts-definitions.png" alt-text="Ambari のアラート定義のビュー":::

Ambari では、次に示すように、可用性に関連する多数の定義済みのアラートを提供しています。

| アラート名                        | 説明   |
|---|---|
| DataNode ヘルスの概要           | 異常な DataNode がある場合に、このサービス レベルのアラートがトリガーされます|
| NameNode の高可用性のヘルス | アクティブな NameNode とスタンバイの NameNode のどちらも実行されていない場合に、このサービス レベルのアラートがトリガーされます。|
| 利用可能な JournalNode の割合    | クラスター内でダウンしている JournalNode の数が、設定された重大しきい値を超えている場合は、このアラートがトリガーされます。 これにより、JournalNode プロセス チェックの結果が集計されます。 |
| 利用可能な DataNode の割合       | クラスター内でダウンしている DataNode の数が、設定された重大しきい値を超えている場合は、このアラートがトリガーされます。 これにより、DataNode プロセス チェックの結果が集計されます。|


アラートの詳細を表示したり、条件を変更したりするには、アラートの **名前** を選択します。 例として **DataNode ヘルスの概要** を取り上げます。 アラートの説明と、'警告' または '重大' のアラートをトリガーする固有の条件、および条件のチェック間隔を確認できます。 構成を編集するには、[構成] ボックスの右上隅にある **[編集]** ボタンを選択します。

:::image type="content" source="media/hdinsight-cluster-availability/ambari-alert-configuration.png" alt-text="Apache Ambari アラートの構成":::

ここでは、説明に加えて、さらに重要な警告または重大アラートのチェック間隔としきい値を編集できます。

:::image type="content" source="media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png" alt-text="Ambari アラートの構成の編集ビュー":::

この例では、異常な DataNodes が 2 つの場合に重大アラートをトリガーし、異常な DataNode が 1 つのみの場合に警告をトリガーできます。 編集が終了したら、 **[保存]** を選択します。

## <a name="email-notifications"></a>メール通知

必要に応じて、Ambari アラートに対する電子メール通知を構成することも可能です。 これを行うには、 **[アラート]** タブ上で、左上にある **[Actions]\(アクション\)** ボタン、 **[Manage Notifications]\(通知の管理\)** の順にクリックします。

:::image type="content" source="media/hdinsight-cluster-availability/ambari-manage-notifications.png" alt-text="Ambari 上で通知のアクションを管理する":::

アラート通知を管理するためのダイアログが開きます。 ダイアログの下部にある **+** を選択し、必須フィールドを入力して、電子メールの送信元となる電子メール サーバーの詳細を Ambari に提供します。

> [!TIP]
> Ambari の電子メール通知の設定は、多数の HDInsight クラスターを管理する場合に 1 つの場所でアラートを受け取るのに適した方法でもあります。

## <a name="next-steps"></a>次のステップ

- [HDInsight における Apache Hadoop クラスターの可用性と信頼性](./hdinsight-business-continuity.md)
- [クラスターの可用性 - Azure Monitor ログ](./cluster-availability-monitor-logs.md)
- [Azure Monitor ログを使用する](hdinsight-hadoop-oms-log-analytics-tutorial.md)
- [Apache Ambari のメール通知](apache-ambari-email.md)
