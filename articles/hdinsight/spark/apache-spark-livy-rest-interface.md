---
title: Livy Spark を使用した Azure HDInsight での Spark クラスターへのジョブの送信
description: Apache Spark REST API を使用して Spark ジョブを Azure HDInsight クラスターにリモート送信する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: f5b3500e1e700abf894fc4e21fb540eb258d5e35
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066067"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Apache Spark REST API を使用してリモート ジョブを HDInsight Spark クラスターに送信する

[Apache Spark](https://spark.apache.org/) REST API の [Apache Livy](https://livy.incubator.apache.org/) を使用する方法について説明します。これを使用して、リモート ジョブを Azure HDInsight Spark クラスターに送信します。 詳細なドキュメントについては、 [https://livy.incubator.apache.org/](https://livy.incubator.apache.org/) に関するページを参照してください。

Livy を使用すると、対話型の Spark シェルを実行したり、Spark で実行されるバッチ ジョブを送信したりすることができます。 この記事では、Livy を使用してバッチ ジョブを送信する方法について説明します。 この記事のスニペットでは、cURL を使用して、Livy Spark エンドポイントへの REST API 呼び出しを行います。

## <a name="prerequisites"></a>前提条件

* HDInsight での Apache Spark クラスター。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](apache-spark-jupyter-spark-sql.md)」を参照してください。

* [cURL](https://curl.haxx.se/)。 この記事では、cURL を使用して、HDInsight Spark クラスターに対して REST API 呼び出しを行う方法を説明します。

## <a name="submit-an-apache-livy-spark-batch-job"></a>Apache Livy Spark バッチ ジョブの送信

バッチ ジョブを送信する前に、クラスターに関連付けられているクラスター ストレージにアプリケーション jar をアップロードする必要があります。 コピーには、[AzCopy](../../storage/common/storage-use-azcopy.md) コマンドライン ユーティリティを使用できます。 データのアップロードに使用できるクライアントは、他にも多数あります。 詳細については、[HDInsight での Apache Hadoop ジョブ用データのアップロード](../hdinsight-upload-data.md)に関するページを参照してください。

```cmd
curl -k --user "<hdinsight user>:<user password>" -v -H "Content-Type: application/json" -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"
```

### <a name="examples"></a>例

* Jar ファイルがクラスター ストレージ (WASB) にある場合

    ```cmd  
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST -d '{ "file":"wasb://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

* 入力ファイル (この例では input.txt) の一部として、jar ファイル名とクラス名を渡す場合

    ```cmd
    curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>クラスターで実行されている Livy Spark バッチの情報の取得

構文:

```cmd
curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"
```

### <a name="examples"></a>例

* クラスターで実行されているすべての Livy Spark バッチを取得する場合

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches" 
    ```

* バッチ ID を指定して特定のバッチを取得する場合

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"
    ```

## <a name="delete-a-livy-spark-batch-job"></a>Livy Spark バッチ ジョブの削除

```cmd
curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"
```

### <a name="example"></a>例

バッチ ID が `5` のバッチ ジョブを削除します。

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/5"
```

## <a name="livy-spark-and-high-availability"></a>Livy Spark と高可用性

Livy は、クラスター上で実行される Spark ジョブに対する高可用性を提供します。 いくつかの例を次に示します。

* リモートから Spark クラスターにジョブを送信した後で Livy サービスがダウンした場合、そのジョブはバックグラウンドで引き続き実行されます。 Livy が再度稼働状態に戻ると、ジョブの状態を復元してその旨を報告します。
* HDInsight の Jupyter Notebook は、バックエンドで Livy が機能しています。 ノートブックで Spark ジョブを実行中に Livy サービスが再起動された場合、コード セルは引き続き実行されます。 

## <a name="show-me-an-example"></a>実際の例

このセクションでは、Livy Spark を使用してバッチ ジョブを送信し、ジョブの進行状況を監視し、最後にジョブを削除する例について説明します。 この例で使用するアプリケーションは、「[スタンドアロン Scala アプリケーションを作成して HDInsight Spark クラスターで実行する](apache-spark-create-standalone-application.md)」という記事で開発したものです。 ここの手順では、以下を前提とします。

* クラスターに関連付けられているストレージ アカウントに、アプリケーション jar を既にコピーしてある。
* この手順を行うコンピューターに CuRL をインストールしてある。

次の手順に従います。

1. まず、クラスターで Livy Spark が実行されていることを確認します。 そのためには、実行中のバッチの一覧を取得します。 Livy を使用するジョブを初めて実行する場合、出力は 0 を返します。

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
    ```

    次のスニペットのような出力が表示されます。

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:47:53 GMT
    < Content-Length: 34
    <
    {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    出力の最後の行に **total:0** とある点に注意してください。これは、実行中のバッチがないことを示しています。

2. それでは、バッチ ジョブを送信してみましょう。 次のスニペットは、入力ファイル (input.txt) を使用して、jar 名とクラス名をパラメーターとして渡します。 この手順を Windows コンピューターから実行している場合は、入力ファイルを使用することをお勧めします。

    ```cmd
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

    **input.txt** ファイル内のパラメーターは、次のように定義されています。

    ```text
    { "file":"wasb:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
    ```

    次のスニペットのような出力が表示されます。

    ```output
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
    ```

    出力の最後の行に **state:starting**とある点に注意してください。 また、 **id:0**とも表示されています。 ここで、**0** はバッチ ID です。

3. これで、バッチ ID を使用して、この特定のバッチの状態を取得できるようになりました。

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
    ```

    次のスニペットのような出力が表示されます。

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:54:42 GMT
    < Content-Length: 509
    <
    {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    今度は出力に **state:success** と表示されます。これは、ジョブが正常に完了したことを意味しています。

4. 必要であれば、バッチを削除することができます。

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
    ```

    次のスニペットのような出力が表示されます。

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Sat, 21 Nov 2015 18:51:54 GMT
    < Content-Length: 17
    <
    {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    出力の最後の行は、バッチが正常に削除されたことを示しています。 実行時にジョブを削除すると、ジョブも強制終了します。 正常に終了したかどうかにかかわらず、完了済みのジョブを削除すると、ジョブ情報が完全に削除されます。

## <a name="updates-to-livy-configuration-starting-with-hdinsight-35-version"></a>HDInsight 3.5 バージョン以降の Livy 構成の更新

HDInsight 3.5 以上のクラスターでは、サンプル データ ファイルまたは jar にアクセスするためのローカル ファイル パスの使用が既定で無効になっています。 そのため、クラスターから jar ファイルやサンプル データ ファイルにアクセスするのではなく、`wasb://` のパスを使用することをお勧めします。

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Azure 仮想ネットワーク内でクラスターの Livy ジョブを送信する

Azure 仮想ネットワーク内から HDInsight Spark クラスターに接続すると、クラスター上の Livy に直接接続することができます。 このような場合、Livy エンドポイントの URL は `http://<IP address of the headnode>:8998/batches` になります。 ここで、**8998** は、クラスター ヘッドノードで Livy が実行されているポートです。 非パブリック ポート上のサービスへのアクセスの詳細については、「[HDInsight 上の Hadoop サービスで使用されるポート](../hdinsight-hadoop-port-settings-for-services.md)」を参照してください。

## <a name="next-steps"></a>次の手順

* [Apache Livy REST API ドキュメント](https://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)