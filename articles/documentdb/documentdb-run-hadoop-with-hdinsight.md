---
title: "Azure DocumentDB と HDInsight を使用した Hadoop ジョブの実行 | Microsoft Docs"
description: "DocumentDB と Azure HDInsight を使用してシンプルな Hive、Pig、および MapReduce ジョブを実行する方法を説明します。"
services: documentdb
author: dennyglee
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 06f0ea9d-07cb-4593-a9c5-ab912b62ac42
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 09/20/2016
ms.author: denlee
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 9304acd9f99b7f492a37bc4243ed8fb617998c6f
ms.lasthandoff: 03/07/2017


---
# <a name="DocumentDB-HDInsight"></a>DocumentDB と HDInsight を使用した Apache Hive、Pig、または Hadoop ジョブの実行
このチュートリアルでは、DocumentDB の Hadoop コネクタを使用して、[Apache Hive][apache-hive]、[Apache Pig][apache-pig]、および [Apache Hadoop][apache-hadoop] MapReduce の各ジョブを Azure HDInsight 上で実行する方法について説明します。 DocumentDB の Hadoop コネクタを使用すると、DocumentDB が Hive、Pig、および MapReduce の各ジョブに対してソースとシンクの両方として機能します。 このチュートリアルでは、Hadoop ジョブのデータ ソースと出力先の両方に DocumentDB を使用します。

このチュートリアルを完了すると、次の項目について説明できるようになります。

* Hive、Pig、または MapReduce ジョブを使用して DocumentDB からどのようにしてデータを読み込むか。
* Hive、Pig、または MapReduce ジョブを使用して DocumentDB にどのようにしてデータを格納するか。

まずは、次のビデオを視聴することをお勧めします。このビデオでは、DocumentDB と HDInsight を使用して Hive ジョブがひととおり実行されます。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Use-Azure-DocumentDB-Hadoop-Connector-with-Azure-HDInsight/player]
>
>

その後で、この記事に戻ってください。この記事では、DocumentDB データに対して分析ジョブを実行する方法について詳しく説明します。

> [!TIP]
> このチュートリアルは、これまでに Apache Hadoop、Hive、Pig の少なくとも&1; つを使用した経験があることを前提としています。 Apache Hadoop、Hive、Pig の使用経験がない場合は、[Apache Hadoop のドキュメント][apache-hadoop-doc]を参照することをお勧めします。 さらに、このチュートリアルでは、DocumentDB の使用経験があり、DocumentDB アカウントを持っていることを想定しています。 DocumentDB を初めて扱う方や DocumentDB アカウントを持っていない方は、[使用の開始][getting-started]に関するページをご覧ください。
>
>

チュートリアルを完了する時間がなく、Hive、Pig、および MapReduce の完全なサンプル PowerShell スクリプトが必要な場合は、 [こちら][documentdb-hdinsight-samples]をクリックすると入手できます。 ダウンロードには、これらのサンプルの hql、pig、および java の各ファイルも含まれています。

## <a name="NewestVersion"></a>最新バージョン
<table border='1'>
    <tr><th>Hadoop コネクタのバージョン</th>
        <td>1.2.0</td></tr>
    <tr><th>スクリプト URI</th>
        <td>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v04.ps1</td></tr>
    <tr><th>更新日時</th>
        <td>04/26/2016</td></tr>
    <tr><th>サポートされる HDInsight のバージョン</th>
        <td>3.1、3.2</td></tr>
    <tr><th>変更履歴</th>
        <td>DocumentDB Java SDK 1.6.0 への更新</br>
            ソースとシンクの両方としてのパーティション分割コレクションのサポートの追加</br>
        </td></tr>
</table>

## <a name="Prerequisites"></a>前提条件
このチュートリアルの手順を実行する前に、次のものを備えておく必要があります。

