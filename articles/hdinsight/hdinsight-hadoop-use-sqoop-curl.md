---
title: "HDInsight での Hadoop Sqoop と Curl の使用 | Microsoft Docs"
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
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: eabcd8b70411a915722a2f1b8e8b70271cd8c7c2
ms.lasthandoff: 03/21/2017


---
# <a name="run-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>HDInsight の Hadoop で Curl を使用して Sqoop ジョブを実行する
[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Curl を使用して HDInsight の Hadoop クラスターで Sqoop ジョブを実行する方法を説明します。

Curl は、未加工の HTTP 要求を使用して HDInsight とやり取りし、Sqoop ジョブを実行、監視して、その結果を取得する方法を示すために使用します。 これは、HDInsight クラスターで提供される WebHCat REST API (旧称: Templeton) を使用することで機能します。

> [!NOTE]
> Linux ベースの Hadoop サーバーは使い慣れているが HDInsight は初めてという場合は、「[Linux での HDInsight の使用方法](hdinsight-hadoop-linux-information.md)」をご覧ください。
> 
> 

## <a name="prerequisites"></a>前提条件
この記事の手順を完了するには、次のものが必要です。

* HDInsight クラスター (Linux または Windows ベース) の Hadoop
* [Curl](http://curl.haxx.se/)
* [jq](http://stedolan.github.io/jq/)

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
   
        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
   
    次のような応答を受け取ります。
   
        {"status":"ok","version":"v1"}
   
    このコマンドで使用されるパラメーターの意味は次のとおりです。
   
   * **-u** : 要求の認証に使用するユーザー名とパスワード
   * **-G** : GET 要求であることを示します。
     
     URL の先頭は **https://CLUSTERNAME.azurehdinsight.net/templeton/v1** で、これはすべての要求で共通です。 パス **/status** は、要求がサーバー用の WebHCat (別名: Templeton) の状態を返すことを示します。 
2. 次のコマンドを使用して sqoop ジョブを送信します。

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /tutorials/usesqoop/data --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop

    このコマンドで使用されるパラメーターの意味は次のとおりです。

    * **-d** - `-G` が使用されていないため、要求は既定で POST メソッドになります。 `-d` は要求で送信されるデータ値を指定します。

        * **user.name** : コマンドを実行するユーザー

        * **command** : 実行する Sqoop コマンド。

        * **statusdir** : ジョブのステータスが書き込まれるディレクトリ

    このコマンドは、ジョブのステータスの確認に使用できる ジョブ ID を返します。

        {"id":"job_1415651640909_0026"}

1. ジョブのステータスを確認するには、次のコマンドを使用します。 **JOBID** を前の手順で返された値に置き換えます。 たとえば、戻り値が `{"id":"job_1415651640909_0026"}` の場合、**JOBID** は `job_1415651640909_0026` になります。
   
        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
   
    ジョブが完了している場合、ステータスは **SUCCEEDED**になります。
   
   > [!NOTE]
   > この Curl 要求では、ジョブに関する情報が記載された JavaScript Object Notation (JSON) ドキュメントが返されます。状態値のみを取得するには jq を使用します。
   > 
   > 
2. ジョブのステータスが **SUCCEEDED** に変わったら、Azure BLOB ストレージからジョブの結果を取得できます。 クエリで渡される `statusdir` パラメーターには出力ファイルの場所を含めます。この場合は、**wasbs:///example/curl** になります。 このアドレスではジョブの出力は、HDInsight クラスターが使用する既定のストレージ コンテナーの **example/curl** ディレクトリに保存されます。
   
    これらのファイルを一覧表示およびダウンロードするには [Azure CLI](../cli-install-nodejs.md)を使用します。 たとえば、 **example/curl**内のファイルを一覧表示するには、次のコマンドを使用します。
   
        azure storage blob list <container-name> example/curl
   
    ファイルをダウンロードするには、次のコマンドを使用します。
   
        azure storage blob download <container-name> <blob-name> <destination-file>
   
   > [!NOTE]
   > `-a` と `-k` パラメーターを使用して BLOB を含むストレージ アカウントの名前を指定するか、環境変数 **AZURE\_STORAGE\_ACCOUNT** と **AZURE\_STORAGE\_ACCESS\_KEY** を設定する必要があります。 詳細情報については、「<a href="hdinsight-upload-data.md" target="_blank"」 をご覧ください。
   > 
   > 

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

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx



