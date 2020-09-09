---
title: Azure HDInsight で実行される Apache Spark ジョブのデバッグ
description: Azure HDInsight の Spark クラスターで実行されるジョブの追跡とデバッグには、YARN UI、Spark UI、Spark History Server を使用します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 817b8976f5d014d990945816c2df1c7e8ed729fb
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86084887"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Azure HDInsight で実行される Apache Spark ジョブのデバッグ

この記事では、HDInsight クラスターで実行されている Apache Spark ジョブを追跡およびデバッグする方法について説明します。 Apache Hadoop YARN UI、Spark UI、および Spark History Server を使用してデバッグします。 まず、「**Machine Learning:MLLib を使用した食品検査データの予測分析**」で使用した Spark クラスターのノートブックを使用して Spark ジョブを実行します。 以下の手順を使用して、他の方法 (**spark-submit** など) を使って送信したアプリケーションも追跡します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

* HDInsight での Apache Spark クラスター。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](apache-spark-jupyter-spark-sql.md)」を参照してください。

* 「 **[Machine Learning:MLlib を使用した食品検査データの予測分析](apache-spark-machine-learning-mllib-ipython.md)** 」のノートブックが既に実行されているはずです。 このノートブックの実行方法については、リンク先のページを参照してください。  

## <a name="track-an-application-in-the-yarn-ui"></a>YARN UI でのアプリケーションの追跡

1. YARN UI を起動します。 **[クラスター ダッシュボード]** で **[Yarn]** を選択します。

    ![Azure portal での YARN UI の起動](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > Ambari UI から YARN UI を起動してもかまいません。 Ambari UI を起動するには、 **[クラスター ダッシュボード]** で **[Ambari ホーム]** を選択します。 Ambari UI で、 **[YARN]**  >  **[クイック リンク]** > アクティブな Resource Manager > **[Resource Manager UI]** に移動します。

2. Jupyter Notebook を使用して Spark ジョブを開始したため、アプリケーションの名前は **remotesparkmagics** (ノートブックから開始されたすべてのアプリケーション用の名前) になっています。 ジョブに関する詳しい情報を確認するには、アプリケーション名に対応するアプリケーション ID を選択します。 この操作により、アプリケーション ビューが起動されます。

    ![Spark History Server での Spark アプリケーション ID の検索](./media/apache-spark-job-debugging/find-application-id1.png)

    Jupyter Notebook から起動されたアプリケーションの場合、ノートブックを終了するまでステータスは常に **[実行中]** になります。

3. アプリケーション ビューからドリルダウンして、アプリケーションやログ (stdout/stderr) に関連付けられているコンテナーを探すことができます。 次のように、 **[追跡 URL]** に対応するリンクをクリックして Spark UI を起動することもできます。

    ![Spark History Server でのコンテナー ログのダウンロード](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Spark UI でのアプリケーションの追跡

以前アプリケーションを実行したときに生成された Spark ジョブは、Spark UI からドリルダウンすることができます。

1. Spark UI を起動するには、アプリケーション ビューから **[追跡 URL]** に対応するリンクを選択します (上のスクリーン ショットを参照)。 Jupyter Notebook で実行中のアプリケーションによって開始されたすべての Spark ジョブを表示できます。

    ![Spark History Server の [Jobs]\(ジョブ\) タブ](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)

2. **[Executors]** タブを選択すると、実行プログラムごとの処理やストレージの情報が表示されます。 **[Thread Dump]\(スレッド ダンプ\)** リンクを選択して、呼び出し履歴を取得することもできます。

    ![Spark History Server の [Executors] タブ](./media/apache-spark-job-debugging/view-spark-executors.png)

3. アプリケーションに関連するステージを確認するには、 **[Stages]\(ステージ\)** タブを選択します。

    ![Spark History Server の [Stages]\(ステージ\) タブ](./media/apache-spark-job-debugging/view-apache-spark-stages.png "View Spark stages")

    1 つのステージに複数のタスクが存在する場合もあり、対応する実行の統計を表示することができます (下図参照)。

    ![Spark History Server の [Stages]\(ステージ\) タブの詳細](./media/apache-spark-job-debugging/view-spark-stages-details.png "Spark ステージの詳細を表示する")

4. ステージの詳細ページから DAG Visualization を起動できます。 以下に示したように、ページの上部にある **[DAG Visualization]** リンクを展開してください。

    ![View Spark stages DAG visualization](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

    DAG (Direct Aclyic Graph) は、アプリケーション内の各種ステージを表します。 グラフ内の青色のボックスはそれぞれ、アプリケーションから呼び出された Spark 操作を表します。

5. ステージの詳細ページから、アプリケーションのタイムライン ビューを起動することもできます。 以下に示したように、ページの上部にある **[Event Timeline (イベントのタイムライン)]** リンクを展開してください。

    ![View Spark stages event timeline](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)

    この画像には、Spark イベントがタイムラインの形式で表示されています。 タイムライン ビューには 3 種類の表示があります (ジョブ間、ジョブ内、ステージ内)。 上の画像は、特定のステージのタイムライン ビューをキャプチャしたものです。

   > [!TIP]  
   > **[Enable zooming (ズームを有効にする)]** チェック ボックスをオンにすると、タイムライン ビューを左右にスクロールすることができます。

6. Spark UI には、Spark インスタンスに関する有益な情報を得ることのできるタブが他にもあります。

   * [ストレージ] タブ - アプリケーション上で RDD が作成されると、[ストレージ] タブ内で情報を確認できます。
   * [環境] タブ: このタブには、Spark インスタンスに関する有益な情報が表示されます。その例を次に示します。
     * Scala バージョン
     * クラスターに関連付けられているイベント ログ ディレクトリ
     * アプリケーションの Executor のコア数

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>完了したジョブに関する情報を Spark History Server で探す

完了したジョブに関する情報は、Spark History Server に保存されています。

1. Spark History Server を起動するには、 **[概要]** ページの **[クラスター ダッシュボード]** で **[Spark History Server]** を選択します。

    ![Azure portal で Spark History Server を起動する](./media/apache-spark-job-debugging/launch-spark-history-server.png "Spark History Server1 を起動する")

   > [!TIP]  
   > Ambari UI から Spark History Server の UI を起動してもかまいません。 Ambari UI を起動するには、[概要] ブレードの **[クラスター ダッシュボード]** で **[Ambari ホーム]** を選択します。 Ambari UI で、 **[Spark2]**  >  **[クイック リンク]**  >  **[Spark2 History Server UI]\(Spark2 History Server の UI\)** に移動します。

2. 完了済みのすべてのアプリケーションが一覧表示されます。 アプリケーションをドリルダウンして、より詳しい情報を入手するには、アプリケーション ID を選択します。

    ![Spark History Server の完了済みアプリケーション](./media/apache-spark-job-debugging/view-completed-applications.png "Spark History Server2 を起動する")

## <a name="see-also"></a>関連項目

* [Azure HDInsight での Apache Spark クラスターのリソースの管理](apache-spark-resource-manager.md)
* [拡張された Spark History Server を使用して Apache Spark ジョブをデバッグする](apache-azure-spark-history-server.md)
* [Azure Toolkit for IntelliJ を使用して SSH 経由で Apache Spark アプリケーションをデバッグする](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
