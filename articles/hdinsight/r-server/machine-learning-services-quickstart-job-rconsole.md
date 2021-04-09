---
title: 'クイックスタート: ML サービスと R コンソールでの R スクリプト - Azure HDInsight'
description: このクイックスタートでは、R コンソールを使用して Azure HDInsight で ML サービス クラスターに対して R スクリプトを実行します。
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/19/2019
ms.custom: mvc
ms.openlocfilehash: eac6fd14acfe12a0f505419a229bb78e423706d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98935734"
---
# <a name="quickstart-execute-an-r-script-on-an-ml-services-cluster-in-azure-hdinsight-using-r-console"></a>クイックスタート: R コンソールを使用して Azure HDInsight で ML サービス クラスターに対して R スクリプトを実行する

Azure HDInsight 上の ML Services により、R スクリプトで Apache Spark と Apache Hadoop MapReduce を使用して、分散計算を実行できます。 ML Services では、コンピューティング コンテキストを設定することによって呼び出しの実行方法が制御されます。 クラスターのエッジ ノードは、クラスターへの接続と R スクリプトの実行に便利な場所です。 エッジ ノードでは、エッジ ノード サーバーのコア間で、RevoScaleR の並列化された分散関数を実行できます。 また、RevoScaleR の Hadoop Map Reduce または Apache Spark コンピューティング コンテキストを使用して、クラスターのノード間でこれらの関数を実行することもできます。

このクイックスタートでは、R コンソールで、分散 R 計算に Spark を使用する方法を示す R スクリプトを実行する方法について説明します。 エッジ ノード上のローカルで計算を実行し、再び HDInsight クラスター内のノードに分散するように計算コンテキストを定義します。

## <a name="prerequisites"></a>前提条件

* HDInsight 上の ML Services クラスター。 [Azure portal を使用した Apache Hadoop クラスターの作成](../hdinsight-hadoop-create-linux-clusters-portal.md)に関するページを参照し、**[クラスターの種類]** で **[ML Services]** を選択してください。

* SSH クライアント 詳細については、[SSH を使用して HDInsight (Apache Hadoop) に接続する方法](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。


## <a name="connect-to-r-console"></a>R コンソールに接続する

1. SSH を使用して ML Services HDInsight クラスターのエッジ ノードに接続します。 次のコマンドを編集して `CLUSTERNAME` をクラスターの名前に置き換えてから、そのコマンドを入力します。

    ```cmd
    ssh sshuser@CLUSTERNAME-ed-ssh.azurehdinsight.net
    ```

1. SSH セッションから、次のコマンドを使用して R コンソールを起動します。

    ```
    R
    ```

    出力画面に ML Server のバージョンが、その他の情報と共に表示されます。


## <a name="use-a-compute-context"></a>コンピューティング コンテキストを使用する

1. `>` プロンプトから R コードを入力できます。 次のコードを使用してサンプル データを HDInsight の既定のストレージに読み込みます。

    ```R
    # Set the HDFS (WASB) location of example data
     bigDataDirRoot <- "/example/data"
    
     # create a local folder for storing data temporarily
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
    ```

    この手順は、完了するまでに約 10 分かかります。

1. データ情報をいくつか作成し、データ ソースを 2 つ定義します。 R コンソールに次のコードを入力します。

    ```R
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
    ```

1. **ローカル** のコンピューティング テキストを使用して、データに対してロジスティック回帰を実行します。 R コンソールに次のコードを入力します。

    ```R
    # Set a local compute context
     rxSetComputeContext("local")
    
     # Run a logistic regression
     system.time(
        modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
     )
    
     # Display a summary
     summary(modelLocal)
    ```

    この計算は、約 7 分で完了します。 次のスニペットのような行で終了する出力が表示されます。

    ```output
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
    ```

1. **Spark** コンテキストを使用して、同じロジスティック回帰を実行します。 Spark コンテキストを使うと、HDInsight クラスターのすべてのワーカー ノードに処理が分散されます。 R コンソールに次のコードを入力します。

    ```R
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
    ```

    この計算は、約 5 分で完了します。

1. R コンソールを終了するには、次のコマンドを使用します。

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートを完了したら、必要に応じてクラスターを削除できます。 HDInsight を使用すると、データは Azure Storage に格納されるため、クラスターは、使用されていない場合に安全に削除できます。 また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。

クラスターを削除するには、「[ブラウザー、PowerShell、または Azure CLI を使用して HDInsight クラスターを削除する](../hdinsight-delete-cluster.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、R コンソールで、分散 R 計算に Spark を使用する方法を示す R スクリプトを実行する方法について学習しました。  次の記事に進み、HDInsight クラスターやエッジ ノードの複数のコア間で実行を並列化するかどうかとその方法を指定する際に利用できるオプションについて学習します。

> [!div class="nextstepaction"]
>[HDInsight 上の ML Services 向けのコンピューティング コンテキスト オプション](./r-server-compute-contexts.md)