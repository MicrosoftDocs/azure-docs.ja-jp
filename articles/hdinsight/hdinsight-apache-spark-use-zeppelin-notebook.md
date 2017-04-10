---
title: "スクリプト アクションを使用した Azure HDInsight の Spark クラスターでの Zeppelin Notebook のインストール | Microsoft Docs"
description: "HDInsight Linux の Spark クラスターに Zeppelin Notebook をインストールして使用する手順を説明します。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: cb276220-fb02-49e2-ac55-434fcb759629
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 9e6e0a696ba27c7758c21fa46754a8539ae2255b
ms.lasthandoff: 03/29/2017


---
# <a name="install-zeppelin-notebooks-for-apache-spark-cluster-on-hdinsight"></a>HDInsight の Apache Spark クラスターに Zeppelin Notebook をインストールする

Apache Spark クラスターに Zeppelin Notebook をインストールする方法と、Zeppelin Notebook を使用して Spark ジョブを実行する方法について説明します。

> [!IMPORTANT]
> HDInsight 3.5 に Spark 1.6 クラスターをプロビジョニングした場合は、「[Azure HDInsight の Apache Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-zeppelin-notebook.md)」の手順に従うことで、既定で Zeppelin にアクセスできます。 HDInsight クラスター バージョン 3.3 または 3.4 で Zeppelin を使用する場合は、この記事の手順に従って Zeppelin をインストールする必要があります。
>
> この記事のスクリプトは、Spark 2.0 クラスターでの Zeppelin のインストールをサポートしていません。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションが必要です。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* HDInsight での Apache Spark クラスター。 手順については、 [Azure HDInsight での Apache Spark クラスターの作成](hdinsight-apache-spark-jupyter-spark-sql.md)に関するページを参照してください。


## <a name="install-zeppelin-on-a-spark-cluster"></a>Spark クラスターへの Zeppelin のインストール
Zeppelin は、スクリプト アクションを使用して Spark クラスターにインストールできます。 スクリプト アクションは、既定で使用できないクラスターにコンポーネントをインストールするためにカスタム スクリプトを使用します。 HDInsight .NET SDK を使用するか、または Azure PowerShell を使用すると、カスタム スクリプトを使用して、Azure ポータルから Zeppelin をインストールできます。 スクリプトによる Zeppelin のインストールは、クラスター作成の一環として、またはクラスターの稼働後に実行できます。 これを行う方法については、以降のセクションのリンクで説明します。

### <a name="using-the-azure-portal"></a>Azure ポータルの使用
Zeppelin をインストールするスクリプト アクションを実行するために Azure ポータルを使用する方法については、「 [スクリプト アクションを使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)」をご覧ください。 その記事の手順は、いくつか変更する必要があります。

* Zeppelin をインストールするには、スクリプトを使用する必要があります。 HDInsight の Spark クラスターに Zeppelin をインストールするカスタム スクリプトは、次のリンクから入手できます。

  * Spark 1.6.0 クラスターの場合 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
  * Spark 1.5.2 クラスターの場合 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`
* スクリプト アクションは、ヘッドノードでのみ実行する必要があります。
* スクリプトにパラメーターは必要ありません。

### <a name="using-hdinsight-net-sdk"></a>HDInsight .NET SDK の使用
Zeppelin をインストールするスクリプト アクションを実行するために HDInsight .NET SDK を使用する方法については、「 [スクリプト アクションを使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)」を参照してください。 その記事の手順は、いくつか変更する必要があります。

* Zeppelin をインストールするには、スクリプトを使用する必要があります。 HDInsight の Spark クラスターに Zeppelin をインストールするカスタム スクリプトは、次のリンクから入手できます。

  * Spark 1.6.0 クラスターの場合 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
  * Spark 1.5.2 クラスターの場合 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`
* スクリプトにパラメーターは必要ありません。
* 作成しているクラスターの種類を Spark に設定します。

### <a name="using-azure-powershell"></a>Azure PowerShell の使用
次の PowerShell スニペットを使用すると、Zeppelin がインストールされている HDInsight Linux の Spark クラスターを作成できます。 所有している Spark クラスターのバージョンに応じて、対応するカスタム スクリプトへのリンクを含めるために、次の PowerShell スニペットを更新する必要があります。

