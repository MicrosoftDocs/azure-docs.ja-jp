---
title: チュートリアル:Azure HDInsight の Spark コンピューティング コンテキストで R を使用する
description: チュートリアル - Azure HDInsight Machine Learning service クラスター上で R と Spark の使用を開始します。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: f072b6905881da7b7854b0e51d690dbbd40dffb5
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227437"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>チュートリアル:Azure HDInsight の Spark コンピューティング コンテキストで R を使用する

このチュートリアルでは、Azure HDInsight Machine Learning service クラスター上で実行される Apache Spark 内で R 関数を使用する方法をステップバイステップで説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ローカル ストレージにサンプル データをダウンロードする
> * 既定のストレージにデータをコピーする
> * データセットをセットアップする
> * データソースを作成する
> * Spark のコンピューティング コンテキストを作成する
> * 線形モデルを適合させる
> * 複合 XDF ファイルを使用する
> * XDF から CSV に変換する

## <a name="prerequisites"></a>前提条件

* Azure HDInsight Machine Learning service クラスター。 [Azure portal を使用した Apache Hadoop クラスターの作成](../hdinsight-hadoop-create-linux-clusters-portal.md)に関するページに移動し、 **[クラスターの種類]** に対して **[ML Services]** を選択します。

## <a name="connect-to-rstudio-server"></a>RStudio Server に接続する

RStudio Server は、クラスターのエッジ ノード上で実行されます。 次のサイトに移動します (URL 内の *CLUSTERNAME* は、作成した HDInsight Machine Learning service クラスターの名前です)。

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

最初にサインインするときは、認証が 2 回実行されます。 最初の認証プロンプトでは、クラスター管理者のユーザー名とパスワードを入力します (既定値は *admin* です)。 2 番目の認証プロンプトでは、SSH のユーザー名とパスワードを入力します (既定値は *sshuser* です)。 以降のサインインでは、SSH 資格情報のみが必要になります。

## <a name="download-the-sample-data-to-local-storage"></a>ローカル ストレージにサンプル データをダウンロードする

*Airline 2012 On-Time Data Set* は、12 個のコンマ区切りファイルで構成されており、各ファイルには 2012 年の米国内の全民間飛行便の発着情報が入っています。 このデータセットは規模が大きく、含まれる観測値は 600 万を超えています。

1. いくつかの環境変数を初期化します。 RStudio Server コンソールで以下のコードを入力します。

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

1. 右側のウィンドウで **[環境]** タブを選択します。変数は **[値]** の下に表示されます。

    ![RStudio](./media/ml-services-tutorial-spark-compute/rstudio.png)

1.  ローカル ディレクトリを作成し、サンプル データをダウンロードします。 RStudio に次のコードを入力します。

    ```R
    # Create local directory
    dir.create(localDir)
    
    # Download data to the tmp folder(local)
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(localDir, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(localDir, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(localDir, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(localDir, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(localDir, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(localDir, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(localDir, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(localDir, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(localDir, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(localDir, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(localDir, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(localDir, "airOT201212.csv"))
    ```

    ダウンロードは約 9.5 分で完了します。

## <a name="copy-the-data-to-default-storage"></a>既定のストレージにデータをコピーする

Hadoop 分散ファイル システム (HDFS) の場所は、`airDataDir` 変数で指定します。 RStudio に次のコードを入力します。

```R
# Set directory in bigDataDirRoot to load the data into
airDataDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

# Create directory (default storage)
rxHadoopMakeDir(airDataDir)

# Copy data from local storage to default storage
rxHadoopCopyFromLocal(localDir, bigDataDirRoot)
    
# Optional. Verify files
rxHadoopListFiles(airDataDir)
```

この手順は約 10 秒で完了します。

## <a name="set-up-a-dataset"></a>データセットをセットアップする

1. 既定値を使用するファイル システム オブジェクトを作成します。 RStudio に次のコードを入力します。

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

1. 元の CSV ファイルにはかなり扱いにくい変数名が付いているので、管理しやすくするために *colInfo* という一覧を用意しました。 RStudio に次のコードを入力します。

    ```R
    airlineColInfo <- list(
         MONTH = list(newName = "Month", type = "integer"),
        DAY_OF_WEEK = list(newName = "DayOfWeek", type = "factor",
            levels = as.character(1:7),
            newLevels = c("Mon", "Tues", "Wed", "Thur", "Fri", "Sat",
                          "Sun")),
        UNIQUE_CARRIER = list(newName = "UniqueCarrier", type =
                                "factor"),
        ORIGIN = list(newName = "Origin", type = "factor"),
        DEST = list(newName = "Dest", type = "factor"),
        CRS_DEP_TIME = list(newName = "CRSDepTime", type = "integer"),
        DEP_TIME = list(newName = "DepTime", type = "integer"),
        DEP_DELAY = list(newName = "DepDelay", type = "integer"),
        DEP_DELAY_NEW = list(newName = "DepDelayMinutes", type =
                             "integer"),
        DEP_DEL15 = list(newName = "DepDel15", type = "logical"),
        DEP_DELAY_GROUP = list(newName = "DepDelayGroups", type =
                               "factor",
           levels = as.character(-2:12),
           newLevels = c("< -15", "-15 to -1","0 to 14", "15 to 29",
                         "30 to 44", "45 to 59", "60 to 74",
                         "75 to 89", "90 to 104", "105 to 119",
                         "120 to 134", "135 to 149", "150 to 164",
                         "165 to 179", ">= 180")),
        ARR_DELAY = list(newName = "ArrDelay", type = "integer"),
        ARR_DELAY_NEW = list(newName = "ArrDelayMinutes", type =
                             "integer"),  
        ARR_DEL15 = list(newName = "ArrDel15", type = "logical"),
        AIR_TIME = list(newName = "AirTime", type =  "integer"),
        DISTANCE = list(newName = "Distance", type = "integer"),
        DISTANCE_GROUP = list(newName = "DistanceGroup", type =
                             "factor",
         levels = as.character(1:11),
         newLevels = c("< 250", "250-499", "500-749", "750-999",
             "1000-1249", "1250-1499", "1500-1749", "1750-1999",
             "2000-2249", "2250-2499", ">= 2500")))
    
    varNames <- names(airlineColInfo)
    ```

