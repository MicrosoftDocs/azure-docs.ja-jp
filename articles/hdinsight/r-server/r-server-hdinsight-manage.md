---
title: HDInsight で ML Services クラスターを管理する - Azure
description: Azure HDInsight で ML Services クラスターを管理する方法について説明します。
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 6c57dff2e0f0c1edb887ddd8f0e5ca206ba8b912
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58110394"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>Azure HDInsight で ML Services クラスターを管理する

この記事では、Azure HDInsight で既存の ML Services クラスターを管理して、複数の同時ユーザーの追加、ML Services クラスターへのリモート接続、コンピューティング コンテキストの変更などのタスクを実行する方法について説明します。

## <a name="prerequisites"></a>前提条件

* **HDInsight 上の ML サービス クラスター**: 手順については、「[HDInsight の ML サービスの概要](r-server-get-started.md)」を参照してください。

* **Secure Shell (SSH) クライアント**: SSH クライアントは、HDInsight クラスターにリモート接続し、そのクラスター上でコマンドを直接実行するために使用されます。 詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページをご覧ください。


## <a name="enable-multiple-concurrent-users"></a>複数の同時ユーザーを有効にする

HDInsight で ML Services クラスターに対して複数の同時ユーザーを有効にするには、RStudio Community バージョンが実行されているエッジ ノードのユーザーを追加します。 HDInsight クラスターを作成するときに、2 人のユーザー (HTTP ユーザーと SSH ユーザー) を指定する必要があります。

![同時ユーザー 1](./media/r-server-hdinsight-manage/concurrent-users-1.png)

- **クラスター ログイン ユーザー名**: 作成した HDInsight クラスターを保護するために使用される、HDInsight ゲートウェイを介した認証用の HTTP ユーザー。 この HTTP ユーザーは、Apache Ambari UI、Apache Hadoop YARN UI、およびその他の UI コンポーネントにアクセスするために使用されます。
- **Secure Shell (SSH) ユーザー名**: Secure Shell を介してクラスターにアクセスする SSH ユーザー。 このユーザーは、ヘッド ノード、ワーカー ノード、エッジ ノードすべてに対応する Linux システムのユーザーです。 そのため、Secure Shell を使用して、リモート クラスター内の任意のノードにアクセスできます。

HDInsight の ML Services クラスターで使用されている R Studio Server Community バージョンでは、サインイン メカニズムとして Linux ユーザー名とパスワードのみを受け付けます。 トークンを渡すことはサポートされていません。 そのため、ML Services クラスターで R Studio にアクセスするのが初めての場合は、2 回サインインする必要があります。

- 最初に、HDInsight ゲートウェイから HTTP ユーザー資格情報を使用してサインインします。 

- 次に、SSH ユーザー資格情報を使用して RStudio にサインインします。
  
現時点では、HDInsight クラスターのプロビジョニング時に作成できるのは、1 つの SSH ユーザー アカウントのみです。 そのため、複数のユーザーが HDInsight クラスターの ML Services クラスターにアクセスできるようにするには、Linux システムで追加のユーザーを作成する必要があります。

RStudio はクラスターのエッジ ノードで実行されているため、ここでいくつかの手順が必要になります。

1. 既存の SSH ユーザーを使用してエッジ ノードにサインインする
2. エッジ ノードで Linux ユーザーをさらに追加する
3. ユーザーが作成された RStudio Community バージョンを使用する

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>手順 1:作成された SSH ユーザーを使用してエッジ ノードにサインインする

「[SSH を使用して HDInsight (Apache Hadoop) に接続する](../hdinsight-hadoop-linux-use-ssh-unix.md)」の手順に従って、エッジ ノードにアクセスします。 HDInsight での ML Services クラスターのエッジ ノード アドレスは `CLUSTERNAME-ed-ssh.azurehdinsight.net` です。

### <a name="step-2-add-more-linux-users-in-edge-node"></a>手順 2:エッジ ノードで Linux ユーザーをさらに追加する

エッジ ノードにユーザーを追加するには、次のコマンドを実行します。

    # Add a user 
    sudo useradd <yournewusername> -m

    # Set password for the new user
    sudo passwd <yournewusername>

次のスクリーンショットは出力を示しています。

![同時ユーザー 3](./media/r-server-hdinsight-manage/concurrent-users-2.png)

"Current Kerberos password:" というメッセージが表示されたら、単に **Enter** キーを押して無視します。 `useradd` コマンドの `-m` オプションは、システムによってユーザーのホーム フォルダーが作成されることを示します。このフォルダーは、RStudio Community バージョンに必要です。

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>手順 3:ユーザーが作成された RStudio Community バージョンを使用する