* Spark 1.6.0 クラスターの場合 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
* Spark 1.5.2 クラスターの場合 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    Login-AzureRMAccount

    # PROVIDE VALUES FOR THE VARIABLES
    $clusterAdminUsername="admin"
    $clusterAdminPassword="<<password>>"
    $clusterSshUsername="adminssh"
    $clusterSshPassword="<<password>>"
    $clusterName="<<clustername>>"
    $clusterContainerName=$clusterName
    $resourceGroupName="<<resourceGroupName>>"
    $location="<<region>>"
    $storage1Name="<<storagename>>"
    $storage1Key="<<storagekey>>"
    $subscriptionId="<<subscriptionId>>"

    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    $passwordAsSecureString=ConvertTo-SecureString $clusterAdminPassword -AsPlainText -Force
    $clusterCredential=New-Object System.Management.Automation.PSCredential ($clusterAdminUsername, $passwordAsSecureString)
    $passwordAsSecureString=ConvertTo-SecureString $clusterSshPassword -AsPlainText -Force
    $clusterSshCredential=New-Object System.Management.Automation.PSCredential ($clusterSshUsername, $passwordAsSecureString)

    $azureHDInsightConfigs= New-AzureRmHDInsightClusterConfig -ClusterType Spark
    $azureHDInsightConfigs.DefaultStorageAccountKey = $storage1Key
    $azureHDInsightConfigs.DefaultStorageAccountName = "$storage1Name.blob.core.windows.net"

    Add-AzureRMHDInsightScriptAction -Config $azureHDInsightConfigs -Name "Install Zeppelin" -NodeType HeadNode -Parameters "void" -Uri "https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh"

    New-AzureRMHDInsightCluster -Config $azureHDInsightConfigs -OSType Linux -HeadNodeSize "Standard_D12" -WorkerNodeSize "Standard_D12" -ClusterSizeInNodes 2 -Location $location -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $clusterCredential -DefaultStorageContainer $clusterContainerName -SshCredential $clusterSshCredential -Version "3.3"

## <a name="access-the-zeppelin-notebook"></a>Zeppelin Notebook へのアクセス

スクリプト アクションを使用して Zeppelin を正常にインストールできたら、以下の手順で、Spark クラスター上の Zeppelin Notebook にアクセスできます。 このセクションでは、%sql および %hive の各ステートメントを実行する方法を説明します。

1. Web ブラウザーから、次のエンドポイントを開きます。

        https://CLUSTERNAME.azurehdinsight.net/zeppelin

   
2. 新しい Notebook を作成します。 ヘッダー ウィンドウで **[Notebook]** をクリックし、**[Note の新規作成]** をクリックします。

    ![新しい Zeppelin Notebook の作成](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.createnewnote.png "新しい Zeppelin Notebook の作成")

    同じページの **[Notebook]** 見出しの下に、**Note XXXXXXXXX** で名前が始まる新しい Notebook が表示されます。 新しい Notebook をクリックします。
3. 新しい Notebook の Web ページで、見出しをクリックし、必要に応じて Notebook の名前を変更します。 Enter キーを押して名前の変更を保存します。 また、Notebook のヘッダーの右上隅に **[接続]** というステータスが表示されることを確認します。

    ![Zeppelin Notebook のステータス](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.newnote.connected.png "Zeppelin Notebook のステータス")

### <a name="run-sql-statements"></a>SQL ステートメントを実行する
1. サンプル データを一時テーブルに読み込みます。 HDInsight の Spark クラスターを作成すると、サンプル データ ファイル **hvac.csv** が、関連するストレージ アカウントの **\HdiSamples\SensorSampleData\hvac** にコピーされます。

    新しい Notebook に既定で作成される空の段落に、次のスニペットを貼り付けます。

        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        // Define a schema
        case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

        // Map the values in the .csv file to the schema
        val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
            s => Hvac(s(0),
                    s(1),
                    s(2).toInt,
                    s(3).toInt,
                    s(6)
            )
        ).toDF()

        // Register as a temporary table called "hvac"
        hvac.registerTempTable("hvac")

    **Shift + Enter** キーを押すか、段落の **[プレイ]** ボタンをクリックして、スニペットを実行します。 段落の右上隅にあるステータスが、[準備完了]、[保留中]、[実行中]、[完了] の順に進行します。 出力が同じ段落の下に表示されます。 スクリーンショットは次のようになります。

    ![生データから一時テーブルを作成する](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.loaddDataintotable.png "生データから一時テーブルを作成する")

    各段落にタイトルを指定することもできます。 右上隅の **[設定]** アイコンをクリックし、**[タイトルの表示]** をクリックします。
