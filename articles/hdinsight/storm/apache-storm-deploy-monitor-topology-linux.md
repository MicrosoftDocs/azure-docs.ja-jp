---
title: Azure HDInsight での Apache Storm トポロジのデプロイと管理
description: Linux ベースの HDInsight で Storm ダッシュボードを使用して Apache Storm トポロジをデプロイ、監視、および管理する方法について説明します。 Hadoop Tools for Visual Studio を使用します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: fb57992c8e26560061faf68443c4993801f9713d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871573"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Azure HDInsight での Apache Storm トポロジのデプロイと管理

このドキュメントでは、HDInsight クラスターの Storm 上で実行されている [Apache Storm](https://storm.apache.org/) トポロジの管理および監視の基本について説明します。

## <a name="prerequisites"></a>前提条件

* HDInsight 上の Apache Storm クラスター。 [Azure portal を使用した Apache Hadoop クラスターの作成](../hdinsight-hadoop-create-linux-clusters-portal.md)に関するページを参照し、 **[クラスターの種類]** で **[Storm]** を選択してください。

* (省略可能) Secure Shell (SSH) と Secure Copy (SCP) に関する知識。 詳細については、[SSH を使用して HDInsight (Apache Hadoop) に接続する方法](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

* (省略可能) Visual Studio、Azure SDK 2.5.1 以降、および Data Lake Tools for Visual Studio。 詳細については、[Apache Hadoop と Visual Studio Data Lake Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md) に関するページを参照してください。

## <a name="submit-a-topology-using-visual-studio"></a>Visual Studio を使用してトポロジを送信する

Data Lake Tools for Visual Studio を使用すると、C# またはハイブリッド トポロジを Storm クラスターに送信できます。 次の例では、サンプル アプリケーションを使用します。 Data Lake Tools を使用したトポロジの作成については、[Visual Studio と C# を使用した Apache Storm トポロジ](apache-storm-develop-csharp-visual-studio-topology.md)に関するページを参照してください。

1. 最新バージョンの Data Lake Tools for Visual Studio をまだインストールしていない場合は、[Data Lake Tools for Visual Studio の使用](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)に関するページを参照してください。

    > [!NOTE]  
    > Azure Data Lake および Stream Analytics ツールは、以前は HDInsight Tools for Visual Studio と呼ばれていました。
    >
    > Azure Data Lake および Visual Studio 用 Stream Analytics ツールは、Visual Studio 2019 用の **Azure 開発** ワークロードに含まれています。

1. Visual Studio を起動します。

1. **[開始]** ウィンドウで、 **[新しいプロジェクトの作成]** を選択します。

1. **[新しいプロジェクトの作成]** ウィンドウで、検索ボックスを選択し、「`Storm`」と入力します。 次に、結果の一覧から **[Storm サンプル]** を選択し、**[次へ]** を選択します。

1. **[新しいプロジェクトを構成する]** ウィンドウで、**[プロジェクト名]** を入力し、新しいプロジェクトを保存する **[場所]** に移動するか、またはそれを作成します。 **[作成]** を選択します。

    :::image type="content" source="./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png" alt-text="[新しいプロジェクトを構成する] ウィンドウ、Visual Studio" border="true":::

1. **[サーバー エクスプローラー]** で、**[Azure]** を右クリックし、**[Microsoft Azure サブスクリプションへの接続]** を選択し、サインイン処理を完了します。

1. **ソリューション エクスプローラー** で、プロジェクトを右クリックして **[HDInsight の Storm に送信]** を選択します。

    > [!NOTE]  
    > メッセージが表示されたら、Azure サブスクリプションのログイン資格情報を入力します。 2 つ以上のサブスクリプションをお持ちの場合は、HDInsight クラスターの Storm があるサブスクリプションにサインインします。

1. **[トポロジの送信]** ダイアログボックスの **[Storm クラスター]** ドロップダウン リストで、お使いの Storm on HDInsight クラスターを選択し、 **[送信]** を選択します。 送信が成功したかどうかは、**[出力]** ウィンドウを表示することによって監視できます。

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>SSH と Storm コマンドを使用してトポロジを送信する

1. [ssh コマンド](../hdinsight-hadoop-linux-use-ssh-unix.md)を使用してクラスターに接続します。 次のコマンドを編集して CLUSTERNAME をクラスターの名前に置き換えてから、そのコマンドを入力します。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. SSH セッションから、次のコマンドを使用して **WordCount** のトポロジの例を開始します。

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    このコマンドにより、クラスターで WordCount トポロジの例が開始されます。 このトポロジは、ランダムに文を生成し、文中の各単語の出現回数をカウントします。

    > [!NOTE]  
    > トポロジをクラスターに送信する場合は、`storm` コマンドを使用する前に、まずそのクラスターを含む .jar ファイルをコピーする必要があります。 ファイルをクラスターにコピーするには、`scp` コマンドを使用できます。 たとえば、「`scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`」と入力します。
    >
    > *WordCount* の例やその他の Storm スターターの例は、`/usr/hdp/current/storm-client/contrib/storm-starter/` のクラスターに既に含まれています。

## <a name="submit-a-topology-programmatically"></a>プログラムでトポロジを送信する

Nimbus サービスを使用してトポロジをプログラムによってデプロイできます。 [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) に用意されている Java アプリケーションの例を参照してください。

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>Visual Studio でトポロジを監視および管理する

Visual Studio を使用してトポロジを送信すると、**[Storm Topology ビュー]** ウィンドウが表示されます。 実行中のトポロジに関する情報を表示するには、一覧からトポロジを選択します。

:::image type="content" source="./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png" alt-text="トポロジを監視する、[Storm Topology ビュー] ウィンドウ、Visual Studio" border="true":::

> [!NOTE]  
> また、**サーバー エクスプローラー** から **Storm トポロジ** を表示することもできます。 その場合、 **[Azure]**  >  **[HDInsight]** の順に展開し、HDInsight クラスターの Storm を右クリックして、 **[Storm トポロジの表示]** を選択します。

情報を表示するスパウトまたはボルト コンポーネントの形状を選択します。 選択された項目に関するコンポーネント情報を含むヒントが表示されます。

### <a name="deactivate-and-reactivate-a-topology"></a>トポロジを非アクティブ化および再アクティブ化する

トポロジを非アクティブ化すると、そのトポロジは、強制終了または再アクティブ化されるまで一時停止されます。 これらの操作を行うには、**[Storm Topology ビュー]** ウィンドウの上部の **[アクション]** 領域にある **[非アクティブ化]** および **[再度有効にする]** ボタンを使用します。

### <a name="rebalance-a-topology"></a>トポロジを再調整する

トポロジを再調整すると、トポロジの並列処理が変更されます。 たとえば、クラスターのサイズを変更してノードを追加した場合は、再調整によりトポロジにそれらの新しいノードを表示できます。

トポロジを再調整するには、**[Storm Topology ビュー]** ウィンドウの **[アクション]** 領域にある **[再調整]** ボタンを使用します。

> [!WARNING]  
> トポロジを再調整すると、そのトポロジは非アクティブ化され、ワーカーがクラスター全体に均等に再分散された後、トポロジが再調整実行前の状態に戻されます。 トポロジがアクティブであった場合は、再度アクティブになります。 トポロジが非アクティブであった場合は、非アクティブのままになります。

### <a name="kill-a-running-topology"></a>実行中のトポロジを強制終了する

Storm トポロジは、それが停止されるか、またはクラスターが削除されるまで実行を続行します。 トポロジを停止するには、**[アクション]** 領域にある **[強制終了]** ボタンを使用します。

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>SSH と Storm コマンドを使用してトポロジを監視および管理する

`storm` ユーティリティを使用すると、コマンド ラインから実行中のトポロジを操作できます。 完全なコマンド一覧を表示するには、 `storm -h` を使用してください。

### <a name="list-topologies"></a>トポロジの一覧を表示する

実行中のすべてのトポロジを一覧表示するには、次のコマンドを使用します。

```shell
storm list
```

このコマンドでは、次のテキストのような情報が返されます。

```shell
Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
-------------------------------------------------------------------
WordCount            ACTIVE     29         2            263
```

### <a name="deactivate-and-reactivate-a-topology"></a>トポロジを非アクティブ化および再アクティブ化する

トポロジを非アクティブ化すると、そのトポロジは、強制終了または再アクティブ化されるまで一時停止されます。 非アクティブ化または再アクティブ化するには、次のコマンドを使用します。

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>実行中のトポロジを強制終了する

Storm トポロジが開始されると、停止されるまで実行が継続されます。 トポロジを停止するには、次のコマンドを使用します。

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>トポロジを再調整する

トポロジを再調整すると、トポロジの並列処理が変更されます。 たとえば、クラスターのサイズを変更してノードを追加した場合は、再調整によりトポロジにそれらの新しいノードを表示できます。

> [!WARNING]  
> トポロジを再調整すると、そのトポロジは非アクティブ化され、ワーカーがクラスター全体に均等に再分散された後、トポロジが再調整実行前の状態に戻されます。 トポロジがアクティブであった場合は、再度アクティブになります。 非アクティブであった場合は、非アクティブのままになります。

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>Storm UI を使用してトポロジを監視および管理する

Storm UI は、実行中のトポロジを操作するための Web インターフェイスを提供するものであり、HDInsight クラスターに含まれています。 Storm UI を表示するには、Web ブラウザーを使用して `https://CLUSTERNAME.azurehdinsight.net/stormui` を開きます。*CLUSTERNAME* は実際のクラスターの名前です。

> [!NOTE]  
> ユーザー名とパスワードを指定するよう求められたら、クラスターを作成するときに使用したクラスター管理者のユーザー名とパスワードを入力します。

### <a name="storm-ui-main-page"></a>Storm UI のメイン ページ

Storm UI のメイン ページには、次の情報が表示されます。

| Section | 説明 |
| --- | --- |
| クラスターの概要| Storm クラスターの基本情報。 |
| [Nimbus summary]\(Nimbus の概要\) | 基本的な Nimbus 情報の一覧。 |
| トポロジの概要 | 実行中のトポロジの一覧。 特定のトポロジに関するより詳細な情報を表示するには、**[名前]** 列にあるそのリンクを選択します。 |
| [Supervisor summary]\(スーパーバイザーの概要\) | Storm のスーパーバイザーに関する情報。 特定のスーパーバイザーに関連付けられている worker リソースを表示するには、**[ホスト]** または **[ID]** 列にあるそのリンクを選択します。 |
| [Nimbus configuration]\(Nimbus 構成\) | クラスターの Nimbus 構成。 |

Storm UI のメイン ページは、次の Web ページのようになります。

:::image type="content" source="./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png" alt-text="メイン ページ、Storm UI、Apache Storm トポロジ、Azure" border="true":::

#### <a name="topology-summary"></a>トポロジの概要

**[トポロジの概要]** セクションのリンクを選択すると、トポロジに関する次の情報が表示されます。

| Section | 説明 |
| --- | --- |
| トポロジの概要 | トポロジの基本情報。 |
| [トポロジのアクション]| トポロジに対して実行できる管理アクション。 使用可能なアクションは、このセクションの後の方で説明します。 |
| トポロジの統計 | トポロジに関する統計。 このセクション内のエントリの期間を設定するには、**[ウィンドウ]** 列にあるそのリンクを選択します。 |
| [Spout] *(期間)* | トポロジで使用するスパウト。 特定のスパウトに関するより詳細な情報を表示するには、**[ID]** 列にあるそのリンクを選択します。 |
| [ボルト] *(期間)* | トポロジで使用するボルト。 特定のボルトに関するより詳細な情報を表示するには、**[ID]** 列にあるそのリンクを選択します。 |
| [Worker resources]\(worker リソース\) | worker リソースの一覧。 特定の worker リソースに関するより詳細な情報を表示するには、**[ホスト]** 列にあるそのリンクを選択します。 |
| [Topology visualization]\(トポロジの視覚化\) | トポロジの視覚化を表示する **[Show Visualization] (視覚化の表示)** ボタン。 |
| トポロジの構成 | 選択したトポロジの構成。 |

Storm のトポロジの概要ページは、次の Web ページのようになります。

:::image type="content" source="./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png" alt-text="[トポロジの概要] ページ、Storm UI、Apache Storm、Azure" border="true":::

**[トポロジのアクション]** セクションでは、次のボタンを選択してアクションを実行できます。

| Button | 説明 |
| --- | --- |
| アクティブ化 | アクティブ化が解除されたトポロジの処理を再開します。 |
| 非アクティブ化 | 実行中のトポロジを一時停止します。 |
| 再調整 | トポロジの並列処理を調整します。 クラスター内のノードの数を変更したら、実行中のトポロジを再調整する必要があります。 この操作により、クラスター内のノード数の増減を埋め合わせるようにトポロジで並列処理を調整できるようになります。<br/><br/>詳細については、<a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Apache Storm トポロジの並列処理の理解</a>に関するページを参照してください。
| 強制終了 | 指定したタイムアウト後に Storm トポロジを停止します。 |
| デバッグ | 実行中のトポロジに対するデバッグ セッションを開始します。 |
| [Stop Debug]\(デバッグの停止\) | 実行中のトポロジに対するデバッグ セッションを終了します。 |
| [Change Log Level]\(ログ レベルの変更\) | デバッグのログ レベルを変更します。 |

##### <a name="spout-and-bolt-summary"></a>スパウトとボルトの概要

**[スパウト]** または **[ボルト]** セクションでアイテムを選択すると、そのアイテムに関する次の情報が表示されます。

| Section | 説明 |
| --- | --- |
| コンポーネントの概要 | スパウトやボルトの基本情報。 |
| [コンポーネントのアクション] | **[デバッグ]** および **[Stop Debug] (デバッグの停止)** ボタン。 |
| [スパウトの統計] または [ボルトの統計] | スパウトやボルトの統計。 このセクション内のエントリの期間を設定するには、**[ウィンドウ]** 列にあるそのリンクを選択します。 |
| (ボルトのみ)<br/>[入力の統計] *(期間)* | ボルトが消費する入力ストリームに関する情報。 |
| [出力の統計] *(期間)* | このスパウトやボルトから出力されるストリームに関する情報。 |
| [Profiling and debugging]\(プロファイリングとデバッグ\) | このページでのコンポーネントのプロファイリングとデバッグのためのコントロール。 **[Status / Timeout (Minutes)] (状態/タイムアウト (分))** 値を設定したり、**[JStack]**、**[Restart Worker] (ワーカーの再起動)**、**[ヒープ]** の各ボタンを選択したりできます。 |
| [Executor] *(期間)* | スパウトやボルトのインスタンスに関する情報。 このインスタンスに対して生成された診断情報のログを表示するには、特定のエグゼキュータの **[ポート]** エントリを選択します。 また、**[ホスト]** 列にあるそのリンクを選択することによって、特定のエグゼキュータに関連付けられている worker リソースを表示することもできます。 |
| エラー | スパウトやボルトのエラー情報。 |

Storm のボルトの概要ページは、次の Web ページのようになります。

:::image type="content" source="./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png" alt-text="ボルトの概要ページ、Storm UI、Apache Storm、Azure" border="true":::

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>REST API を使用してトポロジを監視および管理する

Storm UI は REST API 上に構築されているため、REST API を使用して、同様の管理および監視タスクを実行できます。 REST API を使用して、Storm トポロジの管理や監視用のカスタム ツールを作成できます。

詳細については、「[Apache Storm UI REST API](https://storm.apache.org/releases/current/STORM-UI-REST-API.html)」を参照してください。 以下は、HDInsight での Apache Storm で REST API を使用する場合の情報です。

> [!IMPORTANT]  
> Storm REST API は、インターネット上には公開されていません。 HDInsight クラスター ヘッド ノードへの SSH トンネルを使用してアクセスする必要があります。 SSH トンネルの作成と使用については、[SSH トンネリングを使用した Azure HDInsight へのアクセス](../hdinsight-linux-ambari-ssh-tunnel.md)に関するページを参照してください。

### <a name="base-uri"></a>ベース URI

Linux ベースの HDInsight クラスターでの REST API のベース URI は、URL アドレス `https://HEADNODEFQDN:8744/api/v1/` で使用できます。ここで、*HEADNODEFQDN* をヘッド ノードに置き換えてください。 ヘッド ノードのドメイン名はクラスターの作成中に生成され、静的ではありません。

クラスター ヘッド ノードの完全修飾ドメイン名 (FQDN) は、次のいくつかの方法で見つけることができます。

| FQDN の検出方法 | 説明 |
| --- | --- |
| SSH セッション | SSH セッションからクラスターに `headnode -f` コマンドを使用します。 |
| Ambari Web | Ambari クラスターの Web ページ (`https://CLUSTERNAME.azurehdinsight.net`) で、ページの一番上から **[サービス]** を選択し、**[Storm]** を選択します。 **[概要 ]** タブで **[Storm UI Server]** を選択します。 Storm UI と REST API をホストするノードの FQDN はページの一番上で確認できます。 |
| Ambari REST API | `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` コマンドを使用し、Storm UI と REST API が実行されているノードに関する情報を取得します。 *CLUSTERNAME* の 2 つのインスタンスをクラスター名に置き換えます。 メッセージが表示されたら、ユーザー (管理者) アカウントのパスワードを入力します。 その応答の JSON 出力の "host_name" エントリにノードの FQDN が含まれています。 |

### <a name="authentication"></a>認証

REST API への要求では *基本認証* を使用する必要があるため、HDInsight クラスターの管理者の名前とパスワードを使用する必要があります。

> [!NOTE]  
> 基本認証はクリア テキストを使用して送信されるため、 *常に* HTTPS を使用してクラスターとの通信を保護する必要があります。

### <a name="return-values"></a>戻り値

REST API から返される情報は、クラスター内からのみ利用可能です。 たとえば、[Apache ZooKeeper](https://zookeeper.apache.org/) サーバーに対して返された完全修飾ドメイン名 (FQDN) はインターネットからアクセスできません。

## <a name="next-steps"></a>次のステップ

[Apache Maven を使用して Java ベースのトポロジを開発する](apache-storm-develop-java-topology.md)方法を学習します。

その他のトポロジの例の一覧については、[Azure HDInsight での Apache Storm トポロジの例](apache-storm-example-topology.md)に関するページを参照してください。
