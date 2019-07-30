---
title: Ambari と Azure Monitor ログを使用してクラスターの可用性を監視する方法
description: Ambari と Azure Monitor ログを使用してクラスターのヘルスと可用性を監視する方法について説明します。
keywords: 監視、ambari、監視する、ログ分析、アラート、可用性、ヘルス
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: tyfox
ms.openlocfilehash: 195999ba685828042fc958e8aed7e67bad694657
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786561"
---
# <a name="how-to-monitor-cluster-availability-with-ambari-and-azure-monitor-logs"></a>Ambari と Azure Monitor ログを使用してクラスターの可用性を監視する方法

HDInsight クラスターには、一目で理解しやすいヘルス情報と事前定義されたアラートを提供する Apache Ambari と、クエリ可能なメトリックとログに加えて構成可能なアラートを提供する Azure Monitor ログの統合の両方が含まれています。

このドキュメントでは、これらのツールを使用してクラスターを監視する方法を示し、Ambari アラートの構成、ノードの可用性率の監視、および 5 時間以内に 1 つ以上のノードからハートビートが受信済みにならなかった場合に発生させる Azure Monitor アラートの作成に関して、いくつかの例を紹介します。

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>ダッシュボード

以下に示した Azure portal の [HDInsight Overview]\(HDInsight の概要\) ブレードの **[Cluster dashboards]\(クラスター ダッシュボード\)** セクションにある **[Ambari home]\(Ambari ホーム\)** リンクをクリックして、Ambari ダッシュボードにアクセスできます。 または、ブラウザーに [https://\<clustername\>.azurehdinsight.net](https://clustername.azurehdinsight.net/) という URL を入力して、アクセスできます。

![HDInsight リソース ポータルのビュー](media/hdinsight-cluster-availability/portal-overview.png)

次に、クラスター ログインのユーザー名とパスワードの入力が求められます。 クラスターを作成した時に選んだ資格情報を入力します。

Ambari ダッシュボードが開かれ、その中にあるウィジェットに、HDInsight クラスターのヘルスについて簡単な概要がわかるいくつかのメトリックが表示されます。 これらのウィジェットには、ライブの DataNodes (ワーカー ノード) および JournalNodes (zookeeper ノード) の数、NameNodes (ヘッド ノード) の稼働時間などのメトリックに加えて、Spark および Hadoop クラスターに対する YARN の ResourceManager の稼働時間など、特定のクラスターの種類に固有のメトリックも表示されます。

![Ambari ダッシュボード](media/hdinsight-cluster-availability/ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>ホスト – 個々のノードの状態を表示する

個々のノードの状態情報を表示することもできます。 **[ホスト]** タブをクリックして、クラスター内のすべてのノードの一覧を表示し、各ノードに関する基本情報を確認します。 各ノード名の左にある緑のチェックは、すべてのコンポーネントがノード上で稼働していることを示します。 ノード上でコンポーネントがダウンしている場合、緑のチェックの代わりに赤いアラートの三角形が表示されます。

![Ambari の [Hosts]\(ホスト\) のビュー](media/hdinsight-cluster-availability/ambari-hosts.png)

次に、ノードの**名前**をクリックして、その特定のノードに関するより詳細なホスト メトリックを表示します。 このビューには、個々のコンポーネントごとの状態/可用性が表示されます。

![Ambari の [Hosts]\(ホスト\) にある単一ノードのビュー](media/hdinsight-cluster-availability/ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Ambari のアラート

Ambari では、特定のイベントの通知を提供できる構成可能なアラートもいくつか提供しています。 アラートは、トリガーされると、Ambari の左上隅にあるアラート数を記した赤いバッジに示されます。 このバッジをクリックすると、現在のアラートの一覧が表示されます。

![Ambari のアラート数](media/hdinsight-cluster-availability/ambari-alerts.png)

アラートの定義と状態の一覧を表示するには、以下に示した **[アラート]** タブをクリックします。

![Ambari のアラート定義のビュー](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari では、次に示すように、可用性に関連する多数の定義済みのアラートを提供しています。

| アラート名                        | 説明                                                                                                                                                                           |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| DataNode ヘルスの概要           | 異常な DataNode がある場合に、このサービス レベルのアラートがトリガーされます                                                                                                                |
| NameNode の高可用性のヘルス | アクティブな NameNode またはスタンバイの NameNode が実行されていない場合に、このサービス レベルのアラートがトリガーされます。                                                                              |
| 利用可能な JournalNode の割合    | クラスター内でダウンしている JournalNode の数が、設定された重大しきい値を超えている場合は、このアラートがトリガーされます。 これにより、JournalNode プロセス チェックの結果が集計されます。 |
| 利用可能な DataNode の割合       | クラスター内でダウンしている DataNode の数が、設定された重大しきい値を超えている場合は、このアラートがトリガーされます。 これにより、DataNode プロセス チェックの結果が集計されます。       |

クラスターの可用性を監視できる Ambari アラートの完全な一覧は、[こちら](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui)で確認できます。

アラートの詳細を表示したり、条件を変更したりするには、アラートの**名前**をクリックします。 例として **DataNode ヘルスの概要**を取り上げます。 アラートの説明と、'警告' または '重大' のアラートをトリガーする固有の条件や条件のチェック間隔を確認できます。 構成を編集するには、[構成] ボックスの右上隅にある **[編集]** ボタンをクリックします。

![Ambari アラートの構成](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

ここでは、説明に加えて、さらに重要な警告または重大アラートのチェック間隔としきい値を編集できます。

![Ambari アラートの構成の編集ビュー](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

この例では、異常な DataNodes が 2 つの場合に重大アラートをトリガーし、異常な DataNode が 1 つのみの場合に警告をトリガーできます。 編集を完了したら、 **[保存]** をクリックします。

### <a name="email-notifications"></a>電子メール通知

必要に応じて、Ambari アラートに対する電子メール通知を構成することも可能です。 これを行うには、 **[アラート]** タブ上で、左上にある **[Actions]\(アクション\)** ボタン、 **[Manage Notifications]\(通知の管理\)** の順にクリックします。

![Ambari 上で通知のアクションを管理する](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

アラート通知を管理するためのダイアログが開きます。 ダイアログの下部にある **+** をクリックして、必須フィールドを入力し、電子メールの送信元となる電子メール サーバーの詳細を Ambari に提供します。

> [!TIP]
> Ambari の電子メール通知の設定は、多数の HDInsight クラスターを管理する場合に 1 つの場所でアラートを受け取るのに適した方法でもあります。

## <a name="azure-monitor-logs-integration"></a>Azure Monitor ログの統合

Azure Monitor ログでは、HDInsight クラスターなどの複数のリソースによって生成されたデータを 1 つの場所に収集して集計し、統合された監視エクスペリエンスを実現できます。

前提条件として、収集されたデータを格納するための Log Analytics ワークスペースが必要になります。 まだ作成していない場合は、次の記事の手順に従います:「[Azure ポータルで Log Analytics ワークスペースを作成する](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)」

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>HDInsight Azure Monitor ログの統合を有効にする

ポータルの HDInsight クラスター リソースのページから、 **[Operations Management Suite]** ブレードをクリックします。 次に、 **[有効]** をクリックして、ドロップダウンから Log Analytics ワークスペースを選択します。

![[HDInsight Operations Management Suite] ブレード](media/hdinsight-cluster-availability/portal-enable-oms.png)

### <a name="query-metrics-and-logs-tables-in-the-logs-blade"></a>[ログ] ブレードにあるメトリックとログのクエリの表

Azure Monitor ログの統合が有効になったら (これには数分かかる場合があります)、 **[Log Analytics ワークスペース]** リソースに移動して、 **[ログ]** ブレードをクリックします。

![Log Analytics ワークスペースの [ログ] ブレード](media/hdinsight-cluster-availability/portal-logs.png)

**[ログ]** ブレードには、次のような多数のサンプル クエリが一覧表示されます。

| クエリ名                      | 説明                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| 今日のコンピューターの可用性    | 1 時間ごとに、ログを送信するコンピューター数をグラフにする                     |
| ハートビードの一覧表示                 | 過去 1 時間のすべてのコンピューターのハートビートを一覧表示する                           |
| 各コンピューターの最新のハートビート | 各コンピューターから送信された最新のハートビートを表示する                             |
| 利用できないコンピューター           | 過去 5 時間以内にハートビートを送信しなかったすべての既知のコンピューターを一覧表示する |
| 可用性率               | 接続されている各コンピューターの可用性率を計算する                |

例として、上記のスクリーン ショットに示すように、**可用性率**のサンプル クエリにある **[実行]** をクリックして、そのクエリを実行します。 これにより、クラスター内にある各ノードの可用性率が割合として表示されます。 複数の HDInsight クラスターを有効にしてメトリックを同じ Log Analytics ワークスペースに送信した場合、表示されているそれらのクラスター内のすべてのノードに対する可用性率が表示されます。

![Log Analytics ワークスペースにある [ログ] ブレードの '可用性率' のサンプル クエリ](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE] 
> 可用性率は 24 時間の期間に測定されるので、正確な可用性率を表示するには、それまでに少なくとも 24 時間、クラスターが実行される必要があります。

右上隅にある **[固定]** をクリックして、共有のダッシュボードにこのテーブルを固定表示することが可能です。 書き込み可能な共有のダッシュボードがない場合は、次の記事で作成方法を確認できます:「[Azure portal でのダッシュボードの作成と共有](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard)」

### <a name="azure-monitor-alerts"></a>Azure Monitor アラート

メトリックの値またはクエリの結果が特定の条件に合った場合にトリガーする Azure Monitor アラートを設定することも可能です。 例として、1 つまたは複数のノードが 5 時間以内にハートビートを送信しなかった (つまり、利用できないと推定される) 場合に電子メールを送信するアラートを作成しましょう。

以下に示すように、 **[ログ]** ブレードから、**利用できないコンピューター**のサンプル クエリにある **[実行]** をクリックして、そのクエリを実行します。

![Log Analytics ワークスペースの [ログ] ブレードの '利用できないコンピューター' のサンプル クエリ](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

すべてのノードが利用可能な場合、このクエリでは現在、必ず 0 の結果を返しています。 **[新しいアラート ルール]** をクリックして、このクエリでのアラートの構成を開始します。

![Log Analytics ワークスペースの [新しいアラート ルール]](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

アラートに対しては、3 つのコンポーネントがあります。ルールの作成対象とする*リソース* (この場合、Log Analytics ワークスペース)、アラートをトリガーする*条件*、アラートがトリガーされたときに発生する動作を決定する*アクション グループ*です。

以下に示すように**条件のタイトル**をクリックして、シグナル ロジックの構成を完了します。

![アラート ルールの条件](media/hdinsight-cluster-availability/portal-condition-title.png)

これにより、 **[シグナル ロジックの構成]** ブレードが開かれます。

**[アラート ロジック]** セクションを次のように設定します。

"*基準:結果の数、条件:より大きい、しきい値:0*"

このクエリでは、利用できないノードだけを結果として返すので、結果の数が 0 より大きい場合、必ずアラートが発生します。

**[評価基準]** セクションで、利用できないノードについてチェックする頻度に基づいて、 **[期間]** および **[頻度]** を設定します。

このアラートの目的のために、必ず **[期間] = [頻度]** になることに留意してください。 期間、頻度、およびその他のアラート パラメーターに関する詳細は、[こちら](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types)で確認できます。

シグナル ロジックの構成が終わったら、 **[完了]** をクリックします。

![アラート ルールにおいてシグナル ロジックを構成する](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

既存のアクション グループがまだない場合は、 **[アクション グループ]** セクション下にある **[新規作成]** をクリックします。

![アラート ルールの新しいアクション グループ](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

これにより、 **[アクション グループの追加]** ブレードが開かれます。 **[アクション グループ名 ]** 、 **[短い名前]** 、 **[サブスクリプション]** 、および **[リソース グループ]** を選択します。 **[アクション]** セクション下で、 **[アクション名]** を選択して、 **[アクションの種類]** として **[Email/SMS/Push/Voice]\(電子メール/SMS/プッシュ/音声\)** を選択します。

> [!NOTE]
> [Email/SMS/Push/Voice]\(電子メール/SMS/プッシュ/音声\) 以外にも、Azure Function、LogicApp、Webhook、ITSM、および Automation Runbook など、アラートがトリガーできる他のアクションが複数あります。 [詳細を確認してください。](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

これにより、 **[Email/SMS/Push/Voice]\(電子メール/SMS/プッシュ/音声\)** ブレードが開かれます。 受信者の **[名前]** を選択し、 **[電子メール]** チェック ボックスを**オン**にして、アラートの送信先になる電子メール アドレスを入力します。 **[Email/SMS/Push/Voice]\(電子メール/SMS/プッシュ/音声\)** ブレードで **[OK]** をクリックしてから、 **[アクション グループの追加]** ブレードでアクション グループの構成を完了します。

![アラート ルールの [アクション グループの追加]](media/hdinsight-cluster-availability/portal-add-action-group.png)

これらのブレードを閉じた後、 **[アクション グループ]** セクション下にアクション グループが一覧表示されていることを確認できます。 最後に、 **[アラート ルール名]** と **[説明]** を入力して、 **[重大度]** を選択し、 **[アラートの詳細]** セクションを完成させます。
**[アラート ルールの作成]** をクリックして完了します。

![アラート ルールの作成を完了する](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> **[重大度]** を指定する機能は、複数のアラートを作成する場合に使用できる優れたツールとなります。 たとえば、単一のヘッド ノードがダウンした場合には [警告 (重大度 1)] を挙げるアラートを 1 つと、両方のヘッド ノードがダウンするという想定外のイベントには [重大 (重大度 0)] を挙げるもう 1 つのアラートを作成することが可能です。

このアラートの条件と一致した場合、アラートが発生し、次のようなアラートの詳細を含む電子メールを受信します。

![Azure Monitor アラートの電子メール](media/hdinsight-cluster-availability/alert-email.png)

また、**Log Analytics ワークスペース**の **[アラート]** ブレードに移動して、発生したすべてのアラートを重大度別にグループ化して表示することもできます。

![Log Analytics ワークスペースのアラート](media/hdinsight-cluster-availability/portal-alerts.png)

重要度によるグループ (つまり、上記に強調表示されている **[重大度 1]** ) をクリックすると、以下のように発生したその重大度の全アラートに対するレコードが表示されます。

![Log Analytics ワークスペースの [重大度 1] のアラート](media/hdinsight-cluster-availability/portal-alerts-sev-1.png)

## <a name="next-steps"></a>次の手順
- [HDInsight における Apache Hadoop クラスターの可用性と信頼性](hdinsight-high-availability-linux.md)