https://CLUSTERNAME.azurehdinsight.net/rstudio/ から RStudio にアクセスします。 クラスターの作成後、初めてログインする場合は、クラスター管理者の資格情報を入力してから、作成した SSH ユーザーの資格情報を入力します。 初めてのログインではない場合は、作成した SSH ユーザーの資格情報だけを入力します。

同時に、別のブラウザー ウィンドウから元の資格情報 (既定では *sshuser*) を使用してサインインすることもできます。

また、新しく追加されたユーザーには Linux システムにおける root 権限はありませんが、リモートの HDFS および WASB ストレージ内のすべてのファイルに対して同じアクセス権があることにも注意してください。

## <a name="connect-remotely-to-microsoft-ml-services"></a>Microsoft ML Services にリモート接続する

ご自身のデスクトップで実行している ML Client のリモート インスタンスから HDInsight Spark コンピューティング コンテキストへのアクセスを設定できます。 それを行うには、デスクトップ上で RxSpark コンピューティング コンテキストを定義するときにオプション (hdfsShareDir、shareDir、sshUsername、sshHostname、sshSwitches、および sshProfileScript) を指定する必要があります。例: 

    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- '<clustername>-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- '<sshuser>'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )

詳細については、[Apache Spark コンピューティング コンテキストで RevoScaleR を使用する方法](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios)に関するページにある Apache Hadoop クライアントとしての Microsoft Machine Learning Server の使用に関するセクションを参照してください

## <a name="use-a-compute-context"></a>コンピューティング コンテキストを使用する

コンピューティング コンテキストを使うと、計算をエッジ ノードでローカルに実行するか、HDInsight クラスターのノード全体に分散するかを制御することができます。

1. RStudio Server または R コンソール (SSH セッション) から次のコードを実行し、HDInsight の既定のストレージにサンプル データを読み込みます。

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"

        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)

        # Download data to the tmp folder
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

        # Set directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

        # Make the directory
        rxHadoopMakeDir(inputDir)

        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. 次に、データ情報をいくつか作成し、データ ソースを 2 つ定義します。

        # Define the HDFS (WASB) file system
        hdfsFS <- RxHdfsFileSystem()

        # Create info list for the airline data
        airlineColInfo <- list(
             DAY_OF_WEEK = list(type = "factor"),
             ORIGIN = list(type = "factor"),
             DEST = list(type = "factor"),
             DEP_TIME = list(type = "integer"),
             ARR_DEL15 = list(type = "logical"))

        # get all the column names
        varNames <- names(airlineColInfo)

        # Define the text data source in hdfs
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

        # Define the text data source in local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

        # formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

3. ローカルのコンピューティング テキストを使用して、データに対してロジスティック回帰を実行します。

        # Set a local compute context
        rxSetComputeContext("local")

        # Run a logistic regression
        system.time(
           modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )

        # Display a summary
        summary(modelLocal)

    次のスニペットのような行で終了する出力が表示されます。

        Data: airOnTimeDataLocal (RxTextData Data Source)
        File name: /tmp/AirOnTimeCSV2012
        Dependent variable(s): ARR_DEL15
        Total independent variables: 634 (Including number dropped: 3)
        Number of valid observations: 6005381
        Number of missing observations: 91381
        -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)

        Coefficients:
                         Estimate Std. Error z value Pr(>|z|)
         (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
         ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
         ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
         ......
         DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
         DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
         DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
         DEST=BPT         Dropped    Dropped Dropped  Dropped

         ---

         Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

         Condition number of final variance-covariance matrix: 11904202
         Number of iterations: 7

4. Spark コンテキストを使用して、同じロジスティック回帰を実行します。 Spark コンテキストを使うと、HDInsight クラスターのすべてのワーカー ノードに処理が分散されます。

        # Define the Spark compute context
        mySparkCluster <- RxSpark()

        # Set the compute context
        rxSetComputeContext(mySparkCluster)

        # Run a logistic regression
        system.time(  
           modelSpark <- rxLogit(formula, data = airOnTimeData)
        )

        # Display a summary
        summary(modelSpark)


## <a name="distribute-r-code-to-multiple-nodes"></a>複数のノードに R コードを分散する

HDInsight の ML Services では、既存の R コードを取得し、`rxExec` を使ってクラスター内の複数のノード全体で実行できます。 この関数は、パラメーター スイープまたはシミュレーションを行うときに便利です。 次のコードは、`rxExec` の使用方法の例です。

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

まだ Spark のコンテキストを使っている場合は、コード `(Sys.info()["nodename"])` が実行されているワーカー ノードのノード名の値が返されます。 たとえば、4 ノードのクラスターでは次のような出力が表示されます。

    $rxElem1
        nodename
    "wn3-mymlser"

    $rxElem2
        nodename
    "wn0-mymlser"

    $rxElem3
        nodename
    "wn3-mymlser"

    $rxElem4
        nodename
    "wn3-mymlser"

## <a name="access-data-in-apache-hive-and-parquet"></a>Apache Hive および Parquet 内のデータにアクセスする

HDInsight ML Services を使うと、Hive および Parquet 内のデータに直接アクセスし、Spark のコンピューティング コンテキスト内の ScaleR 関数で使うことができます。 RxHiveData と RxParquetData という新しい ScaleR データ ソース関数で、以下の機能を使用できます。これらの関数は、ローカルのデータを ScaleR で分析できるように、Spark SQL を使用して Spark データフレームに直接読み込みます。

次のコードは、これらの新しい関数の使用方法に関するサンプル コードを示します。

    #Create a Spark compute context:
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model:
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model:
    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)

    #Check on Spark data objects, cleanup, and close the Spark session:
    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)