* DocumentDB アカウント、データベース、およびドキュメントが含まれているコレクション 詳細については、[DocumentDB の使用][getting-started]に関するページをご覧ください。 [DocumentDB インポート ツール][documentdb-import-data]を使用して、DocumentDB アカウントにサンプル データをインポートします。
* スループット。 HDInsight の読み取りと書き込みは、コレクションに割り当てられた要求単位に達するまでカウントされます。
* 各出力コレクションに含まれる追加のストアド プロシージャ用の容量。 ストアド プロシージャは、生成されたドキュメントを転送するために使用されます。
* Hive、Pig、または MapReduce のジョブから生成されたドキュメント用の容量。
* *オプション*: 追加のコレクション用の容量。

> [!WARNING]
> 何かのジョブの実行中に新しいコレクションが作成されることを避けるために、stdout に結果を書き出して出力を WASB コンテナーに格納するか、既存のコレクションを指定することができます。 既存のコレクションを指定した場合、新しいドキュメントはコレクション内に作成され、 *ID*に競合がない限り既存のドキュメントに影響は及びません。 **ID の競合がある場合は、既存のドキュメントがコネクタによって自動的に上書きされます**。 この機能を無効にするには、upsert オプションを false に設定します。 upsert が false に設定された状態で競合が発生すると、Hadoop ジョブは失敗し、ID 競合エラーが報告されます。
>
>

## <a name="ProvisionHDInsight"></a>手順 1: 新しい HDInsight クラスターを作成する
このチュートリアルでは、Azure Portal からスクリプト アクションを使用して、HDInsight クラスターをカスタマイズします。 このチュートリアルでは、HDInsight クラスターの作成に、Azure Portal を使用します。 PowerShell コマンドレットまたは HDInsight .NET SDK を使用する方法については、[スクリプト アクションを使用した HDInsight クラスターのカスタマイズ][hdinsight-custom-provision]に関する記事をご覧ください。

1. [Azure Portal][azure-portal] にサインインします。
2. 左側のナビゲーションの上部にある **[+ 新規]** をクリックし、[新規] ブレードの上部にある検索バーで **HDInsight** を検索します。
3. **Microsoft** によって発行された **HDInsight** が、結果の先頭に表示されます。 それをクリックし、 **[作成]**をクリックします。
4. [新しい HDInsight クラスター] 作成ブレードで、**クラスター名**を入力し、このリソースのプロビジョニング先の**サブスクリプション**を選択します。

    <table border='1'>
        <tr><td>クラスター名</td><td>クラスターの名前を指定します。<br/>
        DNS 名の最初と最後の文字は英数字にする必要があります。それ以外ではダッシュ (-) を使用できます。<br/>
        3 ～ 63 文字のクラスター名を入力できます。</td></tr>
        <tr><td>サブスクリプション名</td>
            <td>複数の Azure サブスクリプションがある場合は、HDInsight クラスターをホストするサブスクリプションを選択します。 </td></tr>
    </table>
5. **[クラスターの種類の選択]** をクリックし、以下のプロパティに、指定された値を設定します。

    <table border='1'>
        <tr><td>クラスターの種類</td><td><strong>Hadoop</strong></td></tr>
        <tr><td>クラスター レベル</td><td><strong>Standard</strong></td></tr>
        <tr><td>オペレーティング システム</td><td><strong>Windows</strong></td></tr>
        <tr><td>バージョン</td><td>最新バージョン</td></tr>
    </table>

    ここで、 **[選択]**をクリックします。

    ![Hadoop HDInsight の初期クラスターの詳細を提供][image-customprovision-page1]
6. **[資格情報]** をクリックし、ログインとリモート アクセスの資格情報を設定します。 **[クラスター ログイン ユーザー名]** と **[クラスター ログイン パスワード]** を選択します。

    クラスターにリモート接続したい場合は、ブレードの下部の *[はい]* を選択し、ユーザー名とパスワードを指定します。
7. **[データ ソース]** をクリックし、データ アクセスのプライマリ ロケーションを設定します。 **[メソッドの選択]** を選択し、既存のストレージ アカウントを指定するか、新しいアカウントを作成します。
8. 同じブレードで、**[既定のコンテナー]** と **[場所]** を指定します。 その後、 **[選択]**をクリックします。

   > [!NOTE]
   > パフォーマンスを良くするために、DocumentDB アカウントのリージョンに近い場所を選択します。
   >
   >
