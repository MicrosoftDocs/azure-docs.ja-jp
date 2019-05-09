---
title: HDInsight での ML Services の概要 - Azure
description: ML サービスを含む HDInsight クラスターで Apache Spark を作成し、そのクラスターで R スクリプトを送信する方法について説明します。
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 474048bc9f31e4630110ea099f3e0b375ad74e51
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58087586"
---
# <a name="get-started-with-ml-services-on-azure-hdinsight"></a>Azure HDInsight の ML サービスの概要

Azure HDInsight を使用すると、ML サービス クラスターを作成できます。 このオプションにより、R スクリプトで [Apache Spark](https://spark.apache.org/) と [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) を使用して、分散計算を実行できます。 この記事では、HDInsight 上で ML サービス クラスターを作成する方法と、分散 R 計算での Spark の使用方法を示す R スクリプトを実行する方法を学習します。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**:このチュートリアルを開始する前に、Azure サブスクリプションが必要です。 詳細については、[Microsoft Azure 無料試用版の入手](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関する記事を参照してください。
* **Secure Shell (SSH) クライアント**:SSH クライアントは、HDInsight クラスターにリモート接続し、そのクラスター上でコマンドを直接実行するために使用されます。 詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-the-cluster-using-the-azure-portal"></a>Azure Portal を使用してクラスターを作成する

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. **[+ リソースの作成]** > **[Analytics]** > **[HDInsight]** に移動します。

3. **[基本]** から次の情報を入力します。

    * **クラスター名**:HDInsight クラスターの名前。
    * **サブスクリプション**:使用するサブスクリプションを選択します。
    * **クラスター ログイン ユーザー名**および**クラスター ログイン パスワード**:HTTPS 経由でクラスターにアクセスする場合のログイン。 これらの資格情報を使用して、Apache Ambari Web UI や REST API などのサービスにアクセスします。
    * **Secure Shell (SSH) ユーザー名**:SSH 経由でクラスターにアクセスする際に使用されるログイン。 既定では、このパスワードは、クラスター ログイン パスワードと同じです。
    * **リソース グループ**:クラスターが作成されるリソース グループ。
    * **[場所]**:クラスターが作成される Azure リージョン。

        ![クラスターの基本情報](./media/r-server-get-started/clustername.png)

4. **[クラスターの種類]** を選択し、**[クラスターの構成]** セクションで次の値を設定します。

    * **クラスターの種類**:ML サービス

    * **[オペレーティング システム]**:Linux

    * **バージョン**:ML Server 9.3 (HDI 3.6)。 ML Server 9.3 のリリース ノートは、[docs.microsoft.com](https://docs.microsoft.com/machine-learning-server/whats-new-in-machine-learning-server) でご確認いただけます。

    * **ML Server の R Studio コミュニティ エディション**:このブラウザーベースの IDE は、既定でエッジ ノードにインストールされます。 この IDE をインストールしない場合は、チェック ボックスをオフにします。 インストールを選択した場合、クラスターの作成後に、ポータルでクラスターのアプリケーション ブレードに RStudio Server へのアクセス用の URL が提供されます。

        ![クラスターの基本情報](./media/r-server-get-started/clustertypeconfig.png)

4. クラスターの種類を選択したら、__[選択]__ ボタンを使用してクラスターの種類を設定します。 次に、__[次へ]__ ボタンを使用して、基本的な構成を完了します。

5. **[ストレージ]** セクションで、ストレージ アカウントを選択または作成します。 このドキュメントの手順では、このセクションの他のフィールドを既定値のままにします。 __[次へ]__ ボタンを使用して、ストレージの構成を保存します。

    ![HDInsight のストレージ アカウント設定](./media/r-server-get-started/cluster-storage.png)

6. **[概要]** セクションで、クラスターの構成を確認します。 間違った設定を変更するには、__[編集]__ リンクを使用します。 最後に、__[作成]__ ボタンを使用してクラスターを作成します。

    ![HDInsight のストレージ アカウント設定](./media/r-server-get-started/clustersummary.png)

    > [!NOTE]  
    > クラスターの作成には最大で 20 分かかります。

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>RStudio Server に接続する

HDInsight クラスターの一部として RStudio Server Community Edition をインストールすることを選択した場合は、次の 2 とおりの方法で RStudio ログインにアクセスできます。

* **方法 1** - 次の URL に移動します (**CLUSTERNAME** は、自分が作成した ML サービス クラスターの名前)。

        https://CLUSTERNAME.azurehdinsight.net/rstudio/

* **方法 2** - Azure portal を使用します。
  ポータルから:
  1. 左側のメニューから **[すべてのサービス]** を選択します。
  2. **[ANALYTICS]** で **[HDInsight クラスター]** を選択します。
  3. **[HDInsight クラスター]** ページから、ご自身のクラスター名を選択します。
  4. **[ML Services ダッシュボード]** から **[R Studio server]\(R Studio サーバー\)** を選択します。 

     ![HDInsight のストレージ アカウント設定](./media/r-server-get-started/r-studio-server-dashboard.png)

     > [!IMPORTANT]  
     > どちらの方法でも、初めてログインするときは認証を 2 回行う必要があります。  最初の認証プロンプトでは、"*クラスター管理者のユーザー ID*" と "*パスワード*" を入力します。 2 回目の認証プロンプトでは、"*SSH ユーザー ID*" と "*パスワード*" を入力します。 以降のログインでは、SSH 資格情報のみが求められます。

接続後の画面は、次のスクリーンショットのようになります。

![RStudio への接続](./media/r-server-get-started/connect-to-r-studio.png)

## <a name="run-a-sample-job"></a>サンプル ジョブを実行する

ScaleR 関数を使用してジョブを送信できます。 ジョブの実行に使用されるコマンドの例を次に示します。

    # Set the HDFS (WASB) location of example data.
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storaging data temporarily.
    source <- "/tmp/AirOnTimeCSV2012"
    dir.create(source)

    # Download data to the tmp folder.
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))

    # Set directory in bigDataDirRoot to load the data.
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory.
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input.
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (WASB) file system.
    hdfsFS <- RxHdfsFileSystem()

    # Create info list for the airline data.
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names.
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS.
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in local system.
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use.
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

    # Define the Spark compute context.
    mySparkCluster <- RxSpark()

    # Set the compute context.
    rxSetComputeContext(mySparkCluster)

    # Run a logistic regression.
    system.time(
        modelSpark <- rxLogit(formula, data = airOnTimeData)
    )

    # Display a summary.
    summary(modelSpark)

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-cluster-edge-node"></a>クラスターのエッジ ノードに接続する