## <a name="create-data-sources"></a>データソースを作成する

Spark コンピューティング コンテキストでは、次の関数を使用してデータ ソースを作成できます。

|Function | 説明 |
|---------|-------------|
|`RxTextData` | コンマ区切りのテキスト データ ソース。 |
|`RxXdfData` | XDF データ ファイル形式のデータ。 RevoScaleR では、1 つのファイルではなく、ファイルの複合セットにデータを格納するために、XDF ファイル形式が Hadoop 用に変更されます。 |
|`RxHiveData` | Hive データ ソース オブジェクトを生成します。|
|`RxParquetData` | Parquet データ ソース オブジェクトを生成します。|
|`RxOrcData` | Orc データ ソース オブジェクトを生成します。|

HDFS にコピーしたファイルを使用して、[RxTextData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata) オブジェクトを作成します。 RStudio に次のコードを入力します。

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-a-compute-context-for-spark"></a>Spark のコンピューティング コンテキストを作成する

ワーカー ノード上でデータを読み込んで分析を実行するには、スクリプトのコンピューティング コンテキストを [RxSpark](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxspark) に設定します。 このコンテキストでは、R 関数はすべてのワーカー ノード全体でワークロードを自動的に分散します。ジョブまたはキューを管理するための組み込み要件はありません。 Spark コンピューティング コンテキストは、`RxSpark` または `rxSparkConnect()` を通じて設定および作成され、`rxSparkDisconnect()` を使用してローカル コンピューティング コンテキストに戻ります。 RStudio に次のコードを入力します。

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>線形モデルを適合させる

1. [rxLinMod](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) 関数を使用して、`airDS` データ ソースを使用する線形モデルを適合させます。 RStudio に次のコードを入力します。

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    この手順は、2 ～ 3 分で完了します。

1. 結果を表示します。 RStudio に次のコードを入力します。

    ```R
    summary(delayArr)
    ```

    次のような結果が表示されます。

    ```output
    Call:
    rxLinMod(formula = ArrDelay ~ DayOfWeek, data = airDS, cube = TRUE)
    
    Cube Linear Regression Results for: ArrDelay ~ DayOfWeek
    Data: airDataXdf (RxXdfData Data Source)
    File name: /tutorial/data/AirOnTimeCSV2012
    Dependent variable(s): ArrDelay
    Total independent variables: 7 
    Number of valid observations: 6005381
    Number of missing observations: 91381 
     
    Coefficients:
                   Estimate Std. Error t value Pr(>|t|)     | Counts
    DayOfWeek=Mon   3.54210    0.03736   94.80 2.22e-16 *** | 901592
    DayOfWeek=Tues  1.80696    0.03835   47.12 2.22e-16 *** | 855805
    DayOfWeek=Wed   2.19424    0.03807   57.64 2.22e-16 *** | 868505
    DayOfWeek=Thur  4.65502    0.03757  123.90 2.22e-16 *** | 891674
    DayOfWeek=Fri   5.64402    0.03747  150.62 2.22e-16 *** | 896495
    DayOfWeek=Sat   0.91008    0.04144   21.96 2.22e-16 *** | 732944
    DayOfWeek=Sun   2.82780    0.03829   73.84 2.22e-16 *** | 858366
    ---
    Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
    Residual standard error: 35.48 on 6005374 degrees of freedom
    Multiple R-squared: 0.001827 (as if intercept included)
    Adjusted R-squared: 0.001826 
    F-statistic:  1832 on 6 and 6005374 DF,  p-value: < 2.2e-16 
    Condition number: 1 
    ```

    この結果は、指定したディレクトリ内のすべての CSV ファイルを使用して、600 万件すべての観察データが処理されたことを示しています。 `cube = TRUE` を指定したため、曜日ごとに推定係数 (切片ではありません) があります。

## <a name="use-composite-xdf-files"></a>複合 XDF ファイルを使用する