9. **[料金]** をクリックし、ノードの数と種類を選択します。 既定の構成のままにしておいて、後でワーカー ノードの数を増減できます。
10. [オプションの構成] ブレードで、**[オプションの構成]**、**[スクリプト操作]** の順にクリックします。

     [スクリプト操作] では、以下の情報を入力して、HDInsight クラスターをカスタマイズします。

     <table border='1'>
         <tr><th>プロパティ</th><th>値</th></tr>
         <tr><td>名前</td>
             <td>スクリプト アクションの名前を指定します。</td></tr>
         <tr><td>スクリプト URI</td>
             <td>クラスターのカスタマイズのために呼び出されるスクリプトへの URI を指定します。</br></br>
             次の値を入力してください。 </br> <strong>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v04.ps1</strong></td></tr>
         <tr><td>ヘッド</td>
             <td>チェック ボックスをオンにして、ヘッド ノードに対して PowerShell スクリプトを実行します。</br></br>
             <strong>このチェック ボックスをオンにします</strong>。</td></tr>
         <tr><td>ワーカー</td>
             <td>チェック ボックスをオンにして、ワーカー ノードに対して PowerShell スクリプトを実行します。</br></br>
             <strong>このチェック ボックスをオンにします</strong>。</td></tr>
         <tr><td>Zookeeper</td>
             <td>チェック ボックスをオンにして、Zookeeper に対して PowerShell スクリプトを実行します。</br></br>
             <strong>不要です</strong>。
             </td></tr>
         <tr><td>parameters</td>
             <td>スクリプトで必要な場合は、パラメーターを指定します。</br></br>
             <strong>パラメーターは必要ありません</strong>。</td></tr>
     </table>
11. 新しい **リソース グループ** を作成するか、Azure サブスクリプション内の既存のリソース グループを使用します。
12. ここで、**[ダッシュボードにピン留めする]** チェック ボックスをオンにしてデプロイを追跡し、**[作成]** をクリックします。

## <a name="InstallCmdlets"></a>手順 2: Azure PowerShell をインストールおよび構成する
1. Azure PowerShell をインストールします。 手順については、[こちら][powershell-install-configure]を参照してください。

   > [!NOTE]
   > または、Hive クエリに対して HDInsight のオンライン Hive エディターを使用できます。 この場合は、[Azure Portal][azure-portal] にサインインし、左側のウィンドウで **[HDInsight]** をクリックすると、HDInsight クラスターの一覧が表示されます。 Hive クエリを実行するクラスターをクリックし、 **[クエリ コンソール]**をクリックします
   >
   >
2. Azure PowerShell Integrated Scripting Environment を開きます。

   * Windows 8 または Windows Server 2012 以降を実行しているコンピューターでは、組み込みの検索機能を使用できます。 スタート画面で、「**powershell ise**」と入力し、**Enter** キーを押します。
   * Windows 8 よりも前、または Windows Server 2012 より前のバージョンを実行しているコンピューターでは、[スタート] メニューを使用します。 [スタート] メニューの検索ボックスに「**コマンド プロンプト**」と入力し、結果の一覧で、**[コマンド プロンプト]** をクリックします。 コマンド プロンプトで、「**powershell_ise**」と入力し、**Enter** キーを押します。
3. Azure アカウントを追加します。

   1. コンソール ウィンドウで、「**Add-AzureAccount**」と入力し、**Enter** キーを押します。
   2. お使いの Azure サブスクリプションに関連付けられている電子メール アドレスを入力し、 **[続行]**をクリックします。
   3. Azure サブスクリプションのパスワードを入力します。
   4. **[サインイン]**をクリックします。
4. 次の図は、Azure PowerShell Scripting Environment の重要な要素を示しています。

    ![図: Azure PowerShell][azure-powershell-diagram]

## <a name="RunHive"></a>手順 3: DocumentDB と HDInsight を使用して Hive ジョブを実行する
> [!IMPORTANT]
> < > で囲まれている変数はすべて、構成設定を使用して入力する必要があります。
>
>

