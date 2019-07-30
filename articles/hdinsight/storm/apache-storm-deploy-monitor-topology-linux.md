---
title: Azure HDInsight での Apache Storm トポロジのデプロイと管理
description: Linux ベースの HDInsight で Storm ダッシュボードを使用して Apache Storm トポロジをデプロイ、監視、管理する方法について説明します。 Hadoop Tools for Visual Studio を使用します。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: ac1a4c77589f4ef88c9ee862cb871b376ca8a0fe
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483849"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Azure HDInsight での Apache Storm トポロジのデプロイと管理 

このドキュメントでは、HDInsight クラスターの Storm 上で実行されている [Apache Storm](https://storm.apache.org/) トポロジの管理および監視の基本について説明します。

## <a name="prerequisites"></a>前提条件

* HDInsight 上の Apache Storm クラスター。 [Azure portal を使用した Apache Hadoop クラスターの作成](../hdinsight-hadoop-create-linux-clusters-portal.md)に関するページを参照し、 **[クラスターの種類]** で **[Storm]** を選択してください。


* (省略可能) SSH と SCP を熟知していること。詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

* (省略可能) Visual Studio:Azure SDK 2.5.1 以降と Data Lake Tools for Visual Studio。 詳細については、[Data Lake Tools for Visual Studio の使用開始](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)に関するページをご覧ください。

    下記いずれかのバージョンの Visual Studio

  * Visual Studio 2012 Update 4

  * Visual Studio 2013 Update 4 または [Visual Studio 2013 Community](https://go.microsoft.com/fwlink/?LinkId=517284)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * Visual Studio 2015 (任意のエディション)

  * Visual Studio 2017 (任意のエディション)。 Data Lake Tools for Visual Studio 2017 は、Azure ワークロードの一部としてインストールされます。

## <a name="submit-a-topology-visual-studio"></a>トポロジの送信:Visual Studio

HDInsight Tools は、C# またはハイブリッド トポロジを Storm クラスターに送信する際に使用できます。 次の例では、サンプル アプリケーションを使用します。 HDInsight Tools を使用して作成する方法の詳細については、「[Visual Studio を使用して HDInsight で Apache Storm の C# トポロジを開発する](apache-storm-develop-csharp-visual-studio-topology.md)」をご覧ください。

1. Data Lake Tools for Visual Studio の最新バージョンをまだインストールしていない場合は、[Data Lake Tools for Visual Studio の使用開始](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)に関するページをご覧ください。

    > [!NOTE]  
    > Data Lake Tools for Visual Studio は、以前は HDInsight Tools for Visual Studio と呼ばれていました。
    >
    > Data Lake Tools for Visual Studio は、Visual Studio 2017 用の __Azure ワークロード__ に含まれています。

2. Visual Studio で、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** を選択します。

3. **[新しいプロジェクト]** ダイアログで、 **[インストール済]**  >  **[テンプレート]** の順に展開して **[HDInsight]** を選択します。 テンプレートの一覧から **[Storm Sample]** を選択します。 ダイアログ ボックスの下部に、アプリケーションの名前を入力します。

    ![image](./media/apache-storm-deploy-monitor-topology-linux/sample.png)

4. **[ソリューション エクスプローラー]** で、プロジェクトを右クリックして **[HDInsight の Storm に送信]** を選択します。

   > [!NOTE]  
   > メッセージが表示されたら、Azure サブスクリプションのログイン資格情報を入力します。 2 つ以上のサブスクリプションをお持ちの場合は、HDInsight クラスターの Storm があるサブスクリプションにログインします。

5. **[Storm クラスター]** ドロップダウン リストから HDInsight クラスターの Storm を選択して、 **[送信]** を選択します。 送信が成功したかどうかは、 **[出力]** ウィンドウを使用して確認できます。

## <a name="submit-a-topology-ssh-and-the-storm-command"></a>トポロジの送信:SSH と Storm コマンド

1. SSH を使用して、HDInsight クラスターに接続します。 **USERNAME** には、SSH ログイン名を指定します。 **CLUSTERNAME** には、HDInsight クラスター名を指定します。

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    SSH を使用して HDInsight クラスターに接続する方法の詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. 次のコマンドを実行してトポロジの例を開始します。

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount

    このコマンドにより、クラスターで WordCount トポロジの例が開始されます。 このトポロジは、ランダムに文を生成し、文中の各単語の出現回数をカウントします。

   > [!NOTE]  
   > トポロジをクラスターに送信する場合、まずクラスターを含む jar ファイルをコピーしてから、`storm` コマンドを実行します。 ファイルをクラスターにコピーするには、`scp` コマンドを使用できます。 たとえば、`scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar` のように指定します。
   >
   > WordCount の例と他の Storm スターターの例は、 `/usr/hdp/current/storm-client/contrib/storm-starter/`のクラスターに既に含まれています。

## <a name="submit-a-topology-programmatically"></a>トポロジの送信: プログラムで

Nimbus サービスを使用してトポロジをプログラムによってデプロイできます。 [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) に用意されている Java アプリケーションの例を参照してください。

## <a name="monitor-and-manage-visual-studio"></a>監視と管理:Visual Studio

Visual Studio を使用してトポロジが送信されたら、**Storm トポロジ** ビューが表示されます。 実行中のトポロジに関する情報を表示するには、一覧からトポロジを選択します。

![Visual Studio モニター](./media/apache-storm-deploy-monitor-topology-linux/vsmonitor.png)

> [!NOTE]  
> また、**Storm トポロジ**は、 **[サーバー エクスプローラー]** で **[Azure]**  >  **[HDInsight]** の順に展開して、HDInsight クラスターの Storm を右クリックして **[View Storm Topologies]** を選択して表示できます。

情報を表示するスパウトまたはボルト コンポーネントの形状を選択します。 各アイテムを選択すると新しいウィンドウが開きます。

### <a name="deactivate-and-reactivate"></a>アクティブ化の解除と再アクティブ化

トポロジが強制終了または再アクティブ化されるまで、トポロジのアクティブ化の解除は一時停止されます。 これらの操作を実行するには、__トポロジの概要__ の上部にある __[非アクティブ化]__ と __[再アクティブ化]__ のボタンを使用します。

### <a name="rebalance"></a>再調整

トポロジを再調整すると、トポロジの並列処理が変更されます。 たとえば、クラスターのサイズを変更してノートを追加すると、再調整によってトポロジに新しいノードが表示されます。

トポロジを再調整するには、 __[トポロジの概要]__ の上部にある __[再調整]__ ボタンを使用します。

> [!WARNING]  
> トポロジを再調整すると、まずトポロジのアクティブ化が解除され、worker がクラスター全体に平均的に再分配され、最終的に、トポロジは再調整の発生前の状態に戻ります。 そのため、トポロジがアクティブであった場合は、再度アクティブになります。 非アクティブであった場合は、非アクティブのままになります。

### <a name="kill-a-topology"></a>トポロジの強制終了

Storm トポロジは停止されるまで、またはクラスターが削除されるまで実行し続けます。 トポロジを停止するには、 __[トポロジの概要]__ の上部にある __[強制終了]__ ボタンを使用します。

## <a name="monitor-and-manage-ssh-and-the-storm-command"></a>監視と管理:SSH と Storm コマンド

`storm` ユーティリティを使用すると、コマンド ラインから実行中のトポロジを操作できます。 完全なコマンド一覧を表示するには、 `storm -h` を使用してください。

### <a name="list-topologies"></a>トポロジの一覧を表示する

実行中のすべてのトポロジを一覧表示するには、次のコマンドを使用します。

    storm list

このコマンドでは、次のテキストのような情報が返されます。

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>アクティブ化の解除と再アクティブ化

トポロジが強制終了または再アクティブ化されるまで、トポロジのアクティブ化の解除は一時停止されます。 次のコマンドを使用して、アクティブ化の解除と再アクティブ化を行います。

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>実行中のトポロジを強制終了する

Storm トポロジが開始されると、停止されるまで実行が継続されます。 トポロジを停止するには、次のコマンドを使用します。

    storm kill TOPOLOGYNAME

### <a name="rebalance"></a>再調整

トポロジを再調整すると、トポロジの並列処理が変更されます。 たとえば、クラスターのサイズを変更してノートを追加すると、再調整によってトポロジに新しいノードが表示されます。

> [!WARNING]  
> トポロジを再調整すると、まずトポロジのアクティブ化が解除され、worker がクラスター全体に平均的に再分配され、最終的に、トポロジは再調整の発生前の状態に戻ります。 そのため、トポロジがアクティブであった場合は、再度アクティブになります。 非アクティブであった場合は、非アクティブのままになります。

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-storm-ui"></a>監視と管理:Storm UI

Storm UI には、トポロジの実行を操作する Web インターフェイスがあり、HDInsight クラスターに含まれています。 Storm UI を表示するには、Web ブラウザーを使用して **https://CLUSTERNAME.azurehdinsight.net/stormui** を開きます。**CLUSTERNAME** は実際のクラスターの名前です。

> [!NOTE]  
> ユーザー名とパスワードの入力が求められたら、クラスターの作成時に使用したクラスター管理者名 (admin) とパスワードを入力します。

### <a name="main-page"></a>メイン ページ

Storm UI のメイン ページには、次の情報が表示されます。

* **クラスターの概要**:Storm クラスターの基本情報。
* **トポロジの概要**:実行中のトポロジの一覧。 このセクションのリンクを使用して、各トポロジの詳細情報を表示します。
* **スーパーバイザーの概要**:Storm のスーパーバイザーに関する情報。
* **Nimbus 構成**:クラスターの Nimbus 構成。

### <a name="topology-summary"></a>トポロジの概要

**[トポロジの概要]** セクションのリンクを選択すると、トポロジに関する次の情報が表示されます。

* **トポロジの概要**:トポロジの基本情報。
* **トポロジのアクション**:トポロジで実行できる管理アクション。

  * **アクティブ化**:アクティブ化が解除されたトポロジの処理を再開します。
  * **非アクティブ化**:実行中のトポロジを一時停止します。
  * **再調整**:トポロジの並列処理を調整します。 クラスターのノード数を変更した場合は、実行中のトポロジを再調整する必要があります。 この操作によって、クラスター内のノード数の増減に合わせて、トポロジの並列処理を調整できます。

    詳細については、<a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Apache Storm トポロジの並列処理の理解</a>に関するページを参照してください。
  * **強制終了**:指定したタイムアウト後に Storm トポロジを停止します。
* **トポロジの統計**:トポロジに関する統計。 ページで残りのエントリの時間枠を設定するには、 **[Window]** 列にあるリンクを使用します。
* **スパウト**:トポロジで使用するスパウト。 このセクションにあるリンクを使用して、各スパウトの詳細情報を表示します。
* **ボルト**:トポロジで使用するボルト。 このセクションにあるリンクを使用して、各ボルトの詳細情報を表示します。
* **トポロジの構成**:選択したトポロジの構成。

### <a name="spout-and-bolt-summary"></a>スパウトとボルトの概要

**[スパウト]** または **[ボルト]** セクションでアイテムを選択すると、そのアイテムに関する次の情報が表示されます。

* **コンポーネントの概要**:スパウトやボルトの基本情報。
* **スパウト/ボルトの統計**:スパウトやボルトの統計。 ページで残りのエントリの時間枠を設定するには、 **[Window]** 列にあるリンクを使用します。
* **入力の統計** (ボルトのみ):ボルトが消費する入力ストリームに関する情報。
* **出力の統計**:このスパウトやボルトから出力されるストリームに関する情報。
* **エグゼキュータ**:スパウトやボルトのインスタンスに関する情報。 特定のエグゼキュータの **[ポート]** エントリを選択して、このインスタンスで生成された診断情報のログを閲覧します
* **エラー**:スパウトやボルトのエラー情報。

## <a name="monitor-and-manage-rest-api"></a>監視と管理:REST API

Storm UI は、REST API を基に構築されているため、REST API を使用して同様の管理や監視機能を実行できます。 REST API を使用して、Storm トポロジの管理や監視用のカスタム ツールを作成できます。

詳細については、「[Apache Storm UI REST API](https://storm.apache.org/releases/current/STORM-UI-REST-API.html)」を参照してください。 以下は、HDInsight での Apache Storm で REST API を使用する場合の情報です。

> [!IMPORTANT]  
> Storm REST API は、インターネットでパブリックに使用できません。SSH トンネルを使用して HDInsight クラスター ヘッド ノードにアクセスする必要があります。 SSH トンネルの作成および使用については、[SSH トンネリングを使用して Apache Ambari Web UI、ResourceManager、JobHistory、NameNode、Apache Oozie、およびその他の Web UI にアクセスする方法](../hdinsight-linux-ambari-ssh-tunnel.md)に関するページを参照してください。

### <a name="base-uri"></a>ベース URI

Linux ベースの HDInsight クラスターの REST API のベース URI は、**https:\//HEADNODEFQDN:8744/api/v1/** にあるヘッド ノードで確認できます。 ヘッド ノードのドメイン名は、クラスターの作成時に生成され、静的ではありません。

クラスター ヘッド ノードの完全修飾ドメイン名 (FQDN) はいくつかの方法で確認できます。

* **SSH セッションから**:SSH セッションからクラスターに `headnode -f` コマンドを使用します。
* **Ambari Web から**:ページの一番上から **[サービス]** を選択し、 **[Storm]** を選択します。 **[概要 ]** タブで **[Storm UI Server]** を選択します。 Storm UI と REST API をホストするノードの FQDN はページの一番上で確認できます。
* **Ambari REST API から**:`curl -u admin -G "https:\//CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` コマンドを使用し、Storm UI と REST API が実行されているノードに関する情報を取得します。 **CLUSTERNAME** をクラスター名に置き換えます。 メッセージが表示されたら、ログイン (管理者) アカウントのパスワードを入力します。 応答の「host_name」エントリにノードの FQDN が含まれます。

### <a name="authentication"></a>Authentication

REST API への要求では、HDInsight クラスターの管理者名とパスワードを使用して、 **基本認証**を使用する必要があります。

> [!NOTE]  
> 基本認証はクリア テキストを使用して送信されるため、 **常に** HTTPS を使用してクラスターとの通信を保護する必要があります。

### <a name="return-values"></a>戻り値

REST API から返される情報は、クラスター内からのみ利用可能です。 たとえば、[Apache ZooKeeper](https://zookeeper.apache.org/) サーバーに対して返された完全修飾ドメイン名 (FQDN) はインターネットからアクセスできません。

## <a name="next-steps"></a>次の手順

[Apache Maven を使用して Java ベースのトポロジを開発する](apache-storm-develop-java-topology.md)方法を学習します。

その他のトポロジの例の一覧については、「[HDInsight での Apache Storm のトポロジ例](apache-storm-example-topology.md)」を参照してください。