これらの新関数の使用方法の詳細については、ML Services で `?RxHivedata` コマンドおよび `?RxParquetData` コマンドを使うと表示されるオンライン ヘルプをご覧ください。  

## <a name="install-additional-r-packages-on-the-cluster"></a>追加の R パッケージをクラスターにインストールする

### <a name="to-install-r-packages-on-the-edge-node"></a>R パッケージをエッジ ノードにインストールするには

エッジ ノードに追加の R パッケージをインストールする場合、SSH 経由でエッジ ノードに接続すれば、R コンソールから `install.packages()` を直接使用できます。 

### <a name="to-install-r-packages-on-the-worker-node"></a>R パッケージを worker ノードにインストールするには

クラスターの worker ノードに R パッケージをインストールするには、スクリプト操作を使用する必要があります。 スクリプト アクションは、HDInsight クラスターの構成を変更するか、追加のソフトウェア (追加の R パッケージなど) をインストールするために使用する Bash スクリプトです。 

> [!IMPORTANT]  
> スクリプト アクションを使用した追加の R パッケージのインストールは、クラスターが作成された後にのみ使用できます。 スクリプトは完全に構成されている ML Services に依存するため、クラスターの作成中はこの手順を実行しないでください。

1. [スクリプト操作を使用したクラスターのカスタマイズ](../hdinsight-hadoop-customize-cluster-linux.md)に関するページの手順に従います。

3. **[スクリプト操作を追加]** で、次の情報を指定します。

   * **[スクリプトの種類]** で、**[カスタム]** を選択します。

   * **[名前]** で、スクリプト操作の名前を指定します。

     * **[バッシュ スクリプト URI]** で、「`https://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`」と入力します。 これは、worker ノードに追加の R パッケージをインストールするスクリプトです

   * **worker** についてのみチェック ボックスをオンにします。

   * **パラメーター**: インストールされる R パッケージ。 たとえば、`bitops stringr arules` のように指定します。

   * **このスクリプト操作を保持する**ためのチェック ボックスをオンにします。  

   > [!NOTE]
   > 1. 既定では、R パッケージはすべて、インストールされている ML Server のバージョンと一貫性のある Microsoft MRAN リポジトリのスナップショットからインストールされます。 より新しいバージョンのパッケージをインストールする場合は、非互換性の問題が発生するリスクが多少あります。 ただし、この種類のインストールは、パッケージ リスト (たとえば `useCRAN bitops, stringr, arules`) の最初の要素として `useCRAN` を指定することにより可能になります。  
   > 2. 一部の R パッケージには、Linux システムの他のライブラリが必要です。 便宜上、HDInsight ML Services には上位 100 の最も一般的な R パッケージで必要な依存関係が事前インストールされています。 ただし、インストールする R パッケージでこれら以外のライブラリが必要な場合、ここで使用する基本スクリプトをダウンロードし、システム ライブラリをインストールする手順を追加します。 その後、変更後のスクリプトを Azure ストレージ内のパブリック BLOB コンテナーにアップロードし、変更後のスクリプトを使用してパッケージをインストールする必要があります。
   >    スクリプト アクションを開発する方法の詳細については、 [スクリプト アクション開発](../hdinsight-hadoop-script-actions-linux.md)に関するページを参照してください。  
   >
   >

   ![スクリプト アクションの追加](./media/r-server-hdinsight-manage/submitscriptaction.png)

4. **[作成]** を選択してスクリプトを実行します。 スクリプトが完了すると、R パッケージをすべてのワーカー ノードで使用できるようになります。

## <a name="next-steps"></a>次の手順

* [HDInsight 上の ML Services クラスターの運用化](r-server-operationalize.md)
* [HDInsight 上の ML サービス クラスター向けのコンピューティング コンテキスト オプション](r-server-compute-contexts.md)
* [HDInsight 上の ML Services クラスター向けの Azure Storage オプション](r-server-storage.md)