1. PowerShell スクリプト ウィンドウで次の変数を設定します。

        # Provide Azure subscription name, the Azure Storage account and container that is used for the default HDInsight file system.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide the HDInsight cluster name where you want to run the Hive job.
        $clusterName = "<HDInsightClusterName>"
2. <p>クエリ文字列の作成から始めましょう。 作成する Hive クエリでは、DocumentDB コレクションからすべてのドキュメントのシステム生成のタイムスタンプ (_ts) と一意の ID (_rid) を取得し、すべてのドキュメントを分単位で集計して、その結果を新しい DocumentDB コレクションに格納します。</p>

    <p>まず、DocumentDB コレクションから Hive テーブルを作成します。 次のコード スニペットを PowerShell スクリプト ウィンドウの #1 から始まっているコード スニペットの<strong>後に</strong>追加します。 _ts および _rid だけにドキュメントをトリミングするためのオプションである DocumentDB.query パラメーターが含まれていることを確認してください。</p>

   > [!NOTE]
   > **DocumentDB.inputCollections という名前は誤りではありません。** 次のように、複数のコレクションを&1; つの入力として追加することができます。 </br>
   >
   >

        '*DocumentDB.inputCollections*' = '*\<DocumentDB Input Collection Name 1\>*,*\<DocumentDB Input Collection Name 2\>*' A1A</br> The collection names are separated without spaces, using only a single comma.

        # Create a Hive table using data from DocumentDB. Pass DocumentDB the query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "drop table DocumentDB_timestamps; "  +
                            "create external table DocumentDB_timestamps(id string, ts BIGINT) "  +
                            "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' "  +
                            "tblproperties ( " +
                                "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                "'DocumentDB.key' = '<DocumentDB Primary Key>', " +
                                "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                "'DocumentDB.inputCollections' = '<DocumentDB Input Collection Name>', " +
                                "'DocumentDB.query' = 'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

1. 次に、出力コレクション用に Hive テーブルを作成します。 出力ドキュメントのプロパティは、月、日、時間、分、および発生した合計回数です。

   > [!NOTE]
   > **ここでも、DocumentDB.outputCollections という名前は誤りではありません。** 次のように、複数のコレクションを&1; つの出力として追加することができます。 </br>
   > '*DocumentDB.outputCollections*' = '*\<DocumentDB Output Collection Name 1\>*,*\<DocumentDB Output Collection Name 2\>*' </br> コレクション名は、間にスペースを入れずにコンマだけで区切ります。 </br></br>
   > ドキュメントは複数のコレクションに対してラウンドロビン形式で分散されます。 ドキュメントの&1; つ目のバッチが&1; つのコレクションに格納され、2 つ目のバッチが次のコレクションに格納されて、以降、同様に処理されます。
   >
   >

       # Create a Hive table for the output data to DocumentDB.
       $queryStringPart2 = "drop table DocumentDB_analytics; " +
                             "create external table DocumentDB_analytics(Month INT, Day INT, Hour INT, Minute INT, Total INT) " +
                             "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' " +
                             "tblproperties ( " +
                                 "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                 "'DocumentDB.key' = '<DocumentDB Primary Key>', " +  
                                 "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                 "'DocumentDB.outputCollections' = '<DocumentDB Output Collection Name>' ); "
2. 最後は、ドキュメントを月単位、日単位、時間単位、および分単位で集計し、結果を出力 Hive テーブルに格納します。

        # GROUP BY minute, COUNT entries for each, INSERT INTO output Hive table.
        $queryStringPart3 = "INSERT INTO table DocumentDB_analytics " +
                              "SELECT month(from_unixtime(ts)) as Month, day(from_unixtime(ts)) as Day, " +
                              "hour(from_unixtime(ts)) as Hour, minute(from_unixtime(ts)) as Minute, " +
                              "COUNT(*) AS Total " +
                              "FROM DocumentDB_timestamps " +
                              "GROUP BY month(from_unixtime(ts)), day(from_unixtime(ts)), " +
                              "hour(from_unixtime(ts)) , minute(from_unixtime(ts)); "
3. 次のスクリプト スニペットを追加して、前のクエリから Hive ジョブ定義を作成します。

        # Create a Hive job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

    -File スイッチを使用して、HDFS 上の HiveQL スクリプト ファイルを指定することもできます。
