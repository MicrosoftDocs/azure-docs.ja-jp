---
title: "HDInsight での Hadoop Sqoop と Curl の使用 - Azure | Microsoft Docs"
description: "Curl を使用して Sqoop ジョブを HDInsight にリモートで送信する方法について説明します。"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 39798321-78ca-428c-bcfe-322e49af4059
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/22/2017
ms.author: jgao
ms.openlocfilehash: 5aa47b4b12dc136a3f6ba66688804859f9eb5446
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="run-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>HDInsight の Hadoop で Curl を使用して Sqoop ジョブを実行する
[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Curl を使用して HDInsight の Hadoop クラスターで Sqoop ジョブを実行する方法を説明します。

Curl は、未加工の HTTP 要求を使用して HDInsight とやり取りし、Sqoop ジョブを実行、監視して、その結果を取得する方法を示すために使用します。 これは、HDInsight クラスターで提供される WebHCat REST API (旧称: Templeton) を使用することで機能します。

## <a name="prerequisites"></a>前提条件
この記事の手順を完了するには、次のものが必要です。

* 「[HDInsight の Hadoop での Sqoop の使用](./hdinsight-use-sqoop.md#create-cluster-and-sql-database)」を完了し、HDInsight クラスターと Azure SQL データベースを含む環境を構成します。
* [Curl](http://curl.haxx.se/)。 Curl は、HDInsight クラスターとの間でデータを転送するツールです。
* [jq](http://stedolan.github.io/jq/)。 jq ユーティリティを使用して REST 要求から返された JSON データを処理します。

## <a name="submit-sqoop-jobs-by-using-curl"></a>Curl を使用して Sqoop ジョブを送信する
> [!NOTE]
> Curl、または WebHCat を使用したその他の REST 通信を使用する場合は、HDInsight クラスター管理者のユーザー名とパスワードを指定して要求を認証する必要があります。 また、サーバーへの要求の送信に使用する Uniform Resource Identifier (URI) にクラスター名を含める必要があります。
> 
> このセクションのコマンドでは、 **USERNAME** をクラスターに対して認証するユーザーの名前に置き換え、 **PASSWORD** をユーザー アカウントのパスワードに置き換えます。 **CLUSTERNAME** をクラスターの名前に置き換えます。
> 
> REST API のセキュリティは、 [基本認証](http://en.wikipedia.org/wiki/Basic_access_authentication)を通じて保護されています。 資格情報をサーバーに安全に送信するには、必ずセキュア HTTP (HTTPS) を使用して要求を行う必要があります。
> 
> 

1. コマンド ラインで次のコマンドを使用して、HDInsight クラスターに接続できることを確認します。

    ```bash   
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    次のような応答を受け取ります。

    ```json   
    {"status":"ok","version":"v1"}
    ```
   
    このコマンドで使用されるパラメーターの意味は次のとおりです。
   
   * **-u** : 要求の認証に使用するユーザー名とパスワード
   * **-G** : GET 要求であることを示します。
     
     URL の先頭は **https://CLUSTERNAME.azurehdinsight.net/templeton/v1** で、すべての要求において共通です。 パス **/status** は、要求がサーバー用の WebHCat (別名: Templeton) の状態を返すことを示します。 
2. 次のコマンドを使用して sqoop ジョブを送信します。

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    このコマンドで使用されるパラメーターの意味は次のとおりです。

    * **-d** - `-G` が使用されていないため、要求は既定で POST メソッドになります。 `-d` は要求で送信されるデータ値を指定します。

        * **user.name** : コマンドを実行するユーザー

        * **command** : 実行する Sqoop コマンド。

        * **statusdir** : ジョブのステータスが書き込まれるディレクトリ

    このコマンドは、ジョブのステータスの確認に使用できるジョブ ID を返します。

        ```json
        {"id":"job_1415651640909_0026"}
        ```

3. ジョブのステータスを確認するには、次のコマンドを使用します。 **JOBID** を前の手順で返された値に置き換えます。 たとえば、戻り値が `{"id":"job_1415651640909_0026"}` の場合、**JOBID** は `job_1415651640909_0026` になります。

    ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    ジョブが完了している場合、ステータスは **SUCCEEDED**になります。
   
   > [!NOTE]
   > この Curl 要求では、ジョブに関する情報が記載された JavaScript Object Notation (JSON) ドキュメントが返されます。状態値のみを取得するには jq を使用します。
   > 
   > 
4. ジョブのステータスが **SUCCEEDED** に変わったら、Azure BLOB ストレージからジョブの結果を取得できます。 クエリで渡される `statusdir` パラメーターには出力ファイルの場所を含めます。この場合は、**wasb:///example/data/sqoop/curl** になります。 このアドレスではジョブの出力は、HDInsight クラスターが使用する既定のストレージ コンテナーの **example/data/sqoop/curl** ディレクトリに保存されます。
   
    Azure Portal を使用して、stderr および stdout BLOB にアクセスできます。  また、Microsoft SQL Server Management Studio を使用して、log4jlogs テーブルにアップロードされているデータを確認することもできます。

## <a name="limitations"></a>制限事項
* 一括エクスポート - Linux ベースの HDInsight では、Microsoft SQL Server または Azure SQL Database にデータをエクスポートするために使用する Sqoop コネクタは、一括挿入を現在サポートしていません。
* バッチ処理 - Linux ベースの HDInsight で、挿入処理実行時に `-batch` スイッチを使用すると、Sqoop は挿入操作をバッチ処理するのではなく、複数の挿入処理を実行します。

## <a name="summary"></a>概要
このドキュメントで示したように、未加工の HTTP 要求を使用して、HDInsight クラスターで Sqoop ジョブを実行、監視し、その結果を表示できます。

この記事で使用されている REST インターフェイスの詳細については、「<a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Sqoop REST API guide (Sqoop REST API ガイド)</a>」をご覧ください。

## <a name="next-steps"></a>次のステップ
HDInsight での Hive に関する全般的な情報

* [HDInsight の Hadoop での Sqoop の使用](hdinsight-use-sqoop.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)
* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)

curl に関するその他の HDInsight の記事:
 
* [Azure REST API を使用して Hadoop クラスターを作成する](hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [REST を使用した HDInsight における Hadoop での Hive クエリの実行](hdinsight-hadoop-use-hive-curl.md)
* [REST を使用して HDInsight の Hadoop で MapReduce ジョブを実行](hdinsight-hadoop-use-mapreduce-curl.md)
* [Curl を使用して HDInsight の Hadoop で Pig ジョブを実行](hdinsight-hadoop-use-pig-curl.md)