2. **hvac** テーブルに対して Spark SQL ステートメントを実行できます。 次のクエリを新しい段落に貼り付けます。 このクエリは、ビル ID と、特定の日の各ビルの目標温度と実温度の差を取得します。 **Shift + Enter**キーを押します。

        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date
        from hvac
        where date = "6/1/13"

    先頭にある **%sql** ステートメントは、Spark SQL インタープリターを使用するように Notebook に指示します。 定義済みのインタープリターは、Notebook ヘッダーの **[インタープリター]** タブで見ることができます。

    次のスクリーンショットでは出力を示します。

    ![Notebook を使用して Spark SQL ステートメントを実行する](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery1.png "Notebook を使用して Spark SQL ステートメントを実行する")

     表示オプション (四角で囲ってある部分) をクリックして、同じ出力の異なる表現に切り替えることができます。 **[設定]** をクリックして、出力のキーと値の構成を選択します。 上記の画面キャプチャでは、**buildingID** をキーとして使用し、**temp_diff** の平均を値として使用しています。
3. クエリの変数を使用して Spark SQL ステートメントを実行することもできます。 次のスニペットでは、クエリで変数 **Temp**と照会できる値を定義する方法を示します。 初めてクエリを実行すると、変数に指定した値がドロップダウンに自動的に設定されます。

        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
        from hvac
        where targettemp > "${Temp = 65,65|75|85}"

    このスニペットを新しい段落に貼り付けて、 **Shift + Enter**キーを押します。 次のスクリーンショットでは出力を示します。

    ![Notebook を使用して Spark SQL ステートメントを実行する](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery2.png "Notebook を使用して Spark SQL ステートメントを実行する")

    後続のクエリでは、ドロップダウンから新しい値を選択し、クエリを再実行できます。 **[設定]** をクリックして、出力のキーと値の構成を選択します。 上の画面キャプチャでは、**buildingID** をキーとして、**temp_diff** の平均を値として、**targettemp** をグループとして使用しています。
4. Spark SQL インタープリターを再起動して、アプリケーションを終了します。 上部の **[インタープリター]** タブをクリックし、Spark インタープリターの **[再起動]** をクリックします。

    ![Zeppelin インタープリターを再起動する](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Zeppelin インタープリターを再起動する")

### <a name="run-hive-statements"></a>Hive ステートメントを実行する
1. Zeppelin Notebook で **[Interpreter]** ボタンをクリックします。

    ![Hive インタープリターを更新する](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-1.png "Hive インタープリターを更新する")
2. **Hive** インタープリターの場合、**[edit]** をクリックします。

    ![Hive インタープリターを更新する](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-2.png "Hive インタープリターを更新する")

    次のプロパティを更新します。

   * **[default.password]** を、HDInsight Spark クラスターの作成時に管理者ユーザーに指定したパスワードに設定します。
   * **[default.url]** を `jdbc:hive2://<spark_cluster_name>.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2` に設定します。 **\<spark_cluster_name>** を Spark クラスターの名前で置き換えます。
   * **[default.user]** を、クラスターの作成時に指定した管理者ユーザーの名前に設定します。 たとえば、 *admin*です。
3. **[Save]** をクリックし、Hive インタープリターを再起動することを求めるメッセージが表示されたら、**[OK]** をクリックします。
4. 新しい Notebook を作成し、次のステートメントを実行してクラスター上のすべての Hive テーブルを一覧表示します。

        %hive
        SHOW TABLES

    既定では、HDInsight クラスターには **hivesampletable** という名前のサンプルのテーブルがあるため、次のような出力が表示されます。

    ![Hive の出力](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-3.png "Hive の出力")
5. 次のステートメントを実行してテーブル内のレコードを一覧表示します。

        %hive
        SELECT * FROM hivesampletable LIMIT 5

    出力は次のように表示されます。

    ![Hive の出力](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-4.png "Hive の出力")

## <a name="seealso"></a>関連項目
* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ
* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applicatons (Linux)](hdinsight-apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