4. 次のスニペットを追加して、開始時刻を格納し、Hive ジョブを送信します。

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition
5. 次のスニペットを追加して、Hive ジョブが完了するのを待機します。

        # Wait for the Hive job to complete.
        Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600
6. 次のスニペットを追加して、標準出力および開始時刻と終了時刻を表示します。

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
7. **実行しましょう** 。 **左側のナビゲーションの上部にある** 。
8. 結果を確認します。 [Azure Portal][azure-portal] にサインインします。

   1. 左側ウィンドウの <strong>[参照]</strong> をクリックします。 </br>
   2. 参照ウィンドウの右上にある <strong>[すべて]</strong> をクリックします。 </br>
   3. <strong>[DocumentDB アカウント]</strong> を見つけてクリックします。 </br>
   4. 次に、自分の <strong>DocumentDB アカウント</strong>、<strong>DocumentDB データベース</strong>、Hive クエリで指定した出力コレクションに関連付けられた <strong>DocumentDB コレクション</strong>を見つけます。</br>
   5. 最後に、<strong>[開発者ツール]</strong> の下にある <strong>[ドキュメント エクスプローラー]</strong> をクリックします。</br></p>

   Hive クエリの結果が表示されます。

   ![Hive クエリの結果][image-hive-query-results]

## <a name="RunPig"></a>手順 4: DocumentDB と HDInsight を使用して Pig ジョブを実行する
> [!IMPORTANT]
> < > で囲まれている変数はすべて、構成設定を使用して入力する必要があります。
>
>

1. PowerShell スクリプト ウィンドウで次の変数を設定します。

        # Provide Azure subscription name.
        $subscriptionName = "Azure Subscription Name"

        # Provide HDInsight cluster name where you want to run the Pig job.
        $clusterName = "Azure HDInsight Cluster Name"
2. <p>クエリ文字列の作成から始めましょう。 作成する Pig クエリでは、DocumentDB コレクションからすべてのドキュメントのシステム生成のタイムスタンプ (_ts) と一意の ID (_rid) を取得し、すべてのドキュメントを分単位で集計して、その結果を新しい DocumentDB コレクションに格納します。</p>
    <p>まず、DocumentDB から HDInsight にドキュメントを読み込みます。 次のコード スニペットを PowerShell スクリプト ウィンドウの #1 から始まっているコード スニペットの<strong>後に</strong>追加します。 _ts および _rid に合わせてドキュメントをトリミングするためのオプションの DocumentDB.query パラメーターが DocumentDB クエリに追加されていることを確認してください。</p>

   > [!NOTE]
   > 次のように、複数のコレクションを&1; つの入力として追加することができます。 </br>
   > '*\<DocumentDB Input Collection Name 1\>*,*\<DocumentDB Input Collection Name 2\>*'</br> コレクション名は、間にスペースを入れずにコンマだけで区切ります。 </b>
   >
   >

    ドキュメントは複数のコレクションに対してラウンドロビン形式で分散されます。 ドキュメントの&1; つ目のバッチが&1; つのコレクションに格納され、2 つ目のバッチが次のコレクションに格納されて、以降、同様に処理されます。

        # Load data from DocumentDB. Pass DocumentDB query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "DocumentDB_timestamps = LOAD '<DocumentDB Endpoint>' USING com.microsoft.azure.documentdb.pig.DocumentDBLoader( " +
                                                        "'<DocumentDB Primary Key>', " +
                                                        "'<DocumentDB Database Name>', " +
                                                        "'<DocumentDB Input Collection Name>', " +
                                                        "'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "
3. 次に、ドキュメントを月単位、日単位、時間単位、分単位、および発生した合計回数で集計します。

       # GROUP BY minute and COUNT entries for each.
       $queryStringPart2 = "timestamp_record = FOREACH DocumentDB_timestamps GENERATE `$0#'id' as id:int, ToDate((long)(`$0#'ts') * 1000) as timestamp:datetime; " +
                           "by_minute = GROUP timestamp_record BY (GetYear(timestamp), GetMonth(timestamp), GetDay(timestamp), GetHour(timestamp), GetMinute(timestamp)); " +
                           "by_minute_count = FOREACH by_minute GENERATE FLATTEN(group) as (Year:int, Month:int, Day:int, Hour:int, Minute:int), COUNT(timestamp_record) as Total:int; "
