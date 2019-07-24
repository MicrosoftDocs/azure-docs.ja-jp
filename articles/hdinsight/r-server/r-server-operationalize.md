---
title: HDInsight の ML サービスの運用化 - Azure
description: Azure HDInsight で ML サービスを運用化する方法について説明します。
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: d0cae4210fa313c5d6e1f33987422e7b0d53e76a
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53579959"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>Azure HDInsight 上の ML サービス クラスターの運用化

HDInsight で ML サービス クラスターを使用して、ご自身のデータ モデリングが完了したら、モデルを運用化して予測を行うことができます。 この記事では、このタスクを実行する方法について説明します。

## <a name="prerequisites"></a>前提条件

* **HDInsight 上の ML サービス クラスター**: 手順については、「[HDInsight の ML サービスの概要](r-server-get-started.md)」を参照してください。

* **Secure Shell (SSH) クライアント**: SSH クライアントは、HDInsight クラスターにリモート接続し、そのクラスター上でコマンドを直接実行するために使用されます。 詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>ML サービス クラスターをワンボックス構成で運用化する

> [!NOTE]  
> 以下の手順は、R Server 9.0 と ML Server 9.1 に適用されます。 ML Server 9.3 については、[運用化構成を管理するための管理ツールの使用](https://docs.microsoft.com/machine-learning-server/operationalize/configure-admin-cli-launch)に関するページをご覧ください。

1. エッジ ノードに SSH 接続します。

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    Azure HDInsight で SSH を使用する方法については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページをご覧ください。

1. 関連するバージョンのディレクトリに変更し、dot net dll を sudo します。 

    - Microsoft ML Server 9.1 の場合:

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - Microsoft R Server 9.0 の場合:

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. 選択できるオプションが表示されます。 次のスクリーンショットに示すように、最初のオプションを選択して、**ML Server を運用化のために構成**します。

    ![ワンボックス操作](./media/r-server-operationalize/admin-util-one-box-1.png)

1. 次に表示されるオプションでは、ML Server を運用化する方法を選択します。 表示されたオプションから最初のオプションを選択します。それには「**A**」を入力します。

    ![ワンボックス操作](./media/r-server-operationalize/admin-util-one-box-2.png)

1. メッセージが表示されたら、ローカル管理者ユーザーのパスワードを入力し、さらに、もう一度入力します。

1. 操作が成功したことを示す出力が表示されます。 また、メニューから他のオプションを選択するよう求められます。 E を選択して、メイン メニューに戻ります。

    ![ワンボックス操作](./media/r-server-operationalize/admin-util-one-box-3.png)

1. 必要に応じて、次のように診断テストを実行することで、診断チェックを実行できます。

    a. メイン メニューから、**6** を選択して、診断テストを実行します。

    ![ワンボックス操作](./media/r-server-operationalize/diagnostic-1.png)

    b. Diagnostic Tests メニューから、**A** を選択します。メッセージが表示されたら、ローカル管理者ユーザーに対して指定したパスワードを入力します。

    ![ワンボックス操作](./media/r-server-operationalize/diagnostic-2.png)

    c. 出力を確認し、全体的な正常性が pass であることを確かめます。

    ![ワンボックス操作](./media/r-server-operationalize/diagnostic-3.png)

    d. 表示されたメニュー オプションから「**E**」を入力して、メイン メニューに戻ります。次に、「**8**」を入力して、管理ユーティリティを終了します。

### <a name="long-delays-when-consuming-web-service-on-apache-spark"></a>Apache Spark で Web サービスを実行しているときの長い待ち時間

mrsdeploy の機能を使って作成された Web サービスを Apache Spark コンピューティング コンテキストで実行しようとしているときに長い待ち時間が生じた場合、いくつかの不足しているフォルダーを追加する必要があります。 Spark アプリケーションは、Web サービスから mrsdeploy の機能を使って呼び出された場合は常に、"*rserve2*" というユーザーに属します。 この問題を回避するには、次のようにします。

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


この段階で、運用化の構成が完了しました。 これで、ご自身の RClient の `mrsdeploy` パッケージを使用してエッジ ノードの運用化に接続し、[リモート実行](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely)や [Web サービス](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)などの機能の使用を開始できます。 クラスターが仮想ネットワーク上に設定されているか否かに応じて、SSH ログイン経由のポート転送トンネリングの設定が必要になる場合があります。 以降のセクションでは、このトンネルの設定方法について説明します。

### <a name="ml-services-cluster-on-virtual-network"></a>仮想ネットワーク上の ML サービス クラスター

ポート 12800 を介したエッジ ノードへのトラフィックを許可していることを確認します。 これで、運用化機能への接続にエッジ ノードを使用できます。


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


`remoteLogin()` でエッジ ノードに接続できなくても、エッジ ノードへの SSH 接続が可能な場合は、ポート 12800 でトラフィックを許可するルールが適切に設定されているかどうかを確認する必要があります。 問題が解決しない場合は、SSH 経由のポート転送トンネリングを設定することで問題を回避できます。 手順については、次のセクションを参照してください。

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>仮想ネットワーク上に設定されていない ML サービス クラスター

クラスターが VNet 上にセットアップされていない場合、または VNet 経由の接続で問題が発生している場合は、SSH ポート転送トンネリングを使用できます。

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

SSH セッションがアクティブになると、ローカル コンピューターのポート 12800 からのトラフィックは、SSH セッション経由でエッジ ノードのポート 12800 に転送されます。 `remoteLogin()` メソッドで `127.0.0.1:12800` を使用していることを確認してください。 これにより、ポート転送経由でエッジ ノードの運用化にログインします。


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>HDInsight worker ノードで運用化コンピューティング ノードをスケーリングする

コンピューティング ノードスケーリングするには、最初に worker ノードの使用を停止し、その worker ノードでコンピューティング ノードを構成します。

### <a name="step-1-decommission-the-worker-nodes"></a>ステップ 1:ワーカー ノードを使用停止する

ML サービス クラスターは [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) では管理されていません。 worker ノードの使用が停止されていないと、YARN リソース マネージャーは、サーバーによってリソースが使用されていることを認識しないため、想定どおりに機能しません。 この状況を防ぐため、コンピューティング ノードをスケールアウトする前に、ワーカー ノードの使用を停止することをお勧めします。

次の手順に従って、worker ノードの使用を停止します。

1. クラスターの Ambari コンソールにログインして、**[ホスト]** タブをクリックします。

1. (使用を停止する) worker ノードを選択します。

1. **[アクション]** > **[選択したホスト]** > **[ホスト]** > **[メンテナンス モードの有効化]** の順にクリックします。 たとえば、次の画像では、使用停止の対象として wn3 と wn4 が選択されています。  

   ![ワーカー ノードの使用停止](./media/r-server-operationalize/get-started-operationalization.png)  

* **[アクション]** > **[選択したホスト]** > **[DataNodes]** の順に選択し、**[使用停止]** をクリックします。
* **[アクション]** > **[選択したホスト]** > **[NodeManagers]** の順に選択し、**[使用停止]** をクリックします。
* **[アクション]** > **[選択したホスト]** > **[DataNodes]** の順に選択し、**[停止]** をクリックします。
* **[アクション]** > **[選択したホスト]** > **[NodeManagers]** の順に選択し、**[停止]** をクリックします。
* **[アクション]** > **[選択したホスト]** > **[ホスト]** の順に選択し、**[Stop All Components]\(すべてのコンポーネントを停止\)** をクリックします。
* ワーカー ノードの選択を解除し、ヘッド ノードを選択します。
* **[アクション]** > **[選択したホスト]** > **[ホスト]** の順に選択し、**[Restart All Components]\(すべてのコンポーネントを再起動\)** をクリックします。

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>手順 2:使用停止された各ワーカー ノード上でコンピューティング ノードを構成する

1. 使用停止されたワーカー ノードに SSH 接続します。

1. ご自身の ML サービス クラスターの関連 DLL を使用して、管理ユーティリティを実行します。 ML Server 9.1 の場合は、次の手順を実行します。

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. 「**1**」を入力して、オプション **[Configure ML Server for Operationalization]** を選択します。

1. 「**C**」を入力して、オプション `C. Compute node` を選択します。 これで、ワーカー ノードでコンピューティング ノードが構成されます。

1. 管理ユーティリティを終了します。

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>手順 3:Web ノード上でコンピューティング ノードの詳細を追加する

使用停止状態の worker ノードすべてがコンピューティング ノードを実行するように構成されたら、エッジ ノードに戻って、使用が停止された worker ノードの IP アドレスを ML Server Web ノードの構成に追加します。

1. エッジ ノードに SSH 接続します。

1. `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json` を実行します。

1. "Uris" セクションを見つけて、worker ノードの IP とポートの詳細を追加します。

       "Uris": {
         "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
         "Values": [
           "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
         ]
       }

## <a name="next-steps"></a>次の手順

* [HDInsight 上の ML サービス クラスターの管理](r-server-hdinsight-manage.md)
* [HDInsight 上の ML サービス クラスター向けのコンピューティング コンテキスト オプション](r-server-compute-contexts.md)
* [HDInsight 上の ML サービス クラスター向けの Azure Storage オプション](r-server-storage.md)
