---
title: "Livy を使用した Azure HDInsight での Spark へのジョブのリモート送信 | Microsoft Docs"
description: "Livy と HDInsight クラスターを使用して Spark ジョブをリモートで送信する方法について説明します。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2817b779-1594-486b-8759-489379ca907d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 6cb0da6d7b3aafeb9a8079b427e31c66811a6281
ms.lasthandoff: 03/25/2017


---
# <a name="submit-spark-jobs-remotely-to-an-apache-spark-cluster-on-hdinsight-using-livy"></a>Livy を使用して HDInsight で Spark ジョブを Apache Spark クラスターにリモートで送信する

Azure HDInsight の Apache Spark クラスターには、Livy が含まれています。これは、Spark クラスターにリモートでジョブを送信するための REST インターフェイスです。 詳細なドキュメントについては、[Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server) に関するページを参照してください。

Livy を使用すると、対話型の Spark シェルを実行したり、Spark で実行されるバッチ ジョブを送信したりすることができます。 この記事では、Livy を使用してバッチ ジョブを送信する方法について説明します。 以下の構文では、Curl を使用して、Livy エンドポイントへの REST 呼び出しを行います。

**前提条件:**

次のものが必要です。

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* HDInsight での Apache Spark クラスター。 手順については、 [Azure HDInsight での Apache Spark クラスターの作成](hdinsight-apache-spark-jupyter-spark-sql.md)に関するページを参照してください。

## <a name="submit-a-batch-job"></a>バッチ ジョブの送信
バッチ ジョブを送信する前に、クラスターに関連付けられているクラスター ストレージにアプリケーション jar をアップロードする必要があります。 コピーには、[**AzCopy**](../storage/storage-use-azcopy.md) コマンド ライン ユーティリティを使用できます。 データのアップロードに使用できるクライアントは、他にも多数あります。 詳細については、「[HDInsight での Hadoop ジョブ用データのアップロード](hdinsight-upload-data.md)」を参照してください。

    curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**例**:

* Jar ファイルがクラスター ストレージ (WASB) にある場合
  
        curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"
* 入力ファイル (この例では input.txt) の一部として、jar ファイル名とクラス名を渡す場合
  
        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## <a name="get-information-on-batches-running-on-the-cluster"></a>クラスターで実行されているバッチの情報の取得
    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**例**:

* クラスターで実行されているすべてのバッチを取得する場合
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
* バッチ ID を指定して特定のバッチを取得する場合
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="delete-a-batch-job"></a>バッチ ジョブの削除
    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**例**:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-and-high-availability"></a>Livy と高可用性
Livy は、クラスター上で実行される Spark ジョブに対する高可用性を提供します。 いくつかの例を次に示します。

* リモートから Spark クラスターにジョブを送信した後で Livy サービスがダウンした場合、そのジョブはバックグラウンドで引き続き実行されます。 Livy が再度稼働状態に戻ると、ジョブの状態を復元してその旨を報告します。
* HDInsight の Jupyter Notebook は、バックエンドで Livy が機能しています。 ノートブックで Spark ジョブを実行中に Livy サービスが再起動された場合、コード セルは引き続き実行されます。 

## <a name="show-me-an-example"></a>実際の例
このセクションでは、Livy を使用して Spark アプリケーションを送信し、アプリケーションの進行状況を監視し、最後にジョブを削除する例について説明します。 この例で使用するアプリケーションは、「[スタンドアロン Scala アプリケーションを作成して HDInsight Spark クラスターで実行する](hdinsight-apache-spark-create-standalone-application.md)」という記事で開発したものです。 この手順では、以下のことを前提としています。

* クラスターに関連付けられているストレージ アカウントに、アプリケーション jar を既にコピーしてある。
* この手順を行うコンピューターに CuRL をインストールしてある。

次の手順に従います。

1. まず、クラスターで Livy が実行されていることを確認します。 そのためには、実行中のバッチの一覧を取得します。 Livy を使用するジョブを初めて実行する場合は、0 が返されるはずです。
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    次のような出力が表示されます。
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:47:53 GMT
        < Content-Length: 34
        <
        {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    出力の最後の行に **total:0** とある点に注意してください。これは、実行中のバッチがないことを示しています。
2. それでは、バッチ ジョブを送信してみましょう。 次のスニペットは、入力ファイル (input.txt) を使用して、jar 名とクラス名をパラメーターとして渡します。 これは、この手順を Windows コンピューターから実行している場合に推奨される方法です。
   
        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    **input.txt** ファイル内のパラメーターは、次のように定義されています。
   
        { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
   
    次のような出力が表示されます。
   
        < HTTP/1.1 201 Created
        < Content-Type: application/json; charset=UTF-8
        < Location: /0
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:51:30 GMT
        < Content-Length: 36
        <
        {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    出力の最後の行に **state:starting**とある点に注意してください。 また、 **id:0**とも表示されています。 これは、バッチ ID です。
3. これで、バッチ ID を使用して、この特定のバッチの状態を取得できるようになりました。
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    次のような出力が表示されます。
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:54:42 GMT
        < Content-Length: 509
        <
        {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    今度は出力に **state:success** と表示されます。これは、ジョブが正常に完了したことを意味しています。
4. 必要であれば、バッチを削除することができます。
   
        curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    次のような出力が表示されます。
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Sat, 21 Nov 2015 18:51:54 GMT
        < Content-Length: 17
        <
        {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    出力の最後の行は、バッチが正常に削除されたことを示しています。 ジョブを実行中に削除すると、ジョブは強制終了されます。 正常に終了したかどうかにかかわらず、完了済みのジョブを削除すると、ジョブ情報が完全に削除されます。

## <a name="using-livy-on-hdinsight-35-spark-clusters"></a>HDInsight 3.5 Spark クラスターでの Livy の使用

HDInsight 3.5 クラスターでは、サンプル データ ファイルまたは jar ファイルにアクセスするためのローカル ファイル パスの使用が既定で無効になっています。 そのため、クラスターから jar ファイルやサンプル データ ファイルにアクセスするのではなく、`wasb://` のパスを使用することをお勧めします。 ローカル パスを使用する場合は、Ambari 構成を適宜更新する必要があります。 そのためには、次の手順を実行します。

1. クラスターの Ambari ポータルに移動します。 Ambari Web UI はお使いの HDInsight クラスター (https://**CLUSTERNAME**.azurehdidnsight.net) にあります。CLUSTERNAME はお使いのクラスターの名前になります。

2. 左側のナビゲーションで、**[Livy]**、**[Configs]** の順にクリックします。

3. ファイル システムへのフル アクセスを許可する場合は、**livy-default** でプロパティ名 `livy.file.local-dir-whitelist` を追加し、その値を **"/"** に設定します。 特定のディレクトリへのアクセスのみを許可する場合は、そのディレクトリへのパスを値として指定します。

## <a name="troubleshooting"></a>トラブルシューティング

Spark クラスターへのリモート ジョブ送信に Livy を使用する際に、問題が発生する可能性があります。

### <a name="using-an-external-jar-from-the-additional-storage-is-not-supported"></a>追加のストレージからの外部 jar の使用がサポートされない

**問題:** クラスターに関連付けられている追加のストレージからの外部 jar を参照する Livy を使用して Spark ジョブを実行すると、ジョブが失敗します。

**解決方法:** 使用する jar が、HDInsight クラスターに関連付けられている既定のストレージで使用可能か確認してください。


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

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applicatons (Linux)](hdinsight-apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](hdinsight-apache-spark-job-debugging.md)