4. 最後に、その結果を新しい出力コレクションに格納します。

   > [!NOTE]
   > 次のように、複数のコレクションを&1; つの出力として追加することができます。 </br>
   > '\<DocumentDB Output Collection Name 1\>,\<DocumentDB Output Collection Name 2\>'</br> コレクション名は、間にスペースを入れずにコンマだけで区切ります。</br>
   > ドキュメントは複数のコレクションに対してラウンドロビン形式で分散されます。 ドキュメントの&1; つ目のバッチが&1; つのコレクションに格納され、2 つ目のバッチが次のコレクションに格納されて、以降、同様に処理されます。
   >
   >

        # Store output data to DocumentDB.
        $queryStringPart3 = "STORE by_minute_count INTO '<DocumentDB Endpoint>' " +
                            "USING com.microsoft.azure.documentdb.pig.DocumentDBStorage( " +
                                "'<DocumentDB Primary Key>', " +
                                "'<DocumentDB Database Name>', " +
                                "'<DocumentDB Output Collection Name>'); "
5. 次のスクリプト スニペットを追加して、前のクエリから Pig ジョブ定義を作成します。

        # Create a Pig job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $queryString -StatusFolder $statusFolder

    -File スイッチを使用して、HDFS 上の Pig スクリプト ファイルを指定することもできます。
6. 次のスニペットを追加して、開始時刻を格納し、Pig ジョブを送信します。

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition
7. 次のスニペットを追加して、Pig ジョブが完了するのを待機します。

        # Wait for the Pig job to complete.
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600
8. 次のスニペットを追加して、標準出力および開始時刻と終了時刻を表示します。

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
9. **実行しましょう** 。 **左側のナビゲーションの上部にある** 。
10. 結果を確認します。 [Azure Portal][azure-portal] にサインインします。

    1. 左側ウィンドウの <strong>[参照]</strong> をクリックします。 </br>
    2. 参照ウィンドウの右上にある <strong>[すべて]</strong> をクリックします。 </br>
    3. <strong>[DocumentDB アカウント]</strong> を見つけてクリックします。 </br>
    4. 次に、自分の <strong>DocumentDB アカウント</strong>、<strong>DocumentDB データベース</strong>、Pig クエリで指定した出力コレクションに関連付けられた <strong>DocumentDB コレクション</strong>を見つけます。</br>
    5. 最後に、<strong>[開発者ツール]</strong> の下にある <strong>[ドキュメント エクスプローラー]</strong> をクリックします。</br></p>

    Pig クエリの結果が表示されます。

    ![Pig クエリの結果][image-pig-query-results]

## <a name="RunMapReduce"></a>手順 5: DocumentDB と HDInsight を使用して MapReduce ジョブを実行する
1. PowerShell スクリプト ウィンドウで次の変数を設定します。

        $subscriptionName = "<SubscriptionName>"   # Azure subscription name
        $clusterName = "<ClusterName>"             # HDInsight cluster name
2. DocumentDB コレクションから各ドキュメント プロパティの発生回数を集計する MapReduce ジョブを実行します。 このスクリプト スニペットを上記のスニペットの **後に** 追加します。

        # Define the MapReduce job.
        $TallyPropertiesJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/TallyProperties-v01.jar" -ClassName "TallyProperties" -Arguments "<DocumentDB Endpoint>","<DocumentDB Primary Key>", "<DocumentDB Database Name>","<DocumentDB Input Collection Name>","<DocumentDB Output Collection Name>","<[Optional] DocumentDB Query>"

   > [!NOTE]
   > TallyProperties-v01.jar は、DocumentDB Hadoop コネクタのカスタム インストールに付属しています。
   >
   >