ご覧のように、Hadoop 上で R を使用して CSV ファイルを直接分析できます。 ただし、より効率的な形式でデータを格納すると、分析をより迅速に実行できます。 R XDF ファイル形式は効率的ですが、個々のファイルが単一の HDFS ブロック内に保持されるように、HDFS 用に多少修正されています (HDFS ブロック サイズはインストールごとに異なりますが、通常は 64 MB か 128 MB です)。 

Hadoop 上で [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) を使用して一連の複合 XDF ファイルを作成する場合は、inData として `AirDS` のような `RxTextData` データ ソースを指定し、outFile 引数として fileSystem が HDFS ファイル システムに設定された `RxXdfData` データ ソースを指定します。 その後、`RxXdfData` オブジェクトは、後続の R 分析でデータ引数として使用できます。

1. `RxXdfData` オブジェクトを定義します。 RStudio に次のコードを入力します。

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. 250,000 行のブロック サイズを設定し、すべてのデータを読み取るように指定します。 RStudio に次のコードを入力します。

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. `rxImport` を使用してデータをインポートします。 RStudio に次のコードを入力します。

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    この手順は、数分で完了します。

1. より高速の新しいデータ ソースを使用して、同じ線形モデルを再予測します。 RStudio に次のコードを入力します。

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    この手順は、1 分かからずに完了します。

1. 結果を表示します。 結果は CSV ファイルの場合と同じになるはずです。 RStudio に次のコードを入力します。

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>XDF から CSV に変換する

### <a name="in-a-spark-context"></a>Spark コンテキストで

分析実行時の効率化のために CSV ファイルを XDF ファイル形式に変換したが、データを元の CSV に戻したい場合は、[rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) を使用します。

CSV ファイルのフォルダーを作成するには、まず、ファイル引数としてディレクトリ名を使用して `RxTextData` オブジェクトを作成します。 このオブジェクトは、CSV ファイルを作成するフォルダーを表します。 `rxDataStep` を実行すると、このディレクトリが作成されます。 次に、`rxDataStep` の `outFile` 引数で、この `RxTextData` オブジェクトを指し示します。 作成された各 CSV には、ディレクトリ名に基づいて名前が付けられ、その後に番号が付けられます。

ロジスティック回帰と予測を実行した後、`airDataXdf` 複合 XDF から HDFS に CSV ファイルのフォルダーを書き出して、新しい CSV ファイルに予測値と残差が含まれるようにしたいとします。 RStudio に次のコードを入力します。

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

この手順は、約 2.5 分で完了します。

`rxDataStep` によって入力複合 XDF ファイル内の XDFD ファイルごとに 1 つの CSV が書き出されました。 これは、コンピューティング コンテキストが `RxSpark` に設定されている場合に、複合 XDF ファイルから HDFS に CSV ファイルを書き込む際の既定の動作です。

### <a name="in-a-local-context"></a>ローカル コンテキストで

一方、分析の実行を終えたらコンピューティング コンテキストを `local` に戻し、CSV ファイルを HDFS に書き出すときにもう少し細かい制御ができる 2 つの引数 (`createFileSet` と `rowsPerOutFile`) を `RxTextData` 内で利用することもできます。 `createFileSet` を `TRUE` に設定すると、CSV ファイルのフォルダーは指定したディレクトリに作成されます。 `createFileSet` を `FALSE` に設定すると、単一の CSV ファイルが作成されます。 2 つ目の引数の `rowsPerOutFile` に整数を設定すると、`createFileSet` が `TRUE` である場合に各 CSV ファイルに書き込む行数を指定できます。

RStudio に次のコードを入力します。

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

この手順は、約 10 分で完了します。

`RxSpark` コンピューティング コンテキストを使用している場合、`createFileSet` は既定で `TRUE` になり、`rowsPerOutFile` は効果がありません。 そのため、単一の CSV を作成したり、ファイルごとの行数をカスタマイズしたりする場合は、`local` コンピューティング コンテキスト内で `rxDataStep` を実行します (その場合でもデータは HDFS 内に置くことができます)。

## <a name="final-steps"></a>最終手順

1. データをクリーンアップします。 RStudio に次のコードを入力します。

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. リモートの Spark アプリケーションを停止します。 RStudio に次のコードを入力します。

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. R セッションを終了します。 RStudio に次のコードを入力します。

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

チュートリアルを完了したら、必要に応じてクラスターを削除できます。 HDInsight を使用すると、データは Azure Storage 内に格納されます。そのため、クラスターは、使用していなければ、削除しても問題ありません。 また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。

クラスターを削除するには、「[Delete an HDInsight cluster by using your browser, PowerShell, or the Azure CLI (ブラウザー、PowerShell、または Azure CLI を使用して HDInsight クラスターを削除する)](../hdinsight-delete-cluster.md)」を参照してください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、HDInsight Machine Learning service クラスター上で実行されている Apache Spark 内で R 関数を使用する方法を説明しました。 詳細については、次の記事を参照してください。

* [Azure HDInsight Machine Learning service クラスターのコンピューティング コンテキスト オプション](r-server-compute-contexts.md)
* [Hadoop 上の Spark 用の R 関数](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)
