---
title: "HDInsight の R Server 概要 | Microsoft Docs"
description: "R Server を含む HDInsight クラスターで Apache Spark を作成し、クラスターで R スクリプトを送信する方法について説明します。"
services: HDInsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: f816a6972c0e80c6a7063705917ecf18debc75f6
ms.lasthandoff: 03/29/2017


---
# <a name="get-started-using-r-server-on-hdinsight"></a>HDInsight での R Server の概要
HDInsight には、HDInsight クラスターに R Server を統合するオプションが含まれています。 これにより、R スクリプトで Spark と MapReduce を使用して、分散計算を実行できます。 このドキュメントでは、HDInsight クラスターで R Server を作成し、分散 R 計算の Spark の使用方法を示す R スクリプトを実行する方法を学習します。

## <a name="prerequisites"></a>前提条件
* **Azure サブスクリプション**: このチュートリアルを開始する前に、Azure サブスクリプションが必要です。 詳細については、[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* **Secure Shell (SSH) クライアント**: SSH クライアントを使用して、HDInsight クラスターにリモート接続し、クラスター上でコマンドを直接実行します。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

  * **SSH キー (省略可能)**: クラスターへの接続に使用する SSH アカウントは、パスワードまたはパブリック キーを使用してセキュリティで保護できます。 パスワードを使用する方が簡単で、公開/秘密のキー ペアを作成しなくても使い始めることができます。 ただし、キーを使用した方が安全です。

      このドキュメントの手順では、パスワードを使用していることを想定しています。

### <a name="access-control-requirements"></a>アクセス制御の要件
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-the-cluster"></a>クラスターを作成する
> [!NOTE]
> このドキュメントの手順では、基本的な構成情報を使用して HDInsight クラスターで R サーバーを作成する方法について説明します。 その他のクラスター構成設定 (追加ストレージの追加、Azure 仮想ネットワークの使用、Hive 用のメタストアの使用など) については、 [Linux ベースの HDInsight クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)に関するページを参照してください。 Azure Resource Manager テンプレートを使用して R Server を作成するには、「[Deploy an R-server HDInsight cluster (R Server HDInsight クラスターのデプロイ)](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/)」を参照してください。
>
>

1. [Azure ポータル](https://portal.azure.com)にサインインします。

2. **[新規]**、**[インテリジェンス + 分析]**、**[HDInsight]** の順に選びます。

    ![新しいクラスターの作成の画像](./media/hdinsight-getting-started-with-r/newcluster.png)

3. **[簡易作成]** エクスペリエンスで、クラスターの名前を **[クラスター名]** フィールドに入力します。 複数の Azure サブスクリプションがある場合、**[サブスクリプション]** エントリを使用して、使用するサブスクリプションを選択します。

    ![クラスター名とサブスクリプションの選択](./media/hdinsight-getting-started-with-r/clustername.png)

4. **[クラスターの種類]** を選択し、**[クラスターの構成]** ブレードを開きます。 **[クラスターの構成]** ブレードで、次のオプションを選択します。

   * **[クラスターの種類]**: R Server
   * **[バージョン]**: クラスターにインストールする R Server のバージョンを選択します。 最新の機能を使用するには最新バージョンを選択します。 その他のバージョンは、互換性のために必要な場合に選択できます。 使用可能な各バージョンのリリース ノートは、[こちら](https://msdn.microsoft.com/en-us/microsoft-r/notes/r-server-notes)にあります。
   * **[R Studio community edition for R Server] \(R Server 向けの R Studio コミュニティ エディション)**: このブラウザーベースの IDE は、既定でエッジ ノードにインストールされます。  この IDE をインストールしない場合は、チェックボックスをオフにします。 インストールを選択した場合、クラスターの作成後に、ポータルでクラスターの [アプリケーション] ブレードに RStudio Server へのアクセス用の URL が表示されます。

   その他のオプションは既定値のままにして、**[選択]** ボタンをクリックし、クラスターの種類を保存します。

   ![[クラスターの種類] ブレードのスクリーン ショット](./media/hdinsight-getting-started-with-r/clustertypeconfig.png)

5. **[クラスター ログイン ユーザー名]** と **[クラスター ログイン パスワード]** を入力します。

   **[SSH ユーザー名]** を指定します。  SSH は、**Secure Shell (SSH)** クライアントを使用してクラスターにリモート接続する場合に使用します。 このダイアログ ボックスで SSH ユーザーを指定するか、クラスターを作成した後に (クラスターの [構成] タブで) SSH ユーザーを指定することができます。 R Server は、**[SSH ユーザー名]** に “remoteuser” が入力されることを想定して構成されています。  **別のユーザー名を使用する場合は、クラスターを作成した後に他の手順を実行する必要があります。**

   公開キーを使用する場合を除き、**[クラスター ログインと同じパスワードを使用します]** のチェック ボックスをオンのままにして、認証の種類として **[パスワード]** を使用します。  RTVS、RStudio、または別のデスクトップ IDE など、リモート クライアントを使用してクラスターの R Server にアクセスする場合は、公開キーと秘密キーのペアが必要になります。 RStudio Server コミュニティ エディションをインストールした場合、SSH パスワードを選択する必要があります。     

   公開/秘密キーのペアを作成して使用する場合、**[クラスター ログインと同じパスワードを使用します]** のチェック ボックスをオフにしてから **[公開キー]** を選択し、次のように進みます。  次の手順では、Cygwin と、ssh-keygen または同等のものがインストールされていることを前提としています。

   * ラップトップ コンピューターのコマンド プロンプトから、公開キーと秘密キーのペアを生成します。

   `ssh-keygen -t rsa -b 2048`

   * キー ファイル名の指定を求めるプロンプトに従い、セキュリティ強化のためのパスフレーズを入力します。 画面は次のようになります。

   ![Windows での SSH コマンドライン](./media/hdinsight-getting-started-with-r/sshcmdline.png)

   * これにより、秘密キー ファイルと、<秘密キーのファイル名>.pub という名前の公開キー ファイルが作成されます (furiosa と furiosa.pub など)。

   ![SSH ディレクトリ](./media/hdinsight-getting-started-with-r/dir.png)

   * 次に HDI クラスターの資格情報を割り当てるときに公開キー ファイル (*.pub) を指定し、最後に、リソース グループとリージョンを確認して **[次へ]** を選択します。

   ![[資格情報] ブレード](./media/hdinsight-getting-started-with-r/publickeyfile.png)  

   * ノート パソコンでの秘密キー ファイルのアクセス許可の変更

   `chmod 600 <private-key-filename>`

   * リモート ログインの SSH を使用した秘密キー ファイルの使用

   `ssh –i <private-key-filename> remoteuser@<hostname public ip>`

   または、クライアントの R Server の Hadoop Spark コンピューティング コンテキストの定義の一部として使用します (オンラインの [Get started with SacaleR on Apache Spark (Apache Spark での SacaleR の概要)](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)に関するドキュメントの「[Creating a Compute Context for Spark」(Spark 用のコンピューティング コンテキストの作成)](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) セクションで、「Using Microsoft R Server as a Hadoop Client」(Hadoop クライアントとして Microsoft R Server を使用する) を参照してください)。

6. [簡易作成] 画面が **[ストレージ]** ブレードに切り替わったら、クラスターで使用される HDFS ファイル システムのプライマリ ロケーションで使用するストレージ アカウントの設定を選択します。 新規または既存の Azure Storage アカウントか、既存の Data Lake ストレージ アカウントのいずれかを選びます。

   1. Azure Storage アカウントを選択する場合は、**[ストレージ アカウントの選択]** を選択してからアカウントを選択することで、既存のアカウントを選択できます。 または、**[ストレージ アカウントの選択]** セクションで **[新規作成]** リンクを使用して新しいアカウントを作成します。

      > [!NOTE]
      > **[新規]** を選択した場合、新しいストレージ アカウントの名前を入力する必要があります。 名前を使用できる場合は、緑色のチェック マークが表示されます。

      **[既定のコンテナー]** は、既定ではクラスターの名前になります。 この値はこのままにします。

      新規ストレージ アカウントのオプションを選択している場合、**[場所]** の選択を求めるプロンプトが表示され、ストレージ アカウントを作成するリージョンを選択できます。  

         ![[データ ソース] ブレード](./media/hdinsight-getting-started-with-r/datastore.png)  

      > [!IMPORTANT]
      > 既定のデータ ソースの場所を選択すると、HDInsight クラスターの場所も設定されます。 クラスターと既定のデータ ソースは、同じリージョンにする必要があります。

   2. 既存の Data Lake Store を使用する場合、使用する ADLS ストレージ アカウントを選び、クラスター ADD アイデンティティをクラスターに追加してストアにアクセスできるようにします。 このプロセスの詳細については、「[Azure Portal を使用して、Data Lake Store を使用する HDInsight クラスターを作成する](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)」を参照してください。

   **[選択]** ボタンを使用し、データ ソースの構成を保存します。


7. 次に、すべての設定を検証するために **[概要]** ブレードが表示されます。 ここでは、**クラスター サイズ**を変更して、クラスター内のサーバーの数を変更し、実行する**スクリプト アクション**を指定することもできます。 大きいクラスターが必要になることがわかっている場合を除き、worker ノードの数は既定値の `4` のままにします。 クラスターの推定コストがブレード内に表示されます。

   ![クラスターの概要](./media/hdinsight-getting-started-with-r/clustersummary.png)

   > [!NOTE]
   > 必要に応じて、後でポータルから ([クラスター]、[設定]、[クラスターのスケール設定]) クラスターのサイズを変更して、worker ノードの数を変更できます。  これは、使用しないクラスターをアイドル状態にする場合や、大規模なタスクのニーズに合わせて容量を追加する場合に役立ちます。
   >
   >

    クラスター、データ ノード、エッジ ノードのサイズを変更するときに、次の点に留意します。  

   * データが大きいとき、Spark での分散 R Server 分析のパフォーマンスは、worker ノードの数に比例します。  

   * R Server 分析のパフォーマンスは、分析されているデータのサイズに比例します。 次に例を示します。  

     * データのサイズが中程度までの場合は、エッジ ノードのローカルのコンピューティング コンテキストで分析するときにパフォーマンスが最高になります。  ローカルと Spark コンピューティング コンテキストのパフォーマンスが最高になるシナリオの詳細については、「Compute context options for R Server on HDInsight」(HDInsight での R Server のコンピューティング コンテキスト オプション) を参照してください。<br>
     * エッジ ノードにログインして R スクリプトを実行すると、ScaleR rx 関数以外のすべてがエッジ ノードで<strong>ローカル</strong>で実行され、エッジ ノードのメモリとコアの数が適宜調整されます。 ノート パソコンからリモート コンピューティング コンテキストとして HDI の RServer を使用する場合、同じことがあてはまります。

     ![[ノード価格レベル] ブレード](./media/hdinsight-getting-started-with-r/pricingtier.png)

     **[選択]** ボタンを使用し、ノードの価格構成を保存します。

   **テンプレートおよびパラメーターをダウンロードする**ためのリンクもあります。 このリンクをクリックすると、選んだ構成でクラスターの作成を自動化するために使用するスクリプトが表示されます。 これらのスクリプトは、クラスターが作成されていれば、そのクラスターの Azure Portal のエントリから入手できます。

   > [!NOTE]
   > クラスターが作成されるまで、通常は約 20 分かかります。 作成プロセスをチェックするには、スタート画面のタイル、またはページの左側にある **[通知]** エントリを使用してください。
   >
   >

## <a name="connect-to-rstudio-server"></a>RStudio Server に接続する

インストールに RStudio Server コミュニティ エディションを含めることを選択した場合は、次の 2 つの異なる方法で RStudio にアクセスできます。

1. 次の URL にアクセスする (**CLUSTERNAME** は作成したクラスターの名前です)。

    https://**CLUSTERNAME**.azurehdinsight.net/rstudio/

2. または、Azure Portal でクラスターのエントリを開き、[R Server ダッシュボード] クイック リンク、[R Studio ダッシュボード] の順にクリックする。

     ![[R Studio ダッシュボード] へのアクセス](./media/hdinsight-getting-started-with-r/rstudiodashboard1.png)

     ![[R Studio ダッシュボード] へのアクセス](./media/hdinsight-getting-started-with-r/rstudiodashboard2.png)

   > [!IMPORTANT]
   > どちらの方法でも、初めてログインするときは認証を 2 回行う必要があります。  1 番目の認証では、クラスターの管理者の ユーザー ID とパスワードを入力します。 2 番目のプロンプトでは、SSH ユーザー ID とパスワードを入力します。 以降のログインでは、SSH パスワードとユーザー ID のみが求められます。

## <a name="connect-to-the-r-server-edge-node"></a>R Server エッジ ノードに接続する
SSH を使用して HDInsight クラスターの R Server エッジ ノードに接続します。

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

> [!NOTE]
> クラスターを選択し、**[すべての設定]**、**[アプリ]**、**[RServer]** の順にクリックし、Azure Portal で `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` のアドレスを確認することもできます。 これにより、エッジ ノードの SSH エンドポイント情報が表示されます。
>
> ![エッジ ノードの SSH エンドポイントの画像](./media/hdinsight-getting-started-with-r/sshendpoint.png)
>
>

SSH ユーザー アカウントを保護するためにパスワードを使用している場合は、パスワードの入力を求められます。 公開キーを使用している場合、 `-i` パラメーターを使用して、対応する秘密キーを指定することが必要な場合があります。 たとえば、「 `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`」のように入力します。

詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

接続すると、次のようなプロンプトが表示されます。

`username@ed00-myrser:~$`

## <a name="use-the-r-console"></a>R コンソールを使用する

1. SSH セッションから、次のコマンドを使用して R コンソールを起動します。  

   ```
   R

   You will see output similar to the following.
   R version 3.2.2 (2015-08-14) -- "Fire Safety"
   Copyright (C) 2015 The R Foundation for Statistical Computing
   Platform: x86_64-pc-linux-gnu (64-bit)

   R is free software and comes with ABSOLUTELY NO WARRANTY.
   You are welcome to redistribute it under certain conditions.
   Type 'license()' or 'licence()' for distribution details.

   Natural language support but running in an English locale

   R is a collaborative project with many contributors.
   Type 'contributors()' for more information and
   'citation()' on how to cite R or R packages in publications.

   Type 'demo()' for some demos, 'help()' for on-line help, or
   'help.start()' for an HTML browser interface to help.
   Type 'q()' to quit R.

   Microsoft R Server version 8.0: an enhanced distribution of R
   Microsoft packages Copyright (C) 2016 Microsoft Corporation

   Type 'readme()' for release notes.
   >
   ```

2. `>` プロンプトから R コードを入力できます。 R Server には、Hadoop との対話を容易にし、分散計算を実行するためのパッケージが含まれています。 たとえば、次のコマンドを使用して、HDInsight クラスターの既定のファイル システムのルートを表示します。

`rxHadoopListFiles("/")`

WASB スタイルのアドレス指定も使用できます。

`rxHadoopListFiles("wasbs:///")`

## <a name="using-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>Microsoft R Server または Microsoft R Client のリモート インスタンスから HDI の R Server を使用する
クラスターにアクセスするための公開キーと秘密キーのペアの使用について説明する前のセクションに従って、デスクトップまたはラップトップで実行されている Microsoft R Server または Microsoft R Client のリモート インスタンスから HDI Hadoop Spark コンピューティング コンテキストへのアクセスを設定することができます (オンラインの [RevoScaleR Hadoop Spark の概要ガイド](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)に関するドキュメントの「[Creating a Compute Context for Spark (Spark 用のコンピューティング コンテキストの作成)](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark)」セクションで、「Using Microsoft R Server as a Hadoop Client (Hadoop クライアントとして Microsoft R Server を使用する)」を参照してください)。  そのためには、ラップトップで RxSpark コンピューティング コンテキストを定義するときに、オプション (hdfsShareDir、shareDir、sshUsername、sshHostname、sshSwitches、sshProfileScript) を指定する必要があります。 次に例を示します。

```
    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- 'rkrrehdi1-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- 'remoteuser'# HDI SSH username
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
```


## <a name="use-a-compute-context"></a>コンピューティング コンテキストを使用する
コンピューティング コンテキストを使用すると、計算をエッジ ノードでローカルに実行するか、HDInsight クラスターのノード全体に分散するかを制御することができます。

1. RStudio Server または R コンソール (SSH セッション) から次をコマンドを実行し、HDInsight の既定のストレージにサンプル データを読み込みます。

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"

        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)

        # Download data to the tmp folder
        remoteDir <- "http://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
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

2. 次に、いくつかのデータ情報を作成し、データを利用できるようにデータ ソースを 2 つ定義しましょう。

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

3. ローカルのコンピューティング テキストを使用し、データに対してロジスティック回帰を実行してみましょう。

        # Set a local compute context
        rxSetComputeContext("local")

        # Run a logistic regression
        system.time(
           modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )

        # Display a summary
        summary(modelLocal)

    次のような行で終了する出力が表示されます。

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

4. 次に、Spark コンテキストを使用して同じロジスティック回帰を実行してみましょう。 Spark コンテキストを使用すると、HDInsight クラスターのすべてのワーカー ノードに処理が分散されます。

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


   > [!NOTE]
   > MapReduce を使用して、クラスター ノード全体に計算を分散することもできます。 コンピューティング コンテキストの詳細については、「[Compute context options for R Server on HDInsight](hdinsight-hadoop-r-server-compute-contexts.md)」 (HDInsight の R Server のコンピューティング コンテキスト オプション) を参照してください。


## <a name="distribute-r-code-to-multiple-nodes"></a>複数のノードに R コードを分散する
R Server では、既存の R コードを容易に取得し、 `rxExec`を使用してクラスター内の複数のノード全体で実行することができます。 これは、パラメーター スイープまたはシミュレーションを行うときに便利です。 `rxExec`を使用する方法の例を次に示します。

`rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )`

Spark や MapReduce のコンテキストを使用している場合は、コード `(Sys.info()["nodename"])` が実行されている worker ノードのノード名の値が返されます。 たとえば、4 ノードのクラスタでは次のような出力が表示されます。


    ```
    $rxElem1
        nodename
    "wn3-myrser"

    $rxElem2
        nodename
    "wn0-myrser"

    $rxElem3
        nodename
    "wn3-myrser"

    $rxElem4
        nodename
    "wn3-myrser"
    ```

## <a name="accessing-data-in-hive-and-parquet"></a>Hive および Parquet 内のデータへのアクセス
R Server 9.0 以上で利用可能な新機能を使用すると、Hive および Parquet 内のデータに直接アクセスし、Spark のコンピューティング コンテキスト内の ScaleR 関数で使用できます。 RxHiveData と RxParquetData という新しい ScaleR データ ソース関数で、以下の機能を使用できます。これらの関数は、ローカルのデータを ScaleR で分析できるように、Spark SQL を使用して Spark データフレームに直接読み込みます。  

次に、これらの新しい関数の使用方法に関するサンプル コードを示します。



    ```
    #..create a Spark compute context

    myHadoopCluster <- rxSparkConnect(reset = TRUE)
    ```


    ```
    #..retrieve some sample data from Hive and run a model

    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)
    ```

    ```
    #..retrieve some sample data from Parquet and run a model

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
    ```


    ```
    #..check on Spark data objects, cleanup, and close the Spark session

    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)
    ```

これらの新関数の使用方法の詳細については、R Server で ?RxHivedata コマンドおよび ?RxParquetData を使用すると表示されるオンライン ヘルプをご覧ください。  


## <a name="install-r-packages"></a>R パッケージをインストールする
エッジ ノードに追加の R パッケージをインストールする場合、SSH 経由でエッジ ノードに接続していれば、R コンソールから `install.packages()` を直接使用できます。 ただし、クラスターのワーカー ノードに R パッケージをインストールする場合は、スクリプト アクションを使用する必要があります。

スクリプト アクションは、HDInsight クラスターの構成を変更するか、追加のソフトウェアをインストールするために使用する Bash スクリプトです。 この場合は追加の R パッケージをインストールするために使用します。 スクリプト アクションを使用して追加のパッケージをインストールするには、次の手順に従います。

> [!IMPORTANT]
> スクリプト アクションを使用した追加の R パッケージのインストールは、クラスターが作成された後にのみ使用できます。 スクリプトは完全にインストールと構成がされている R Server に依存するため、クラスターの作成中は使用できません。
>
>

1. [Azure Portal](https://portal.azure.com) で、HDInsight クラスター上の R Server を選択します。

2. **[設定]** ブレードで、**[スクリプト アクション]**、**[新規で送信]** と選び、新しいスクリプト アクションを送信します。

   ![[スクリプト アクション] ブレードの画像](./media/hdinsight-getting-started-with-r/scriptaction.png)

3. **[スクリプト操作を追加]** ブレードで、次の情報を指定します。

   * **[名前]**: このスクリプトを識別するための表示名

   * **[バッシュ スクリプト URI]**: `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * **[ヘッド]**: **オフに設定**します

   * **[ワーカー]**: **オンに設定**します

   * **[エッジ ノード]**: **オフに設定**します

   * **[Zookeeper]**: **オフに設定**します

   * **[パラメーター]**: インストールする R パッケージ。 たとえば、`bitops stringr arules` のように指定します。

   * **[Persist this script...] \(このスクリプトの保持…)**: **オンに設定**します  

   > [!NOTE]
   > 1. 既定では、R パッケージはすべて、インストールされている R Server のバージョンと一貫性のある Microsoft MRAN リポジトリのスナップショットからインストールされます。  非互換性の問題が発生するリスクはありますが、パッケージ一覧の最初の要素として `useCRAN` を指定して (例: `useCRAN bitops, stringr, arules`)、パッケージの新しいバージョンをインストールすることもできます。  
   > 2. 一部の R パッケージには、Linux システムの他のライブラリが必要です。 便宜上、上位 100 の最も一般的な R パッケージで必要な依存関係が事前インストールされています。 ただし、インストールする R パッケージでこれら以外のライブラリが必要な場合、ここで使用する基本スクリプトをダウンロードし、システム ライブラリをインストールする手順を追加します。 その後、変更後のスクリプトを Azure ストレージ内のパブリック BLOB コンテナーにアップロードし、変更後のスクリプトを使用してパッケージをインストールする必要があります。
   >    スクリプト アクションを開発する方法の詳細については、 [スクリプト アクション開発](hdinsight-hadoop-script-actions-linux.md)に関するページを参照してください。  
   >
   >

   ![スクリプト アクションの追加](./media/hdinsight-getting-started-with-r/submitscriptaction.png)

4. **[作成]** を選択してスクリプトを実行します。 スクリプトが完了すると、R パッケージをすべてのワーカー ノードで使用できるようになります。

## <a name="using-microsoft-r-server-operationalization"></a>Microsoft R Server の運用化の使用
データ モデリングが完了したら、モデルを運用化して、新しいデータについての予測を行うことができます。 Microsoft R Server の運用化を構成するには、次の手順を実行します。

最初に、Edge ノードに SSH 接続します。 たとえば、「 ```ssh -L USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net```」のように入力します。

SSH を使用した後、ディレクトリを次のディレクトリに変更し、次に示すように dotnet dll を sudo します。

```
   cd /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil
   sudo dotnet Microsoft.DeployR.Utils.AdminUtil.dll
```

Microsoft R Server の運用化をワンボックス構成で構成するには、次の操作を行います。

* "1.  Configure R Server for Operationalization" を選択します。
* "A.  One-box (web + compute nodes)" を選択します。
* **admin** ユーザーのパスワードを入力します。

![ワンボックス操作](./media/hdinsight-hadoop-r-server-get-started/admin-util-one-box-.png)

省略可能な手順として、次のように診断テストを実行することで、診断チェックを実行できます。

* "6.  Run diagnostic tests" を選択します。
* "A.  Test configuration" を選択します。
* Username として admin を、パスワードとして前の手順で入力したパスワードを入力します。
* Overall Health が pass になっていることを確認します。
* 管理ユーティリティを終了します。
* SSH を終了します。

![操作の診断](./media/hdinsight-hadoop-r-server-get-started/admin-util-diagnostics.png)

この段階で、運用化の構成が完了しました。 これで、RClient の 'mrsdeploy' パッケージを使用して Edge ノードの運用化に接続し、[リモート実行](https://msdn.microsoft.com/microsoft-r/operationalize/remote-execution)や [Web サービス](https://msdn.microsoft.com/microsoft-r/mrsdeploy/mrsdeploy-websrv-vignette)などの機能の使用を開始できます。 クラスターが仮想ネットワーク上に設定されているか否かに応じて、SSH ログイン経由のポート転送トンネリングの設定が必要になる場合があります。

### <a name="rserver-cluster-on-virtual-network"></a>仮想ネットワーク上の RServer クラスター

ポート 12800 から Edge ノードへのトラフィックを許可していることを確認します。 これで、運用化機能への接続に Edge ノードを使用できます。

```
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

remoteLogin() では Edge ノードに接続できないが、Edge ノードに SSH 接続できる場合は、ポート 12800 でトラフィックを許可するルールが適切に設定されていることを確認する必要があります。 問題が解決しない場合は、SSH 経由のポート転送トンネリングを設定する回避策を使用できます。

### <a name="rserver-cluster-not-set-up-on-virtual-network"></a>仮想ネットワーク上にセットアップされていない RServer クラスター

クラスターが VNet 上にセットアップされていない場合、または VNet 経由の接続で問題が発生する場合は、次に示す SSH ポート転送トンネリングを使用することができます。

```
ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
```

Putty でも、同様にセットアップできます。

![putty ssh 接続](./media/hdinsight-hadoop-r-server-get-started/putty.png)

SSH セッションがアクティブになったら、コンピューターのポート 12800 からのトラフィックは、SSH セッション経由で Edge ノードのポート 12800 に転送されます。 remoteLogin() メソッドで `127.0.0.1:12800` を使用していることを確認してください。 これは、ポート転送経由で Edge ノードの運用化にログインします。

```
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://127.0.0.1:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

## <a name="how-to-scale-microsoft-r-server-operationalization-compute-nodes-on-hdinsight-worker-nodes"></a>HDInsight ワーカー ノードで Microsoft R Server 運用化コンピューティング ノードをスケーリングする方法


### <a name="decommission-the-worker-nodes"></a>ワーカー ノードを使用停止する
Microsoft R Server は、現時点では Yarn を通じて管理されません。 ワーカー ノードが使用停止されていない場合、Yarn リソース マネージャーは予期したとおりに機能しません。これは、リソースがサーバーによって使用されることを認識しないためです。 これを防ぐために、コンピューティング ノードをスケーリングするワーカー ノードの使用停止を行うことをお勧めします。

ワーカー ノードを使用停止する手順:

* HDI クラスターの Ambari コンソールにログインして、[ホスト] タブをクリックします。
* 使用停止するワーカー ノードを選択し[アクション]、[選択したホスト]、[ホスト] の順にクリックし、[メンテナンス モードの有効化] をクリックします。 たとえば、次の画像では、使用停止の対象として wn3 と wn4 が選択されています。  

   ![ワーカー ノードの使用停止](./media/hdinsight-hadoop-r-server-get-started/get-started-operationalization.png)  

* [アクション]、[選択したホスト]、[DataNodes] の順に選択し、[使用停止] をクリックします。
* [アクション]、[選択したホスト]、[NodeManagers] の順に選択し、[使用停止] をクリックします。
* [アクション]、[選択したホスト]、[DataNodes] の順に選択し、[停止] をクリックします。
* [アクション]、[選択したホスト]、[NodeManagers] の順に選択し、[停止] をクリックします。
* [アクション]、[選択したホスト]、[ホスト] の順に選択し、[Stop All Components (すべてのコンポーネントを停止)] をクリックします。
* ワーカー ノードの選択を解除し、ヘッド ノードを選択します。
* [アクション]、[選択したホスト]、[ホスト] の順に選択し、[Restart All Components (すべてのコンポーネントを再起動)] をクリックします。


###    <a name="configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>使用停止されたワーカー ノードにコンピューティング ノードを構成する

* 使用停止されたワーカー ノードに SSH 接続します。
* 以下を使用して管理ユーティリティを実行します。`dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll`
* 「1」を入力して、オプション "1.  Configure R Server for Operationalization" を選択します。
* 「c」を入力して、オプション "C.  Compute node" を選択します。 これで、ワーカー ノードでコンピューティング ノードが構成されます。
* 管理ユーティリティを終了します。

### <a name="add-compute-nodes-details-on-web-node"></a>Web ノードにコンピューティング ノードの詳細を追加する
すべての使用停止されたワーカー ノードがコンピューティング ノードを実行するように構成されたら、Edge ノードに戻って、使用停止されたワーカー ノードの IP アドレスを Microsoft R Server Web ノードの構成に追加します。

* Edge ノードに SSH 接続します。
* `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json` を実行します。
* "URI" セクションを見つけて、ワーカー ノードの IP とポートの詳細を追加します。

![ワーカー ノードの使用停止のコマンドライン](./media/hdinsight-hadoop-r-server-get-started/get-started-op-cmd.png)

## <a name="next-steps"></a>次のステップ
ここでは、R Server を含む新しい HDInsight クラスターを作成する方法、SSH セッションから R コンソールを使用する方法の基本を説明しました。次のリンクを使用して、HDInsight で R Server を使用するための他の方法を見てみましょう。

* [HDInsight へ RStudio Server を追加する (クラスター作成時にインストールされていない場合)](hdinsight-hadoop-r-server-install-r-studio.md)
* [HDInsight の R Server (プレビュー) の計算コンテキストのオプション](hdinsight-hadoop-r-server-compute-contexts.md)
* [HDInsight の R Server 向けの Azure Storage オプション](hdinsight-hadoop-r-server-storage.md)