3. 次のコマンドを追加して、MapReduce ジョブを送信します。

        # Save the start time and submit the job.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $TallyPropertiesJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $TallyPropertiesJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    MapReduce ジョブ定義に加えて、MapReduce ジョブを実行する HDInsight クラスター名、および資格情報も指定します。 Start-AzureHDInsightJob は非同期呼び出しです。 ジョブの完了を確認するには、 *Wait-AzureHDInsightJob* コマンドレットを使用します。
4. 次のコマンドを追加して、MapReduce ジョブの実行中に発生したエラーを確認します。

        # Get the job output and print the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $TallyPropertiesJob.JobId -StandardError
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
5. **実行しましょう** 。 **左側のナビゲーションの上部にある** 。
6. 結果を確認します。 [Azure Portal][azure-portal] にサインインします。

   1. 左側ウィンドウの <strong>[参照]</strong> をクリックします。
   2. 参照ウィンドウの右上にある <strong>[すべて]</strong> をクリックします。
   3. <strong>[DocumentDB アカウント]</strong> を見つけてクリックします。
   4. 次に、自分の <strong>DocumentDB アカウント</strong>、<strong>DocumentDB データベース</strong>、MapReduce ジョブで指定した出力コレクションに関連付けられた <strong>DocumentDB コレクション</strong>を見つけます。
   5. 最後に、<strong>[開発者ツール]</strong> の下にある <strong>[ドキュメント エクスプローラー]</strong> をクリックします。

      MapReduce ジョブの結果が表示されます。

      ![MapReduce クエリの結果][image-mapreduce-query-results]

## <a name="NextSteps"></a>次のステップ
ご利用ありがとうございます。 Azure DocumentDB および HDInsight を使用して最初の Hive、Pig、および MapReduce ジョブを実行しました。

Microsoft では Hadoop コネクタをオープン ソース化しています。 関心がある場合は、[GitHub][documentdb-github] に投稿できます。

詳細については、次の記事を参照してください。

* [DocumentDB を使用した Java アプリケーションの開発][documentdb-java-application]
* [HDInsight での Hadoop 用 Java MapReduce プログラムの開発][hdinsight-develop-deploy-java-mapreduce]
* [HDInsight で Hive と Hadoop を使用し、モバイル ハンドセットの使用状況を分析する][hdinsight-get-started]
* [HDInsight での MapReduce の使用][hdinsight-use-mapreduce]
* [HDInsight での Hive の使用][hdinsight-use-hive]
* [HDInsight での Pig の使用][hdinsight-use-pig]
* [スクリプト アクションを使って HDInsight クラスターをカスタマイズする][hdinsight-hadoop-customize-cluster]

[apache-hadoop]: http://hadoop.apache.org/
[apache-hadoop-doc]: http://hadoop.apache.org/docs/current/
[apache-hive]: http://hive.apache.org/
[apache-pig]: http://pig.apache.org/
[getting-started]: documentdb-get-started.md

[azure-portal]: https://portal.azure.com/
[azure-powershell-diagram]: ./media/documentdb-run-hadoop-with-hdinsight/azurepowershell-diagram-med.png

[documentdb-hdinsight-samples]: http://portalcontent.blob.core.windows.net/samples/documentdb-hdinsight-samples.zip
[documentdb-github]: https://github.com/Azure/azure-documentdb-hadoop
[documentdb-java-application]: documentdb-java-application.md
[documentdb-import-data]: documentdb-import-data.md

[hdinsight-custom-provision]: ../hdinsight/hdinsight-provision-clusters.md
[hdinsight-develop-deploy-java-mapreduce]: ../hdinsight/hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-hadoop-customize-cluster]: ../hdinsight/hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight/hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-storage]: ../hdinsight/hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: ../hdinsight/hdinsight-use-hive.md
[hdinsight-use-mapreduce]: ../hdinsight/hdinsight-use-mapreduce.md
[hdinsight-use-pig]: ../hdinsight/hdinsight-use-pig.md

[image-customprovision-page1]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page1.png
[image-hive-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/hivequeryresults.PNG
[image-mapreduce-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/mapreducequeryresults.PNG
[image-pig-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/pigqueryresults.PNG

[powershell-install-configure]: /powershell/azureps-cmdlets-docs

