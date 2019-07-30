---
title: Ambari Web UI を使用して Azure HDInsight を監視および管理する
description: Ambari を使用して Linux ベースの HDInsight クラスターを監視および管理する方法を説明します。 このドキュメントでは、HDInsight クラスターに含まれている Ambari Web UI を使用する方法について説明します。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: hrasheed
ms.openlocfilehash: d0641a1c058db59acd5e9a64b10bb57b334f82bd
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442067"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>Ambari Web UI を使用した HDInsight クラスターの管理

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari には使いやすい Web UI と REST API が用意されているため、Apache Hadoop クラスターを簡単に管理および監視できます。 Ambari は HDInsight クラスターに含まれ、クラスターの監視と構成の変更を行うために使用します。

このドキュメントでは、HDInsight クラスターに含まれている Ambari Web UI を使用する方法について説明します。

## <a id="whatis"></a>Apache Ambari とは

[Apache Ambari](https://ambari.apache.org) は使いやすい Web UI を提供することにより、Hadoop の管理を簡略化します。 Ambari を使って、Hadoop クラスターを管理および監視できます。 開発者は、 [Ambari REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)を使用して、これらの機能をアプリケーションに統合することができます。

## <a name="connectivity"></a>接続

Ambari Web UI はお使いの HDInsight クラスター (`https://CLUSTERNAME.azurehdinsight.net`) にあります。`CLUSTERNAME` はお使いのクラスターの名前です。

> [!IMPORTANT]  
> HDInsight の Ambari に接続するには、HTTPS が必要です。 認証情報の入力を求められたら、クラスターの作成時に提供された管理者アカウント名とパスワードを入力します。

## <a name="ssh-tunnel-proxy"></a>SSH トンネル (プロキシ)

クラスター用の Ambari にはインターネットから直接アクセスできますが、Ambari Web UI の一部のリンク (JobTracker など) はインターネット上で公開されていません。 これらのサービスにアクセスするには、SSH トンネルを作成する必要があります。 詳細については、[HDInsight での SSH トンネリングの使用](hdinsight-linux-ambari-ssh-tunnel.md)に関するページを参照してください。

## <a name="ambari-web-ui"></a>Ambari Web UI

> [!WARNING]  
> HDInsight では、Ambari Web UI の機能の一部がサポートされません。 詳しくは、このドキュメントの「[サポートされていない操作](#unsupported-operations)」セクションをご覧ください。

Ambari Web UI に接続すると、そのページに対する認証が求められます。 クラスターの作成時に利用したクラスター管理者ユーザー (既定では Admin) とパスワードを使用します。

ページが開くと、上部にバーがあります。 このバーには、次の情報とコントロールが含まれています。

![ambari ナビゲーション](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

|Item |説明 |
|---|---|
|Ambari ロゴ|ダッシュボードを表示します。これを使用してクラスターを監視できます。|
|クラスター名 # ops|進行中の Ambari 操作の数が表示されます。 クラスター名または **[# ops]** を選択すると、バックグラウンドでの操作の一覧が表示されます。|
|# alerts|クラスターの警告またはクリティカルなアラートの数 (ある場合) が表示されます。|
|ダッシュボード|ダッシュボードが表示されます。|
|サービス|クラスターのサービスの情報と構成設定。|
|ホスト|クラスター内のノードの情報と構成設定。|
|アラート|情報、警告、クリティカル アラートのログ。|
|[Admin]|クラスターにインストールされたソフトウェア スタック/サービス、サービス アカウント情報、Kerberos セキュリティ。|
|[Admin] ボタン|Ambari の管理、ユーザー設定、サインアウト。|

## <a name="monitoring"></a>監視

### <a name="alerts"></a>アラート

次の一覧には、Ambari で使用される一般的なアラート状態が含まれています。

* **OK**
* **Warning**
* **CRITICAL**
* **UNKNOWN**

**[OK]** 以外のアラートでは、アラート数を表示する **[# alerts]** エントリがページ上部に表示されます。 このエントリを選択すると、アラートとそのステータスが表示されます。

アラートはいくつかの既定のグループにまとめられます。このグループは、 **[Alerts]** ページで表示できます。

![alerts ページ](./media/hdinsight-hadoop-manage-ambari/alerts.png)

グループを管理するには、 **[Actions]** メニューを使用して、 **[Manage Alert Groups]** を選択します。

![manage alert groups ダイアログ](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

**[Actions]** メニューで、 __[Manage Alert Notifications ]__ を選択して、アラート方法を管理したり、アラート通知を作成したりすることもできます。 現在の通知が表示されます。 ここから通知を作成することもできます。 特定のアラート/重要度の組み合わせが発生したとき、通知は **EMAIL** または **SNMP** で送信されます。 たとえば、 **[YARN Default]** グループのいずれかのアラートが **[Critical]** に設定されたときに電子メール メッセージを送信できます。

![Create alert ダイアログ](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

最後に、 __[Actions]__ メニューの __[Manage Alert Settings]__ を選択すると、通知の送信前にアラートが発生する回数を設定できます。 この設定は、一時的なエラーの通知を防ぐために使用できます。

### <a name="cluster"></a>クラスター

ダッシュボードの **[Metrics]** タブには、クラスターのステータスを一目で簡単に確認できる一連のウィジェットが用意されています。 **[CPU Usage]** などのいくつかのウィジェットをクリックすると、追加の情報が表示されます。

![metrics のダッシュボード](./media/hdinsight-hadoop-manage-ambari/metrics.png)

**[Heatmaps]** タブには、緑から赤までの色分けによるメトリックが表示されます。

![heatmaps のダッシュボード](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

クラスター内のノードの詳細については、 **[Hosts]** を選択します。 次に、関心のある特定のノードを選択します。

![ホストの詳細](./media/hdinsight-hadoop-manage-ambari/host-details.png)

### <a name="services"></a>サービス

ダッシュボードの **[Services]** サイド バーでは、クラスターで実行中のサービスのステータスを視覚的に簡単に確認できます。 ステータスまたは実行する必要があるアクションを示すために、さまざまなアイコンが使用されます。 たとえば、サービスをリサイクルする必要がある場合は、黄色のリサイクル記号が表示されます。

![サービスのサイド バー](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

> [!NOTE]  
> 表示されるサービスは、HDInsight クラスターの種類とバージョンによって異なります。 ここに表示されるサービスは、お使いのクラスターに表示されるサービスとは異なる場合があります。

サービスを選択すると、サービスの詳細が表示されます。

![サービスの概要情報](./media/hdinsight-hadoop-manage-ambari/service-details.png)

#### <a name="quick-links"></a>クイック リンク:

一部のサービスでは、ページの上部に **[Quick Links]** が表示されます。 これを使用すると、サービスに固有の次のような Web UI にアクセスできます。

* **Job History** - MapReduce ジョブ履歴
* **Resource Manager** - YARN リソース マネージャー UI
* **NameNode** - Hadoop 分散ファイル システム (HDFS) NameNode UI
* **Oozie Web UI** - Oozie UI

これらのいずれかのリンクを選択すると、ブラウザーに新しいタブが開き、選択したページが表示されます。

> [!NOTE]  
> サービスの **[クイック リンク]** エントリを選択すると、「サーバーが見つかりません」というエラーが返される場合があります。 このエラーが発生した場合、このサービスの **[クイック リンク]** エントリを使用するときは SSH トンネルを使用する必要があります、 詳細については、[HDInsight での SSH トンネリングの使用](hdinsight-linux-ambari-ssh-tunnel.md)に関するページを参照してください。

## <a name="management"></a>管理

### <a name="ambari-users-groups-and-permissions"></a>Ambari ユーザー、グループ、およびアクセス許可

[ドメイン参加済み](./domain-joined/hdinsight-security-overview.md) HDInsight クラスターを使用する場合は、ユーザー、グループ、アクセス許可の管理がサポートされています。 ドメイン参加済みクラスターでの Ambari 管理 UI の使用の詳細については、[ドメイン参加済み HDInsight クラスターの管理](./domain-joined/hdinsight-security-overview.md)に関する記事を参照してください。

> [!WARNING]  
> Linux ベースの HDInsight クラスターでは、Ambari ウォッチドッグ (hdinsightwatchdog) のパスワードは変更しないでください。 パスワードを変更すると、スクリプト アクションを使用したり、クラスターでスケール操作を実行する能力が損なわれます。

### <a name="hosts"></a>ホスト

**[Hosts]** ページには、クラスター内のすべてのホストが一覧表示されます。 ホストを管理するには、次の手順に従います。

![hosts ページ](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [!NOTE]  
> ホストの追加、使用停止、および再任命は HDInsight クラスターでは使用しないことをお勧めします。

1. 管理するホストを選択します。

2. **[Actions]** メニューを使用して、実行する操作を選択します。

    |Item |説明 |
    |---|---|
    |Start all components|ホスト上のすべてのコンポーネントを起動します。|
    |Stop all components|ホスト上のすべてのコンポーネントを停止します。|
    |Restart all components|ホスト上のすべてのコンポーネントを停止してから起動します。|
    |Turn on maintenance mode|ホストのアラートを抑制します。 アラートを生成するアクションを実行している場合は、このモードを有効にする必要があります。 たとえば、サービスの停止と開始です。|
    |Turn off maintenance mode|ホストを通常の警告に戻します。|
    |Stop|ホスト上の DataNode または NodeManagers を停止します。|
    |start|ホスト上の DataNode または NodeManagers を起動します。|
    |Restart|ホスト上の DataNode または NodeManagers を停止して起動します。|
    |Decommission|クラスターからホストを削除します。 **HDInsight クラスターではこの操作は使用しないでください。**|
    |Recommission|以前に使用停止したホストをクラスターに追加します。 **HDInsight クラスターではこの操作は使用しないでください。**|

### <a id="service"></a>サービス

**[Dashboard]** または **[Services]** ページでサービスの一覧の下部にある **[Actions]** ボタンを使用して、すべてのサービスを停止し、開始します。

![[Service Actions]](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

> [!WARNING]  
> このメニューには **[サービスの追加]** が表示されますが、これを使用してサービスを HDInsight クラスターに追加しないでください。 新しいサービスは、クラスターのプロビジョニング中にスクリプト アクションを使用して追加する必要があります。 スクリプト アクションの使用の詳細については、「 [Script Action を使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。

**[Actions]** ボタンではすべてのサービスを再起動できますが、特定のサービスを開始、停止、または再起動する必要がある場合があります。 個々のサービスで操作を実行するには、次の手順に従います。

1. **[Dashboard]** または **[Services]** ページでサービスを選択します。

2. **[Summary]** タブの上部で **[Service Actions]** ボタンを使用して実行する操作を選択します。 これにより、すべてのノードでサービスが再起動されます。

    ![service action](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > クラスターの実行中にサービスを再起動すると、アラートが生成される場合があります。 アラートを回避するには、 **[Service Actions]** ボタンを使用して、再起動を実行する前に、サービスの **[Maintenance mode]** を有効にします。

3. 操作を選択したら、ページ上部の **[# op]** エントリが増分され、バックグラウンド操作が実行されていることが示されます。 バックグラウンド操作を表示するように設定されている場合は、バックグラウンド操作の一覧が表示されます。

   > [!NOTE]  
   > サービスの **[Maintenance mode]** を有効にした場合は、操作が完了したら、 **[Service Actions]** ボタンを使用してこれを忘れずに無効にしてください。

サービスを構成するには、次の手順を実行します。

1. **[Dashboard]** または **[Services]** ページでサービスを選択します。

2. **[Configs]** タブをクリックします。現在の構成が表示されます。 以前の構成の一覧も表示されます。

    ![構成](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. 表示されたフィールドを使用して構成を変更し、 **[Save]** を選択します。 または、以前の構成を選択し、 **[Make current]** を選択して以前の設定にロールバックします。

## <a name="ambari-views"></a>Ambari ビュー

Ambari ビューを使うと、開発者は [Apache Ambari ビュー フレームワーク](https://cwiki.apache.org/confluence/display/AMBARI/Views)を使用して Ambari Web UI に UI 要素をプラグインできます。 HDInsight には、Hadoop クラスター タイプの異なる次のビューが用意されています。

* Hive ビュー:Hive ビューを使用すると、Web ブラウザーから直接 Hive クエリを実行できます。 クエリの保存、結果の表示、結果のクラスター ストレージへの保存、または結果のローカル システムへのダウンロードを行えます。 Hive ビューの使用法の詳細については、[HDInsight での Apache Hive ビューの使用](hadoop/apache-hadoop-use-hive-ambari-view.md)に関するページを参照してください。

* Tez ビュー:Tez ビューによって、ジョブの理解が深まり、最適化を改善できます。 Tez ジョブがどのように実行されて、どのリソースが使用されているかに関する情報を見ることができます。

## <a name="unsupported-operations"></a>サポートされていない操作

Ambari の次の操作は、HDInsight ではサポートされていません。

* __メトリック コレクター サービスの移動__。 メトリック コレクター サービスで情報を表示するとき、[Service Actions]\(サービス アクション\) メニューで使うことができるアクションの 1 つに __[Move Metrics collector]\(メトリック コレクターの移動\)__ があります。 HDInsight では、このアクションはサポートされていません。

## <a name="next-steps"></a>次の手順

HDInsight で [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) を使う方法を学習します。