このセクションでは、SSH を使って ML サービス HDInsight クラスターのエッジ ノードに接続する方法について説明します。 SSH の使用方法については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページをご覧ください。

ML サービス クラスターのエッジ ノードに接続するための SSH コマンドは次のとおりです。

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

クラスターの SSH コマンドを入手するには、Azure Portal でクラスター名をクリックし、**[SSH + Cluster login]\(SSH + クラスター ログイン\)** をクリックして、**[ホスト名]** でエッジ ノードを選択します。 これにより、エッジ ノードの SSH エンドポイント情報が表示されます。

![エッジ ノードの SSH エンドポイントの画像](./media/r-server-get-started/sshendpoint.png)

SSH ユーザー アカウントを保護するためにパスワードを使用している場合は、パスワードの入力を求められます。 公開キーを使用している場合、 `-i` パラメーターを使用して、対応する秘密キーを指定することが必要な場合があります。 例: 

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

接続すると、次のようなプロンプトが表示されます。

    sshuser@ed00-myrclu:~$

<a name="use-r-console"></a>
## <a name="use-the-r-console"></a>R コンソールを使用する

1. SSH セッションから、次のコマンドを使用して R コンソールを起動します。  

        R

2. 出力画面に ML Server のバージョンが、その他の情報と共に表示されます。
    
3. `>` プロンプトから R コードを入力できます。 HDInsight の ML サービスには、Hadoop との対話を容易にし、分散計算を実行するためのパッケージが含まれています。 たとえば、次のコマンドを使用して、HDInsight クラスターの既定のファイル システムのルートを表示します。

        rxHadoopListFiles("/")

4. WASB スタイルのアドレス指定も使用できます。

        rxHadoopListFiles("wasb:///")

5. R コンソールを終了するには、次のコマンドを使用します。

        quit()

## <a name="automated-cluster-creation"></a>自動クラスター作成

HDInsight の ML サービス クラスターの作成操作は、SDK および PowerShell を使用して自動化できます。

<!---* To create an ML Server cluster using an Azure Resource Management template, see [Deploy an R Server for HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/).--->
* .NET SDK テンプレートを使用して ML サービス クラスターを作成するには、「[.NET SDK を使用した HDInsight の Linux ベースのクラスターの作成](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)」を参照してください。
* PowerShell を使って ML サービス クラスターを作成する方法については、[Azure PowerShell を使用した HDInsight クラスターの作成](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)に関する記事を参照してください。

## <a name="delete-the-cluster"></a>クラスターを削除する

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>トラブルシューティング

HDInsight クラスターの作成で問題が発生した場合は、「[アクセス制御の要件](../hdinsight-hadoop-create-linux-clusters-portal.md)」を参照してください。

## <a name="next-steps"></a>次の手順

この記事では、Azure HDInsight で新しい ML サービス クラスターを作成する方法のほか、SSH セッションからの R コンソールの基本的な使い方について説明しました。 以下の記事では、HDInsight 上の ML サービスを管理および使用する他の方法について説明します。

* [R Tools for Visual Studio からのジョブの送信](r-server-submit-jobs-r-tools-vs.md)
* [HDInsight 上の ML サービス クラスターの管理](r-server-hdinsight-manage.md)
* [HDInsight 上の ML サービス クラスターの運用化](r-server-operationalize.md)
* [HDInsight 上の ML サービス クラスター向けのコンピューティング コンテキスト オプション](r-server-compute-contexts.md)
* [HDInsight 上の ML サービス クラスター向けの Azure Storage オプション](r-server-storage.md)
